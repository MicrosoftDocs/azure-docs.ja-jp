---
title: Azure Active Directory Domain Services を使用した Enterprise セキュリティ パッケージの構成 - Azure HDInsight
description: Azure Active Directory Domain Services を使って HDInsight Enterprise セキュリティ パッケージのクラスターをセットアップして構成する方法について説明する。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: 8699533cd64e6b1778c5e78b8c51eb1efe518c75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67126215"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する

Enterprise セキュリティ パッケージ (ESP) のクラスターでは、Azure HDInsight クラスターに対するマルチ ユーザー アクセスが可能です。 ESP の HDInsight クラスターはドメインに接続されるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受け、ビッグ データ ジョブを実行することができます。

この記事では、Azure Active Directory Domain Services (Azure AD-DS) を使って、ESP の HDInsight クラスターを構成する方法について説明します。

> [!NOTE]  
> ESP は、HDInsight 3.6 および 4.0 で一般提供となっています。対象となるクラスターの種類は、Apache Spark、Interactive、Apache Hadoop、HBase です。 Apache Kafka クラスター タイプ用の ESP はプレビュー段階です。

## <a name="enable-azure-ad-ds"></a>Azure AD-DS を有効にする

> [!NOTE]  
> Azure AD-DS を有効にする特権が与えられているのはテナント管理者だけです。 クラスター記憶域が Azure Data Lake Storage (ADLS) Gen1 または Gen2 の場合、基本的な Kerberos 認証を使用してクラスターにアクセスする必要があるユーザーに対してのみ、Multi-Factor Authentication (MFA) を無効にする必要があります。 [信頼済み IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) または[条件付きアクセス](../../active-directory/conditional-access/overview.md)を使用して特定のユーザーの MFA を無効にすることができるのは、HDInsight クラスターの VNET IP 範囲にアクセスしている場合のみです。 条件付きアクセスを使用している場合は、AD サービス エンドポイントが HDInsight VNET 上で有効になっていることを確認してください。
>
> クラスター ストレージが Azure Blob Storage (WASB) の場合は、MFA を無効にしないでください。

前提条件として、ESP の HDInsight クラスターを作成する前に Azure AD-DS を有効にします。 詳細については、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/create-instance.md)」を参照してください。 

Azure AD-DS が有効の場合、すべてのユーザーとオブジェクトについて、Azure Active Directory (AAD) から Azure AD-DS への同期が既定で開始されます。 同期操作の長さは、Azure AD 内のオブジェクトの数によって異なります。 数十万のオブジェクトがある場合、同期には数日かかる場合があります。 

必要に応じて、アクセスが必要なグループのみを HDInsight クラスターと同期させることができます。 特定のグループのみを同期するこのオプションは、"*範囲指定された同期*" と呼ばれます。 手順については、「[Configure scoped synchronization from Azure AD to your managed domain (Azure AD からマネージド ドメインまで範囲指定された同期を構成する)](../../active-directory-domain-services/scoped-synchronization.md)」を参照してください。

セキュリティで保護された LDAP を有効にする場合は、証明書のサブジェクト名およびサブジェクトの別名にドメイン名を指定します。 たとえば、お使いのドメイン名が *contoso100.onmicrosoft.com* の場合は、ご自身の証明書サブジェクト名およびサブジェクトの別名にその正確な名前が存在することを確認します。 詳細については、「[Azure AD-DS のマネージド ドメインに対するセキュリティで保護された LDAP の構成](../../active-directory-domain-services/configure-ldaps.md)」を参照してください。 以下に、自己署名証明書を作成してサブジェクト名と DnsName (サブジェクト代替名) の両方にドメイン名 (*contoso100.onmicrosoft.com*) を指定する例を示します。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Azure AD-DS 正常性状態の確認
**[管理]** カテゴリで **[正常性]** を選択して、Azure Active Directory Domain Services の正常性状態を表示します。 Azure AD-DS の状態が緑 (実行中) で、同期が完了していることを確認します。

![Azure Active Directory Domain Services の正常性](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>マネージド ID の作成と承認

**ユーザー割り当てマネージド ID** は、ドメイン サービス操作を簡略化して保護するために使用されます。 HDInsight ドメイン サービス共同作成者ロールをマネージド ID に割り当てると、ドメイン サービス操作を読み取り、作成、変更、および削除できるようになります。 OU やサービス プリンシパルの作成など、一部のドメイン サービス操作が HDInsight Enterprise セキュリティ パッケージに必要です。 マネージド ID は、どのサブスクリプションでも作成できます。 一般的なマネージド ID の詳細については、「[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。 Azure HDInsight でマネージド ID がどのように機能するかに関する詳細は、「[Managed identities in Azure HDInsight (Azure HDInsight のマネージド ID)](../hdinsight-managed-identities.md)」を参照してください。

ESP クラスターを設定するには、まだ作成していない場合は、ユーザー割り当てマネージド ID を作成します。 手順については、「[Azure Portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」をご覧ください。 次に、Azure AD-DS アクセス制御のマネージド ID に **HDInsight ドメイン サービス共同作成者**ロールを割り当てます (このロールを割り当てるには、AAD-DS 管理者特権が必要です)。

![Azure Active Directory Domain Services のアクセス制御](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

**HDInsight ドメイン サービス共同作成者**ロールを割り当てると、AAD-DS ドメインで OU の作成や OU の削除などのドメイン サービス操作を行うための適切な (代理の) アクセスがこの ID に付与されます。

マネージド ID が作成され、適切なロールに割り当てられると、AAD-DS 管理者はこのマネージド ID を使用できるユーザーを設定できます。 マネージド ID のユーザーを設定するには、管理者はポータルでマネージド ID を選択し、 **[概要]** で **[アクセス制御 (IAM)]** をクリックする必要があります。 次に、右側で **Managed Identity Operator** ロールを、HDInsight ESP クラスターを作成するユーザーまたはグループに割り当てます。 たとえば、AAD-DS 管理者は、次の図に示すように **sjmsi** マネージド ID 用にこのロールを **MarketingTeam** グループに割り当てることができます。 これにより、組織の適切なユーザーは、ESP クラスターを作成する目的でこのマネージド ID を使用するためのアクセス権を持つようになります。

![HDInsight のマネージド ID オペレーター ロールの割り当て](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>ネットワークに関する考慮事項

> [!NOTE]  
> Azure AD DS は、Azure Resource Manager (ARM) ベースの vNET にデプロイする必要があります。 Azure AD-DS では、クラシック仮想ネットワークはサポートされません。 詳しくは、「[Azure portal を使用して Azure Active Directory Domain Services を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started-network.md)」をご覧ください。

Azure AD-DS を有効にした後、ローカルのドメイン ネーム サービス (DNS) サーバーが AD Virtual Machines (VM) で実行されます。 Azure AD-DS 仮想ネットワーク (VNET) を、これらのカスタム DNS サーバーを使用するように構成します。 適切な IP アドレスを見つけるには、 **[マネージド]** カテゴリで **[プロパティ]** を選択し、 **[仮想ネットワーク上の IP アドレス]** の下に表示される IP アドレスを参照します。

![ローカル DNS サーバーの IP アドレスを見つける](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Azure AD-DS VNET の DNS サーバーの構成を変更し、 **[設定]** カテゴリの下の **[DNS サーバー]** を選択して、これらのカスタム IP を使用します。 次に、 **[カスタム]** の横のラジオ ボタンをクリックし、下のテキスト ボックスに最初の IP アドレスを入力して、 **[保存]** をクリックします。 同じ手順に従って、その他の IP アドレスを追加します。

![VNet DNS 構成の更新](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Azure AD-DS インスタンスと HDInsight クラスターの両方を同じ Azure 仮想ネットワークに配置する方が簡単です。 別の VNet を使用する場合は、ドメイン コント ローラーが HDI VM に表示されるように、これらの仮想ネットワークをピアリングする必要があります。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。 

VNET がピアリングされたら、HDInsight VNET を カスタム DNS サーバーを使用するように構成し、Azure AD-DS のプライベート IP を DNS サーバーのアドレスとして入力します。 両方の VNET が同じ DNS サーバーを使用したとき、カスタム ドメイン名は、正しい IP に解決され、HDInsight から到達可能になります。 たとえば、ドメイン名が "contoso.com" の場合、この手順の後に "contoso.com" ping すると、正しい Azure AD-DS IP に解決されます。 

![ピアリングされた VNET のカスタム DNS サーバーを構成する](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

お客様の HDInsight サブネットでネットワーク セキュリティ グループ (NSG) 規則を使用している場合は、インバウンド トラフィックとアウトバウンド トラフィックの両方に対して[必須 IP](../hdinsight-extend-hadoop-virtual-network.md) を許可する必要があります。 

ネットワークが正しく設定されているかどうかを**テストする**には、Windows VM を HDInsight VNET/サブネットに参加させて、ドメイン名に対して ping を実行し (IP に解決されます)、**ldp.exe** を実行して、Azure AD-DS ドメインにアクセスします。 次に、**この Windows VM をドメインに参加**させて、必要なすべての RPC 呼び出しがクライアントとサーバー間で成功していることを確認します。 **nslookup** を使って、ストレージ アカウント、または使用する任意の外部 DB (外部の Hive metastore、Ranger DB など) へのネットワーク アクセスを確認することもできます。
AAD-DS が NSG によって保護されている場合、[必須ポート](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)がすべて、AAD-DS サブネット ネットワーク セキュリティ グループ ルールのホワイトリストに含まれていることを確認してください。 この Windows VM のドメイン参加に成功した場合、次の手順に進んで ESP クラスターを作成することができます。

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP の HDInsight クラスターの作成

前の手順を正しく設定したら、次の手順は、ESP が有効になった HDInsight クラスターを作成することです。 HDInsight クラスターを作成するとき、**カスタム** タブで Enterprise セキュリティ パッケージを有効にできます。デプロイ用に Azure Resource Manager テンプレートを使用する場合は、ポータルのエクスペリエンスを一度使用して、最後の "概要" ページに後で再利用するために事前入力されたテンプレートをダウンロードします。

> [!NOTE]  
> ESP クラスター名の最初の 6 文字は、環境内で一意である必要があります。 たとえば、異なる VNET 内に複数の ESP クラスターがある場合、クラスター名の最初の 6 文字が一意になるように名前付け規則を選択する必要があります。

![Azure HDInsight Enterprise セキュリティ パッケージのドメインの検証](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

ESP を有効にすると、Azure AD-DS に関連する一般的な構成ミスが自動的に検出され、検証されます。 これらのエラーを修正した後は、次の手順に進むことができます。 

![Aazure HDInsight Enterprise セキュリティ パッケージがドメインの検証に失敗しました](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP で HDInsight クラスターを作成するときは、次のパラメーターを指定する必要があります。

- **クラスター管理者ユーザー**: 同期された Azure AD-DS からクラスターの管理者を選択します。 このドメイン アカウントは同期済みで、Azure AD-DS で使用できなければなりません。

- **クラスターのアクセス グループ**: ユーザーをクラスターに同期させてアクセスさせるセキュリティ グループが、Azure AD-DS で使用できる必要があります。 たとえば、HiveUsers グループです。 詳細については、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

- **LDAPS URL**: たとえば、ldaps://contoso.com:636 です。

次のスクリーンショットは、Azure portal における適切な構成を示しています。

![Azure HDInsight ESP Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)。

作成したマネージド ID は、新しいクラスターを作成するときに、ユーザー割り当てマネージド ID ドロップダウンから選択できます。

![Azure HDInsight ESP Active Directory Domain Services の構成](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png)。

## <a name="next-steps"></a>次の手順

* Hive ポリシーの構成と Hive クエリの実行については、[ESP を使用した HDInsight クラスター用の Apache Hive ポリシーの構成](apache-domain-joined-run-hive.md)に関する記事を参照してください。
* SSH と ESP を使用して HDInsight クラスターに接続する方法については、[Linux、Unix、または OS X からの HDInsight 上の Linux ベースの Hadoop での SSH キーの使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)に関する記事を参照してください。
