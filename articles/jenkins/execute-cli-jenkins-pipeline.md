---
title: Jenkins を使用して Azure CLI を実行する
description: Azure CLI を使用して Java Web アプリを Jenkins パイプラインで Azure にデプロイする方法について説明します
ms.service: jenkins
keywords: Jenkins, Azure, 開発, App Service, CLI
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: 5728a9ab70c5b7db10a123d6964b498e70f96588
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162206"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Jenkins と Azure CLI を使用して Azure App Service にデプロイする
Java Web アプリを Azure にデプロイするには、[Jenkins パイプライン](https://jenkins.io/doc/book/pipeline/)で Azure CLI を使用します。 このチュートリアルでは、Azure VM で CI/CD パイプラインを作成します｡この作成は､以下のような手順で構成されます｡

> [!div class="checklist"]
> * Jenkins VM を作成する
> * Jenkins を構成する
> * Azure で Web アプリを作成する
> * GitHub レポジトリを準備する
> * Jenkins パイプラインを作成する
> * パイプラインを実行し、Web アプリを確認する

このチュートリアルには、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Jenkins インスタンスの作成と構成
Jenkins マスターを持っていない場合は、[ソリューション テンプレート](install-jenkins-solution-template.md)から始めます。ここには、必要な [Azure Credentials](https://plugins.jenkins.io/azure-credentials) プラグインが既定で含まれています。 

Azure 資格情報のプラグインでは、Jenkins に Microsoft Azure サービス プリンシパルの資格情報を格納できます。 バージョン 1.2 では、Jenkins パイプラインで Azure 資格情報を取得できるようにサポートを強化しました。 

バージョン 1.2 以降を使用していることを確認します。
* Jenkins ダッシュボード内で、 **[Manage Jenkins]\(Jenkins の管理\) -> [プラグイン マネージャー] ->** の順にクリックして **[Azure 資格情報]** を検索します。 
* バージョンが 1.2 以前である場合は、プラグインを更新します。

Java JDK と Maven も Jenkins マスターで必要です。 インストールするには、SSH を使用して Jenkins マスターにサインインし、次のコマンドを実行します。
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Jenkins 資格情報への Azure サービス プリンシパルの追加

Azure CLI を実行するには、Azure の資格情報が必要です。

* Jenkins ダッシュボードで、 **[資格情報] -> [システム] ->** の順にクリックします。 **[Global credentials(unrestricted)]\(グローバル資格情報 (制限なし)\)** をクリックします。
* **[資格情報の追加]** をクリックして、サブスクリプション ID、クライアント ID、クライアント シークレット、OAuth 2.0 トークン エンドポイントなどの値を入力し、[Microsoft Azure サービス プリンシパル](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)を追加します。 後の手順で使用する ID を指定します。

![資格情報の追加](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Java Web アプリをデプロイするための Azure App Service の作成

[az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) CLI コマンドを使用して、**Free** 価格レベルで Azure App Service プランを作成します。 App Service プランは、アプリをホストするために使用される物理リソースを定義します。 App Service プランに割り当てられたすべてのアプリケーションは、これらのリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

プランの準備が完了すると、Azure CLI で次の例のような出力が表示されます。

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web アプリを作成する

 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) CLI コマンドを使用して、`myAppServicePlan` App Service プランで Web アプリ定義を作成します。 Web アプリ定義によって、アプリケーションにアクセスするための URL が提供され、Azure にコードをデプロイするためのいくつかのオプションが構成されます。 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

`<app_name>` プレースホルダーを独自の一意のアプリ名で置き換えます。 この一意の名前は、Web アプリの既定のドメイン名の一部です。そのため、この名前は Azure のすべてのアプリで一意である必要があります。 Web アプリをユーザーに公開する前に、カスタム ドメイン名エントリを Web アプリにマップできます。

Web アプリ定義の準備が完了すると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Java を構成する 

[az appservice web config update](/cli/azure/webapp/config) コマンドを使用して、アプリで必要な Java ランタイム構成を設定します。

次のコマンドでは、最新の Java 8 JDK および [Apache Tomcat](https://tomcat.apache.org/) 8.0 で動作するように Web アプリを構成します。

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>GitHub レポジトリを準備する
[Azure 用のシンプルな Java Web アプリ](https://github.com/azure-devops/javawebappsample) レポジトリを開きます。 自身の GitHub アカウントにリポジトリをフォークするには､右上隅の **[Fork]** ボタンをクリックします｡

* GitHub Web UI で、**Jenkinsfile** ファイルを開きます。 鉛筆アイコンをクリックしてこのファイルを編集します。20 行目と 21 行目にある、リソース グループと Web アプリの名前をそれぞれ更新します。

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* 23 行目を変更して Jenkins インスタンスの資格情報 ID を更新します。

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Jenkins パイプラインを作成する
Web ブラウザーで Jenkins を開き、 **[新しい項目]** をクリックします。 

* ジョブの名前を指定し、 **[パイプライン]** を選択します。 Click **OK**.
* **[パイプライン]** タブをクリックします。 
* **[定義]** で、 **[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。
* **[SCM]** で、 **[Git]** を選択します。
* フォークしたレポジトリの GitHub URL を入力します。https:\<分岐したレポジトリ\>.git
* **[保存]**

## <a name="test-your-pipeline"></a>パイプラインをテストする
* 作成したパイプラインに移動して、 **[Build Now]\(ビルド実行\)** をクリックします
* ビルドは数秒後に成功し、ビルドに移動して **[コンソール出力]** をクリックし、詳細を確認します

## <a name="verify-your-web-app"></a>Web アプリを検証する
Web アプリに WAR ファイルが正常にデプロイされたことを確認するには、以下のことを行います。 Web ブラウザーを開いて以下を行います。

* http://&lt;app_name>.azurewebsites.net/api/calculator/ping に移動します  
次のものがあります。

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (&lt;x> と &lt;y> は任意の数字に置き換える) に移動して、x と y の合計を取得します

![Calculator: add](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Azure Web App on Linux へのデプロイ
これで Jenkins パイプライン内での Azure CLI の使い方がわかったため、スクリプトを変更して Azure Web App on Linux にデプロイすることができます。

Azure Web App on Linux はデプロイを行う別の方法をサポートしていて、この方法では Docker を使用します。 デプロイを行うには、Web アプリとサービス ランタイムを Docker イメージにパッケージ化する Dockerfile を用意する必要があります。 プラグインはイメージを作成して、Docker レジストリに転送し、Web アプリにイメージをデプロイします。

* Linux 上で実行される Azure Web App を作成するには、[こちら](../app-service/containers/quickstart-nodejs.md)の手順に従ってください。
* こちらの[記事](https://docs.docker.com/engine/installation/linux/ubuntu/)の手順に従って、Docker を Jenkins インスタンスにインストールします。
* [こちら](/azure/container-registry/container-registry-get-started-azure-cli)の手順に従って、Azure ポータルにコンテナー レジストリを作成します。
* 追加した同じ [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) リポジトリで、**Jenkinsfile2** ファイルを編集します。
    * 18～21 行を、自分のリソース グループ、Web アプリ、ACR の名前に更新します。 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * 24 行で、\<azsrvprincipal\> を自分の資格情報 ID に更新します。
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Azure Web App を Windows にデプロイしたときと同様に新規の Jenkins パイプラインを作成します。ただし、この場合は代わりに **Jenkinsfile2** を使用します。
* 新しいジョブを実行します。
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
    
## <a name="next-steps"></a>次の手順
このチュートリアルでは、GitHub レポジトリのソース コードをチェックアウトする Jenkins パイプラインを構成しました。 Maven を実行して war ファイルを構築し、Azure CLI を使用して Azure App Service をデプロイしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Jenkins VM を作成する
> * Jenkins を構成する
> * Azure で Web アプリを作成する
> * GitHub レポジトリを準備する
> * Jenkins パイプラインを作成する
> * パイプラインを実行し、Web アプリを確認する
