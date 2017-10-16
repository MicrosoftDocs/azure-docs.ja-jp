---
title: "Jenkins を使用した Web アプリの Azure へのデプロイ | Microsoft Docs"
description: "Jenkins と Docker を使用して、GitHub から Azure App Service に Java Web アプリの継続的インテグレーションを設定します。"
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 3a2635ac968d843226f05dc51cf4a5f078235c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Jenkins による Azure App Service への継続的インテグレーションとデプロイの設定

このチュートリアルでは Jenkins を使用して、[Azure App Service Web App on Linux](/azure/app-service/containers/app-service-linux-intro) に対して、[Spring Boot](http://projects.spring.io/spring-boot/) フレームワークで開発したサンプル Java Web アプリの継続的インテグレーションおよび継続的デプロイ (CI/CD) を設定します。

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * Azure App Service のデプロイに必要な Jenkins プラグインをインストールします。
> * 新しいコミットがプッシュされたときに、GitHub リポジトリから Docker イメージを作成するための Jenkins ジョブを定義します。
> * Linux 用の新しい Azure Web アプリを定義し、Azure Container Registry にプッシュされる Docker イメージをデプロイするように構成します。
> * Azure App Service Jenkins プラグインを構成します。
> * サンプル アプリを手動でビルドして、Azure App Service にデプロイします。
> * GitHub に変更をプッシュして、Jenkins に Web アプリのビルドと更新をトリガーします。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、次のものが必要です。

* JDK および Maven ツールが構成されている [Jenkins](https://jenkins.io/)。 Jenkins システムがない場合は、Azure 上で [Jenkins ソリューション テンプレート](/azure/jenkins/install-jenkins-solution-template)から作成します。
* [GitHub](https://github.com) アカウント。
* [Azure CLI 2.0](/cli/azure/overview) (ローカルのコマンドラインまたは [Azure Cloud Shell](/azure/cloud-shell/overview) のいずれかから)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Jenkins プラグインのインストール

1. Web ブラウザーで Jenkins Web コンソールを開き、左側のメニューから **[Manage Jenkins]\(Jenkins の管理\)** を選択し、**[Manage Plugins]\(プラグインの管理\)** を選択します。
2. **[Available]\(利用可能\)** タブをクリックします。
3. 下記のプラグインの横にあるチェックボックスを探して選択します。   

    - [Azure App Service プラグイン](https://plugins.jenkins.io/azure-app-service)
    - [GitHub ブランチ ソース プラグイン](https://plugins.jenkins.io/github-branch-source)

    プラグインが表示されない場合は、**[Installed]\(インストール済み\)** タブを確認して、既にインストールされていないかを確認してください。

1. **[Download now and install after restart]\(今すぐダウンロードして再起動後にインストール\)** を選択して、お使いの Jenkins 構成でプラグインを有効にします。

## <a name="configure-github-and-jenkins"></a>GitHub と Jenkins の構成

お使いのアカウントのリポジトリに新しいコミットがプッシュされたときに、[GitHub webhook](https://developer.github.com/webhooks/) を受信できるように Jenkins を設定します。

1. **[Manage Jenkins]\(Jenkins の管理\)** を選択し、次に **[Configure System]\(システムの構成\)** を選択します。 **[GitHub]** セクションで **[Manage hooks]\(フックの管理\)** が選択されていることを確認したら、**[Manage additional GitHub actions]\(GitHub の追加アクションの管理\)** を選択して、**[Convert login and password to token]\(ログインとパスワードをトークンに変換する\)** を選びます。
2. **[ログインとパスワードの保存]** を選択して、GitHub ユーザー名とパスワードを入力します。 **[Create token credentials]\(トークン資格情報の作成\)** を選択して、新しい [GitHub 個人用アクセス トークン](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)を作成します。   
   ![ログインとパスワードから GitHub PAT を作成する](media/jenkins-java-quickstart/create_github_credentials.png)
3.  GitHub サーバー構成の **[Credentials]\(資格情報\)** ボックスの一覧から、新しく作成したトークンを選択します。 **[Test connection]\(テスト接続\)** を選択して、認証が機能していることを検証します。   
   ![PAT の構成後に GitHub への接続を検証する](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> GitHub アカウントで 2 要素認証が有効になっている場合は、GitHub でトークンを作成して、Jenkins で使用するように構成します。 詳細については、[Jenkins の GitHub プライグイン](https://wiki.jenkins.io/display/JENKINS/Github+Plugin)に関するドキュメントをご覧ください。

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>サンプル リポジトリのフォークと Jenkins ジョブの作成 

1. [Spring Boot のサンプル アプリケーション リポジトリ](https://github.com/spring-guides/gs-spring-boot-docker)を開き、右上端の **Fork** を選択して、お客様独自のGitHub アカウントにフォークします。   
    ![GitHub からのフォーク](media/jenkins-java-quickstart/fork_github_repo.png)
1. Jenkins Web コンソールで **[New Item]\(新しいアイテム\)** を選択して **MyJavaApp** という名前を付け、**[Freestyle project]\(フリースタイル プロジェクト\)** を選択して **[OK]** を選択します。   
    ![Jenkins の新しいフリースタイル プロジェクト](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. **[General]\(一般\)** セクションから **GitHub** プロジェクトを選択し､フォークしたリポジトリの URL (例: https://github.com/raisa/gs-spring-boot-docker) を入力します｡
3. **[Source code management]\(ソース コード管理\)** セクションから **Git** を選択し､フォークした `.git` リポジトリの URL (例: https://github.com/raisa/gs-spring-boot-docker.git) を入力します｡
4. [**Build Triggers**] セクションから **GitHub hook trigger for GITscm polling** を選択します｡
5. **[Build]\(ビルド\)** セクションから **[Add build step]\(ビルド手順の追加\)** を選択し、**[Invoke top-level Maven targets]\(Maven の最上位レベルのターゲットを呼び出す\)** を選びます。 **[Goals]\(目標\)** フィールドに `package` を入力します。
6. [ **保存**] を選択します。 ジョブをテストするには、プロジェクト ページから **[Build Now]\(今すぐ作成\)** を選択します。

## <a name="configure-azure-app-service"></a>Azure App Service の構成 

1. Azure CLI または [Cloud Shell](/azure/cloud-shell/overview) を使用して、新しい [Linux 上の Web アプリ](/azure/app-service/containers/app-service-linux-intro)を作成します。 このチュートリアルでは Web アプリケーションの名前を `myJavaApp` にしていますが、独自のアプリでは一意の名前を使用する必要があります。
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan
    ```

2. Jenkins によってビルドされた Docker イメージを格納するために、[Azure Container Registry](/azure/container-registry/container-registry-intro) を作成します。 このチュートリアルではコンテナー レジストリの名前に `jenkinsregistry` を使用していますが、独自のコンテナー レジストリでは一意の名前を使用する必要があります。 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. コンテナー レジストリにプッシュされる Docker イメージを実行するように Web アプリを構成し、コンテナーで実行されているアプリがポート 8080 で要求をリッスンするように指定します。   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Azure App Service の Jenkins プラグインの構成

1. Jenkins Web コンソールで、作成した **MyJavaApp** ジョブを選択し、ページの左側にある **[Configure]\(構成\)** を選択します。
2. 下方向にスクロールして **[Post-build Actions]\(ビルド後のアクション\)** まで移動し、**[Add post-build action]\(ビルド後のアクションの追加\)** を選択して、**[Publish an Azure Web App]\(Azure Web アプリの発行\)** を選びます。
3. **[Publish an Azure Web App]\(Azure Web アプリの発行\)** から、**[Azure Credentials]\(Azure 資格情報\)** の横にある **[Add]\(追加\)** を選択し、**[Jenkins]** を選びます。
4. **[Add Credentials]\(資格情報の追加\)** ダイアログで、**[Kind]\(種類\)** ボックスの一覧から **[Microsoft Azure Service Principal]\(Microsoft Azure サービス プリンシパル\)** を選択します。
5. Azure CLI または [Cloud Shell](/azure/cloud-shell/overview) から Active Directory サービス プリンシパルを作成します。
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. サービス プリンシパルから **[Add Credentials]\(資格情報の追加\)** ダイアログに資格情報を入力します。 Azure サブスクリプション ID がわからない場合は、CLI で次のようにすると照会できます。
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Azure サービス プリンシパルを構成する](media/jenkins-java-quickstart/azure_service_principal.png)
6. **[Verify Service Principal]\(サービス プリンシパルの検証\)** を選択して、Azure でのサービス プリンシパル認証を確認します。 
7. **[追加]** を選択して資格情報を保存します。
8. **[Publish an Azure Web App]\(Azure Web アプリの発行\)** の構成に戻ってきたら、**[Azure Credentials]\(Azure 資格情報\)** ボックスの一覧から追加したサービス プリンシパル資格情報を選択します。
9. **[アプリの構成]** で、ドロップダウンの一覧からリソース グループとWeb アプリ名を選択します。
10. **[Publish via Docker]\(Docker から発行\)** を選択します。
11. **Dockerfile path** に `complete/Dockerfile` を入力します。
12. **[Docker registry URL]\(Docker の登録 URL\)** フィールドに `https://jenkinsregistry.azurecr.io` を入力します。
13. **[Registry Credentials]\(レジストリ資格情報\)** の横にある **[追加]** を選択します。 
14. **Username** で作成した管理者ユーザー名を Azure Container Registry に入力します。
15. **[パスワード]** フィールドに Azure Container Registry のパスワードを入力します。 Azure Portal または 次の CLI コマンドを利用して、ユーザー名とパスワードを取得できます。

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![コンテナー レジストリの資格情報の追加](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. **[追加]** を選択して資格情報を保存します。
16. **[Publish an Azure Web App]\(Azure Web アプリの発行\)** の **[アプリの構成]** パネルで、新しく作成された資格情報を **[Registry Credentials]\(レジストリ資格情報\)** ボックスの一覧から選択します。 完成したビルド後の動作は、次の図のようになります。   
    ![Azure App Service デプロイのビルド後のアクションの構成](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. **[保存]** をクリックしてジョブを保存します。

## <a name="deploy-the-app-from-github"></a>GitHub からのアプリのデプロイ

1. Jenkins プロジェクトから **[今すぐ作成]** を選択し、サンプル アプリを Azure にデプロイします。
2. ビルドが完了すると、アプリは Azure が公開する URL (例: http://myjavaapp.azurewebsites.net) で閲覧できるようになります。   
   ![Azure にデプロイされたアプリを表示します](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>変更のプッシュと再デプロイ

1. GitHub のフォークから、Web で `complete/src/main/java/Hello/Application.java` を閲覧します。 GitHub インターフェイスの右側にある **[このファイルの編集]** を選択します。
2. `home()` メソッドに次の変更を行い、変更内容をリポジトリのマスター ブランチにコミットします。
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. 新しいビルドが Jenkins 上で開始されます。このビルドは、リポジトリの `master` ブランチに対して新しいコミットが行われることでトリガーされます。 完了すると、アプリを Azure 上に再度読み込みます。     
      ![Azure にデプロイされたアプリを表示する](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>次のステップ

- [Azure VMs をビルド エージェントとして使用する](/azure/jenkins/jenkins-azure-vm-agents)
- [Azure CLI を使用してジョブのリソースとパイプラインを管理する](/azure/jenkins/execute-cli-jenkins-pipeline)
 
