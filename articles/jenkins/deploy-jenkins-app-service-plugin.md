---
title: Jenkins プラグインを使用した Azure App Service へのデプロイ
description: Azure App Service Jenkins プラグインを使用して、Jenkins で Java Web アプリを Azure にデプロイする方法について説明します。
ms.service: jenkins
keywords: Jenkins, Azure, 開発, App Service
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 58835b66824d55b64b77e34df64d34c8da1c269a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864815"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Jenkins プラグインを使用した Azure App Service へのデプロイ 

Java Web アプリを Azure にデプロイするには、[Jenkins パイプライン](/azure/jenkins/execute-cli-jenkins-pipeline)で Azure CLI を使用するか、[Azure App Service Jenkins プラグイン](https://plugins.jenkins.io/azure-app-service)を使用します。 Jenkins プラグイン バージョン 1.0 では、次のツールを介して Azure App Service の Web Apps 機能を使用することで継続的なデプロイをサポートします。
* ファイルのアップロード。
* Web Apps on Linux 用 Docker

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * ファイルのアップロードによって Web Apps をデプロイするように Jenkins を構成する。
> * Web App for Containers をデプロイするように Jenkins を構成する

## <a name="create-and-configure-a-jenkins-instance"></a>Jenkins インスタンスを作成して構成する

Jenkins マスターがまだない場合は、[ソリューション テンプレート](install-jenkins-solution-template.md)から始めます。このテンプレートには、Java Development Kit (JDK) バージョン 8 と次の必須の Jenkins プラグインが含まれています。

* [Jenkins Git クライアント プラグイン](https://plugins.jenkins.io/git-client) バージョン 2.4.6 
* [Docker Commons プラグイン](https://plugins.jenkins.io/docker-commons) バージョン 1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) バージョン 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) バージョン 0.1

Jenkins プラグインを使用して、Web Apps でサポートされている言語 (C#、PHP、Java、Node.js など) の Web アプリをデプロイできます。 このチュートリアルでは、[Azure 用の単純な Java Web アプリ](https://github.com/azure-devops/javawebappsample)を使用します。 自分の GitHub アカウントにリポジトリをフォークするには、GitHub インターフェイスの右上隅にある **[Fork]** ボタンをクリックします。  

> [!NOTE]
> Java プロジェクトをビルドするには、Java JDK と Maven が必要です。 これらのコンポーネントを、Jenkins マスターまたは VM エージェント (エージェントを継続的インテグレーションに使用する場合) にインストールします。 Java SE アプリケーションをデプロイする場合は、ビルド サーバーに ZIP も必要です。
>

コンポーネントをインストールするには、SSH を使用して Jenkins インスタンスにサインインし、次のコマンドを実行します。

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Web App for Containers にデプロイする場合は、ビルドに使用する Jenkins マスターまたは VM エージェントに Docker をインストールします。 手順については、[Ubuntu への Docker のインストール](https://docs.docker.com/engine/installation/linux/ubuntu/)に関するページを参照してください。

## <a name="service-principal"></a>Jenkins 資格情報に Azure サービス プリンシパルを追加する

Azure にデプロイするには、Azure サービス プリンシパルが必要です。 


1. Azure サービス プリンシパルを作成するには、[Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)  または [Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal) を使用します。
2. Jenkins ダッシュボードで、**[Credentials]\(資格情報\)** > **[System]\(システム\)** を選択します。 次に、**[Global credentials(unrestricted)]\(グローバル資格情報 (制限なし)\)** を選択します。
3. Microsoft Azure サービス プリンシパルを追加するには、**[Add Credentials]\(資格情報の追加\)** を選択します。 **[Subscription ID]\(サブスクリプション ID\)**、**[Client ID]\(クライアント ID\)**、**[Client Secret]\(クライアント シークレット\)**、**[OAuth 2.0 Token Endpoint]\(OAuth 2.0 トークン エンドポイント\)** の各フィールドに値を入力します。 **[ID]** フィールドを **[mySp]** に設定します。 この ID は、この記事の以降の手順で使用します。


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>ファイルのアップロードによって Web Apps をデプロイするように Jenkins を構成する

ご自身のプロジェクトを Web Apps にデプロイする場合、ファイルのアップロードによってご自身のビルド成果物をアップロードできます。 Azure App Service では、複数のデプロイ オプションがサポートされます。 Azure App Service Jenkins プラグインにより、この処理がシンプルになり、デプロイ オプションは、ファイルの種類に基づいて取得されます。 

* Java EE アプリケーションの場合は、[WAR デプロイ](/azure/app-service/deploy-zip#deploy-war-file)が使用されます。
* Java SE アプリケーションの場合は、[ZIP デプロイ](/azure/app-service/deploy-zip#deploy-zip-file)が使用されます。
* その他の言語については、[Git デプロイ](/azure/app-service/deploy-local-git)が使用されます。

Jenkins でジョブを設定する前に、Java アプリを実行するための Azure App Service プランと Web アプリが必要です。


1. `az appservice plan create` [Azure CLI コマンド](/cli/azure/appservice/plan#az-appservice-plan-create)を使用して、**Free** 価格レベルで Azure App Service プランを作成します。 App Service プランでは、アプリをホストするために使用される物理リソースを定義します。 App Service プランに割り当てられたすべてのアプリケーションがこれらのリソースを共有します。 共有リソースにより、複数のアプリをホストするときにコストを節約できます。
2. Web アプリを作成します。 [Azure Portal](/azure/app-service-web/web-sites-configure) または次の `az` Azure CLI コマンドをご利用いただけます。
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. アプリで必要な Java ランタイム構成を設定します。 次の Azure CLI コマンドは、最新の JDK 8 および [Apache Tomcat](https://tomcat.apache.org/) バージョン 8.0 で動作するように Web アプリを構成します。
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Jenkins ジョブを設定する

1. Jenkins ダッシュボードで新しい**フリースタイル** プロジェクトを作成します。
2. [Azure 用の単純な Java Web アプリ](https://github.com/azure-devops/javawebappsample)のローカル フォークを使用するように **[Source Code Management]\(ソース コード管理\)** フィールドを構成します。 **リポジトリの URL** 値を指定します  (例: http:\//github.com/&lt;your_ID>/javawebappsample)。
3. **[Execute shell]\(シェルの実行\)** コマンドを追加することで、Maven を使用してプロジェクトをビルドするステップを追加します。 この例では、ターゲット フォルダー内の \*.war ファイルの名前を **ROOT.war** に変更する追加のコマンドが必要です。   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. **[Publish an Azure Web App]\(Azure Web App の発行\)** を選択することで、ビルド後のアクションを追加します。
5. Azure サービス プリンシパルとして **mySp** を指定します。 このプリンシパルは、前の手順で [Azure 資格情報](#service-principal)として保存されています。
6. **[App Configuration]\(アプリの構成\)** セクションで、サブスクリプションのリソース グループと Web アプリを選択します。 Jenkins プラグインは、Web アプリが Windows ベースと Linux ベースのどちらであるかを自動的に検出します。 Windows Web アプリの場合、**[Publish Files]\(ファイルの発行\)** オプションが表示されます。
7. デプロイするファイルを入力します。 たとえば、Java を使用している場合は WAR パッケージを指定します。 オプションの **[Source Directory]\(ソース ディレクトリ\)** および **[Target Directory]\(ターゲット ディレクトリ\)** パラメーターを使用して、ファイル アップロードに使用するソース フォルダーとターゲット フォルダーを指定します。 Azure の Java Web アプリは、Tomcat サーバーで実行されます。 そのため、Java の場合、WAR パッケージを webapps フォルダーにアップロードします。 この例では、**[Source Directory]\( ソース ディレクトリ\)** の値を **[target]\(ターゲット\)** に設定し、**[Target Directory]\(ターゲット ディレクトリ\)** の値を **[webapps]** に設定します。
8. 運用環境以外のスロットにデプロイする場合は、**スロット**名を設定することもできます。
9. プロジェクトを保存してビルドします。 ビルドが完了すると、Web アプリが Azure にデプロイされます。

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Jenkins パイプラインを使用したファイルのアップロードによって Web Apps for Containers をデプロイする

Azure App Service Jenkins プラグインは、パイプラインに対応しています。 GitHub リポジトリの次のサンプルを参照できます。

1. GitHub インターフェイスで、**Jenkinsfile_ftp_plugin** ファイルを開きます。 ファイルを編集するには、鉛筆アイコンをクリックします。 11 行目と 12 行目にある、Web アプリの **resourceGroup** と **webAppName** の定義を更新します。
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 14 行目の **withCredentials** の定義を、Jenkins インスタンスの資格情報 ID に設定します。
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins パイプラインを作成する

1. Web ブラウザーで Jenkins を開きます。 **[New Item (新しい項目)]** を選択します。
2. ジョブの名前を指定し、**[パイプライン]** を選択します。 **[OK]** を選択します。
3. **[Pipeline]\(パイプライン\)** タブを選択します。
4. **[Definition]\(定義\)** で、**[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。
5. **[SCM]** で **[Git]** を選択します。 フォークしたリポジトリの GitHub URL を入力します  (例: https://&lt;フォークしたリポジトリ>.git)。
6. **[Script Path]\(スクリプト パス\)** の値を **Jenkinsfile_ftp_plugin** に更新します。
7. **[Save]\(保存\)** をクリックし、ジョブを実行します。

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Web App for Containers をデプロイするように Jenkins を構成する

Web Apps on Linux では、Docker を使用したデプロイをサポートしています。 Docker を使用して Web アプリをデプロイするには、Web アプリとサービス ランタイムを Docker イメージにパッケージ化する Dockerfile を用意する必要があります。 その後、Jenkins プラグインがイメージをビルドして Docker レジストリにプッシュし、イメージを Web アプリにデプロイします。

Web Apps on Linux では、Git やファイルのアップロードなどの従来のデプロイ方法もサポートしていますが、組み込みの言語 (.NET Core、Node.js、PHP、Ruby) のみが対象です。 その他の言語については、アプリケーション コードとサービス ランタイムをまとめて Docker イメージにパッケージ化し、Docker を使用してデプロイする必要があります。

Jenkins でジョブを設定するには、Linux 上の Web アプリが必要です。 また、プライベート Docker コンテナー イメージを保存し、管理するためのコンテナー レジストリも必要です。 コンテナー レジストリは、DockerHub を使用して作成できます。 この例では、Azure Container Registry を使用します。

* [Linux 上の Web アプリを作成](../app-service/containers/quickstart-nodejs.md)します。
* Azure Container Registry は、オープン ソースの Docker Registry バージョン 2.0 に基づく管理された [Docker レジストリ](https://docs.docker.com/registry/)サービスです。 [Azure コンテナー レジストリを作成](/azure/container-registry/container-registry-get-started-azure-cli)します。 DockerHub を使用することもできます。

### <a name="set-up-the-jenkins-job-for-docker"></a>Docker の Jenkins ジョブを設定する

1. Jenkins ダッシュボードで新しい**フリースタイル** プロジェクトを作成します。
2. [Azure 用の単純な Java Web アプリ](https://github.com/azure-devops/javawebappsample)のローカル フォークを使用するように **[Source Code Management]\(ソース コード管理\)** フィールドを構成します。 **リポジトリの URL** 値を指定します  (例: http:\//github.com/&lt;your_ID>/javawebappsample)。
3. **[Execute shell]\(シェルの実行\)** コマンドを追加することで、Maven を使用してプロジェクトをビルドするステップを追加します。 コマンドに次の行を含めます。
    ```bash
    mvn clean package
    ```

4. **[Publish an Azure Web App]\(Azure Web App の発行\)** を選択することで、ビルド後のアクションを追加します。
5. Azure サービス プリンシパルとして **mySp** を指定します。 このプリンシパルは、前の手順で [Azure 資格情報](#service-principal)として保存されています。
6. **[App Configuration]\(アプリの構成\)** セクションで、サブスクリプションのリソース グループと Linux Web アプリを選択します。
7. **[Publish via Docker]\(Docker を介した発行\)** を選択します。
8. **Dockerfile** パスを入力します。 既定値の "/Dockerfile" をそのまま使用できます。
Azure Container Registry を使用している場合は、**[Docker registry URL]\(Docker レジストリの URL\)** に https://&lt;レジストリ名>.azurecr.io の形式で URL を入力します。 DockerHub を使用している場合は、値を空白のままにしておきます。
9. **[Registry credentials]\(レジストリの資格情報\)** に、コンテナー レジストリの資格情報を追加します。 ユーザー ID とパスワードは、Azure CLI で次のコマンドを実行することで取得できます。 最初のコマンドは、Administrator アカウントを有効にします。
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. **[Advanced]\(詳細\)** タブの Docker イメージ名とタグ値は省略可能です。 既定では、イメージ名の値は、Azure Portal の **[Docker コンテナー]** 設定で構成済みのイメージ名から取得されます。 タグは $BUILD_NUMBER から生成されます。
    > [!NOTE]
    > 必ず、Azure Portal でイメージ名を指定するか、**[Advanced]\(詳細\)** タブの **[Docker Image]\(Docker イメージ\)** に値を入力してください。この例では、**[Docker Image]\(Docker イメージ\)** の値を &lt;レジストリ名>.azurecr.io/calculator に設定し、**[Docker Image Tag]\(Docker イメージ タグ\)** は空白のままにしておきます。

11. 組み込みの Docker イメージ設定を使用すると、デプロイは失敗します。 Azure Portal の **[Docker コンテナー]** 設定のカスタム イメージを使用するように Docker 構成を変更します。 組み込みのイメージの場合は、ファイル アップロードの方法を使用してデプロイします。
12. ファイル アップロードの方法と同様に、**運用環境**以外の別の**スロット**名を選択できます。
13. プロジェクトを保存してビルドします。 コンテナー イメージがレジストリにプッシュされ、Web アプリがデプロイされます。

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Jenkins パイプラインを使用して Web App for Containers をデプロイする

1. GitHub インターフェイスで、**Jenkinsfile_container_plugin** ファイルを開きます。 ファイルを編集するには、鉛筆アイコンをクリックします。 11 行目と 12 行目にある、Web アプリの **resourceGroup** と **webAppName** の定義を更新します。
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. 13 行目を、使用するコンテナー レジストリ サーバーに変更します。
    ```java
    def registryServer = '<registryURL>'
    ```

3. 16 行目を変更して、Jenkins インスタンスの資格情報 ID を使用します。
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Jenkins パイプラインを作成する    

1. Web ブラウザーで Jenkins を開きます。 **[New Item (新しい項目)]** を選択します。
2. ジョブの名前を指定し、**[パイプライン]** を選択します。 **[OK]** を選択します。
3. **[Pipeline]\(パイプライン\)** タブを選択します。
4. **[Definition]\(定義\)** で、**[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。
5. **[SCM]** で **[Git]** を選択します。 フォークしたリポジトリの GitHub URL を入力します  (例: https://&lt;フォークしたリポジトリ>.git)。
7. **[Script Path]\(スクリプト パス\)** の値を **Jenkinsfile_container_plugin** に更新します。
8. **[Save]\(保存\)** をクリックし、ジョブを実行します。

## <a name="verify-your-web-app"></a>Web アプリを検証する

1. WAR ファイルが Web アプリに 正常にデプロイされたことを確認するには、Web ブラウザーを開きます。
2. http://&lt;アプリ名>.azurewebsites.net/api/calculator/ping に移動します。 &lt;アプリ名> を実際の Web アプリの名前に置き換えます。 次のメッセージが表示されます。
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. http://&lt;アプリ名>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> に移動します。 &lt;x> と &lt;y> を任意の数値に置き換えて、x と y の合計を取得します。 計算ツールに合計が表示されます。![計算ツール: 加算](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Azure App Service on Linux の場合

1. Web アプリを検証するには、Azure CLI で次のコマンドを実行します。
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    次のメッセージが表示されます。
    ```CLI
    ["calculator"]
    ```
    
2. http://&lt;アプリ名>.azurewebsites.net/api/calculator/ping に移動します。 &lt;アプリ名> を実際の Web アプリの名前に置き換えます。 次のメッセージが表示されます。 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. http://&lt;アプリ名>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> に移動します。 &lt;x> と &lt;y> を任意の数値に置き換えて、x と y の合計を取得します。
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins プラグインのトラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure App Service Jenkins プラグインを使用して Azure にデプロイしました。

以下の方法について学習しました。

> [!div class="checklist"]
> * ファイルのアップロードを介して Azure App Service をデプロイするように Jenkins を構成する 
> * Web App for Containers にデプロイするように Jenkins を構成する 
