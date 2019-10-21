---
title: Azure HDInsight で Enterprise セキュリティ パッケージ クラスターを作成および構成する
description: Azure HDInsight で Enterprise セキュリティ パッケージ クラスターを作成および構成する方法について説明します
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: aed656c65fc70946f7d91cb4354e1c081954e68c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030398"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Azure HDInsight で Enterprise セキュリティ パッケージ クラスターを作成および構成する

Azure HDInsight の Enterprise セキュリティ パッケージ (ESP) を使用すると、Azure の Apache Hadoop クラスターに対する Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御にアクセスできます。 HDInsight ESP クラスターを使用すると、厳格な企業セキュリティ ポリシーに従っている組織は、機密データを安全に処理できます。

このガイドでは、ESP 対応の Azure HDInsight クラスターを作成する方法について説明します。 また、Windows IaaS VM を作成して、その上で Active Directory とドメイン ネーム システム (DNS) を有効にする方法についても説明します。 このガイドを使用して、オンプレミス ユーザーが ESP 対応の HDInsight クラスターにサインインできるように、必要なリソースを構成してください。

作成するサーバーは、"*実際の*" オンプレミス環境の代わりとして機能します。 セットアップと構成の手順にそれを使用します。 後で、独自の環境でその手順を繰り返します。 

このガイドは、Azure Active Directory (Azure AD) とのパスワード ハッシュ同期を使用して、ハイブリッド ID 環境を作成する場合にも役立ちます。 このガイドは、「[HDInsight で Enterprise セキュリティ パッケージを使用する](apache-domain-joined-architecture.md)」を補完するものです。

独自の環境でこのプロセスを使用する前に:
* Active Directory と DNS を設定します。
* Azure AD を有効にします。
* オンプレミス ユーザー アカウントを Azure AD と同期します。

![Azure AD のアーキテクチャの図](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>オンプレミス環境を作成する

このセクションでは、Azure クイックスタート デプロイ テンプレートを使用して、新しい VM を作成し、DNS を構成して、新しい AD フォレストを追加します。

1. [新しい Active Directory フォレストを使用して Azure VM を作成する](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)ためのクイックスタート デプロイ テンプレートに移動します。

1. **[Azure に配置する]** を選択します。
1. Azure サブスクリプションにサインインします。
1. **[Create an Azure VM with a new AD Forest]\(新しい AD フォレストで Azure VM を作成する\)** ページで、次のようにします。
    1. **[サブスクリプション]** ドロップダウン リストから、リソースをデプロイするサブスクリプションを選択します。
    1. **[リソース グループ]** の横の **[新規作成]** を選択し、「*OnPremADVRG*」という名前を入力します。
    1. テンプレートの残りのフィールドに、次の詳細を入力します。

        * **[場所]** :米国中部
        * **管理ユーザー名**: HDIFabrikamAdmin
        * **管理パスワード**: \<YOUR_PASSWORD>
        * **ドメイン名**:HDIFabrikam.com
        * **DNS プレフィックス**: hdifabrikam

        ![新しい Azure AD フォレストで Azure VM を作成するためのテンプレート](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. **[購入]** を選択します。
    1. デプロイを監視し、完了するまで待ちます。
    1. 正しいリソース グループ **OnPremADVRG** の下にリソースが作成されていることを確認します。

## <a name="configure-users-and-groups-for-cluster-access"></a>クラスター アクセス用のユーザーとグループを構成する

このセクションでは、このガイドの最後までに HDInsight クラスターにアクセスできるようになるユーザーを作成します。

1. リモート デスクトップを使用してドメイン コントローラーに接続します。
    1. 冒頭で説明したテンプレートを使用した場合、ドメイン コントローラーは **OnPremADVRG** リソース グループ内の **adVM** という名前の VM です。
    1. Azure portal で、 **[リソース グループ]**  >  **[OnPremADVRG]**  >  **[adVM]**  >  **[接続]** の順に選択します。
    1. **[RDP]** タブを選択し、 **[RDP ファイルのダウンロード]** を選択します。
    1. ファイルをコンピューターに保存して開きます。
    1. 資格情報の入力を求められたら、ユーザー名として *HDIFabrikam\HDIFabrikamAdmin* を使用します。 その後、管理者アカウントに対して選択したパスワードを入力します。

1. リモート デスクトップ セッションがドメイン コントローラー VM 上で開いたら、 **[サーバー マネージャー]** ダッシュボードで **[Active Directory ユーザーとコンピューター]** を開きます。 右上隅の **[ツール]**  >  **[Active Directory ユーザーとコンピューター]** を選択します。

    ![[サーバー マネージャー] ダッシュボードで Active Directory の管理を開く](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 次の 2 人のユーザーを新しく作成します: **HDIAdmin** と **HDIUser**。 これら 2 人のユーザーは、HDInsight クラスターにサインインします。

    1. **[Active Directory ユーザーとコンピューター]** ページで、 **[アクション]**  >  **[新規]**  >  **[ユーザー]** を選択します。

        ![新しい Active Directory ユーザーを作成します。](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. **[新しいオブジェクト - ユーザー]** ページで、 **[ユーザー ログオン名]** に「*HDIUser*」と入力します。 次に、 **[次へ]** を選択します。

        ![1 つ目の管理者ユーザー オブジェクトを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 表示されるポップアップ ウィンドウで、新しいアカウントのパスワードを入力します。 **[パスワードを無期限にする]**  >  **[OK]** を選択します。
    1. **[完了]** を選択して新しいアカウントを作成します。
    1. *HDIAdmin* という名前で別のユーザーを作成します。

        ![2 つ目の管理者ユーザー オブジェクトを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. **[Active Directory ユーザーとコンピューター]** ページで、 **[アクション]**  >  **[新規]**  >  **[グループ]** を選択します。 *HDIUserGroup* という名前のグループを追加します。

    ![新しい Active Directory グループを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![新しいオブジェクトを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. **HDIUser** をグループ メンバーとして **HDIUserGroup** に追加します。

    1. **HDIUserGroup** を右クリックし、 **[プロパティ]** を選択します。
    1. **[メンバー]** タブで **[追加]** を選択します。
    1. **[選択するオブジェクト名を入力してください]** ボックスに、「*HDIUser*」と入力します。 **[OK]** をクリックします。
    1. **HDIAdmin** アカウントに対して、前の手順を繰り返します。

        ![メンバー HDIUser をグループ HDIUserGroup に追加する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

これで Active Directory 環境が作成されました。 HDInsight クラスターにアクセスできる 2 人のユーザーとユーザー グループを追加しました。

そのユーザーは Azure AD と同期されます。

### <a name="create-an-azure-ad-directory"></a>Azure AD Directory を作成する

1. Azure ポータルにサインインします。
1. **[リソースの作成]** を選択し、「*directory*」と入力します。 **[Azure Active Directory]**  >  **[作成]** を選択します。
1. **[組織名]** に「*HDIFabrikam*」と入力します。
1. **[初期ドメイン名]** に「*HDIFabrikamoutlook*」と入力します。
1. **作成** を選択します。

    ![Azure AD Directory を作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Azure portal の左側で、 **[Azure Active Directory]** を選択します。
1. 必要な場合は、 **[ディレクトリの切り替え]** を選択し、新しい **HDIFabrikamoutlook** ディレクトリに変更します。
1. **[管理]** で **[カスタム ドメイン名]**  >  **[カスタム ドメインの追加]** を選択します。
1. **[カスタム ドメイン名]** に「*HDIFabrikam.com*」と入力し、 **[ドメインの追加]** を選択します。

![カスタム ドメインを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Azure AD テナントを構成する

次に、オンプレミスの Active Directory インスタンスからクラウドにユーザーとグループを同期できるように、Azure AD テナントを構成します。

1. Active Directory テナント管理者を作成します。
    1. Azure portal にサインインして、Azure AD テナント **HDIFabrikam** を選択します。
    1. **[管理]** で **[ユーザー]**  >  **[新規ユーザー]** を選択します。
    1. 新しいユーザーについて次の詳細を入力します。
        * **名前**: fabrikamazureadmin
        * **ユーザー名**: fabrikamazureadmin@hdifabrikam.com
        * **Password**:任意の安全なパスワード

    1. **[グループ]** セクションで、**AAD DC Administrators** を検索し、 **[選択]** をクリックします。

        ![Azure AD の [グループ] ダイアログ ボックス](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. **[ディレクトリ ロール]** セクションを開き、右側で **[グローバル管理者]**  >  **[OK]** を選択します。

        ![Azure AD ロール ダイアログ ボックス](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. ユーザーのパスワードを入力します。 **[作成]** を選択します。

1. 新しく作成したユーザー \<fabrikamazureadmin@hdifabrikam.com> のパスワードを変更する場合は、ID を使用して Azure portal にサインインします。 パスワードの変更を求められます。

## <a name="sync-on-premises-users-to-azure-ad"></a>オンプレミス ユーザーを Azure AD と同期する

### <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect をダウンロードしてインストールする

1. [Azure AD Connect のダウンロード](https://www.microsoft.com/download/details.aspx?id=47594)。

1. ドメイン コントローラーに Azure AD Connect をインストールします。

    1. ダウンロードした実行可能ファイルを開き、ライセンス条項に同意します。 **[続行]** をクリックします。

        ![[Azure AD Connect へようこそ] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. **[簡単設定を使う]** を選択して、インストールを完了します。

        ![Azure AD Connect の簡単設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>オンプレミスのドメイン コントローラーとの同期を構成する

1. **[Azure AD に接続]** ページで、Azure AD のグローバル管理者のユーザー名とパスワードを入力します。 Active Directory テナントを構成するときに作成したユーザー名 `fabrikamazureadmin@hdifabrikam.com` を使用します。 次に、 **[次へ]** を選択します。 

    ![[Azure AD に接続] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. **[Active Directory ドメイン サービスへの接続]** ページで、エンタープライズ管理者アカウントのユーザー名とパスワードを入力します。 前に作成したユーザー名 `HDIFabrikam\HDIFabrikamAdmin` とそのパスワードを使用します。 次に、 **[次へ]** を選択します。 

   ![[Azure AD に接続] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. **[Azure AD サインインの構成]** ページで、 **[次へ]** を選択します。
   ![[Azure AD サインインの構成] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. **[構成の準備完了]** ページで、 **[インストール]** を選択します。

   ![[構成の準備完了] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. **[構成が完了しました]** ページで、 **[終了]** を選択します。
   ![[構成が完了しました] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 同期が完了したら、IaaS ディレクトリで作成したユーザーが Azure AD に同期されていることを確認します。
   1. Azure ポータルにサインインします。
   1. **[Azure Active Directory]**  >  **[HDIFabrikam]**  >  **[ユーザー]** を選択します。

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

Azure AD Domain Services (Azure AD DS) の構成に使用できるユーザー割り当てマネージド ID を作成します。 詳しくは、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」をご覧ください。

1. Azure ポータルにサインインします。
1. **[リソースの作成]** を選択し、「*managed identity*」と入力します。 **[ユーザー割り当てマネージド ID]**  >  **[作成]** を選択します。
1. **[リソース名]** に「*HDIFabrikamManagedIdentity*」と入力します。
1. サブスクリプションを選択します。
1. **[リソース グループ]** の **[新規作成]** を選択し、「*HDIFabrikam-CentralUS*」と入力します。
1. **[場所]** で **[米国中部]** を選択します。
1. **作成** を選択します。

![ユーザー割り当てマネージド ID を新規作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure AD DS を有効にする

Azure AD DS を有効にするには、以下の手順のようにします。 詳しくは、[Azure portal を使用して Azure AD DS を有効にする](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)方法に関する記事をご覧ください。

1. Azure AD DS をホストする仮想ネットワークを作成します。 次の PowerShell コードを実行します。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure ポータルにサインインします。
1. **[リソースの作成]** を選択し、「*Domain services*」と入力して **[Azure AD Domain Services]** を選択します。
1. **[基本]** ページで次のようにします。
    1. **[ディレクトリ名]** で、作成した Azure AD ディレクトリを選択します: **HDIFabrikam**。
    1. **[DNS ドメイン名]** に「*HDIFabrikam.com*」と入力します。
    1. サブスクリプションを選択します。
    1. リソース グループ **HDIFabrikam-CentralUS** を指定します。 **[場所]** には **[米国中部]** を選択します。

        ![Azure AD DS の基本の詳細](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. **[ネットワーク]** ページで、PowerShell スクリプトを使用して作成したネットワーク (**HDIFabrikam-VNET**) とサブネット (**AADDS-subnet**) を選択します。 または、 **[新規作成]** を選択してここで仮想ネットワークを作成します。

    !["仮想ネットワークの作成" ステップ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. **[管理者グループ]** ページに、このグループを管理するために **AAD DC Administrators** という名前のグループが既に作成されているという通知が表示されます。 必要に応じてこのグループのメンバーシップを変更できますが、この場合は変更する必要はありません。 **[OK]** を選択します。

    ![Azure AD 管理者グループを表示する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. **[同期]** ページで、 **[すべて]**  >  **[OK]** を選択して、完全な同期を有効にします。

    ![Azure AD DS の同期を有効にする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. **[概要]** ページで、Azure AD DS の詳細を確認して、 **[OK]** を選択します。

    !["Azure AD Domain Services の有効化" の概要](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Azure AD DS を有効にすると、ローカル DNS サーバーが Azure AD VM 上で実行されます。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD DS 仮想ネットワークを構成する

以下の手順を使用して、カスタム DNS サーバーを使用するように Azure AD DS 仮想ネットワーク (**HDIFabrikam-AADDSVNET**) を構成します。

1. カスタム DNS サーバーの IP アドレスを見つけます。 
    1. **HDIFabrikam.com** Azure AD DS リソースを選択します。 
    1. **[管理]** の下で、 **[プロパティ]** を選択します。 
    1. **[仮想ネットワーク上の IP アドレス]** で IP アドレスを検索します。

    ![Azure AD DS のカスタム DNS IP アドレスを見つける](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. カスタム IP アドレス 10.0.0.4 と 10.0.0.5 を使用するように、**HDIFabrikam-AADDSVNET** を構成します。

    1. **[設定]** で、 **[DNS サーバー]** を選択します。 
    1. **[カスタム]** を選択します。
    1. テキスト ボックスに、最初の IP アドレス (*10.0.0.4*) を入力します。
    1. **[保存]** を選択します。
    1. 手順を繰り返して、他の IP アドレス (*10.0.0.5*) を追加します。

このシナリオでは、IP アドレス 10.0.0.4 と 10.0.0.5 を使用するように Azure AD DS を構成し、同じ IP アドレスを Azure AD DS 仮想ネットワークに設定しました。

![カスタム DNS サーバーのページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP トラフィックをセキュリティで保護する

Azure Active Directory に対する読み書きには、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) が使用されます。 Secure Sockets Layer (SSL) または Transport Layer Security (TLS) テクノロジを使用して、LDAP トラフィックを機密情報にしてセキュリティで保護することができます。 適切な形式の証明書をインストールすることで、LDAP over SSL (LDAPS) を有効にすることができます。

Secure LDAP について詳しくは、[Azure AD DS マネージド ドメインに対する LDAP の構成](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)に関する記事をご覧ください。

このセクションでは、自己署名証明書を作成し、証明書をダウンロードして、**HDIFabrikam** Azure AD DS マネージド ドメイン用に LDAPS を構成します。

次のスクリプトでは、**HDIFabrikam** 用の証明書が作成されます。 証明書は *LocalMachine* パスに保存されます。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> SSL 証明書要求の作成には、有効な公開キー暗号化標準 (PKCS) \#10 要求が作成される任意のユーティリティーまたはアプリケーションを使用できます。

証明書がコンピューターの**個人用**ストアにインストールされていることを確認します。

1. Microsoft 管理コンソール (MMC) を起動します。
1. ローカル コンピューターの証明書を管理する **[証明書]** スナップインを追加します。
1. **[証明書 (ローカル コンピューター)]**  >  **[個人用]**  >  **[証明書]** を展開します。 新しい証明書が**個人用**ストアに存在するはずです。 この証明書は完全修飾ホスト名に対して発行されます。

    ![ローカル証明書の作成を確認する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 右側のウィンドウで、作成した証明書を右クリックします。 **[すべてのタスク]** をポイントし、 **[エクスポート]** を選択します。

1. **[秘密キーのエクスポート]** ページで **[はい、秘密キーをエクスポートします]** を選択します。 キーのインポート先のコンピューターでは、暗号化されたメッセージを読み取るために秘密キーが必要です。

    ![証明書エクスポート ウィザードの [秘密キーのエクスポート] ページ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. **[エクスポート ファイルの形式]** ページでは、既定値をそのまま使用し、 **[次へ]** を選択しますます。 
1. **[パスワード]** ページで、秘密キーのパスワードを入力します。 **[暗号化]** では、**TripleDES-SHA1** を選択します。 次に、 **[次へ]** を選択します。
1. **[エクスポートするファイル]** ページで、エクスポートした証明書ファイルのパスと名前を入力し、 **[次へ]** を選択します。 ファイル名の拡張子は .pfx である必要があります。 このファイルは、セキュリティで保護された接続を確立するために Azure portal で構成されます。
1. Azure AD DS マネージド ドメインに対して LDAPS を有効にします。
    1. Azure portal でドメイン **HDIFabrikam.com** を選択します。
    1. **[管理]** で、 **[Secure LDAP]** を選択します。
    1. **[Secure LDAP]** ページの **[Secure LDAP]** で、 **[有効]** を選択します。
    1. コンピューターにエクスポートした .pfx 証明書ファイルを参照します。
    1. 証明書のパスワードを入力します。

    ![セキュリティで保護された LDAP を有効にする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. LDAPS を有効にしたので、ポート 636 を有効にして到達可能であることを確認します。
    1. **HDIFabrikam-CentralUS** リソース グループで、ネットワーク セキュリティ グループ **AADDS-HDIFabrikam.com-NSG** を選択します。
    1. **[設定]** で **[受信セキュリティ規則]**  >  **[追加]** を選択します。
    1. **[受信セキュリティ規則の追加]** ページで、次のプロパティを入力して **[追加]** を選択します。

        | プロパティ | 値 |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<望ましい値> |
        | 名前 | Port_LDAP_636 |

    ![[受信セキュリティ規則の追加] ダイアログ ボックス](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** は、ユーザー割り当てマネージド ID です。 HDInsight ドメイン サービス共同作成者ロールにマネージド ID を割り当てると、この ID でドメイン サービス操作の読み取り、作成、変更、および削除を行うことができるようになります。

![ユーザー割り当てマネージド ID を作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP 対応の HDInsight クラスターを作成する

この手順には、次の前提条件が必要です。

1. **米国西部**の場所に、新しいリソース グループ *HDIFabrikam-WestUS* を作成します。
1. ESP 対応の HDInsight クラスターをホストする仮想ネットワークを作成します。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure AD DS がホストされている仮想ネットワーク (`HDIFabrikam-AADDSVNET`) と ESP 対応の HDInsight クラスターがホストされている仮想ネットワーク (`HDIFabrikam-HDIVNet`) の間に、ピア関係を作成します。 2 つの仮想ネットワークをピアリングするには、次の PowerShell コードを使用します。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. **Hdigen2store** という名前の新しい Azure Data Lake Storage Gen2 アカウントを作成します。 ユーザー マネージド ID **HDIFabrikamManagedIdentity** を使用して、アカウントを構成します。 詳しくは、「[Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)」をご覧ください。

1. **HDIFabrikam-AADDSVNET** 仮想ネットワーク上にカスタム DNS を設定します。
    1. Azure portal、 **[リソース グループ]**  >  **[OnPremADVRG]**  >  **[HDIFabrikam-AADDSVNET]**  >  **[DNS サーバー]** の順に移動します。
    1. **[カスタム]** を選択し、「*10.0.0.4*」と「*10.0.0.5*」を入力します。
    1. **[保存]** を選択します。

        ![仮想ネットワークのカスタム DNS 設定を保存する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. ESP 対応の新しい HDInsight Spark クラスターを作成します。
    1. **[カスタム (サイズ、設定、アプリ)]** を選択します。
    2. **[基本]** の詳細を入力します (セクション 1)。 **[クラスターの種類]** が **Spark 2.3 (HDI 3.6)**  であることを確認します。 **[リソース グループ]** が **HDIFabrikam-CentralUS** であることを確認します。

    1. **[セキュリティとネットワーク]** (セクション 2) では、次の詳細を入力します。
        * **[Enterprise セキュリティ パッケージ]** で **[有効]** を選択します。
        * **[クラスター管理者ユーザー]** を選択し、オンプレミス管理者ユーザーとして作成した **HDIAdmin** アカウントを選択します。 **[選択]** をクリックします。
        * **[クラスター アクセス グループ]**  > **HDIUserGroup** を選択します。 以降、このグループに追加したすべてのユーザーは HDInsight クラスターにアクセスできるようになります。

            ![クラスター アクセス グループ HDIUserGroup を選択する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. クラスター構成の他の手順を完了し、 **[クラスターの概要]** で詳細を確認します。 **作成** を選択します。

1. `https://CLUSTERNAME.azurehdinsight.net` で新しく作成したクラスターの Ambari UI にサインインします。 管理者ユーザー名 `hdiadmin@hdifabrikam.com` とそのパスワードを使用します。

    ![Apache Ambari UI のサインイン ウィンドウ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. クラスター ダッシュボードから **[Roles]\(ロール\)** を選択します。
1. **[Roles]\(ロール\)** ページの **[Assign roles to these]\(これらにロールを割り当てる\)** で、 **[Cluster Administrator]\(クラスター管理者\)** ロールにグループ *hdiusergroup* を入力します。 

    ![クラスター管理者ロールを hdiusergroup に割り当てる](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Secure Shell (SSH) クライアントを開き、クラスターにサインインします。 オンプレミスの Active Directory インスタンスで作成した **hdiuser** を使用します。

    ![SSH クライアントを使用してクラスターにサインインする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

このアカウントでサインインできる場合、ESP クラスターはオンプレミスの Active Directory インスタンスと同期するように正しく構成されています。

## <a name="next-steps"></a>次の手順

[ESP による Apache Hadoop セキュリティの概要](hdinsight-security-overview.md)に関する記事をご覧ください。
