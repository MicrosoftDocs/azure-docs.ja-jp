---
title: "App Services のデプロイ: Azure Stack | Microsoft Docs"
description: "Azure Stack への App Service のデプロイに関する詳しいガイダンスです"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 4b4f978f008dbcd8a7424f285198535cf133d7e2
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>App Service リソース プロバイダーを Azure Stack に追加する

テナントによる Azure Stack サブスクリプションを使った Web アプリケーション、モバイル アプリケーション、API アプリケーションの作成を可能にする場合は、[Azure App Service リソースプロバイダー](azure-stack-app-service-overview.md)を Azure Stack のデプロイに追加する必要があります。 この記事ではその手順を説明します。

## <a name="download-the-required-components"></a>必要なコンポーネントをダウンロードする

1. [App Service on Azure Stack プレビュー インストーラー](http://aka.ms/appsvconmasrc1installer)をダウンロードします。

2. [App Service on Azure Stack デプロイ ヘルパー スクリプト](http://aka.ms/appsvconmasrc1helper)をダウンロードします。

3. ヘルパー スクリプトの zip ファイルからファイルを展開します。 次のファイルおよびフォルダー構造が表示されます。

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - Modules
      - AzureStack.Identity.psm1
      - GraphAPI.psm1
   
## <a name="create-certificates-required-by-app-service-on-azure-stack"></a>App Service on Azure Stack に必要となる証明書を作る

この 1 つ目のスクリプトでは、Azure Stack 証明機関と連携して、App Service で必要な 3 つの証明書が作成されます。 スクリプトを Azure Stack ホストで実行して、PowerShell を azurestack\AzureStackAdmin として実行していることを確認します。

1. azurestack\AzureStackAdmin として実行している PowerShell セッションで、ヘルパー スクリプトを展開したフォルダーから **Create-AppServiceCerts.ps1** スクリプトを実行します。 このスクリプトでは、App Service で必要となる証明書作成スクリプトと同じフォルダーに、3 つの証明書が作成されます。

2. .pfx ファイルを守るためのパスワードを入力し、そのパスワードを書き留めます。 それを App Service on Azure Stack インストーラーに入力する必要があります。

### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 のパラメーター

| パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| pfxPassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |
| CertificateAuthority | 必須 | AzS-CA01.azurestack.local | 証明機関のエンドポイント |

## <a name="use-the-installer-to-download-and-install-app-service-on-azure-stack"></a>インストーラーを使って App Service on Azure Stack をダウンロードしてインストールする

appservice.exe インストーラーでは次のことが行われます。

* Microsoft およびサードパーティのソフトウェア ライセンス条項への同意を求めます。
* Azure Stack のデプロイ情報を収集します。
* 指定された Azure Stack ストレージ アカウントに BLOB コンテナーを作成します。
* App Service リソース プロバイダーのインストールに必要なファイルをダウンロードします。
* Azure Stack 環境に App Service リソース プロバイダーをデプロイするためのインストールを準備します。
* App Service ストレージ アカウントにファイルをアップロードします。
* App Service リソース プロバイダーをデプロイします。
* App Service の DNS ゾーンとエントリを作成します。
* App Service リソース プロバイダーを登録します。
* App Service のギャラリー項目を登録します。

次の手順では、インストールのステージを順番に説明します。

> [!NOTE]
> 管理者特権のアカウント (ローカルまたはドメイン管理者) を使ってインストーラーを実行する "*必要があります*"。 azurestack\azurestackuser としてサインインすると、管理者特権の資格情報を求められます。

1. azurestack\AzureStackAdmin として appservice.exe を実行します。

2. **[Azure Stack クラウドに App Service をデプロイします]** をクリックします。

    ![App Service on Azure Stack インストーラー][1]

3. Microsoft ソフトウェア プレリリース ライセンス条項を確認して同意し、**[次へ]** をクリックします。

4. サードパーティのライセンス条項を確認して同意し、**[次へ]** をクリックします。

5. App Service クラウド構成情報を確認し、**[次へ]** をクリックします。

    ![App Service on Azure Stack の App Service クラウドの構成][2]

    > [!NOTE]
    > App Service on Azure Stack インストーラーには、1 つのノードでの Azure Stack インストールのために既定値が用意されています。 Azure Stack のデプロイ時にオプションをカスタマイズした場合は (たとえば、ドメイン サフィックス)、それに応じてこのウィンドウ内の値を編集する必要があります。 たとえば、ドメイン サフィックス mycloud.com を使う場合は、管理 Azure Resource Manager エンドポイントを adminmanagement.[region].mycloud.com に変更する必要があります。

6. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスの横にある **[接続]** をクリックします。

   - Azure Active Directory (Azure AD) を使っている場合は、Azure AD の管理者アカウントとパスワードを入力する必要があります。 **[サインイン]**をクリックします。 Azure Stack をデプロイするときに指定した Azure AD のアカウントを入力する "*必要があります*"。
   - Active Directory フェデレーション サービス (AD FS) を使っている場合は、管理者アカウントを指定する必要があります (例: azurestackadmin@azurestack.local)。 パスワードを入力し、**[サインイン]** をクリックします。

7. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスで自分のサブスクリプションを選びます。

8. **[Azure Stack Locations]\(Azure Stack の場所\)** ボックスで、デプロイしているリージョンに対応する場所を選びます。 たとえば、**[ローカル]** を選びます。 **[次へ]** をクリックします。

    ![App Service on Azure Stack のサブスクリプション選択][3]

9. App Service デプロイの**リソース グループ名**を入力します。 既定では、**[APPSERVICE-LOCAL]\(APPSERVICE-LOCAL\)** に設定されています。

10. App Service でインストールの一環として作る、**ストレージ アカウントの名前**を入力します。 既定では、**[appsvclocalstor]\(appsvclocalstor\)** に設定されています。

11. App Service リソース プロバイダー データベースをホストするために使うインスタンスについて、SQL Server の詳細を入力します。 **[次へ]** をクリックします。インストーラーにより、SQL 接続のプロパティが検証されます。

    ![App Service on Azure Stack のリソース グループ、ストレージ、SQL Server の情報][4]

12. **[App Service default SSL certificate file]\(App Service の既定の SSL 証明書ファイル\)** ボックスの横にある **[参照]** をクリックします。 [先ほど作った](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps) **_.appservice.local.AzureStack.external** 証明書に移動します。 証明書を作ったときに別の場所とドメイン サフィックスを指定した場合は、対応する証明書を選びます。

13. 証明書を作ったときに設定した証明書パスワードを入力します。

14. **[Resource provider SSL certificate file]\(リソース プロバイダーの SSL 証明書ファイル\)** ボックスの横にある **[参照]** をクリックします。 [先ほど作った](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps) **api.appservice.local.AzureStack.external** 証明書に移動します。 証明書を作ったときに別の場所とドメイン サフィックスを指定した場合は、対応する証明書を選びます。

15. 証明書を作ったときに設定した証明書パスワードを入力します。

16. **[Resource provider root certificate file]\(リソース プロバイダーのルート証明書ファイル\)** ボックスの横にある **[参照]** をクリックします。 [先ほど作った](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps) **AzureStackCertificationAuthority** 証明書に移動します。

17. **[次へ]** をクリックします。 インストーラーにより、指定された証明書パスワードが確認されます。

    ![App Service on Azure Stack の証明書詳細][5]

18. App Service のロール構成を確認します。 ロールごとに、お勧めの最小インスタンス SKU が既定値として設定されます。 デプロイの計画に役立つように、コア要件とメモリ要件の概要が提供されます。 必要な項目を選んだら、**[次へ]** クリックします。

    - **コントローラー**: 既定では、1 つの Standard A1 インスタンスが選ばれます。 これは、お勧めの最小限の設定です。 コントローラー ロールは、App Service クラウドの正常性の管理と維持を担当します。
    - **管理**: 既定では、1 つの Standard A2 インスタンスが選ばれます。 フェールオーバーを提供するには、2 つのインスタンスを使うことをお勧めします。 管理ロールは、App Service Azure Resource Manager および API のエンドポイント、ポータル拡張機能 (管理、テナント、Functions ポータル)、データ サービスを担当します。
    - **パブリッシャー**: 既定では、1 つの Standard A1 インスタンスが選ばれます。 これは、お勧めの最小限の設定です。 パブリッシャー ロールは、FTP および Web デプロイを介したコンテンツ公開を担当します。
    - **フロントエンド**: 既定では、1 つの Standard A1 インスタンスが選ばれます。 これは、お勧めの最小限の設定です。 フロントエンド ロールは、App Service アプリケーションへの要求のルーティングを担当します。
    - **共有ワーカー**: 既定では、1 つの Standard A1 インスタンスが選ばれますが、さらに追加できます。 管理者は、オファリングを定義することや、任意の SKU レベルを選ぶことができます。 レベルには、少なくとも 1 つのコアが必要です。 共有ワーカー ロールは、Web アプリケーション、モバイル アプリケーション、API アプリケーション、Azure Functions アプリのホストを担当します。

    ![App Service on Azure Stack のロール構成][6]

    > [!NOTE]
    > テクニカル プレビューでは、App Service リソース プロバイダー インストーラーにより、Azure Resource Manager をサポートする単純なファイル サーバーとして機能するように 1 つの Standard A1 インスタンスもデプロイされます。 これは、単一ノード開発キット用に残ります。 実稼働ワークロードについては、一般提供開始時に、App Service インストーラーにより、高可用性ファイル サーバーを使うことができるようになります。

19. App Service クラウド用のコンピューティング リソース プロバイダーで選択可能な項目の中から、デプロイの **Windows Server 2016** VM イメージを選びます。 **[次へ]** をクリックします。

    ![App Service on Azure Stack の VM イメージ選択][7]

20. App Service クラウドで構成されている Worker ロールについて、ユーザー名とパスワードを入力します。 その他すべての App Service ロールについて、ユーザー名とパスワードを入力します。 **[次へ]** をクリックします。

    ![App Service on Azure Stack の 資格情報入力][8]

21. 概要画面で、選択内容を確認します。 変更するには、画面を戻り、選択内容を変更します。 構成が希望どおりの場合は、チェック ボックスをオンにします。 デプロイを始めるには、**[次へ]** をクリックします。

    ![App Service on Azure Stack の選択概要][9]

22. インストールの進行状況を追跡します。 App Service on Azure Stack を既定の選択内容に基づいてデプロイするには、45 から 60 分くらいかかります。

    ![App Service on Azure Stack のインストールの進行状況][10]

23. インストーラーが正常に完了したら、**[終了]** をクリックします。

## <a name="validate-the-app-service-on-azure-stack-installation"></a>App Service on Azure Stack インストールを検証する

1. Azure Stack 管理ポータルで、インストーラーによって作られたリソース グループを参照します。 既定では、このグループは **APPSERVICE-LOCAL** です。

2. **CN0-VM** を見つけます。 仮想マシンに接続するには、**[仮想マシン]** ブレードで **[接続]** をクリックします。

3. この仮想マシンのデスクトップで、**[Web Cloud Management Console]\(Web クラウド管理コンソール\)** をダブルクリックします。

4. **[管理されたサーバー]** に移動します。

5. すべてのマシンで、1 つ以上の Worker について **[準備完了]** と表示されたら、手順 6 に進みます。

6. リモートのデスクトップ マシンを終了して、App Service インストーラーを実行したマシンに戻ります。

    > [!NOTE]
    > 1 つ以上の Worker に **[準備完了]** と表示されて App Service on Azure Stack のインストールが完了するまで待つ必要はありません。 ただし、少なくとも 1 つの Worker で、Web アプリ、モバイル アプリ、API アプリ、Azure Functions をデプロイする準備ができている必要があります。

    ![App Service on Azure Stack の管理されたサーバーのステータス][11]

## <a name="configure-an-azure-ad-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>Worker 層での仮想マシン スケール セット統合のために Azure AD サービス プリンシパルを、Azure Functions ポータルと高度な開発者ツールのために SSO を構成します

>[!NOTE]
> 次の手順は、Azure AD で保護されている Azure Stack 環境のみに適用されます。

管理者は、次のことのために SSO を構成する必要があります。

* App Service 内の高度な開発者ツール (Kudu) を有効にします。
* Azure Functions ポータルのエクスペリエンスを使うことができようにします。

次の手順に従います。

1. azurestack\azurestackadmin として PowerShell インスタンスを開きます。

2. [前提条件の手順](#Download-Required-Components)でダウンロードして展開したスクリプトの場所に移動します。

3. [Azure Stack PowerShell 環境をインストール](azure-stack-powershell-install.md)して[構成](azure-stack-powershell-configure-admin.md)します。

4. 同じ PowerShell セッションで、**CreateIdentityApp.ps1** スクリプトを実行します。 Azure AD テナント ID の指定を求められたら、Azure Stack のデプロイに使っている Azure AD テナント ID (例: myazurestack.onmicrosoft.com) を入力します。

5. **[資格情報]** ウィンドウで、Azure AD サービスの管理者アカウントとパスワードを入力します。 **[OK]**をクリックします。

6. [先ほど作った証明書](# Create certificates to be used by App Service on Azure Stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、sso.appservice.local.azurestack.external.pfx です。

7. このスクリプトでは、テナント Azure AD で新しいアプリケーションが作られ、新しい PowerShell スクリプト **UpdateConfigOnController.ps1** が生成されます。

    >[!NOTE]
    > PowerShell の出力で返される**アプリケーション ID** を書き留めておきます。 手順 12 でこの情報を検索する必要があります。

8. リモート デスクトップ セッションを使って、本人確認アプリ証明書ファイルと生成されたスクリプトを **CN0 VM** にコピーします。

9. 新しいブラウザー ウィンドウを開き、**Azure Active Directory サービス管理者**として Azure Portal (portal.azure.com) にサインインします。

10. Azure AD リソース プロバイダーを開きます。

11. **[アプリの登録]** をクリックします。

12. 手順 7 で返された**アプリケーション ID** を検索します。 一覧に App Service アプリケーションが表示されます。

13. 一覧で **[アプリケーション]** をクリックして、**[キー]** ブレードを開きます。

14. **[Description - Functions Portal]\(説明 - Functions ポータル\)** で新しいキーを追加し、**[有効期限]** を **[期限なし]** に設定します。

15. **[保存]** をクリックし、生成されたキーをコピーします。

    >[!NOTE]
    > 生成されたキーを、必ず書き留めるかコピーしておきます。 保存した後で再度表示することはできず、新しいキーを再生成する必要があります。

    ![App Service on Azure Stack アプリケーションのキー][12]

16. Azure AD で **[アプリケーションの登録]** に戻ります。

17. **[必要なアクセス許可]** > **[アクセス許可の付与]** > **[はい]** の順にクリックします。

    ![App Service on Azure Stack の SSO の付与][13]

18. **CN0-VM** に戻り、**[Web Cloud Management Console]\(Web クラウド管理コンソール\)** を再び開きます。

19. 左側のウィンドウで **[設定]** を選び、**[ApplicationClientSecret]\(ApplicationClientSecret\)** の設定を探します。

20. 右クリックして、**[編集]** を選びます。 手順 15 で生成されたキーを貼り付けて、**[OK]** をクリックします。

    ![App Service on Azure Stack アプリケーションのキー][14]

21. 管理者として PowerShell ウィンドウを開きます。 手順 7 でスクリプト ファイルと証明書をコピーしたディレクトリに移動します。

22. スクリプト ファイルを実行します。 このスクリプト ファイルでは、App Service on Azure Stack 構成にプロパティが入力され、すべてのフロントエンド ロールと管理ロールでの修復操作が開始されます。

| パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | 必須 | Null | Azure AD テナント ID。 GUID または文字列を指定します (例: myazureaaddirectory.onmicrosoft.com) |
| TenantAzure Resource ManagerEndpoint | 必須 | management.local.azurestack.external | テナントの Azure Resource Manager のエンドポイント |
| AzureStackCredential | 必須 | Null | Azure AD 管理者 |
| CertificateFilePath | 必須 | Null | 以前生成された本人確認アプリケーション証明書ファイルへのパス |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |
| AdfsMachineName | 省略可能 | Azure AD のデプロイの場合は無視しますが、AD FS のデプロイでは必要です。 AD FS マシン名 (例: AzS-ADFS01.azurestack.local) |

## <a name="configure-an-ad-fs-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>Worker 層での仮想マシン スケール セット統合のために AD FS サービス プリンシパルを、Azure Functions ポータルと高度な開発者ツールのために SSO を構成します

>[!NOTE]
> 次の手順は、AD FS で保護されている Azure Stack 環境のみに適用されます。

管理者は、次のことのために SSO を構成する必要があります。

* Worker 層での仮想マシン スケール セット統合のためにサービス プリンシパルを構成します。
* App Service 内の高度な開発者ツール (Kudu) を有効にします。
* Azure Functions ポータルのエクスペリエンスを使うことができようにします。 

次の手順に従います。

1. azurestack\azurestackadmin として PowerShell インスタンスを開きます。

2. [前提条件の手順](#Download-Required-Components)でダウンロードして展開したスクリプトの場所に移動します。

3. [Azure Stack PowerShell 環境をインストール](azure-stack-powershell-install.md)して[構成](azure-stack-powershell-configure-admin.md)します。

4. 同じ PowerShell セッションで、**CreateIdentityApp.ps1** スクリプトを実行します。 Azure AD テナント ID の指定を求められたら、「**ADFS**」と入力します。

5. **[資格情報]** ウィンドウで、AD FS サービスの管理者アカウントとパスワードを入力します。 **[OK]**をクリックします。

6. [先ほど作った証明書](# Create certificates to be used by App Service on Azure Stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、sso.appservice.local.azurestack.external.pfx です。

7. このスクリプトでは、テナント Azure AD で新しいアプリケーションが作られ、新しい PowerShell スクリプト **UpdateConfigOnController.ps1** が生成されます。

8. リモート デスクトップ セッションを使って、本人確認アプリ証明書ファイルと生成されたスクリプトを **CN0-VM** にコピーします。

9. **CN0-VM** に戻ります。

10. 管理者として PowerShell ウィンドウを開き、手順 7 でスクリプト ファイルと証明書をコピーしたディレクトリに移動します。

11. スクリプト ファイルを実行します。 このスクリプト ファイルでは、App Service on Azure Stack 構成にプロパティが入力され、すべてのフロントエンド ロールと管理ロールでの修復操作が開始されます。

| パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | 必須 | Null | AD FS 環境の場合は **ADFS** を使います |
| TenantAzure Resource ManagerEndpoint | 必須 | management.local.azurestack.external | テナントの Azure Resource Manager のエンドポイント |
| AzureStackCredential | 必須 | Null | AD FS サービスの管理者アカウント |
| CertificateFilePath | 必須 | Null | 以前生成された本人確認アプリケーション証明書ファイルへのパス |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |
| AdfsMachineName | 省略可能 | AD FS マシン名 (例: AzS-ADFS01.azurestack.local) |

## <a name="test-drive-app-service-on-azure-stack"></a>App Service on Azure Stack を試してみる

App Service リソース プロバイダーをデプロイして登録したら、テストしてテナントで Web アプリ、モバイル アプリ、API アプリをデプロイできることを確認します。

> [!NOTE]
> プラン内の Microsoft.Web 名前空間があるオファーを作る必要があります。 その後、このオファーをサブスクライブするテナント サブスクリプションが必要となります。 詳しくは、「[Azure Stack でのオファーの作成](azure-stack-create-offer.md)」および「[Azure Stack でのプランの作成](azure-stack-create-plan.md)」をご覧ください。
>
App Service on Azure Stack を使うアプリケーションを作るには、テナント サブスクリプションが*必要です*。 管理ポータル内でサービス管理者が実行できる唯一の機能は、App Service のリソース プロバイダー管理に関連しています。 これらの機能には、容量の追加、デプロイ ソースの構成、Worker 層と SKU の追加などがあります。
>
3 番目のテクニカル プレビューの時点では、Web アプリ、モバイル アプリ、API アプリ、Azure Functions アプリを作るには、テナント ポータルを使い、テナント サブスクリプションがある必要があります。 

1. Azure Stack テナント ポータルで、**[新規]** > **[Web + モバイル]** > **[Web アプリ]** をクリックします。

2. **[Web アプリ]** ブレードで、**[Web アプリ]** ボックスに名前を入力します。

3. **[リソース グループ]** の下にある **[新規]** をクリックします。 **[リソース グループ]** ボックスに名前を入力します。

4. **[App Service プラン/場所]** > **[新規作成]** の順にクリックします。

5. **[App Service プラン]** ブレードで、**[App Service プラン]** ボックスに名前を入力します。

6. **[価格レベル]** > **[Free-Shared]\(無料-共有\)** または **[Shared-Shared]\(共有-共有\)** > **[選択]** > **[OK]** > **[作成]** をクリックします。

7. 1 分以内に、ダッシュボードに新規 Web アプリのタイルが表示されます。 タイルをクリックします。

8. **[Web アプリ]** ブレードで、**[参照]** をクリックして、このアプリの既定の Web サイトを表示します。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress、DNN、Django の Web サイトをデプロイする (省略可能)

1. Azure Stack テナント ポータルで、**[+]** をクリックして Azure Marketplace にアクセスし、Django Web サイトをデプロイして、正常に完了するまで待ちます。 Django Web プラットフォームでは、ファイル システム ベースのデータベースが使われます。 SQL や MySQL など、追加のリソース プロバイダーは必要ありません。

2. MySQL リソース プロバイダーもデプロイした場合は、Marketplace から WordPress Web サイトをデプロイできます。 データベース パラメーターを求められたら、お好きなユーザー名とサーバー名で、ユーザー名を *User1@Server1* の形式で入力します。

3. SQL Server リソース プロバイダーもデプロイした場合は、Marketplace から DNN Web サイトをデプロイできます。 データベース パラメーターを求められたら、使用しているリソース プロバイダーに接続された SQL Server を実行しているコンピューター内のデータベースを選びます。

## <a name="next-steps"></a>次のステップ

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

- [SQL Server リソース プロバイダー](azure-stack-sql-resource-provider-deploy.md)
- [MySQL リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-exe-start.png
[2]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-cloud-configuration.png
[3]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-subscription-location-populated.png
[4]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-resource-group-sql.png
[5]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-certificates.png
[6]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-role-configuration.png
[7]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-vm-image-selection.png
[8]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-role-credentials.png
[9]: ./media/azure-stack-app-service-deploy/app-service-exe-selection-summary.png
[10]: ./media/azure-stack-app-service-deploy/app-service-exe-installation-progress.png
[11]: ./media/azure-stack-app-service-deploy/managed-servers.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sso-keys.png
[13]: ./media/azure-stack-app-service-deploy/app-service-sso-grant.png
[14]: ./media/azure-stack-app-service-deploy/app-service-application-secret.png

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

