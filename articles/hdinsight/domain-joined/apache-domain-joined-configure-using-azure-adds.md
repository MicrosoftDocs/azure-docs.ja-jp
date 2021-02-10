---
title: Azure Active Directory 統合用のクラスターの構成
titleSuffix: Azure HDInsight
description: Azure Active Directory Domain Services と Enterprise セキュリティ パッケージ機能を使用して、Active Directory と統合された HDInsight クラスターを設定して構成する方法について学習します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: 15869a547ec5debee939c956d7495bfa58357555
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946919"
---
# <a name="configure-hdinsight-clusters-for-azure-active-directory-integration-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージを使用して Azure Active Directory 統合用に HDInsight クラスターを構成する

この記事では、Azure Active Directory と統合された HDInsight クラスターを作成および構成するプロセスの要約と概要について説明します。 この統合は、Enterprise セキュリティ パッケージ (ESP) と呼ばれる HDInsight 機能、Azure Active Directory Domain Services (Azure AD DS)、および既存のオンプレミス Active Directory に依存しています。

Azure でドメインを設定して構成し、ESP が有効なクラスターを作成して、オンプレミスのユーザーを同期するための詳細な手順については、「[Azure HDInsight で Enterprise セキュリティ パッケージ クラスターを作成および構成する](apache-domain-joined-create-configure-enterprise-security-cluster.md)」を参照してください。

## <a name="background"></a>バックグラウンド

Enterprise セキュリティ パッケージ (ESP) には、Azure HDInsight 向けの Active Directory 統合が用意されています。 この統合により、ドメイン ユーザーは自分のドメイン資格情報を使用して HDInsight クラスターの認証を受け、ビッグ データ ジョブを実行することができます。

> [!NOTE]  
> ESP は、次の種類のクラスターを対象として、HDInsight 3.6 および 4.0 で一般提供されています: Apache Spark、Interactive、Hadoop、HBase。 クラスターの種類 Apache Kafka 用の ESP は、プレビュー段階にあり、ベスト エフォート サポートのみとなっています。 ESP GA の日付 (2018 年 10 月 1 日) より前に作成された ESP クラスターはサポートされません。

## <a name="prerequisites"></a>前提条件

ESP が有効な HDInsight クラスターを作成する前に、いくつかの前提条件を満たす必要があります。

- 既存のオンプレミスの Active Directory と Azure Active Directory
- Azure AD-DS を有効にする
- Azure AD-DS の正常性状態を確認して、同期を確実に完了する
- マネージド ID を作成して承認する
- DNS および関連する問題のネットワーク設定を完了する

これらの各項目については、以下で詳しく説明します。 これらすべての手順を完了するためのチュートリアルについては、「[Azure HDInsight で Enterprise セキュリティ パッケージ クラスターを作成および構成する](apache-domain-joined-create-configure-enterprise-security-cluster.md)」を参照してください。

### <a name="enable-azure-ad-ds"></a>Azure AD DS を有効にする

Azure AD DS を有効にすることは、ESP を使用する HDInsight クラスターを作成するための前提条件です。 詳細については、[Azure portal を使用した Azure Active Directory Domain Services の有効化](../../active-directory-domain-services/tutorial-create-instance.md)に関するページを参照してください。

Azure AD DS が有効の場合、すべてのユーザーとオブジェクトについて、Azure Active Directory (Azure AD) から Azure AD DS への同期が既定で開始されます。 同期操作の長さは、Azure AD 内のオブジェクトの数によって異なります。 数十万のオブジェクトがある場合、同期には数日かかることがあります。

Azure AD DS で使用するドメイン名は、HDInsight で動作するように、39 文字以下とする必要があります。

必要に応じて、アクセスが必要なグループのみを HDInsight クラスターと同期させることができます。 特定のグループのみを同期するこのオプションは、"*範囲指定された同期*" と呼ばれます。 手順については、[Azure AD からご利用のマネージド ドメインまで範囲指定された同期の構成](../../active-directory-domain-services/scoped-synchronization.md)に関するページを参照してください。

セキュリティで保護された LDAP を有効にする場合は、サブジェクト名にドメイン名を入力します。 およびサブジェクトの別名を証明書に入力します。 ドメイン名が *contoso100.onmicrosoft.com* の場合は、自分の証明書サブジェクト名とサブジェクトの別名にその正確な名前が存在することを確認します。 詳細については、「[Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成](../../active-directory-domain-services/tutorial-configure-ldaps.md)」を参照してください。

次の例では、自己署名証明書を作成します。 ドメイン名 *contoso100.onmicrosoft.com* は、`Subject` (サブジェクト名) と `DnsName` (サブジェクトの別名) の両方の中にあります。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Azure AD DS を有効にする特権が与えられているのはテナント管理者だけです。 クラスター記憶域が Azure Data Lake Storage Gen1 または Gen2 の場合、基本的な Kerberos 認証を使用してクラスターにアクセスする必要があるユーザーに対してのみ、Azure AD Multi-Factor Authentication を無効にする必要があります。
>
> [信頼済み IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) または [条件付きアクセス](../../active-directory/conditional-access/overview.md)を使用して特定のユーザーに対して Multi-Factor Authentication を無効にすることができるのは、それらが HDInsight クラスターの仮想ネットワーク用の IP 範囲にアクセスしている場合 "*のみ*" です。 条件付きアクセスを使用している場合は、Active Directory サービス エンドポイントが HDInsight 仮想ネットワーク上で有効になっていることを確認します。
>
> クラスター記憶域が Azure Blob Storage である場合は、Multi-Factor Authentication を無効にしないでください。

### <a name="check-azure-ad-ds-health-status"></a>Azure AD DS 正常性状態の確認

**[管理]** カテゴリで **[正常性]** を選択して、Azure Active Directory Domain Services の正常性状態を表示します。 Azure AD DS の状態が緑 (実行中) で、同期が完了していることを確認します。

![Azure AD DS の正常性](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

### <a name="create-and-authorize-a-managed-identity"></a>マネージド ID の作成と承認

セキュリティで保護されたドメイン サービスの操作を簡略化するには、"*ユーザー割り当てマネージド ID*" を使用します。 **HDInsight ドメイン サービス共同作成者** ロールをマネージド ID に割り当てると、ドメイン サービス操作を読み取り、作成、変更、および削除できるようになります。

OU やサービス プリンシパルの作成など、一部のドメイン サービス操作が HDInsight Enterprise セキュリティ パッケージに必要です。 マネージド ID は、どのサブスクリプションでも作成できます。 一般的なマネージド ID の詳細については、「[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。 Azure HDInsight でマネージド ID がどのように機能するかに関する詳細は、「[Azure HDInsight のマネージド ID](../hdinsight-managed-identities.md)」を参照してください。

ESP クラスターを設定するには、まだ作成していない場合は、ユーザー割り当てマネージド ID を作成します。 [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)に関するページを参照してください。

次に、**HDInsight ドメイン サービス共同作成者** ロールを、Azure AD DS の **アクセス制御** でマネージド ID に割り当てます。 このロールの割り当てを行うには、Azure AD DS 管理特権が必要です。

![Azure Active Directory Domain Services のアクセス制御](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

**HDInsight ドメイン サービス共同作成者** ロールを割り当てると、Azure AD DS ドメインに対してドメイン サービス操作を行うための適切な (`on behalf of`) アクセスがこの ID に確実に付与されます。 これらの操作には、OU の作成と削除が含まれます。

マネージド ID がロールに与えられると、Azure AD DS 管理者がそれを使用するユーザーを管理します。 まず、管理者がポータルでマネージド ID を選択します。 次に、 **[概要]** で **[アクセス制御 (IAM)]** を選択します。 管理者が **マネージド ID オペレーター** ロールを、ESP クラスターを作成するユーザーまたはグループに割り当てます。

たとえば、Azure AD DS 管理者は、**sjmsi** マネージド ID 用にこのロールを **MarketingTeam** グループに割り当てることができます。 次の図に例を示します。 この割り当てにより、組織内の適切なユーザーがマネージド ID を使用して ESP クラスターを確実に作成できるようになります。

![HDInsight のマネージド ID オペレーター ロールの割り当て](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

### <a name="network-configuration"></a>ネットワークの構成

> [!NOTE]  
> Azure AD DS は、Azure Resource Manager ベースの仮想ネットワークにデプロイする必要があります。 Azure AD DS では、クラシック仮想ネットワークはサポートされません。 詳細については、[Azure portal を使用した Azure Active Directory Domain Services の有効化](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)に関するページを参照してください。

Azure AD DS を有効にします。 その後、ローカルのドメイン ネーム システム (DNS) サーバーが Active Directory 仮想マシン (VM) 上で実行されます。 ご利用の Azure AD DS 仮想ネットワークを、これらのカスタム DNS サーバーを使用するように構成します。 適切な IP アドレスを見つけるには、 **[マネージド]** カテゴリで **[プロパティ]** を選択し、 **[仮想ネットワーク上の IP アドレス]** の下を確認します。

![ローカル DNS サーバーの IP アドレスを見つける](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Azure AD DS 仮想ネットワーク内の DNS サーバーの構成を変更します。 これらのカスタム IP アドレスを使用するには、 **[設定]** カテゴリで **[DNS サーバー]** を選択します。 次に、 **[カスタム]** オプションを選択し、テキスト ボックスに最初の IP アドレスを入力して、 **[保存]** を選択します。 同じ手順に従って、さらに IP アドレスを追加します。

![仮想ネットワークの DNS 構成の更新](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Azure AD DS インスタンスと HDInsight クラスターは、どちらも同じ Azure 仮想ネットワークに配置した方が簡単です。 別の仮想ネットワークを使用する予定の場合は、ドメイン コント ローラーが HDInsight VM に表示されるように、それらの仮想ネットワークをピアリングする必要があります。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

仮想ネットワークがピアリングされたら、HDInsight 仮想ネットワークをカスタム DNS サーバーを使用するように構成します。 Azure AD DS のプライベート IP を DNS サーバーのアドレスとして入力します。 両方の仮想ネットワークで同じ DNS サーバーが使用されている場合、ご利用のカスタム ドメイン名は、正しい IP に解決され、HDInsight から到達可能になります。 たとえば、ドメイン名が `contoso.com` の場合、この手順の後の `ping contoso.com` で正しい Azure AD DS IP に解決されるはずです。

![ピアリングされた仮想ネットワークに合わせてカスタム DNS サーバーを構成する](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

ご自分の HDInsight サブネット内でネットワーク セキュリティ グループ (NSG) 規則を使用している場合は、インバウンドおよびアウトバウンドの両方のトラフィックに対して[必須 IP](../hdinsight-management-ip-addresses.md) を許可する必要があります。

ご利用のネットワークが設定されていることをテストするには、Windows VM を HDInsight 仮想ネットワークまたはサブネットに参加させ、ドメイン名に対して ping を実行します (IP に解決されるはずです)。**ldp.exe** を実行して、Azure AD DS ドメインにアクセスします。 次に、この Windows VM をドメインに参加させて、必要なすべての RPC 呼び出しがクライアントとサーバー間で成功していることを確認します。

**nslookup** を使用して、自分のストレージ アカウントへのネットワーク アクセスを確認します。 または、使用する可能性のある任意の外部データベース (外部 Hive メタストアや Rangers DB など)。 NSG によって Azure AD DS がセキュリティで保護されている場合は、Azure AD DS サブネットの NSG 規則で[必須ポート](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)が許可されていることを確認します。 この Windows VM のドメイン参加に成功した場合、次の手順を続行して ESP クラスターを作成することができます。

## <a name="create-an-hdinsight-cluster-with-esp"></a>ESP を使用する HDInsight クラスターを作成する

前の手順を正しく設定したら、次の手順として、ESP が有効になった HDInsight クラスターを作成します。 HDInsight クラスターを作成するとき、 **[セキュリティとネットワーク]** タブで Enterprise セキュリティ パッケージを有効にできます。デプロイ用の Azure Resource Manager テンプレートには、ポータル エクスペリエンスを 1 回使用します。 次に、後で再利用するために、 **[確認および作成]** ページで、事前入力されたテンプレートをダウンロードします。

クラスターの作成時に [HDInsight ID ブローカー](identity-broker.md) 機能を有効にすることもできます。 ID ブローカー機能を使用すると、Multi-Factor Authentication を使用して Ambari にサインインし、Azure AD DS でパスワード ハッシュを使用することなく必要な Kerberos チケットを取得できます。

> [!NOTE]  
> ESP クラスター名の最初の 6 文字は、環境内で一意である必要があります。 たとえば、異なる仮想ネットワーク内に ESP クラスターが複数ある場合、クラスター名の最初の 6 文字が確実に一意になるように名前付け規則を選択する必要があります。

![Azure HDInsight Enterprise セキュリティ パッケージにおけるドメインの検証](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

ESP を有効にすると、Azure AD DS に関連する一般的な構成ミスが自動的に検出され、検証されます。 これらのエラーを修正した後は、次の手順を続行することができます。

![Azure HDInsight Enterprise セキュリティ パッケージがドメインの検証に失敗しました](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

ESP を使用する HDInsight クラスターを作成する場合は、次のパラメーターを指定する必要があります。

* **クラスター管理者ユーザー**: 同期された Azure AD DS インスタンスから、クラスターの管理者を選択します。 このドメイン アカウントは、同期済みであり、Azure AD DS で使用できなければなりません。

* **クラスターのアクセス グループ**: ユーザーをクラスターに同期させてアクセスさせるセキュリティ グループが、Azure AD DS で使用できる必要があります。 たとえば、HiveUsers グループです。 詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

* **LDAPS URL**: たとえば `ldaps://contoso.com:636` です。

作成したマネージド ID は、新しいクラスターを作成するときに、 **[ユーザー割り当てマネージド ID]** ドロップダウン リストから選択することができます。

![Azure HDInsight ESP Active Directory Domain Services のマネージド ID](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>次のステップ

* Hive ポリシーの構成と Hive クエリの実行については、[ESP を使用した HDInsight クラスター用の Apache Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事を参照してください。
* SSH と ESP を使用して HDInsight クラスターに接続する方法については、[Linux、Unix、または OS X からの HDInsight 上の Linux ベースの Hadoop での SSH キーの使用](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)に関する記事を参照してください。
