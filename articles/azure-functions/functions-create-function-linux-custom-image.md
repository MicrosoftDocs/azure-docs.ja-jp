---
title: カスタム イメージを使用して Linux 上で Azure Functions を作成する
description: カスタム Linux イメージで実行する Azure Functions を作成する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 525cb444ad7b1b78de867f83539ac338ddd144e9
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562918"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>カスタム イメージを使用して Linux で関数を作成する

Azure Functions を使用して、独自のカスタム コンテナー内の Linux 上で関数をホストできます。 [既定の Azure App Service コンテナー上でホストする](functions-create-first-azure-function-azure-cli-linux.md)こともできます。 この機能には [Functions 2.x ランタイム](functions-versions.md)が必要です。

このチュートリアルでは、関数をカスタム Docker イメージとして Azure にデプロイする方法について説明します。 このパターンは、組み込みのコンテナー イメージをカスタマイズする必要がある場合に便利です。 特定の言語バージョン、特定の依存関係、または組み込みイメージで提供されない構成が関数に必要になるときに、カスタム イメージを使用することがあります。 Azure Functions でサポートされている基本イメージについては、[Azure Functions 基本イメージ リポジトリ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。 

このチュートリアルでは、Azure Functions Core Tools を使用して、カスタム Linux イメージに関数を作成する方法について説明します。 このイメージを、Azure CLI を使用して作成された Azure の関数アプリに発行します。 後で、Azure Queue storage に接続するように関数を更新します。 有効にすることもできます。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Core Tools を使用して関数アプリと Dockerfile を作成します。
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。
> * Azure ストレージ アカウントを作成します。
> * Premium ホスティング プランを作成します。
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。
> * 継続的デプロイを有効にします。
> * Application Insights の監視を追加します。

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。 

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下が必要です。

* [Azure Core Tools バージョン 2.x](functions-run-local.md#v2) をインストールします。

* [Azure CLI]( /cli/azure/install-azure-cli) をインストールします。 この記事では、Azure CLI バージョン 2.0.46 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。  
[Azure Cloud Shell](https://shell.azure.com/bash) を使用することもできます。

* 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>ローカルの関数アプリ プロジェクトを作成する

コマンド ラインから次のコマンドを実行し、現在のローカル ディレクトリの `MyFunctionProj` フォルダー内に関数アプリ プロジェクトを作成します。

```bash
func init MyFunctionProj --docker
```

`--docker` オプションを含めると、プロジェクト用に Dockerfile が生成されます。 このファイルは、プロジェクトの実行先となるカスタム コンテナーを作成するために使用されます。 使用される基本イメージは、選択されたワーカー ランタイム言語によって異なります。  

メッセージが表示されたら、次の言語からワーカー ランタイムを選択します。

* `dotnet`: .NET Core クラス ライブラリ プロジェクト (.csproj) を作成します。
* `node`: JavaScript プロジェクトを作成します。
* `python`: Python プロジェクトを作成します。

コマンドを実行すると、次のような出力が表示されます。

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

次のコマンドを使用して、新しい `MyFunctionProj` プロジェクト フォルダーに移動します。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Docker ファイルからイメージを作成する

プロジェクトのルート フォルダーで _Dockerfile_ を確認します。 このファイルには、Linux 上で Function App を実行するために必要な環境が記述されています。 次の例は、JavaScript (Node.js) ワーカー ランタイムで関数アプリを実行するコンテナーを作成する Dockerfile です。 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Azure Functions でサポートされている基本イメージの詳細な一覧については、[Azure Functions 基本イメージ ページ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。

### <a name="run-the-build-command"></a>`build` コマンドを実行します
ルート フォルダーで、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを実行し、名前に `mydockerimage`、タグに `v1.0.0` を指定します。 `<docker-id>` を Docker Hub アカウント ID で置換します。 このコマンドでは、コンテナーの Docker イメージがビルドされます。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

コマンドを実行すると、次のような出力が表示されます。この場合は、JavaScript ワーカー ランタイムに関する出力です。

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>ローカルでのイメージのテスト
ローカルのコンテナーで Docker イメージを実行して、ビルドしたイメージの動作を確認します。 [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを発行し、イメージの名前とタグを渡します。 必ず `-p` 引数を使用してポートを指定してください。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

ローカルの Docker コンテナーでカスタム イメージを実行させながら、<http://localhost:8080> に移動して Function App とコンテナーが正しく機能していることを確認します。

![Function App をローカルでテストします。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

必要に応じて、もう一度関数をテストできます。今回は、次の URL を使用してローカルのコンテナーでテストします。

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

コンテナー内に Function App を確認した後で、実行を停止します。 これで、カスタム イメージを Docker Hub アカウントにプッシュできます。

## <a name="push-the-custom-image-to-docker-hub"></a>Docker Hub へのカスタム イメージのプッシュ

レジストリは、イメージをホストし、サービス イメージとコンテナー サービスを提供するアプリケーションです。 イメージを共有するには、レジストリにプッシュする必要があります。 Docker Hub は Docker イメージのレジストリであり、パブリックまたはプライベートの独自のリポジトリをホストすることができます。

イメージをプッシュする前に、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して Docker Hub にサインインする必要があります。 `<docker-id>` をアカウント名に置き換え、プロンプトで、コンソールにパスワードを入力します。 その他の Docker Hub パスワード オプションについては、[docker login コマンドのドキュメント](https://docs.docker.com/engine/reference/commandline/login/)を参照してください。

```bash
docker login --username <docker-id>
```

「ログインに成功しました」のメッセージで、ログインしていることを確認します。 サインインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュします。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

コマンドの出力を調べて、プッシュが成功したことを確認します。

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

これで、Azure の新しい Function App のデプロイ ソースとして、このイメージを使用できます。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Premium プランを作成する

カスタム Functions コンテナーの Linux ホスティングは、[専用 (App Service) プラン](functions-scale.md#app-service-plan)および [Premium プラン](functions-premium-plan.md#features)でサポートされています。 このチュートリアルでは、必要に応じて拡張できる Premium プランを使用します。 ホスティングについて詳しくは、「[Azure Functions のホスティング プランの比較](functions-scale.md)」をご覧ください。

次の例では、**Elastic Premium 1** 価格レベル (`--sku EP1`) の `myPremiumPlan` という名前の Premium プランを米国西部リージョン (`-location WestUS`) で Linux コンテナー (`--is-linux`) に作成します。

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-and-deploy-the-custom-image"></a>カスタム イメージの作成とデプロイ

関数アプリは、ホスティング プランでの関数の実行を管理します。 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用して Docker Hub イメージから Function App を作成します。

次のコマンドでは、`<app_name>` プレースホルダーを一意の Function App 名で、`<storage_name>` をストレージ アカウント名で置き換えます。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 以前と同様に、`<docker-id>` は Docker アカウント名です。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

_deployment-container-image-name_ パラメーターは、Function App を作成するために使用する、Docker Hub でホストされているイメージを示します。 デプロイに使用されているイメージに関する情報を表示するには、[az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) コマンドを使用します。 別のイメージからデプロイするには、[az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) コマンドを使用します。

## <a name="configure-the-function-app"></a>Function App を構成する

関数が既定のストレージ アカウントに接続するには接続文字列が必要です。 カスタム イメージをプライベート コンテナー アカウントに発行するときは、[ENV 命令](https://docs.docker.com/engine/reference/builder/#env)などを使用して、代わりにこれらのアプリケーション設定を Dockerfile の環境変数として設定する必要があります。

ここでは、`<storage_name>` は作成したストレージ アカウントの名前です。 [az storage account show-connection-string](/cli/azure/storage/account) コマンドで接続文字列を取得します。 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドで、これらのアプリケーション設定を Function App に追加します。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> コンテナーがプライベートである場合、次のアプリケーション設定も設定する必要があります。  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> これらの値を適用するためには、関数アプリを停止してから起動する必要があります。

これで、Linux 上で実行される関数を Azure でテストできます。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>継続的配置を有効にする

コンテナーを使用する利点の 1 つは、コンテナーがレジストリで更新されたときに自動的に更新プログラムを配置できることです。 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) コマンドを使用して、継続的配置を有効にします。

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

継続的配置を有効にすると、このコマンドから配置 Webhook URL が返されます。 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) コマンドを使用してこの URL を返すこともできます。 

配置 URL をコピーし、DockerHub リポジトリを参照し、 **[Webhook]** タブを選択し、Webhook の **[webhook 名]** を入力し、 **[Webhook URL]** に URL を貼り付けます。次にプラス記号 ( **+** ) を選択します。

![DockerHub リポジトリに Webhook を追加する](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Webhook を設定して、DockerHub のリンクされたイメージを更新すると、関数アプリで最新のイメージがダウンロードされ、インストールされます。

## <a name="enable-application-insights"></a>Application Insights を有効にする

関数の実行を監視するための推奨される方法は、関数アプリを Azure Application Insights と統合することです。 Azure Portal で関数アプリを作成する場合、この統合は、既定で自動的に行われます。 ただし、Azure CLI を使用して関数アプリを作成する場合は、Azure で関数アプリの統合は実行されません。

関数アプリ用に Application Insights を有効にするには:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

詳細については、「[Azure Functions を監視する](functions-monitoring.md)」を参照してください。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Core Tools を使用して関数アプリと Dockerfile を作成します。
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。
> * Azure ストレージ アカウントを作成します。
> * Linux Premium プランを作成します。
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。
> * 継続的デプロイを有効にします。
> * Application Insights の監視を追加します。

> [!div class="nextstepaction"] 
> [Azure に関数をデプロイするためのオプションについて詳しく学習します](functions-deployment-technologies.md)
