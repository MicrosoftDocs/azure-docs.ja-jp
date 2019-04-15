---
title: 高可用性構成で Azure Stack App Service をデプロイする | Microsoft Docs
description: 高可用性構成を使用して Azure Stack に App Service をデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/23/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1c105548f19994c4ca0ce161eedcfe11736864c7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370025"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>高可用性構成で App Service をデプロイする

この記事では、Azure Stack Marketplace アイテムを使用して、高可用性構成で Azure Stack 用の App Service をデプロイする方法について説明します。 このソリューションは、利用可能な Marketplace アイテムに加えて、[appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack クイックスタート テンプレートも使用します。 このテンプレートによって、App Service リソース プロバイダーをホストするための高可用性インフラストラクチャの作成が自動化されます。 その後、この高可用性 VM インフラストラクチャに App Service がインストールされます。 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>高可用性 App Service インフラストラクチャ VM をデプロイする
[appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack クイックスタート テンプレートを使用すると、高可用性構成で App Service を簡単にデプロイできます。 これは [Default Provider Subscription]\(既定のプロバイダー サブスクリプション\) にデプロイする必要があります。 

このテンプレートを使用して Azure Stack でカスタム リソースを作成すると、このテンプレートによって次のものが作成されます。
- 仮想ネットワークと必要なサブネット。
- ファイル サーバー、SQL Server、および Active Directory Domain Services (AD DS) サブネットのネットワーク セキュリティ グループ。
- VM ディスクとクラスター クラウド監視のストレージ アカウント。
- プライベート IP が SQL AlwaysOn リスナーにバインドされた SQL VM 用の 1 つの内部ロードバランサー。
- AD DS、ファイル サーバー クラスター、および SQL クラスターの 3 つの可用性セット。
- 2 ノード SQL クラスター。
- 2 ノード ファイル サーバー クラスター。
- 2 台のドメイン コントローラー。

### <a name="required-azure-stack-marketplace-items"></a>必須の Azure Stack Marketplace アイテム
このテンプレートを使用する前に、次の [ Azure Stack Marketplace アイテム](azure-stack-marketplace-azure-items.md)が Azure Stack インスタンスで利用可能であることを確認してください。

- Windows Server 2016 Datacenter Core イメージ (AD DS とファイルサーバー VM 用)
- Windows Server 2016 (Enterprise) の SQL Server 2016 SP2
- 最新の SQL IaaS 拡張機能 
- 最新の PowerShell Desired State Configuration 拡張機能 

> [!TIP]
> テンプレートの要件と既定値について詳しくは、GitHub の[テンプレートの README ファイル](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha)を確認してください。 

### <a name="deploy-the-app-service-infrastructure"></a>App Service インフラストラクチャをデプロイする
このセクションの手順に従い、**appservice-fileshare-sqlserver-ha** Azure Stack クイックスタート テンプレートを使用してカスタム デプロイを作成します。

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. **\+[** **リソースの作成]** > **[カスタム]** の順に選択し、**[テンプレートのデプロイ]** を選択します。

   ![カスタム テンプレートのデプロイ](media/app-service-deploy-ha/1.png)


3. **[カスタム デプロイ]** ブレードで **[テンプレートの編集]** > **[クイック スタート テンプレート]** を選択し、使用できるカスタム テンプレートのドロップダウン リストを使用して **appservice-fileshare-sqlserver-ha** テンプレートを選択し、**[OK]**、**[保存]** の順にクリックします。

   ![appservice-fileshare-sqlserver-ha クイック スタート テンプレートを選択します。](media/app-service-deploy-ha/2.png)

4. **[カスタム デプロイ]** ブレードで、**[パラメーターの編集]** を選択し、下にスクロールしてテンプレートの既定値を確認します。 必要に応じてそれらの値を変更して、必要なすべてのパラメーター情報を入力し、**[OK]** をクリックします。<br><br> 少なくとも、ADMINPASSWORD、FILESHAREOWNERPASSWORD、FILESHAREUSERPASSWORD、SQLSERVERSERVICEACCOUNTPASSWORD、SQLLOGINPASSWORD の各パラメーターには複雑なパスワードを指定してください。
    
   ![カスタム デプロイ パラメーターを編集する](media/app-service-deploy-ha/3.png)

5. **[カスタム デプロイ]** ブレードで、使用するサブスクリプションとして **[Default Provider Subscription]\(既定のプロバイダー サブスクリプション\)** が選択されていることを確認してから、カスタム デプロイ用に新しいリソース グループを作成するか、既存のリソース グループを選択します。<br><br> 次に、リソース グループの場所 (ASDK のインストールの場合は **local**) を選択し、**[作成]** をクリックします。 テンプレートのデプロイが開始される前にカスタムのデプロイ設定が検証されます。

    ![カスタム デプロイを作成する](media/app-service-deploy-ha/4.png)

6. 管理ポータルで、**[リソース グループ]** を選択してから、カスタム デプロイ用に作成したリソース グループの名前を選択します (この例では **app-service-ha**)。 すべてのデプロイが正常に完了したことを確認するために、デプロイの状態を表示します。

   > [!NOTE]
   > テンプレートのデプロイが完了するまでに約 1 時間かかります。

   [![](media/app-service-deploy-ha/5-sm.png "テンプレートのデプロイの状態を確認する")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>テンプレートの出力を記録する
テンプレートのデプロイが正常に完了したら、テンプレートのデプロイの出力を記録します。 App Service インストーラーを実行するときに、この情報を入力する必要があります。 

これらの出力値のそれぞれを必ず記録してください。
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLServer
- SQLuser

テンプレートの出力値を見つけるには、次の手順に従ってください。

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 管理ポータルで、**[リソース グループ]** を選択してから、カスタム デプロイ用に作成したリソース グループの名前を選択します (この例では **app-service-ha**)。 

3. **[デプロイ]** をクリックしてから、**[Microsoft.Template]** を選択します。

    ![Microsoft.template のデプロイ](media/app-service-deploy-ha/6.png)

4. **[Microsoft.Template]** デプロイを選択した後、**[出力]** を選択し、テンプレート パラメーターの出力を記録します。 App Service をデプロイするときに、この情報が必要になります。

    ![パラメーターの出力](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>高可用性構成で App Service をデプロイする
このセクションの手順に従って、[appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack クイックスタート テンプレートに基づき、高可用性構成で Azure Stack 用の App Service をデプロイします。 

App Service リソース プロバイダーをインストールした後で、オファーやプランに含めることができます。 ユーザーはサブスクライブしてサービスを取得し、アプリケーションの作成を開始できます。

> [!IMPORTANT]
> リソース プロバイダーのインストーラーを実行する前に、App Service の各リリースに付属しているリリース ノートを読んで、新しい機能、修正点、およびデプロイに影響を与える可能性のある既知の問題を把握していることを確認してください。

### <a name="prerequisites"></a>前提条件
App Service インストーラーを実行する前に、「[App Service on Azure Stack を開始する前に](azure-stack-app-service-before-you-get-started.md)」の記事で説明されている手順をいくつか実行する必要があります。

> [!TIP]
> テンプレートのデプロイによってインフラストラクチャ VM が自動的に構成されるため、必ずしも「開始する前に」の記事で説明されているすべての手順が必要というわけではありません。 

- [App Service インストーラーおよびヘルパー スクリプトをダウンロードする](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)。
- [Azure Stack Marketplace に最新のカスタム スクリプト拡張機能をダウンロードする](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)。
- [必要な証明書を生成する](azure-stack-app-service-before-you-get-started.md#get-certificates)。
- Azure Stack 用に選択した ID プロバイダーに基づいて ID アプリケーションを作成する。 ID アプリケーションは [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application)または [Active Directory フェデレーション サービス (AD FS)](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) のいずれかに対して作成し、アプリケーション ID を記録できます。
- Windows Server 2016 Datacenter イメージを Azure Stack Marketplace に追加したことを確認してください。 これは App Service のインストールに必要です。

### <a name="deploy-app-service-in-highly-available-configuration"></a>高可用性構成で App Service をデプロイする
App Service リソース プロバイダーのインストールには少なくとも 1 時間かかります。 必要な時間の長さは、デプロイするロール インスタンスの数によって異なります。 デプロイ中に、インストーラーは次のタスクを実行します。

- 指定された Azure Stack ストレージ アカウントに BLOB コンテナーを作成します。
- App Service の DNS ゾーンとエントリを作成します。
- App Service リソース プロバイダーを登録します。
- App Service のギャラリー アイテムを登録します。

App Service リソースプロバイダーをデプロイするには、次の手順を実行します。

1. Azure Stack 管理の Azure Resource Management エンドポイントにアクセスできるコンピューターから、以前にダウンロードした App Service インストーラー (**appservice.exe**) を管理者として実行します。

2. **[Deploy App Service or upgrade to the latest version]\(App Service をデプロイするか、または最新バージョンにアップグレードする\)** を選択します。

    ![デプロイまたはアップグレード](media/app-service-deploy-ha/01.png)

3. Microsoft のライセンス条項に同意し、**[次へ]** をクリックします。

    ![Microsoft のライセンス条項](media/app-service-deploy-ha/02.png)

4. Microsoft 以外のライセンス条項に同意し、**[次へ]** をクリックします。

    ![Microsoft 以外のライセンス条項](media/app-service-deploy-ha/03.png)

5. Azure Stack 環境に App Service クラウド エンドポイント構成を指定します。

    ![App Service のクラウド エンドポイント構成](media/app-service-deploy-ha/04.png)

6. インストールに使用する Azure Stack サブスクリプションに**接続**し、場所を選択します。 

    ![Azure Stack サブスクリプションに接続する](media/app-service-deploy-ha/05.png)

7. 高可用性テンプレートのデプロイに使用されるリソース グループに **[既存の VNet とサブネットを使用する]** と **[リソース グループ名]** を選択します。<br><br>次に、テンプレートのデプロイの一環として作成された仮想ネットワークを選択してから、ドロップダウン リスト オプションから適切なロールのサブネットを選択します。 

    ![VNet の選択](media/app-service-deploy-ha/06.png)

8. ファイル共有パスとファイル共有所有者のパラメーターに、以前に記録したテンプレート出力情報を入力します。 完了したら、 **[次へ]** をクリックします。

    ![ファイル共有の出力情報](media/app-service-deploy-ha/07.png)

9. App Service のインストールに使用されているマシンは、App Service のファイル共有をホストするために使用されているファイル サーバーと同じ VNet 上に配置されていないため、ユーザーは名前を解決することができません。 **これは予想される動作です**。<br><br>ファイル共有の UNC パスとアカウント情報に入力した情報が正しいことを確認し、アラート ダイアログで **[はい]** を押して App Service のインストールを続行してください。

    ![予期されるエラー ダイアログ](media/app-service-deploy-ha/08.png)

    ファイル サーバーに接続するために既存の仮想ネットワークと内部 IP アドレスへデプロイする場合は、送信セキュリティ規則を追加して、worker サブネットとファイル サーバー間の SMB トラフィックを有効にする必要があります。 管理ポータルで WorkersNsg に移動し、次のプロパティを持つ送信セキュリティ規則を追加します。
    - ソース:任意
    - 送信元ポート範囲: *
    - 変換先:IP アドレス
    - 宛先 IP アドレス範囲:ファイル サーバーの IP の範囲
    - 送信先ポート範囲:445
    - プロトコル:TCP
    - アクション:ALLOW
    - 優先順位:700
    - 名前:Outbound_Allow_SMB445

10. Identity Application ID を入力し、ID 証明書のパスとパスワードを入力して、**[次へ]** をクリックします。
    - Identity Application 証明書 (**sso.appservice.local.azurestack.external.pfx** の形式)
    - Azure Resource Manager ルート証明書 (**AzureStackCertificationAuthority.cer**)

    ![Identity Application 証明書とルート証明書](media/app-service-deploy-ha/008.png)

10. 次に、以下の証明書に必要な残りの情報を入力して、**[次へ]** をクリックします。
    - 既定の Azure Stack SSL 証明書 (**_.appservice.local.azurestack.external.pfx** の形式)
    - API SSL 証明書 (**api.appservice.local.azurestack.external.pfx** の形式)
    - パブリッシャー証明書 (**ftp.appservice.local.azurestack.external.pfx** の形式) 

    ![追加の構成証明書](media/app-service-deploy-ha/09.png)

11. 高可用性テンプレートのデプロイ出力からの SQL Server 接続情報を使用して、SQL Server 接続情報を入力します。

    ![SQL Server の接続情報](media/app-service-deploy-ha/10.png)

12. App Service のインストールに使用されているマシンは、App Service データベースをホストするために使用されている SQL Server と同じ VNet 上に配置されていないため、ユーザーは名前を解決することができません。  **これは予想される動作です**。<br><br>SQL Server の名前とアカウント情報に入力した情報が正しいことを確認し、**[はい]** を押して App Service のインストールを続行してください。 **[次へ]** をクリックします。

    ![SQL Server の接続情報](media/app-service-deploy-ha/11.png)

13. 既定のロール構成値を受け入れるか、または推奨値に変更して **[次へ]** をクリックします。<br><br>高可用性構成の場合は、App Service インフラストラクチャ ロール インスタンスの既定値を次のように変更することをお勧めします。

    |Role|既定値|高可用性の推奨設定|
    |-----|-----|-----|
    |コントローラー ロール|2|2|
    |管理ロール|1|3|
    |パブリッシャー ロール|1|3|
    |FrontEnd ロール|1|3|
    |共有 worker ロール|1|10|
    |     |     |     |

    ![インフラストラクチャ ロール インスタンスの値](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 既定値からこのチュートリアルで推奨されている値に変更すると、App Service をインストールするためのハードウェア要件が増加します。 15 VM 用に既定の 18 コアおよび 32,256 MB の RAM をサポートする代わりに、推奨される 21 VM をサポートするには、合計 26 コアおよび 46,592 MB のRAM が必要です。

14. App Service インフラストラクチャ VM のインストールに使用するプラットフォーム イメージを選択して、**[次へ]** をクリックします。

    ![プラットフォーム イメージの選択](media/app-service-deploy-ha/13.png)

15. 使用する App Service インフラストラクチャ ロールの資格情報を入力して、**[次へ]** をクリックします。

    ![インフラストラクチャ ロールの資格情報](media/app-service-deploy-ha/14.png)

16. App Service のデプロイに使用される情報を確認し、**[次へ]** をクリックしてデプロイを開始します。 

    ![インストールの概要の確認](media/app-service-deploy-ha/15.png)

17. App Service デプロイの進行状況を確認します。 特定のデプロイ構成とハードウェアによっては、これに 1 時間以上かかることがあります。 インストーラーが正常に完了したら、**[終了]** を選択します。

    ![セットアップの完了](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>次の手順

[App Service をスケールアウトします](azure-stack-app-service-add-worker-roles.md)。 ご使用の環境で予想されるアプリケーション需要を満たすために、App Service インフラストラクチャ ロール worker を追加する必要が生じる場合があります。 既定では、Azure Stack 上の App Service は無料の共有 worker 層をサポートしています。 他の worker 階層を追加するには、worker ロールを追加する必要があります。

[デプロイ ソースを構成します](azure-stack-app-service-configure-deployment-sources.md)。 GitHub、BitBucket、OneDrive、DropBox などの複数のソース管理プロバイダーからのオンデマンド デプロイをサポートするには、追加の構成が必要です。

[App Service をバックアップします](app-service-back-up.md)。 App Service を正常にデプロイして構成したら、データ損失を防ぎ、復旧操作中のサービスの不必要なダウンタイムを回避するために、ディザスター リカバリーに必要なすべてのコンポーネントが確実にバックアップされていることを確認する必要があります。