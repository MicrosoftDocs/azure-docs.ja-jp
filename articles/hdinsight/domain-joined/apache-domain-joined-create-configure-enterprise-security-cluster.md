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
ms.openlocfilehash: 8da50757182609402ecb035b6f3e92959758ef46
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442302"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Azure HDInsight で Enterprise セキュリティ パッケージ クラスターを作成および構成する

Azure HDInsight の Enterprise セキュリティ パッケージを使用すると、Azure の Apache Hadoop クラスターの Active Directory ベースの認証、マルチユーザーのサポート、ロールベースのアクセス制御にアクセスできます。 HDInsight ESP クラスターを使用すると、厳格な企業セキュリティ ポリシーに従っている組織が機密データを安全に処理できます。

このガイドの目的は、オンプレミス ユーザーが ESP 対応の HDInsight クラスターにサインインできるように、必要なリソースを正しく構成することです。 この記事では、Enterprise セキュリティ パッケージ対応の Azure HDInsight クラスターを作成するために必要な手順について説明します。 この手順では、Active Directory およびドメイン ネーム サービス (DNS) を有効にした Windows IaaS VM の作成について説明します。 このサーバーは、お客様の**実際の**オンプレミス環境の代わりとして機能し、設定と構成の手順を進めて、後でお客様の環境で繰り返すことができます。 このガイドは、パスワード ハッシュの Azure Active Directory との同期を使用してハイブリッド ID 環境を作成する場合にも役立ちます。

このガイドは、「[HDInsight で Enterprise セキュリティ パッケージを使用する](apache-domain-joined-architecture.md)」を補足するものです。

このプロセスをお客様の環境で使用する前に、Active Directory とドメイン ネーム サービス (DNS) を設定してください。 また、Azure Active Directory を有効にして、オンプレミス ユーザー アカウントを Azure Active Directory と同期してください。

![アーキテクチャ ダイアグラム](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>オンプレミス環境を作成する

概要:このセクションでは、Azure Quick Deployment テンプレートを使用して、新しい VM を作成し、ドメイン ネーム サービス (DNS) と新しい AD フォレストを構成します。

1. 「[Create an Azure VM with a new AD Forest (新しい AD フォレスト を使用して Azure VM を作成する)](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)」にアクセスしてクイック デプロイ テンプレートを表示します。

1. **[Azure へのデプロイ]** をクリックします。
1. Azure サブスクリプションにサインインします。
1. **[Create an Azure VM with a new AD Forest]\(新しい AD フォレスト を使用して Azure VM を作成する\)** 画面で次の手順を実行します。
    1. リソースをデプロイするサブスクリプションを **[サブスクリプション]** ドロップダウンから選択します。
    1. **[リソース グループ]** の横の **[新規作成]** を選択し、「**OnPremADVRG**」という名前を入力します。
    1. 残りのテンプレート フィールドに次の詳細を入力します。

        * **[場所]** :米国中部
        * **管理ユーザー名**: HDIFabrikamAdmin
        * **管理者パスワード**: <お使いのパスワード>
        * **ドメイン**:HDIFabrikam.com
        * **DNS プレフィックス**: hdifabrikam

        ![Azure VM と AD フォレストの作成のテンプレート](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. **[購入]** をクリックします
    1. デプロイを監視し、完了するまで待ちます。
    1. 正しいリソース グループ `OnPremADVRG` の下にリソースが作成されていることを確認します。

## <a name="configure-users-and-groups-for-cluster-access"></a>クラスター アクセス用のユーザーとグループを構成する

概要:このセクションでは、このガイドの最後まで HDInsight クラスターにアクセスできるユーザーを作成します。

1. リモート デスクトップを使用してドメイン コントローラーに接続します。
    1. 冒頭で説明したテンプレートを使用した場合、ドメイン コントローラーは `OnPremADVRG` リソース グループ内の **adVM** という名前の VM です。
    1. Azure portal、 **[リソース グループ]**  >  **[OnPremADVRG]**  >  **[adVM]**  >  **[接続]** の順に移動します。
    1. **[RDP]** タブをクリックしてから **[RDP ファイルのダウンロード]** をクリックします。
    1. ファイルをコンピューターに保存して開きます。
    1. 資格情報の入力を求められたら、ユーザー名として `HDIFabrikam\HDIFabrikamAdmin` を使用し、その管理者アカウントに選択したパスワードを入力します。

1. リモート デスクトップ セッションがドメイン コントローラー VM 上で開いたら、 **[サーバー マネージャー]** ダッシュボードから **[Active Directory ユーザーとコンピューター]** を起動します。 右上の **[ツール]** をクリックし、ドロップダウンから **[Active Directory ユーザーとコンピューター]** をクリックします。

    ![[サーバー マネージャー] で Active Directory 管理を開く](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 2 人の新規ユーザー、**HDIAdmin**、**HDIUser** を作成します。 これら 2 人のユーザーは、HDInsight クラスターにサインインするために使用されます。

    1. **[Active Directory ユーザーとコンピューター]** 画面で、 **[アクション]**  >  **[新規]**  >  **[ユーザー]** をクリックします。

        ![新しい Active Directory ユーザーを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. **[新しいオブジェクト - ユーザー]** 画面で、 **[ユーザー ログオン名]** に「`HDIUser`」と入力し、 **[次へ]** をクリックします。

        ![最初の管理者ユーザーを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. 表示されるポップアップで、新しいアカウントに必要なパスワードを入力します。 **[パスワードを無期限にする]** というチェックボックスをオンにします。 **[OK]** をクリックします。
    1. **[完了]** をクリックして新しいアカウントを作成します。
    1. 別のユーザー `HDIAdmin` を作成します。

        ![2 つ目の管理者ユーザーを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. **[Active Directory ユーザーとコンピューター]** 画面で、 **[アクション]**  >  **[新規]**  >  **[グループ]** をクリックします。 新しいグループとして `HDIUserGroup` を作成します。

    ![新しい Active Directory グループを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![新しい group2 を作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. 前の手順で作成した **HDIUser** をメンバーとして **HDIUserGroup** に追加します。

    1. **[HDIUserGroup]** を右クリックし、 **[プロパティ]** をクリックします。
    1. **[メンバー]** タブに移動し、 **[追加]** をクリックします。
    1. **[選択するオブジェクト名を入力してください]** というラベルのボックスに「`HDIUser`」と入力し、 **[OK]** をクリックします。
    1. もう 1 つのアカウント `HDIAdmin` について前の手順を繰り返します。

        ![グループにメンバーを追加する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

これで、HDInsight クラスターにアクセスするための 2 人のユーザーと 1 つのユーザー グループと共に、Active Directory 環境が作成されました。

これらのユーザーは Azure AD と同期されます。

### <a name="create-a-new-azure-active-directory"></a>新しい Azure Active Directory を作成する

1. Azure ポータルにサインインします。
1. **[リソースの作成]** をクリックし、「**directory**」と入力します。 **[Azure Active Directory]**  >  **[作成]** を選択します。
1. **[組織名]** に「**HDIFabrikam**」と入力します。
1. **[初期ドメイン名]** に「**HDIFabrikamoutlook**」と入力します。
1. **Create** をクリックしてください。
1. Azure portal の左側にある **[Azure Active Directory]** をクリックします。
1. 必要に応じて、 **[ディレクトリの切り替え]** をクリックし、**HDIFabrikamoutlook** を作成した新しいディレクトリに切り替えます。
1. **[管理]** で **[カスタム ドメイン名]**  >  **[カスタム ドメインの追加]** をクリックします。
1. **[カスタム ドメイン名]** に「**HDIFabrikam.com**」と入力し、 **[ドメインの追加]** をクリックします。

![新しい Azure Active Directory を作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![新しいカスタム ドメインを作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Azure AD テナントを構成する

概要:これで、Azure AD テナントを構成して、ユーザーとグループをオンプレミスの AD からクラウドに同期できるようにします。

1. AD テナント管理者を作成します。
    1. Azure portal にサインインして、Azure AD テナント **HDIFabrikam** を選択します
    1. **[管理]** の **[ユーザー]** を選択し、次に **[新しいユーザー]** を選択します。
    1. 新しいユーザーについて次の詳細を入力します。

        * 名前: fabrikamazureadmin
        * ユーザー名: fabrikamazureadmin@hdifabrikam.com
        * パスワード: 任意の安全なパスワード

    1. **[グループ]** セクションをクリックし、 **[AAD DC Administrators]** を検索し、 **[選択]** をクリックします。

        ![グループ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. **[ディレクトリ ロール]** セクションをクリックし、右側の **[グローバル管理者]** を選択します。 **[OK]** をクリックします。

        ![ディレクトリ ロール](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. ユーザーのパスワードを入力します。 **Create** をクリックしてください。

1. 新しく作成されたユーザー <fabrikamazureadmin@hdifabrikam.com> のパスワードを変更する場合。 ID を使用して Azure portal にサインインすると、パスワードを変更するように求められます。

## <a name="sync-on-premises-users-to-azure-ad"></a>オンプレミス ユーザーを Azure AD と同期する

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory Connect をダウンロードしてインストールする

1. [Azure AD Connect のダウンロード](https://www.microsoft.com/download/details.aspx?id=47594)。

1. Microsoft Azure Active Directory Connect をドメイン コントローラーにインストールします。
    1. 前の手順でダウンロードした実行可能ファイルを開き、ライセンス条項に同意します。 **[続行]** をクリックします。

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. **[簡単設定を使う]** をクリックしてインストールを完了します。

        ![簡単設定を使う](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>オンプレミス ドメイン コントローラーとの同期を構成する

1. **[Azure AD に接続]** 画面で、Azure AD のグローバル管理者のユーザー名とパスワードを入力します。  **[次へ]** をクリックします。 これは、AD テナントを構成するときに作成したユーザー名 `fabrikamazureadmin@hdifabrikam.com` です。
    ![Connect to Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. **[Active Directory ドメイン サービスへの接続]** 画面で、エンタープライズ管理者アカウントのユーザー名とパスワードを入力します。  **[次へ]** をクリックします。 これは、以前に作成したユーザー名 `HDIFabrikam\HDIFabrikamAdmin` とそれに対応するパスワードです。

   ![Active Directory ドメイン サービスへの接続](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. **[Azure AD サインインの構成]** ページで、 **[次へ]** をクリックします。
    ![Azure AD サインインの構成](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. [構成の準備完了] 画面で、 **[インストール]** をクリックします。
    ![インストール](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. **[構成が完了しました]** 画面が表示されたら、 **[終了]** をクリックします。
    ![構成が完了しました](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. 同期が完了したら、IAAS Active Directory に作成したユーザーが Azure Active Directory と同期されているかどうかを確認します。
    1. Azure ポータルにサインインします。
    1. **[Azure Active Directory]**  >  **[HDIFabrikam]**  >  **[ユーザー]** を選択します。

### <a name="create-an-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

Azure Active Directory Domain Services (Azure AD-DS) の構成に使用されるユーザー割り当てマネージド ID を作成します。 ユーザー割り当てマネージド ID の作成の詳細については、「[Azure Portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」を参照してください。

1. Azure ポータルにサインインします。
1. **[リソースの作成]** をクリックし、「**managed identity**」と入力します。 **[ユーザー割り当てマネージド ID]**  >  **[作成]** を選択します。
1. **[リソース名]** に「**HDIFabrikamManagedIdentity**」と入力します。
1. サブスクリプションを選択します。
1. **[リソース グループ]** の **[新規作成]** をクリックし、「**HDIFabrikam-CentralUS**」と入力します。
1. **[場所]** で **[米国中部]** を選択します。
1. **Create** をクリックしてください。

![ユーザー割り当てマネージド ID を作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services を有効にする

詳細については、「[Azure Portal を使用して Azure Active Directory Domain Services を有効にする](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)」を参照してください。

1. Azure AD-DS をホストする仮想ネットワークを作成します。 次の PowerShell コードを実行します。

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure ポータルにサインインします。
1. **[リソースの作成]** をクリックし、「**Domain services**」と入力して **[Azure AD Domain Services]** を選択します。
1. **[基本]** 画面で、以下の手順を実行します。
    1. **[ディレクトリ名]** で、この記事用に作成した Azure Active Directory である **[HDIFabrikam]** を選択します。
    1. **HDIFabrikam.com** の **[DNS ドメイン名]** を入力します。
    1. サブスクリプションを選択します。
    1. リソース グループ **[HDIFabrikam-CentralUS]** と **[場所]** の **[米国中部]** を指定します。

        ![azure ad ds の基本の詳細](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. **[ネットワーク]** 画面が完了したら、前の PowerShell スクリプトで作成したネットワーク (**HDIFabrikam-VNET**) とサブネット (**AADDS-subnet**) を選択します。 または **[新規作成]** オプションを使用して、すぐに仮想ネットワークを作成することもできます。

    ![ネットワークを選択する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. **[管理者グループ]** 画面に、このグループを管理するために **AAD DC Administrators** という名前のグループが既に作成されているという通知が表示されます。 必要に応じて、このグループのメンバーシップを変更できますが、この記事の手順には必要ありません。 Click **OK**.

    ![管理者グループを表示する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. **[同期]** 画面で、 **[すべて]** を選択して完全な同期を有効にして **[OK]** をクリックします。

    ![同期を有効にする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. **[概要]** 画面で、Azure AD-DS の詳細を確認し、 **[OK]** をクリックします。

    ![詳細を確認する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Azure AD-DS を有効にした後、ローカルのドメイン ネーム サービス (DNS) サーバーが AD Virtual Machines (VM) で実行されます。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD-DS 仮想ネットワークを構成する

このセクションの手順を使用すると、カスタム DNS サーバーを使用するように Azure AD-DS 仮想ネットワーク (**HDIFabrikam-AADDSVNET**) を構成するために役立ちます。

1. カスタム DNS サーバーの IP アドレスを見つけます。 **HDIFabrikam.com** の AD-DS リソースをクリックし、 **[管理]**   の  **[プロパティ]** をクリックして、 **[仮想ネットワーク上の IP アドレス]** に表示される IP アドレスを確認します。

    ![Azure AD-DS のカスタム DNS IP アドレスを見つける](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. **HDIFabrikam-AADDSVNET** をカスタム IP の `10.0.0.4` と `10.0.0.5` に構成します。

    1.  **[設定]**   カテゴリの **[DNS サーバー]**   を選択します。 次に、 **[カスタム]** の横のラジオ ボタンをクリックし、下のテキスト ボックスに最初の IP アドレス (10.0.0.4) を入力して、 **[保存]** をクリックします。
    1. 同じ手順に従って、その他の IP アドレス (10.0.0.5) を追加します。

1. このシナリオでは、Azure AD-DS は IP アドレス 10.0.0.4 と 10.0.0.5 を使用するように構成されており、下の図に示すように AADDS VNet 上で同じ IP アドレスを設定しています。

    ![カスタム DNS サーバーを表示する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>LDAP トラフィックをセキュリティで保護する

Active Directory に対する読み書きには、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) が使用されます。 Secure Sockets Layer (SSL) / Transport Layer Security (TLS) テクノロジを使用して、LDAP トラフィックを機密情報にしてセキュリティで保護することができます。 適切な形式の証明書をインストールすることで、LDAP over SSL (LDAPS) を有効にすることができます。

セキュリティで保護された LDAP の詳細については、「[Azure AD Domain Services のマネージド ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)」を参照してください。

このセクションでは、自己署名証明書を作成し、証明書をダウンロードして、**hdifabrikam** Azure AD-DS マネージド ドメイン用にセキュリティで保護された LDAP (LDAPS) を構成します。

次のスクリプトで、hdifabrikam の証明書が作成されます。 証明書はパス "LocalMachine" 以下に保存されます。

> [!Note] 
> SSL 証明書要求の作成には、有効な PKCS \#10 要求を作成する任意のユーティリティーまたはアプリケーションを使用できます。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

証明書がコンピューターの個人用ストアにインストールされていることを確認します。 次の手順を完了します。

1. Microsoft 管理コンソール (MMC) を起動します。
1. ローカル コンピューターの証明書を管理する証明書スナップインを追加します。
1.  **[証明書 (ローカル コンピューター)]** 、 **[個人用]** 、 **[証明書]** の順に展開します。 個人用ストアに新しい証明書が表示されます。 この証明書は完全修飾ホスト名に対して発行されます。

    ![証明書の作成を確認する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. 右側のウィンドウで、前の手順で作成した証明書を右クリックし、 **[すべてのタスク]** をポイントして  **[エクスポート]** をクリックします。

1.  **[秘密キーのエクスポート]**   ページで  **[はい、秘密キーをエクスポートします]** をクリックします。 秘密キーは、暗号化されたメッセージをキーのインポート先コンピューターから読み取るために必要です。

    ![秘密キーをエクスポートする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1.  **[エクスポート ファイル形式]**   ページで、既定設定をそのままにしてから、 **[次へ]** をクリックします。 
1.  **[パスワード]**   ページで、秘密キーのパスワードを入力し、 **[暗号化]** に **[TripleDES-SHA1]** を選択して、 **[次へ]** をクリックします。
1.  **[エクスポートするファイル]**   ページで、エクスポートした証明書ファイルのパスと名前を入力し、 **[次へ]** をクリックします。
1. ファイル名は .pfx 拡張子にする必要があります。このファイルは、セキュリティで保護された接続を確立するために Azure portal で構成されています。
1. Azure AD Domain Services のマネージド ドメインに対してセキュリティで保護された LDAP (LDAPS) を有効にします。
    1. Azure portal からドメイン **[HDIFabrikam.com]** を選択します。
    1. **[管理]** の **[Secure LDAP]** をクリックします。
    1. **[Secure LDAP]** 画面で、 **[Secure LDAP]** の **[有効]** をクリックします。
    1. コンピューターにエクスポートした .pfx 証明書ファイルを参照します。
    1. 証明書のパスワードを入力します。

    ![Secure LDAP を有効にする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Secure LDAP を有効にしたので、ポート 636 を有効にして到達可能であることを確認します。
    1. **HDIFabrikam-CentralUS** リソース グループのネットワーク セキュリティ グループ **AADDS-HDIFabrikam.com-NSG** をクリックします。
    1. **[設定]** で **[受信セキュリティ規則]**  >  **[追加]** をクリックします。
    1. **[受信セキュリティ規則の追加]** 画面で、次のプロパティを入力して **[追加]** をクリックします。

        | プロパティ | 値 |
        |---|---|
        | ソース | 任意 |
        | ソース ポート範囲 | * |
        | 宛先 | Any |
        | 送信先ポート範囲 | 636 |
        | Protocol | Any |
        | Action | Allow |
        | 優先度 | \<指定する数字\> |
        | EnableAdfsAuthentication | Port_LDAP_636 |

    ![受信セキュリティ規則](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` はユーザー割り当てマネージド ID です。HDInsight ドメイン サービス共同作成者ロールにマネージド ID を割り当てると、この ID がドメイン サービス操作の読み取り、作成、変更、および削除できるようになります。

    ![ユーザー割り当てマネージド ID を作成する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Enterprise セキュリティ パッケージ対応の HDInsight クラスターを作成する

この手順には、次の前提条件が必要です。

1. 場所 `West US` に新しいリソース グループ `HDIFabrikam-WestUS` を作成します。
1. ESP 対応の HDInsight クラスターをホストする仮想ネットワークを作成します。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. AADDS をホストしている仮想ネットワーク (`HDIFabrikam-AADDSVNET`) と ESP 対応の HDInsight クラスターをホストする仮想ネットワーク (`HDIFabrikam-HDIVNet`) の間にピア関係を作成します。 これら 2 つの仮想ネットワークをピア接続するには、次の PowerShell コードを使用します。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. ユーザー マネージド ID **HDIFabrikamManagedIdentity** を使用して構成された新しい Azure Data Lake Storage Gen2 アカウント **Hdigen2store** を作成します。 ユーザー マネージド ID に対応する Data Lake Storage Gen2 アカウントの作成の詳細については、「[Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)」を参照してください。

1. **HDIFabrikam-AADDSVNET** 仮想ネットワーク上にカスタム DNS を設定します。
    1. Azure portal、 **[リソース グループ]**  >  **[OnPremADVRG]**  >  **[HDIFabrikam-AADDSVNET]**  >  **[DNS サーバー]** の順に移動します。
    1. **[カスタム]** を選択し、「`10.0.0.4`」と「`10.0.0.5`」を入力します。
    1. **[Save]** をクリックします。

        ![カスタム DNS 設定を保存する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. ESP 対応の新しい HDInsight Spark クラスターを作成します。
    1. **[カスタム (サイズ、設定、アプリ)]** をクリックします。
    2. セクション 1 の **[基本]** に必要な詳細を入力します。 **[クラスターの種類]** が **[Spark 2.3 (HDI 3.6)]** で、 **[リソース グループ]** が **[HDIFabrikam-CentralUS]** であることを確認します。

    1. セクション 2 の **[セキュリティとネットワーク]** で次の手順を実行します。
        1. **[Enterprise セキュリティ パッケージ]** の **[有効]** をクリックします。
        1. **[クラスター管理者ユーザー]** をクリックし、以前に作成した **HDIAdmin** アカウントをオンプレミス管理者ユーザーとして選択します。 **[選択]** をクリックします。

        1. **[クラスター アクセス グループ]** をクリックし、 **[HDIUserGroup]** を選択します。 以降、このグループに追加したすべてのユーザーは HDInsight クラスターにアクセスできるようになります。

            ![クラスター アクセス グループを選択する](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. クラスター構成の他の手順を完了し、 **[クラスターの概要]** で詳細を確認します。 **Create** をクリックしてください。

1. 管理者ユーザー名 `hdiadmin@hdifabrikam.com` とパスワードを使用して、`https://CLUSTERNAME.azurehdinsight.net` に新しく作成されたクラスターの Ambari UI にサインインします。

    ![Ambari にサインインする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. クラスターのダッシュボードから **[ロール]** をクリックします。
1. **[ロール]** ページで、グループ **hdiusergroup** を入力して、 **[Assign roles to these]\(これらにロールを割り当てる\)** で **[Cluster Administrator]\(クラスター管理者\)** ロールに割り当てます。

    ![クラスター管理者ロールを hdiusergroup に割り当てる](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. SSH クライアントを開き、オンプレミスの Active Directory に以前に作成した **hdiuser** を使用してクラスターにログインします。

    ![SSH を使用してクラスターにログインする](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

このアカウントを使用してログインできる場合は、オンプレミスのアクティブ ディレクトリと同期するように ESP クラスターを正しく構成しています。

## <a name="next-steps"></a>次の手順

* [Enterprise セキュリティ パッケージを使用する Apache Hadoop セキュリティの概要](hdinsight-security-overview.md)
