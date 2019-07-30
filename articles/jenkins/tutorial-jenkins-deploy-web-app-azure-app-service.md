---
title: チュートリアル - Jenkins を使用して GitHub から Azure App Service にデプロイする
description: GitHub からの継続的インテグレーション (CI) と Azure App Service への継続的デプロイ (CD) を Java Web アプリで行えるよう、Jenkins を設定します
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: 019c4a8f77f2664c68dcc6499fb2f27cc0d1447c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326918"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>チュートリアル: Jenkins の継続的インテグレーションおよびデプロイを使用して GitHub から Azure App Service にデプロイする

このチュートリアルでは、Jenkins で継続的インテグレーション (CI) と継続的デプロイ (CD) を設定して、GitHub から [Azure App Service on Linux](/azure/app-service/containers/app-service-linux-intro) にサンプル Java Web アプリをデプロイします。 GitHub にコミットをプッシュしてアプリを更新すると、Jenkins によって自動的にお客様のアプリがビルドされ、Azure App Service に再発行されます。 このチュートリアルのサンプル アプリは、[Spring Boot](https://projects.spring.io/spring-boot/) フレームワークを使用して開発されました。 

![概要](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

このチュートリアルでは、以下のタスクを完了します。

> [!div class="checklist"]
> * GitHub からのビルド、Azure App Service へのデプロイ、その他の関連タスクを行えるよう、Jenkins プラグインをインストールする。
> * 作業コピーを用意するためにサンプル GitHub リポジトリをフォークする。
> * Jenkins を GitHub に接続する。
> * お客様の資格情報を使用しなくても Jenkins が Azure にアクセスできるよう、Azure サービス プリンシパルを作成する。
> * お客様のサービス プリンシパルを Jenkins に追加する。
> * GitHub での更新のたびにサンプル アプリをビルドしてデプロイする Jenkins パイプラインを作成する。
> * お客様の Jenkins パイプライン用にビルド ファイルとデプロイ ファイルを作成する。
> * ビルドとデプロイのスクリプトでお客様の Jenkins パイプラインを設定する。
> * 手動ビルドを実行して、お客様のサンプル アプリを Azure にデプロイする。
> * GitHub でアプリの更新プログラムをプッシュする。これにより、Jenkins によるビルドと Azure への再デプロイがトリガーされます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

* Java Development Kit (JDK) と Maven ツールがインストールされた、Azure Linux VM 上の [Jenkins](https://jenkins.io/) サーバー

  Jenkins サーバーがない場合は、まず、Azure portal で [Azure Linux VM での Jenkins サーバーの作成](/azure/jenkins/install-jenkins-solution-template)に関するページの手順を完了してください。

* サンプル Java Web アプリ用の作業コピー (フォーク) を取得するための [GitHub](https://github.com) アカウント。 

* [Azure CLI](/cli/azure/install-azure-cli) (ローカルのコマンド ラインまたは [Azure Cloud Shell](/azure/cloud-shell/overview) で実行できます)

## <a name="install-jenkins-plug-ins"></a>Jenkins プラグインのインストール

1. 次の場所を指定して、お客様の Jenkins Web コンソールにサインインします。

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Jenkins のメイン ページで、 **[Manage Jenkins]\(Jenkins の管理\)**  >  **[Manage Plugins]\(プラグインの管理\)** の順に選択します。

   ![Jenkins プラグインの管理](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. **[Available]\(利用可能\)** タブで、以下のプラグインを選択します。

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Environment Injector プラグイン](https://plugins.jenkins.io/envinject)
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

   これらのプラグインが表示されていない場合は、 **[Installed]\(インストール済み\)** タブを確認して、既にインストール済みでないかを確認してください。

1. お客様が選択したプラグインをインストールするには、 **[Download now and install after restart]\(今すぐダウンロードし、再起動後にインストール\)** を選択します。

1. 完了後、Jenkins メニューで **[Manage Jenkins]\(Jenkins の管理\)** を選択します。これで、後の手順を行う Jenkins 管理ページに戻ります。

## <a name="fork-sample-github-repo"></a>サンプル GitHub リポジトリのフォーク

1. [サンプル Spring Boot アプリ用の GitHub リポジトリにサインインします。](https://github.com/spring-guides/gs-spring-boot) 

1. GitHub の右上隅にある **[Fork]\(フォーク\)** を選択します。

   ![GitHub からのサンプル リポジトリのフォーク](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. プロンプトに従ってお客様の GitHub アカウントを選択し、フォークを完了します。

次に、お客様の GitHub 資格情報を使用して Jenkins を設定します。

## <a name="connect-jenkins-to-github"></a>Jenkins と GitHub の接続

Jenkins によって GitHub が監視され、お客様の GitHub フォークにある Web アプリに新しいコミットがプッシュされたときに対応が行われるようにするには、Jenkins で [GitHub Webhook](https://developer.github.com/webhooks/) を有効にします。

> [!NOTE]
> 
> 以下の手順では、Jenkins が GitHub と連携できるよう、お客様の GitHub ユーザー名とパスワードを使用して個人用アクセス トークン資格情報を作成します。 
> ただし、お客様の GitHub アカウントで 2 要素認証が使用される場合は、GitHub でお客様のトークンを作成して、代わりにそのトークンが使用されるよう Jenkins を設定します。 
> 詳細については、[Jenkins の GitHub プライグイン](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin)に関するドキュメントを参照してください。

1. **[Manage Jenkins]\(Jenkins の管理\)** ページで **[Configure System]\(システムの構成\)** を選択します。 

   ![システムの構成](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. **[GitHub]** セクションで、お客様の GitHub サーバーの詳細を指定します。 **[Add GitHub Server]\(GitHub サーバーの追加\)** リストで、 **[GitHub Server]\(GitHub サーバー\)** を選択します。 

   ![GitHub サーバーの追加](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. **[Manage hooks]\(フックの管理\)** プロパティが選択されていない場合は、このプロパティを選択します。 他の設定を指定できるよう、 **[Advanced]\(詳細\)** を選択します。 

   ![[Advanced]\(詳細\) の選択による追加設定の表示](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. **[Manage additional GitHub actions]\(GitHub の追加アクションの管理\)** リストで、 **[Convert login and password to token]\(ログインとパスワードをトークンに変換する\)** を選択します。

   ![[Manage additional GitHub actions]\(GitHub の追加アクションの管理\) の選択](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. **[From login and password]\(ログインとパスワードから\)** を選択します。これで、お客様の GitHub ユーザー名とパスワードを入力できます。 完了したら、 **[Create token credentials]\(トークン資格情報の作成\)** を選択します。これにより、[GitHub 個人用アクセス トークン (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) が作成されます。   

   ![ログインとパスワードからの GitHub PAT の作成](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. **[GitHub Server]\(GitHub サーバー\)** セクションの **[Credentials]\(資格情報\)** リストで、お客様の新しいトークンを選択します。 **[Test connection]\(テスト接続\)** を選択して、認証が機能していることを確認します。

   ![新しい PAT による GitHub サーバーへの接続の確認](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

次に、Azure リソースに対する認証とアクセスのために Jenkins で使用される Azure サービス プリンシパルを作成します。

## <a name="create-service-principal"></a>サービス プリンシパルの作成

後のセクションでは、GitHub からお客様のアプリをビルドして Azure App Service にデプロイする Jenkins パイプライン ジョブを作成します。 お客様の資格情報を入力しなくても Jenkins が Azure にアクセスできるようにするには、Azure Active Directory の[サービス プリンシパル](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)を Jenkins 用に作成します。 サービス プリンシパルは、Azure リソースに対するアクセスの認証を行うために Jenkins で使用できる別個の ID です。 このサービス プリンシパルを作成するには、ローカルのコマンド ラインまたは Azure Cloud Shell で、Azure CLI コマンド [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) を実行します。例: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

必ず引用符を使用してサービス プリンシパル名を囲んでください。 さらに、[Azure Active Directory のパスワードの規則と制限](/azure/active-directory/active-directory-passwords-policy)に基づいて、強力なパスワードを作成します。 パスワードを指定しない場合は、お客様用のパスワードが Azure CLI によって作成されます。 

**`create-for-rbac`** コマンドによって生成される出力を次に示します。 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> サービス プリンシパルが既にある場合、代わりにその ID を再利用できます。
> 認証のためにサービス プリンシパルの値を指定する際は、`appId`、`password`、`tenant` のプロパティ値を使用します。 
> 既存のサービス プリンシパルを検索する際は、`displayName` プロパティ値を使用します。

## <a name="add-service-principal-to-jenkins"></a>Jenkins へのサービス プリンシパルの追加

1. Jenkins メイン ページで、 **[Credentials]\(資格情報\)**  >  **[System]\(システム\)** を選択します。 

1. **[System]\(システム\)** ページの **[Domain]\(ドメイン\)** で、 **[Global credentials(unrestricted)]\(グローバル資格情報 (制限なし)\)** を選択します。

1. 左側のメニューで、 **[Add Credentials]\(資格情報の追加\)** を選択します。

1. **[Kind]\(種類\)** リストで、 **[Azure Service Principal]\(Azure サービス プリンシパル\)** を選択します。

1. お客様のサービス プリンシパルと Azure サブスクリプションに関する情報を、この手順の表で説明されているプロパティに入力します。

   ![Azure サービス プリンシパルの資格情報の追加](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------| 
   | **サブスクリプション ID** | <*yourAzureSubscription-ID*> | お客様の Azure サブスクリプションの GUID 値 <p>**ヒント**:お客様の Azure サブスクリプション ID がわからない場合は、コマンド ラインまたは Cloud Shell で次の Azure CLI コマンドを実行してから `id` GUID 値を使用します。 <p>`az account list` | 
   | **クライアント ID** | <*yourAzureServicePrincipal-ID*> | お客様の Azure サービス プリンシパル用に先ほど生成された `appId` GUID 値 | 
   | **クライアント シークレット** | <*yourSecurePassword*> | `password` 値、またはお客様が自分の Azure サービス プリンシパルに指定した "シークレット" | 
   | **テナント ID** | <*yourAzureActiveDirectoryTenant-ID*> | お客様の Azure Active Directory テナントの `tenant` GUID 値 | 
   | **ID** | <*yourAzureServicePrincipalName*> | お客様の Azure サービス プリンシパルの `displayName` 値 | 

1. お客様のサービス プリンシパルが機能することを確認するために、 **[Verify Service Principal]\(サービス プリンシパルの検証\)** を選択します。 完了したら、 **[OK]** を選びます。

次に、お客様のアプリをビルドしてデプロイする Jenkins パイプラインを作成します。

## <a name="create-jenkins-pipeline"></a>Jenkins パイプラインを作成する

Jenkins で、お客様のアプリをビルドしてデプロイするためのパイプライン ジョブを作成します。

1. お客様の Jenkins ホーム ページに戻って、 **[New item]\(新しい項目\)** を選択します。 

   ![[New Item (新しい項目)] を選択する](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. お客様のパイプライン ジョブに名前 ("My-Java-Web-App" など) を付けて、 **[Pipeline]\(パイプライン\)** を選択します。 下部にある **[OK]** を選択します。  

   ![[Pipeline]\(パイプライン\) の選択](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. お客様のサービス プリンシパルを使用して Jenkins を設定します。これにより、ご自分の資格情報を使用せずに Jenkins による Azure へのデプロイが可能になります。

   1. **[General]\(全般\)** タブで、 **[Prepare an environment for the run]\(実行用の環境を準備\)** を選択します。 

   1. 表示される **[Properties Content]\(プロパティ コンテンツ\)** ボックスで、以下の環境変数とそれらの値を追加します。 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![[Prepare an environment for the run]\(実行用の環境を準備\) の選択と環境変数の設定](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. 完了したら、 **[保存]** を選択します。

次に、Jenkins 用のビルド スクリプトとデプロイ スクリプトを作成します。

## <a name="create-build-and-deployment-files"></a>ビルド ファイルとデプロイ ファイルの作成

ここでは、お客様のアプリをビルドしてデプロイするために Jenkins で使用されるファイルを作成します。

1. お客様の GitHub フォークの `src/main/resources/` フォルダーに、`web.config` という名前でアプリ構成ファイルを作成します。ここには次の XML が含まれますが、`$(JAR_FILE_NAME)` を `gs-spring-boot-0.1.0.jar` に置き換えてください。

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. お客様の GitHub フォークのルート フォルダーに、`Jenkinsfile` という名前でビルドとデプロイのスクリプトを作成します。ここには、次のテキストが含まれます ([GitHub のソースはこちら](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se))。

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. `web.config` ファイルと `Jenkinsfile` ファイルの両方をお客様の GitHub フォークにコミットして、変更をプッシュします。

## <a name="point-pipeline-at-script"></a>スクリプトでのパイプラインの設定

ここでは、Jenkins で使用したいビルドとデプロイのスクリプトを指定します。

1. Jenkins で、先ほど作成したお客様のパイプライン ジョブを選択します。 

   ![お客様の Web アプリに使用するパイプライン ジョブの選択](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. 左側のメニューで **[Configure]\(構成\)** を選択します。

1. **[Pipeline]\(パイプライン\)** タブにある **[Definition]\(定義\)** リストで、 **[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。

   1. 表示される **[SCM]** ボックスで、お客様のソース管理として **[Git]** を選択します。 

   1. **[Repositories]\(リポジトリ\)** セクションの **[Repository URL]\(リポジトリ URL\)** に、お客様の GitHub フォークの URL を入力します。例: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. **[Credentials]\(資格情報\)** には、先ほど作成したお客様の GitHub 個人用アクセス トークンを選択します。

   1. **[Script Path]\(スクリプト パス\)** ボックスで、お客様の "Jenkinsfile" スクリプトへのパスを追加します。

   完成したら、お客様のパイプライン定義は次の例のようになります。 

   ![スクリプトでのパイプラインの設定](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. 完了したら、 **[保存]** を選択します。

次に、お客様のアプリをビルドして Azure App Service にデプロイします。 

## <a name="build-and-deploy-to-azure"></a>ビルドと Azure へのデプロイ

1. (コマンド ラインまたは Azure Cloud Shell で) Azure CLI を使用して、[Azure App Service on Linux の Web アプリ](/azure/app-service/containers/app-service-linux-intro)を作成します。ここでは、お客様の Web アプリがビルド完了後に Jenkins によってデプロイされます。 お客様の Web アプリには一意の名前を付けてください。

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   これらの Azure CLI コマンドの詳細については、以下のページを参照してください。

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Jenkins で、お客様のパイプライン ジョブを選択し、 **[Build Now]\(いますぐビルド\)** を選択します。

   ビルドの完了後、お客様のアプリは Jenkins によってデプロイされ、発行 URL で Azure に公開されます。例: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Azure にデプロイされたアプリを表示します](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>変更のプッシュと再デプロイ

1. Web ブラウザーで、お客様の Web アプリの GitHub フォーク内にある次の場所に移動します。

   `complete/src/main/java/Hello/Application.java`
   
1. GitHub の右上隅にある **[Edit this file]\(このファイルを編集\)** を選択します。

1. `commandLineRunner()` メソッドに次の変更を行い、リポジトリの `master` ブランチに変更をコミットします。 `master` ブランチでのこのコミットによって、Jenkins でビルドが開始されます。 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. ビルドが完了し、Jenkins によって Azure への再デプロイが行われた後、お客様のアプリを最新の情報に更新すると、お客様の変更内容が反映されます。

   ![Azure にデプロイされたアプリを表示します](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Jenkins プラグインのトラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure VMs をビルド エージェントとして使用する](/azure/jenkins/jenkins-azure-vm-agents)
