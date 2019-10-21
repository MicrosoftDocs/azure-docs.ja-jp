---
title: カスタム イメージを使用して Linux 上で Azure Functions を作成する
description: カスタム Linux イメージで実行する Azure Functions を作成する方法について説明します。
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: d4a72edbe762afd2a94962c1440357ce3ad46862
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329559"
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
> * コンテナーへの SSH 接続を有効にします。
> * Queue storage の出力バインドを追加します。 
> * Application Insights の監視を追加します。

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。 

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下が必要です。

* [Azure Core Tools バージョン 2.x](functions-run-local.md#v2) をインストールします。

* [Azure CLI]( /cli/azure/install-azure-cli) をインストールします。 この記事では、Azure CLI バージョン 2.0.46 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。  
[Azure Cloud Shell](https://shell.azure.com/bash) を使用することもできます。

* 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>ローカル プロジェクトを作成する

コマンド ラインから次のコマンドを実行し、現在のローカル ディレクトリの `MyFunctionProj` フォルダー内に関数アプリ プロジェクトを作成します。 Python プロジェクトの場合は、[仮想環境で実行する必要があります](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional)。

```bash
func init MyFunctionProj --docker
```

`--docker` オプションを含めると、プロジェクト用に Dockerfile が生成されます。 このファイルは、プロジェクトの実行先となるカスタム コンテナーを作成するために使用されます。 使用される基本イメージは、選択されたワーカー ランタイム言語によって異なります。  

メッセージが表示されたら、次の言語からワーカー ランタイムを選択します。

* `dotnet`: .NET Core クラス ライブラリ プロジェクト (.csproj) を作成します。
* `node`: JavaScript プロジェクトを作成します。
* `python`: Python プロジェクトを作成します。  

次のコマンドを使用して、新しい `MyFunctionProj` プロジェクト フォルダーに移動します。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Docker ファイルからビルドする

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

コマンドが完了すると、新しいコンテナーをローカルで実行できます。

### <a name="run-the-image-locally"></a>イメージをローカルで実行する
ローカルのコンテナーで Docker イメージを実行して、ビルドしたイメージの動作を確認します。 [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを発行し、イメージの名前とタグを渡します。 必ず `-p` 引数を使用してポートを指定してください。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

ローカルの Docker コンテナーでカスタム イメージを実行させながら、<http://localhost:8080> に移動して Function App とコンテナーが正しく機能していることを確認します。

![関数アプリをローカルで実行します。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> この時点で、特定の HTTP 関数を呼び出そうとすると、HTTP 401 エラー応答が返されます。 関数が実行されているのはローカル コンテナー内ですが、Azure 内で実行される想定になっているためです。つまり、Azure 内で実行するのであれば関数キーが必要になります。 コンテナーはまだ関数アプリに公開されていないため、使用できる関数キーは存在しません。 後で Core Tools を使用してコンテナーを公開すると、関数キーが見えるようになります。 ローカル コンテナーで実行されている関数をテストしたい場合は、[承認キー](functions-bindings-http-webhook.md#authorization-keys)を `anonymous` に変更してください。 

コンテナー内に Function App を確認した後で、実行を停止します。 これで、カスタム イメージを Docker Hub アカウントにプッシュできます。

## <a name="push-to-docker-hub"></a>Docker Hub にプッシュする

レジストリは、イメージをホストし、サービス イメージとコンテナー サービスを提供するアプリケーションです。 イメージを共有するには、レジストリにプッシュする必要があります。 Docker Hub は Docker イメージのレジストリであり、パブリックまたはプライベートの独自のリポジトリをホストすることができます。

イメージをプッシュする前に、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して Docker Hub にサインインする必要があります。 `<docker-id>` をアカウント名に置き換え、プロンプトで、コンソールにパスワードを入力します。 その他の Docker Hub パスワード オプションについては、[docker login コマンドのドキュメント](https://docs.docker.com/engine/reference/commandline/login/)を参照してください。

```bash
docker login --username <docker-id>
```

"ログインに成功しました" のメッセージで、ログインしていることを確認します。 サインインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュします。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

プッシュが成功すると、Azure の新しい関数アプリのデプロイ ソースとして、このイメージを使用できます。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Premium プランを作成する

カスタム Functions コンテナーの Linux ホスティングは、[専用 (App Service) プラン](functions-scale.md#app-service-plan)および [Premium プラン](functions-premium-plan.md#features)でサポートされています。 このチュートリアルでは、必要に応じて拡張できる Premium プランを使用します。 ホスティングについて詳しくは、「[Azure Functions のホスティング プランの比較](functions-scale.md)」をご覧ください。

次の例では、**Elastic Premium 1** 価格レベル (`--sku EP1`) の `myPremiumPlan` という名前の Premium プランを米国西部リージョン (`-location WestUS`) で Linux コンテナー (`--is-linux`) に作成します。

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>アプリをイメージから作成する

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

## <a name="verify-your-functions"></a>関数を確認する

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

作成した HTTP によってトリガーされる関数では、エンドポイントを呼び出すときに[関数キー](functions-bindings-http-webhook.md#authorization-keys)が必要です。 現時点では、キーを含む関数 URL を取得する最も簡単な方法は、[Azure portal] を使用する方法です。 

> [!TIP]
> また、[キー管理 API シリーズ](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)を使用して関数キーを取得することもできます。これには、[認証用のベアラー トークン](/cli/azure/account#az-account-get-access-token)を提示する必要があります。

[Azure portal] で対象の新しい関数アプリを見つけます。そのためには、ページの上部にある**検索**ボックスに対象の関数アプリ名を入力し、 **[App Service]** リソースを選択します。

**[MyHttpTrigger]** 関数を選択し、 **[</> 関数の URL の取得]**  >  **[default (Function key)]\(既定 (関数キー)\)**  >  **[コピー]** の順に選択します。

![Azure Portal からの関数 URL のコピー](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

この URL では、関数キーは `code` クエリ パラメーターです。 

> [!NOTE]  
> 関数アプリはコンテナーとしてデプロイされるため、ポータルでその関数コードを変更することはできません。 代わりに、ローカル コンテナー内でプロジェクトを更新し、Azure に再発行する必要があります。

関数 URL をブラウザーのアドレス バーに貼り付けます。 この URL の末尾にクエリ文字列 `&name=<yourname>` を追加し、キーボードで`Enter` キーを押して要求を実行します。 ブラウザーに表示される関数によって返される応答が表示されます。

次の例は、ブラウザーでの応答を示しています。

![ブラウザーでの関数の応答。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

要求 URL には、既定では HTTP 経由で関数にアクセスするために必要なキーが含まれています。 

## <a name="enable-continuous-deployment"></a>継続的配置を有効にする

コンテナーを使用する利点の 1 つは、継続的デプロイのサポートです。 関数を使用すると、レジストリでコンテナーが更新されたときに更新を自動的にデプロイできます。 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) コマンドを使用して、継続的配置を有効にします。

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

継続的配置を有効にすると、このコマンドから配置 Webhook URL が返されます。 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) コマンドを使用してこの URL を返すこともできます。 

配置 URL をコピーし、DockerHub リポジトリを参照し、 **[Webhook]** タブを選択し、Webhook の **[webhook 名]** を入力し、 **[Webhook URL]** に URL を貼り付けます。次にプラス記号 ( **+** ) を選択します。

![DockerHub リポジトリに Webhook を追加する](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Webhook を設定して、DockerHub のリンクされたイメージを更新すると、関数アプリで最新のイメージがダウンロードされ、インストールされます。

## <a name="enable-ssh-connections"></a>SSH 接続を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 SSH が有効になっている場合は、App Service Advanced Tools (Kudu) を使用してコンテナーに接続できます。 SSH を使用してコンテナーに簡単に接続できるように、Functions では、SSH が既に有効になっている基本イメージが提供されています。 

### <a name="change-the-base-image"></a>基本イメージを変更する

dockerfile で、`FROM` 命令の基本イメージに文字列 `-appservice` を追加します。JavaScript プロジェクトの場合、次のようになります。

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

これらの 2 つの基本イメージの違いにより、コンテナーへの SSH 接続が可能になります。 これらの違いについては、[この App Services のチュートリアル](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)で詳しく説明しています。

### <a name="rebuild-and-redeploy-the-image"></a>イメージをリビルドして再デプロイする

ルート フォルダーで、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを再度実行します。このとき、以前と同様に `<docker-id>` を自分の Docker Hub アカウント ID に置き換えてください。 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

更新されたイメージを Docker Hub にプッシュして戻します。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

更新されたイメージが関数アプリに再デプロイされます。

### <a name="connect-to-your-container-in-azure"></a>Azure で対象のコンテナーに接続する

ブラウザーで、対象の関数アプリ コンテナーの次の Advanced Tools (Kudu) `scm.` エンドポイントに移動します。このとき、`<app_name>` を対象の関数アプリの名前に置き換えてください。

```
https://<app_name>.scm.azurewebsites.net/
```

自分の Azure アカウントにサインインし、 **[SSH]** タブを選択して、対象のコンテナーへの SSH 接続を作成します。

接続が確立されたら、`top` コマンドを実行して、現在実行中のプロセスを表示します。 

![SSH セッションで実行されている Linux top コマンド。](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Queue storage に書き込む

Functions を使用すると、独自の統合コードを記述しなくても、Azure サービスやその他のリソースを関数に接続できます。 これらの*バインド*は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 "*トリガー*" は、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

このセクションでは、関数と Azure Storage キューを統合する方法について説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

### <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>拡張バンドルを有効にする

Queue storage の出力バインドを使用しているため、このプロジェクトを実行する前に Storage のバインド拡張機能をインストールしておく必要があります。 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript または Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

HTTP トリガーとタイマー トリガーを除き、バインドは拡張機能パッケージとして実装されます。 ターミナル ウィンドウで次の [dotnet add package](/dotnet/core/tools/dotnet-add-package) コマンドを実行して、Storage 拡張機能パッケージをプロジェクトに追加します。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Visual Studio を使用する場合は、NuGet パッケージ マネージャーを使用してこのパッケージを追加することもできます。

---

これで、プロジェクトに Storage 出力バインディングを追加できるようになります。

### <a name="add-an-output-binding"></a>出力バインディングを追加する

Functions では、各種のバインドで、`direction`、`type`、および固有の `name` が function.json ファイル内で定義される必要があります。 これらの属性を定義する方法は、関数アプリの言語によって異なります。

# <a name="javascript--pythontabnodejspython"></a>[JavaScript または Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

バインドが定義されたら、そのバインドの `name` を使用して、関数シグネチャの属性としてアクセスできます。 出力バインドを使用すると、認証、キュー参照の取得、またはデータの書き込みに、Azure Storage SDK のコードを使用する必要がなくなります。 Functions ランタイムおよびキューの出力バインドが、ユーザーに代わってこれらのタスクを処理します。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>ホストされているコンテナーを更新する

ルート フォルダーで [docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを再度実行します。今回は、タグ内のバージョンを `v1.0.2` に更新します。 以前と同様に、`<docker-id>` を Docker Hub アカウント ID に置き換えてください。 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

更新されたイメージをリポジトリにプッシュして戻します。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Azure で更新を確認する

ブラウザーで以前と同じ URL を使用して、対象の関数をトリガーします。 同じ応答が表示されます。 ただし、今回は、`name` パラメーターとして渡された文字列が `outqueue` ストレージ キューに書き込まれます。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

カスタム コンテナーを Azure の関数アプリに正常にデプロイした後は、次のトピックの詳細を確認することを検討してください。

+ [関数の監視](functions-monitoring.md)
+ [スケールとホスティングのオプション](functions-scale.md)
+ [Kubernetes ベースのサーバーレス ホスティング](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
