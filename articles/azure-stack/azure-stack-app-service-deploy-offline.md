---
title: "オフライン環境に App Service をデプロイする: Azure Stack | Microsoft Docs"
description: "AD FS によって保護されている切断された Azure Stack 環境に App Service をデプロイする方法についての詳細なガイダンスです。"
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
ms.openlocfilehash: b733851d4459ead1ae437604a27ca7720e2a3a87
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>App Service リソースプロバイダーを AD FS によって保護されている切断された Azure Stack 環境に追加する

次のような場合、[Azure App Service リソースプロバイダー](azure-stack-app-service-overview.md)をお客様の Azure Stack デプロイに追加する必要があります。

* Active Directory フェデレーション サービス (AD FS) によって保護されている分離された環境で Azure Stack を実行している場合。 
* Azure Stack サブスクリプションを使う Web アプリケーション、モバイル アプリケーション、API アプリケーション、および Azure Functions アプリケーションを作る機能をテナントに与える場合。 

実行するには、この記事で示されている手順に従ってください。

## <a name="download-the-required-components"></a>必須コンポーネントをダウンロードする

1. [App Service on Azure Stack プレビュー インストーラー](http://aka.ms/appsvconmasrc1installer)をダウンロードします。

2. [App Service on Azure Stack デプロイ ヘルパー スクリプト](http://aka.ms/appsvconmasrc1helper)をダウンロードします。

3. ヘルパー スクリプトの zip ファイルからファイルを展開します。 次のファイルおよびフォルダー構造が表示されます。

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - モジュール
      - AzureStack.Identity.psm1
      - GraphAPI.psm1

## <a name="create-an-offline-installation-package"></a>オフライン インストール パッケージを作成する

分離された環境で App Service をデプロイするには、インターネットに接続しているマシンからオフライン インストール パッケージを作る必要があります。

1. インターネットに接続しているマシンから App Service on Azure Stack プレビュー インストーラー (AppService.exe) を実行します。

2. **[詳細設定]** タブ、**[オフライン インストール パッケージを作成する]** の順にクリックします。

    ![App Service on Azure Stack の [オフライン インストール パッケージを作成する]][1]

3. App Service インストーラーによりオフライン インストール パッケージが作られると、パスが表示されて、フォルダーを開くオプションが表示されます。

   ![App Service on Azure Stack のオフライン インストール パッケージ][2]

4. App Service on Azure Stack プレビュー インストーラー (AppService.exe) とオフライン インストール パッケージを Azure Stack ホスト マシンにコピーします。

## <a name="create-certificates-required-by-app-service-on-azure-stack"></a>App Service on Azure Stack に必要となる証明書を作成する

この 1 つ目のスクリプトでは、Azure Stack 証明機関と連携して、App Service で必要となる 3 つの証明書が作られます。 スクリプトを Azure Stack ホストで実行して、PowerShell を azurestack\administrator として実行していることを確認します。

1. azurestack\administrator として実行している PowerShell セッションで、ヘルパー スクリプトを展開した場所から **Create-AppServiceCerts.ps1** スクリプトを実行します。  このスクリプトでは、App Service で必要となる証明書作成スクリプトと同じフォルダーに、3 つの証明書が作られます。

2. .pfx ファイルを保護するためのパスワードを入力し、そのパスワードを書き留めておきます。 それを App Service on Azure Stack インストーラーに入力する必要があります。

### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 のパラメーター

| パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| pfxPassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |
| CertificateAuthority | 必須 | AzS-CA01.azurestack.local | 証明機関のエンドポイント |

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>App Service on Azure Stack のオフライン インストールを実行する

> [!NOTE]
> 管理者特権のアカウント (ローカルまたはドメイン管理者) を使ってインストーラーを実行する*必要があります*。 azurestack\azurestackuser としてサインインすると、管理者特権の資格情報を求められます。

1. azurestack\administrator として appservice.exe を実行します。

2. **[詳細設定]** タブ、**[オフライン インストールが完了しました]** の順にクリックします。

    ![App Service on Azure Stack の [オフライン インストールが完了しました]][3]

3. 先ほど作ったオフライン インストール パッケージの場所を指定し、**[次へ]** をクリックします。

    ![App Service on Azure Stack のオフライン インストール パッケージの場所][4]

4. Microsoft ソフトウェア プレリリース ライセンス条項を確認のうえ同意し、**[次へ]** をクリックします。

5. サード パーティのライセンス条項を確認のうえ同意し、**[次へ]** をクリックします。

6. App Service クラウド構成情報を確認し、**[次へ]** をクリックします。

    ![App Service on Azure Stack のクラウド構成][5]

    > [!NOTE]
    > App Service on Azure Stack インストーラーには、1 つのノードでの Azure Stack インストールのために既定値が用意されています。 Azure Stack のデプロイ時にオプションをカスタマイズした場合は (たとえば、ドメイン サフィックス)、それに応じてこのウィンドウ内の値を編集する必要があります。 たとえば、ドメイン サフィックス mycloud.com を使う場合は、管理 Azure Resource Manager エンドポイントを adminmanagement.[region].mycloud.com に変更する必要があります。

7. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスの横にある **[接続]** をクリックします。 管理者アカウントを入力します。たとえば、azurestackadmin@azurestack.local などです。 パスワードを入力し、**[サインイン]** をクリックします。

8. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスで自分のサブスクリプションを選びます。

9. **[Azure Stack Locations]\(Azure Stack の場所\)** ボックスで、デプロイしているリージョンに対応する場所を選びます。 たとえば、**[ローカル]** を選びます。 **[次へ]** をクリックします。

    ![App Service on Azure Stack のサブスクリプション選択][6]

10. お客様の App Service デプロイの **[リソース グループ名]** を入力します。 既定では、**[APPSERVICE-LOCAL]\(APPSERVICE-LOCAL\)** に設定されています。

11. インストールの一環として、App Service で作成する **[ストレージ アカウント名]** を入力します。 既定では、**[appsvclocalstor]\(appsvclocalstor\)** に設定されています。

12. App Service リソースプロバイダー データベースをホストするために使うインスタンスについて、SQL Server の詳細を入力します。 **[次へ]** をクリックします。インストーラーにより、SQL 接続のプロパティが検証されます。

13. **[App Service default SSL certificate file]\(App Service の既定の SSL 証明書ファイル\)** ボックスの横にある **[参照]** をクリックします。 [先ほど作った](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps) **_.appservice.local.AzureStack.external** 証明書に移動します。 証明書を作ったときに別の場所とドメイン サフィックスを指定した場合は、対応する証明書を選びます。

14. 証明書を作ったときに設定した証明書パスワードを入力します。

15. **[Resource provider SSL certificate file]\(リソースプロバイダーの SSL 証明書ファイル\)** ボックスの横にある **[参照]** をクリックします。 [先ほど作った](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps) **api.appservice.local.AzureStack.external** 証明書に移動します。 証明書を作ったときに別の場所とドメイン サフィックスを指定した場合は、対応する証明書を選びます。

16. 証明書を作ったときに設定した証明書パスワードを入力します。

17. **[Resource provider root certificate file]\(リソースプロバイダーのルート証明書ファイル\)** ボックスの横にある **[参照]** をクリックします。 [先ほど作った](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps) **AzureStackCertificationAuthority** 証明書に移動します。

18. **[次へ]** をクリックします。 インストーラーにより、指定された証明書パスワードが確認されます。

    ![App Service on Azure Stack の証明書の詳細][8]

19. App Service のロール構成を確認します。 ロールごとに、お勧めの最小インスタンス SKU が既定値として設定されます。 お客様のデプロイの計画に役立つように、コア要件とメモリ要件の概要を説明します。 必要な項目を選んだら、**[次へ]** をクリックします。

    - **コントローラー**: 既定では、1 つの Standard A1 インスタンスが選ばれます。 これは、お勧めの最小限の設定です。 コントローラー ロールは、App Service クラウドの正常性の管理と維持を担当します。
    - **管理**: 既定では、1 つの Standard A2 インスタンスが選ばれます。 フェールオーバーを利用するには、2 つのインスタンスを使うことをお勧めします。 管理ロールは、App Service Azure Resource Manager および API のエンドポイント、ポータル拡張機能 (管理、テナント、Functions ポータル)、データ サービスを担当します。
    - **発行者**: 既定では、1 つの Standard A1 インスタンスが選ばれます。 これは、お勧めの最小限の設定です。 発行者ロールは、FTP および Web デプロイを介したコンテンツの発行を担当します。
    - **フロントエンド**: 既定では、1 つの Standard A1 インスタンスが選ばれます。 これは、お勧めの最小限の設定です。 フロントエンド ロールは、App Service アプリケーションへの要求のルーティングを担当します。
    - **共有ワーカー**: 既定では、1 つの Standard A1 インスタンスが選ばれますが、さらに追加できます。 管理者は、サービスを定義することや任意の SKU レベルを選ぶことができます。 レベルには、少なくとも 1 つのコアが必要です。 共有ワーカー ロールは、Web アプリケーション、モバイル アプリケーション、API アプリケーション、Azure Functions アプリのホストを担当します。

    ![App Service on Azure Stack のロール構成][9]

    > [!NOTE]
    > テクニカル プレビューでは、App Service リソースプロバイダー インストーラーにより、1 つの Standard A1 インスタンスもデプロイされます。このインスタンスは単純なファイル サーバーとして機能し、Azure Resource Manager をサポートします。 これは、単一ノードの接点として残されています。 実稼働ワークロードについては、一般提供開始時に、App Service インストーラーにより、高可用性ファイル サーバーを使うことができるようになります。

20. App Service クラウド用のコンピューティング リソースプロバイダーで選択可能な項目の中から、お客様のデプロイの **Windows Server 2016** VM イメージを選びます。 **[次へ]** をクリックします。 

    ![App Service on Azure Stack の VM イメージ選択][10]

21. App Service クラウドで構成されている Worker ロールについて、ユーザー名とパスワードを入力します。 その他すべての App Service ロールについて、ユーザー名とパスワードを入力します。 **[次へ]** をクリックします。

    ![App Service on Azure Stack の 資格情報入力][11]

22. 概要画面で、選択した内容を確認します。 変更するには、画面を戻り、選択した内容を変更します。 構成が希望どおりの場合は、チェック ボックスをオンにします。 デプロイを始めるには、**[次へ]** をクリックします。 

    ![選択した App Service on Azure Stack の概要][12]

23. インストールの進行状況を追跡します。 App Service on Azure Stack を既定の選択内容に基づいてデプロイするには、45 分から 60 分ほどかかります。

    ![App Service on Azure Stack のインストールの進行状況][13]

24. インストーラーが正常に完了したら、**[終了]** をクリックします。

## <a name="configure-an-ad-fs-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>Worker 層での仮想マシン スケール セット統合のために AD FS サービス プリンシパルを、Azure Functions ポータルと高度な開発者ツールのために SSO を構成する

>[!NOTE]
> 次の手順は、AD FS で保護されている Azure Stack 環境のみに適用されます。

次のことを行うには、管理者は SSO を構成する必要があります。

* Worker 層での仮想マシン スケール セット統合のためにサービス プリンシパルを構成する。
* App Service 内の高度な開発者ツール (Kudu) を有効にする。
* Azure Functions ポータルのエクスペリエンスを有効にする。 

次の手順に従います。

1. azurestack\azurestackadmin として PowerShell インスタンスを開きます。

2. [前提条件の手順](#Download-Required-Components)でダウンロードして展開したスクリプトの場所に移動します。

3. [Azure Stack PowerShell 環境をインストール](azure-stack-powershell-install.md)して[構成](azure-stack-powershell-configure-admin.md)します。

4. 同じ PowerShell セッションで、**CreateIdentityApp.ps1** スクリプトを実行します。 Azure Active Directory (Azure AD) テナント ID を求められたら、**「ADFS」**と入力します。

5. **[資格情報]** ウィンドウで、AD FS サービスの管理者アカウントとパスワードを入力します。 **[OK]**をクリックします。

6. [先ほど作った証明書](# Create certificates to be used by App Service on Azure Stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、sso.appservice.local.azurestack.external.pfx です。

7. このスクリプトでは、テナント Azure AD で新しいアプリケーションが作られ、新しい PowerShell スクリプトが生成されます。

8. リモート デスクトップ セッションを使って、ID アプリ証明書ファイルと生成されたスクリプトを **CN0-VM** にコピーします。

9. **CN0-VM** に戻ります。

10. 管理者用 PowerShell ウィンドウを開き、手順 7 でスクリプト ファイルと証明書をコピーしたディレクトリに移動します。

11. スクリプト ファイルを実行します。 このスクリプト ファイルでは、App Service on Azure Stack 構成にプロパティが入力され、すべてのフロントエンド ロールと管理ロールでの修復操作が開始されます。

| パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | 必須 | Null | AD FS 環境のために **ADFS** を使います |
| TenantAzure Resource ManagerEndpoint | 必須 | management.local.azurestack.external | テナントの Azure Resource Manager のエンドポイント |
| AzureStackCredential | 必須 | Null | AD FS サービスの管理者アカウント |
| CertificateFilePath | 必須 | Null | 先ほど生成された ID アプリケーション証明書ファイルへのパス |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |
| AdfsMachineName | 省略可能 | AD FS マシン名。たとえば、AzS-ADFS01.azurestack.local など |


## <a name="validate-the-app-service-on-azure-stack-installation"></a>App Service on Azure Stack インストールを検証する

1. Azure Stack 管理ポータルで、インストーラーによって作られたリソース グループを参照します。 既定では、このグループは **APPSERVICE-LOCAL** です。

2. **CN0-VM** を見つけます。 VM に接続するには、**[仮想マシン]** ブレードで **[接続]** をクリックします。

3. この VM のデスクトップで、**[Web Cloud Management Console]\(Web クラウド管理コンソール\)** をダブルクリックします。

4. **[管理されたサーバー]** に移動します。

5. すべてのマシンで、1 つ以上の Worker について **[準備完了]** と表示されたら、手順 6 に進みます。

6. リモートのデスクトップ マシンを終了して、App Service インストーラーを実行したマシンに戻ります。

    > [!NOTE]
    > 1 つ以上の Worker に **[準備完了]** と表示されて App Service on Azure Stack のインストールが完了するまで待つ必要はありません。 ただし、少なくとも 1 つの Worker で、Web アプリ、モバイル アプリ、API アプリ、Azure Functions をデプロイする準備ができている必要があります。
    
    ![App Service on Azure Stack の管理されたサーバーのステータス][14]

## <a name="test-drive-app-service-on-azure-stack"></a>App Service on Azure Stack を試してみる

App Service リソースプロバイダーをデプロイして登録したら、テストしてテナントで Web アプリ、モバイル アプリ、API アプリをデプロイできることを確認します。

> [!NOTE]
> プラン内に Microsoft.Web 名前空間があるサービスを作る必要があります。 その後、このサービスをサブスクライブするテナント サブスクリプションが必要となります。 詳細については、[サービスの作成](azure-stack-create-offer.md)と[プランの作成](azure-stack-create-plan.md)に関するページをご覧ください。
>

App Service on Azure Stack を使うアプリケーションを作るには、テナント サブスクリプションが*必要となります*。 管理ポータル内でサービス管理者が実行できる機能は、App Service のリソースプロバイダー管理に関連するものだけです。 これらの機能には、容量の追加、デプロイ ソースの構成、Worker 層と SKU の追加などがあります。

3 番目のテクニカル プレビューの時点では、Web アプリ、モバイル アプリ、API アプリを作るには、テナント ポータルを使うことと、テナント サブスクリプションがあることが必要になります。  

1. Azure Stack テナント ポータルで、**[新規]**  >  **[Web + モバイル]**  >  **[Web アプリ]** の順にクリックします。

2. **[Web アプリ]** ブレードで、**[Web アプリ]** ボックスに名前を入力します。

3. **[リソース グループ]** の下にある **[新規]** をクリックします。 次に、**[リソース グループ]** ボックスに名前を入力します。

4. **[App Service プラン/場所]** > **[新規作成]** の順にクリックします。

5. **[App Service プラン]** ブレードで、**[App Service プラン]** ボックスに名前を入力します。

6. **[価格レベル]**  >  **[Free-Shared]\(無料 - 共有\)** または **[Shared-Shared]\(共有 - 共有\)**  >  **[選択]**  >  **[OK]**  >  **[作成]** の順にクリックします。

7. 1 分以内に、ダッシュボードに新規 Web アプリのタイルが表示されます。 タイルをクリックします。

8. **[Web アプリ]** ブレードで、**[参照]** をクリックして、このアプリの既定の Web サイトを表示します。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress、DNN、Django の Web サイトをデプロイする (省略可能)

1. Azure Stack テナント ポータルで、**+** をクリックします。 Azure Marketplace にアクセスし、Django Web サイトをデプロイし、正常に完了するまで待ちます。 Django Web プラットフォームでは、ファイル システム ベースのデータベースが使われます。 SQL や MySQL など、追加のリソースプロバイダーは必要ありません。

2. MySQL リソースプロバイダーもデプロイした場合は、Marketplace から WordPress Web サイトをデプロイできます。 データベース パラメーターを求められたら、お好きなユーザー名とサーバー名で、ユーザー名を *User1@Server1* の形式で入力します。

3. SQL Server リソースプロバイダーもデプロイした場合は、Marketplace から DNN Web サイトをデプロイできます。 データベース パラメーターを求められたら、使用しているリソースプロバイダーに接続された SQL Server を実行しているコンピューター内のデータベースを選びます。

## <a name="next-steps"></a>次のステップ

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

- [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
- [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step1.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step2.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step3.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-offline-step4.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-cloud-configuration.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-subscription-location-populated.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-resource-group-SQL.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-certificates.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-configuration.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-vm-image-selection.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-default-entries-step-role-credentials.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-selection-summary.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-installation-progress.png
[14]: ./media/azure-stack-app-service-deploy-offline/managed-servers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

