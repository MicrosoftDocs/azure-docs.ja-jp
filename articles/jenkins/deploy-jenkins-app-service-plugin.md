---
title: "Jenkins プラグインを使用して Azure App Service にデプロイする | Microsoft Docs"
description: "Azure App Service Jenkins プラグインを使用して Jenkins で Java Web アプリを Azure にデプロイする方法について説明します"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: dfb1abd4eefdb2baea8cdbd497bc8fcc95d200e6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="deploy-to-azure-app-service-with-jenkins-plugin"></a>Jenkins プラグインを使用して Azure App Service にデプロイする 
Java Web アプリを Azure にデプロイするには、[Jenkins パイプライン](/azure/jenkins/execute-cli-jenkins-pipeline)で Azure CLI を使用するか、[Azure App Service Jenkins プラグイン](https://plugins.jenkins.io/azure-app-service)を使用できます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * FTP を介して Azure App Service をデプロイするように Jenkins を構成する 
> * Docker を介して Azure App Service on Linux にデプロイするように Jenkins を構成する 

## <a name="create-and-configure-jenkins-instance"></a>Jenkins インスタンスの作成と構成
Jenkins マスターを持っていない場合は、[ソリューション テンプレート](install-jenkins-solution-template.md)から始めます。このテンプレートには、JDK8 と次の必須プラグインが含まれています。

* [Jenkins Git クライアント プラグイン](https://plugins.jenkins.io/git-client) v.2.4.6 
* [Docker Commons Plugin](https://plugins.jenkins.io/docker-commons) v.1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) v.1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) v.0.1

App Service プラグインを使用して、Azure App Service でサポートされているすべての言語 (C#、PHP、Java、node.js など) の Web アプリをデプロイできます。 このチュートリアルでは、サンプル Java アプリである [Azure 用の単純な Java Web アプリ](https://github.com/azure-devops/javawebappsample)を使用します。 自身の GitHub アカウントにレポジトリをフォークするには､右上隅の [**Fork**] ボタンをクリックします｡  

Java プロジェクトをビルドするには、Java JDK と Maven が必要です。 これらのコンポーネントを、Jenkins マスター (または継続的インテグレーションで使用する VM エージェント ) に必ずインストールしてください。 

インストールするには、SSH を使用して Jenkins インスタンスにログインし、次のコマンドを実行します。

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

App Service on Linux にデプロイする場合は、ビルドに使用する Jenkins マスターまたは VM エージェントに Docker もインストールする必要があります。 Docker のインストールについては、こちらの記事 (https://docs.docker.com/engine/installation/linux/ubuntu/) を参照してください。

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Jenkins 資格情報への Azure サービス プリンシパルの追加

Azure にデプロイするには、Azure サービス プリンシパルが必要です。 

<ol>
<li>[Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) または [Azure ポータル](/azure/azure-resource-manager/resource-group-create-service-principal-portal)を使用して、Azure サービス プリンシパルを作成します。</li>
<li>Jenkins ダッシュボードで、**[資格情報] -> [システム] ->** の順にクリックします。 **[Global credentials(unrestricted)]\(グローバル資格情報 (制限なし)\)** をクリックします。</li>
<li>**[Add Credentials]\(資格情報の追加\)** をクリックし、サブスクリプション ID、クライアント ID、クライアント シークレット、および OAuth 2.0 トークン エンドポイントを入力して Microsoft Azure サービス プリンシパルを追加します。 後の手順で使用する ID (**mySp**) を指定します。</li>
</ol>

## <a name="azure-app-service-plugin"></a>Azure App Service プラグイン

Azure App Service プラグイン v1.0 では、以下を介した Azure Web アプリへの継続的配置をサポートします。

* Git および FTP
* Web App on Linux 用の Docker

## <a name="configure-jenkins-to-deploy-web-app-through-ftp-using-the-jenkins-dashboard"></a>Jenkins ダッシュボードで FTP を介して Web アプリをデプロイするように Jenkins を構成する

プロジェクトを Azure Web アプリにデプロイするには、ビルド成果物 (Java の .war ファイルなど) を Git または FTP を使用してアップロードできます。

Jenkins でジョブを設定する前に、Java アプリを実行するための Azure App Service プランと Web アプリが必要です。


1. [az appservice plan create](/cli/azure/appservice/plan#create) CLI コマンドを使用して、**Free** 価格レベルで Azure App Service プランを作成します。 App Service プランは、アプリをホストするために使用される物理リソースを定義します。 App Service プランに割り当てられたすべてのアプリケーションは、これらのリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。
2. Web アプリを作成します。 [Azure ポータル](/azure/app-service-web/web-sites-configure)を使用するか、次の Az CLI コマンドを使用できます。
```azurecli-interactive 
az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
```

3. アプリで必要な Java ランタイム構成を必ず設定します。 次の Azure CLI コマンドは、最新の Java 8 JDK および [Apache Tomcat](http://tomcat.apache.org/) 8.0 で動作するように Web アプリを構成します。
```azurecli-interactive
az webapp config set \
--name <myAppName> \
--resource-group <myResourceGroup> \
--java-version 1.8 \
--java-container Tomcat \
--java-container-version 8.0
```

### <a name="set-up-the-jenkins-job"></a>Jenkins ジョブを設定する


1. Jenkins ダッシュボードで、新しい**freestyle** プロジェクトを作成します。
2. **リポジトリの URL** を指定して、[Azure 用の単純な Java Web アプリ](https://github.com/azure-devops/javawebappsample)のローカル フォークを使用するように **[Source Code Management]\(ソース コードの管理\)** を構成します。 例: http://github.com/&lt;自分の ID>/javawebappsample。
3. Maven を使用してプロジェクトをビルドするビルド手順を追加します。 **Execute shell** (シェルの実行) を追加することでこれを行います。 この例では、ターゲット フォルダー内の *.war ファイルの名前を ROOT.war に変更する追加手順が必要です。   
```bash
mvn clean package
mv target/*.war target/ROOT.war
```

4. **[Publish an Azure Web App]\(Azure Web App の発行\)** を選択することで、ビルド後のアクションを追加します。
5. 前の手順で保存した、Azure サービス プリンシパルの "mySp" を指定します。
6. **[App Configuration]\(アプリの構成\)** セクションで、サブスクリプションのリソース グループと Web アプリを選択します。 プラグインは、Web アプリが Windows ベースであるか Linux ベースであるかを自動的に検出します。 Windows ベースの Web アプリの場合は、[Publish Files]\(ファイルの発行\) オプションが表示されます。
7. デプロイするファイルを入力します (たとえば Java を使用している場合は war パッケージになります)。ソース ディレクトリとターゲット ディレクトリは省略可能です。 パラメーターを使用して、ファイルをアップロードするときのソース フォルダーとターゲット フォルダーを指定できます。 Azure の Java Web アプリは、Tomcat サーバーで実行されます。 このため、war パッケージは webapps フォルダーにアップロードします。 この例では、**[Source Directory]\( ソース ディレクトリ\)** を [target]\(ターゲット\) に設定し、**[Target Directory]\(ターゲット ディレクトリ\)** に [webapps] を指定します。
8. 運用環境以外のスロットにデプロイする場合は、**[Slot Name]\(スロット名\)**を設定することもできます。
9. プロジェクトを保存してビルドします。 Web アプリは、ビルドの完了時に Azure にデプロイされます。

### <a name="deploy-web-app-through-ftp-using-jenkins-pipeline"></a>Jenkins パイプラインで FTP を介して Web アプリをデプロイする

プラグインは、パイプラインに対応しています。 GitHub リポジトリのサンプルを参照できます。

1. GitHub Web UI で、**Jenkinsfile_ftp_plugin** ファイルを開きます。 このファイルを編集するために鉛筆アイコンをクリックし、11 行目と 12 行目にあるリソース グループと Web アプリの名前をそれぞれ更新します。    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 14 行目を変更して Jenkins インスタンスの資格情報 ID を更新します。    
```java
withCredentials([azureServicePrincipal('<mySp>')]) {
```

### <a name="create-a-jenkins-pipeline"></a>Jenkins パイプラインを作成する

1. Web ブラウザーで Jenkins を開き、**[新しい項目]** をクリックします。
2. ジョブの名前を指定し、**[パイプライン]** を選択します。 **[OK]**をクリックします。
3. **[パイプライン]** タブをクリックします。
4. **[定義]** で、**[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。
5. **[SCM]** で、**[Git]** を選択します。 フォークされるリポジトリの GitHub URL を入力します (https:&lt;フォークされるリポジトリ>.git)。
6. **[Script Path]\(スクリプト パス\)** を [Jenkinsfile_ftp_plugin] に変更します。
7. **[保存]** をクリックしてジョブを実行します。

## <a name="configure-jenkins-to-deploy-web-app-on-linux-through-docker"></a>Docker を介して Web App on Linux にデプロイするように Jenkins を構成する

Git/FTP とは別に、Web App on Linux は、Docker を使用したデプロイをサポートします。 Docker を使用してデプロイするには、Web アプリとサービス ランタイムを Docker イメージにパッケージ化する Dockerfile を用意する必要があります。 その後、プラグインがイメージをビルドして Docker レジストリにプッシュし、Web アプリにイメージをデプロイします。

Web App on Linux は、Git や FTP のような従来の方法もサポートしますが、組み込みの言語 (.NET Core、Node.js、PHP、および Ruby) のみをサポートします。 その他の言語については、アプリケーション コードとサービス ランタイムをまとめて Docker イメージにパッケージ化し、Docker を使用してデプロイする必要があります。

Jenkins でジョブ設定する前に、Azure App Service on Linux が必要です。 プライベート Docker コンテナー イメージを保存して管理するためのコンテナー レジストリも必要です。 DockerHub を使用できます。この例では Azure Container Registry を使用します。

* [こちら](/azure/app-service-web/app-service-linux-how-to-create-web-app)の手順に従って、Linux で実行される Web アプリを作成できます。 
* Azure Container Registry は、オープン ソースの Docker Registry 2.0 に基づく、管理された [Docker レジストリ] (https://docs.docker.com/registry/) サービスです。 これを行う方法の詳しいガイダンスについては、[こちら] (/azure/container-registry/container-registry-get-started-azure-cli) の手順に従ってください。 DockerHub を使用することもできます。

### <a name="to-deploy-using-docker"></a>Docker を使用してデプロイするには:

1. Jenkins ダッシュボードで、新しい freestyle プロジェクトを作成します。
2. **リポジトリの URL** を指定して、[Azure 用の単純な Java Web アプリ](https://github.com/azure-devops/javawebappsample)のローカル フォークを使用するように **[Source Code Management]\(ソース コードの管理\)** を構成します。 例: http://github.com/&lt;自分の ID>/javawebappsample。
Maven を使用してプロジェクトをビルドするビルド手順を追加します。 **Execute shell** (シェルの実行) を追加し、次の行を **Command** に追加することで、これを行います。    
```bash
mvn clean package
```

3. **[Publish an Azure Web App]\(Azure Web App の発行\)** を選択することで、ビルド後のアクションを追加します。
4. 前の手順で保存した、Azure サービス プリンシパルの **mySp** を Azure 資格情報として指定します。
5. **[App Configuration]\(アプリの構成\)** セクションで、サブスクリプションのリソース グループと Linux Web アプリを選択します。
6. [Publish via Docker]\(Docker を介した発行\) を選択します。
7. **Dockerfile** パスを入力します。 既定値の "/Dockerfile" を保持できます。Azure Container Registry を使用している場合は、**[Docker registry URL]\(Docker レジストリの URL\)** に https://&lt;myRegistry>.azurecr.io の形式で URL を入力します。 DockerHub を使用している場合は空白のままにします。
8. **[Registry credentials]\(レジストリの資格情報\)** に、Azure Container Registry の資格情報を追加します。 ユーザー ID とパスワードは、Azure CLI で次のコマンドを実行することで取得できます。 最初のコマンドは、Administrator アカウントを有効にします。    
```azurecli-interactive
az acr update -n <yourRegistry> --admin-enabled true
az acr credential show -n <yourRegistry>
```

9. **[Advanced]\(詳細\)** タブの Docker イメージの名前とタグは省略可能です。 既定では、イメージの名前は、Azure ポータル (の Docker コンテナーの設定) で構成したイメージの名前から取得します。タグは $BUILD_NUMBER から生成されます。 イメージの名前が Azure ポータルに指定しているか、**[Advanced]\(詳細\)** タブの **[Docker Image]\(Docker イメージ\)** に値が入力されていることを確認してください。 この例では、**[Docker image]\(Docker イメージ\)** に [&lt;自分のレジストリ>.azurecr.io/calculator] を指定し、**[Docker Image Tag]\(Docker イメージ タグ\)** は空白のままにします。
10. 組み込みの Docker イメージの設定を使用すると、デプロイは失敗することに注意してください。 Azure ポータルの Docker コンテナーの設定に指定されたカスタム イメージを使用するように Docker 構成を必ず変更してください。 組み込みのイメージの場合は、ファイルをアップロードする方法を使用してデプロイします。
11. ファイルをアップロードする方法と同じように、運用環境以外の別のスロットを選択できます。
12. プロジェクトを保存してビルドします。 コンテナー イメージがレジストリへプッシュされ、Web アプリがデプロイされます。

### <a name="deploy-to-web-app-on-linux-through-docker-using-jenkins-pipeline"></a>Jenkins パイプラインで Docker を介して Web App on Linux にデプロイする

1. GitHub Web UI で、**Jenkinsfile_container_plugin** ファイルを開きます。 このファイルを編集するために鉛筆アイコンをクリックし、11 行目と 12 行目にあるリソース グループと Web アプリの名前をそれぞれ更新します。    
```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<myAppName>'
```

2. 13 行目を、使用するコンテナー レジストリ サーバーに変更します。    
```java
def registryServer = '<registryURL>'
```    

3. 16 行目を変更して Jenkins インスタンスの資格情報 ID を更新します。    
```java
azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
```    
### <a name="create-jenkins-pipeline"></a>Jenkins パイプラインを作成する    

1. Web ブラウザーで Jenkins を開き、**[新しい項目]** をクリックします。
2. ジョブの名前を指定し、**[パイプライン]** を選択します。 **[OK]**をクリックします。
3. **[パイプライン]** タブをクリックします。
4. **[定義]** で、**[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。
5. **[SCM]** で、**[Git]** を選択します。
6. フォークされるリポジトリの GitHub URL を入力します (https:&lt;フォークされるリポジトリ>.git)。</li>
**[Script Path]\(スクリプト パス\)** を [Jenkinsfile_container_plugin] に変更します。
8. **[保存]** をクリックしてジョブを実行します。

## <a name="verify-your-web-app"></a>Web アプリを検証する

1. Web アプリに WAR ファイルが正常にデプロイされたことを確認するには、以下のことを行います。 Web ブラウザーを開きます。
2. http://&lt;app_name>.azurewebsites.net/api/calculator/ping に移動します。以下が表示されます。    
     Welcome to Java Web App!!! This is updated!
   Sun Jun 17 16:39:10 UTC 2017
3. http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (&lt;x> と &lt;y> は任意の数字に置き換える) に移動して、x と y の合計を取得します        
    ![Calculator: add](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-app-service-on-linux"></a>App Service on Linux の場合

* 検証するために Azure CLI で、次のコマンドを実行します。

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    次の結果が得られます。
    
    ```
    [
    "calculator"
    ]
    ```
    
    http://&lt;app_name>.azurewebsites.net/api/calculator/ping に移動します。 次のメッセージが表示されます。 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (&lt;x> と &lt;y> は任意の数字に置き換える) に移動して、x と y の合計を取得します
    
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure App Service プラグインを使用して Azure にデプロイしています。

以下の方法について学習しました。

> [!div class="checklist"]
> * FTP を介して Azure App Service をデプロイするように Jenkins を構成する 
> * Docker を介して Azure App Service on Linux にデプロイするように Jenkins を構成する 
