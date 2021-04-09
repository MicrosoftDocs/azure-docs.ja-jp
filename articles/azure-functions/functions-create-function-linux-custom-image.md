---
title: カスタム イメージを使用して Linux 上で Azure Functions を作成する
description: カスタム Linux イメージで実行する Azure Functions を作成する方法について説明します。
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 1c7a9fd83131ea6282d2ef4860b744fa348153ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070919"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>カスタム コンテナーを使用して Linux で関数を作成する

このチュートリアルでは、コードを作成し、Linux の基本イメージを使用したカスタム Docker コンテナーとして Azure Functions にデプロイします。 カスタム イメージを使用するのは通常、特定の言語バージョン、特定の依存関係、または組み込みイメージで提供されない構成が関数に必要になるときです。

::: zone pivot="programming-language-other"
Azure Functions では、[カスタム ハンドラー](functions-custom-handlers.md)を使用するすべての言語またはランタイムがサポートされています。 このチュートリアルで使用する R プログラミング言語など、一部の言語では、カスタム コンテナーの使用を必要とする依存関係として、ランタイムまたは追加のライブラリをインストールする必要があります。
::: zone-end

関数コードをカスタム Linux コンテナーにデプロイするには、[Premium プラン](functions-premium-plan.md)または[専用 (App Service) プラン](dedicated-plan.md)のホスティングが必要です。 このチュートリアルを完了すると、お使いの Azure アカウントで数ドルのコストが発生します。これは、完了時に[リソースをクリーンアップする](#clean-up-resources)ことによって最小限に抑えることができます。

[Linux でホストされる初めての関数の作成](./create-first-function-cli-csharp.md?pivots=programming-language-python)に関するページで説明されている既定の Azure App Service コンテナーを使用することもできます。 Azure Functions でサポートされている基本イメージについては、[Azure Functions 基本イメージ リポジトリ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。

このチュートリアルでは、以下の内容を学習します。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Azure Functions Core Tools を使用して関数アプリと Dockerfile を作成します。
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。
> * 関数アプリ用の関連リソースを Azure に作成します。
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。
> * 継続的デプロイを有効にします。
> * コンテナーへの SSH 接続を有効にします。
> * Queue storage の出力バインドを追加します。 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Azure Functions Core Tools を使用して関数アプリと Dockerfile を作成します。
> * Docker を使用してカスタム イメージをビルドします。
> * カスタム イメージをコンテナー レジストリに発行します。
> * 関数アプリ用の関連リソースを Azure に作成します。
> * Docker Hub から Function App をデプロイします。
> * Function App にアプリケーション設定を追加します。
> * 継続的デプロイを有効にします。
> * コンテナーへの SSH 接続を有効にします。
::: zone-end

このチュートリアルは、Windows、macOS、または Linux が動作している任意のコンピューターで実行できます。 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ `docker login` を実行して Docker にサインインします。 Docker が動作していない場合、このコマンドは失敗します。その場合は、Docker を起動してコマンドを再試行してください。

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>ローカル関数プロジェクトを作成してテストする

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
ターミナルまたはコマンド プロンプトで、自分が選択した言語に合わせて次のコマンドを実行し、`LocalFunctionsProject` という名前のフォルダーに関数アプリ プロジェクトを作成します。  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
空のフォルダーで次のコマンドを実行して、[Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)から Functions プロジェクトを生成します。

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion` パラメーターは、使用する Java のバージョンを Functions Runtime に指示します。 Java 11 で関数を実行する場合は、`-DjavaVersion=11` を使用します。 `-DjavaVersion` を指定しない場合、Maven の既定値は Java 8 になります。 詳細については、「[Java のバージョン](functions-reference-java.md#java-versions)」を参照してください。

> [!IMPORTANT]
> この記事の作業を行うには、`JAVA_HOME` 環境変数を、適切なバージョンの JDK のインストール場所に設定する必要があります。

Maven により、デプロイ時にプロジェクトの生成を終了するための値の入力が求められます。   
入力を求められたら、次の値を入力します。

| Prompt | 値 | 説明 |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Java の[パッケージ命名規則](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)に従って、すべてのプロジェクトにわたって対象のプロジェクトを一意に識別する値。 |
| **artifactId** | `fabrikam-functions` | バージョン番号のない、jar の名前である値。 |
| **version** | `1.0-SNAPSHOT` | 既定値を選択します。 |
| **package** | `com.fabrikam.functions` | 生成された関数コードの Java パッケージである値。 既定値を使用します。 |

「`Y`」と入力するか、Enter キーを押して確認します。

Maven により、_artifactId_ という名前の新しいフォルダーにプロジェクト ファイルが作成されます (この例では `fabrikam-functions`)。 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

`--docker` オプションによって、プロジェクトの `Dockerfile` が生成されます。これにより、Azure Functions および選択されたランタイムで使用するための適切なカスタム コンテナーが定義されます。

プロジェクト フォルダーに移動します。
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` によって、関数と同じ名前のサブフォルダーが作成されます。ここには、プロジェクト用に選択した言語に適したコード ファイルと、*function.json* という名前の構成ファイルが含まれます。

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
次のコマンドを使用して、関数を自分のプロジェクトに追加します。ここで、`--name` 引数は関数の一意の名前で、`--template` 引数は関数のトリガーを指定するものです。 `func new` によって、関数と同じ名前のサブフォルダーが作成されます。このサブフォルダーには、*function.json* という名前の構成ファイルが含まれます。

```console
func new --name HttpExample --template "HTTP trigger"
```

テキスト エディターで、プロジェクト フォルダー内に *handler.R* という名前のファイルを作成します。 その内容として、以下を追加します。

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

*host.json* の `customHandler` セクションを、カスタム ハンドラーのスタートアップ コマンドを構成するように変更します。

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

関数をローカルでテストするために、プロジェクト フォルダーのルートでローカルの Azure Functions ランタイム ホストを起動します。 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

出力に `HttpExample` エンドポイントが表示されたら、`http://localhost:7071/api/HttpExample?name=Functions` に移動します。 `name` クエリ パラメーターに指定された値、`Functions` をエコーバックする "hello" メッセージがブラウザーに表示されます。

**Ctrl** - **C** キーを使用してホストを停止します。

## <a name="build-the-container-image-and-test-locally"></a>コンテナー イメージを作成してローカルでテストする

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(省略可) プロジェクト フォルダーのルートにある *Dockerfile* を確認します。 Dockerfile には、Linux 上で関数アプリを実行するために必要な環境が記述されています。  Azure Functions でサポートされている基本イメージの詳細な一覧については、[Azure Functions 基本イメージ ページ](https://hub.docker.com/_/microsoft-azure-functions-base)を参照してください。
::: zone-end

::: zone pivot="programming-language-other"
プロジェクト フォルダーのルートにある *Dockerfile* を確認します。 Dockerfile には、Linux 上で関数アプリを実行するために必要な環境が記述されています。 カスタム ハンドラー アプリケーションでは、そのベースとして `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` イメージが使用されます。

*Dockerfile* を、R をインストールするように変更します。*Dockerfile* の内容を、次のように置き換えます。

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

ルート プロジェクト フォルダーで、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドを実行し、名前に `azurefunctionsimage`、タグに `v1.0.0` を指定します。 `<DOCKER_ID>` を Docker Hub アカウント ID で置換します。 このコマンドでは、コンテナーの Docker イメージがビルドされます。

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

コマンドが完了すると、新しいコンテナーをローカルで実行できます。
    
ビルドをテストするために、ローカル コンテナーで [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してイメージを実行します。この場合も、`<DOCKER_ID` は実際の Docker ID に置き換え、ポート引数 `-p 8080:80` を追加してください。

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
ローカル コンテナーでイメージが実行状態になったら、ブラウザーで `http://localhost:8080` を開きます。以下に示したプレースホルダー画像が表示されます。 この時点で画像が表示されるということは、Azure で実行されるときと同じように、ローカル コンテナーで関数が実行されているということです。つまり、*function.json* に `"authLevel": "function"` プロパティで定義されたアクセス キーによって関数は保護されています。 ただし、Azure の関数アプリに対してまだコンテナーが発行されていないため、そのキーはまだ利用できません。 ローカル コンテナーに対してテストしたい場合は、Docker を停止し、承認プロパティを `"authLevel": "anonymous"` に変更して、イメージをリビルドしてから Docker を再起動してください。 その後、*function.json* で `"authLevel": "function"` をリセットします。 詳細については、[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)に関するセクションを参照してください。

![コンテナーがローカルで実行されていることを示すプレースホルダー画像](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
ローカル コンテナーでイメージが実行状態になったら、`http://localhost:8080/api/HttpExample?name=Functions` に移動します。そこに、前と同じ "hello" メッセージが表示されます。 Maven アーキタイプでは、匿名の承認を使用する、HTTP によってトリガーされる関数が生成されるため、関数はコンテナーで実行されている場合でも呼び出すことができます。 
::: zone-end  

コンテナー内の関数アプリを確認したら、**Ctrl** + **C** キーで Docker を停止します。

## <a name="push-the-image-to-docker-hub"></a>イメージを Docker Hub にプッシュする

Docker Hub は、イメージのホストとしてイメージ サービスとコンテナー サービスを提供するコンテナー レジストリです。 イメージを共有するには (Azure へのデプロイも含む)、それをレジストリにプッシュする必要があります。

1. まだ Docker にサインインしていない場合は、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドでサインインします。`<docker_id>` は、実際の Docker ID に置き換えてください。 このコマンドでは、ユーザー名とパスワードを入力するよう求められます。 "ログインに成功しました" のメッセージで、サインインしていることを確認します。

    ```console
    docker login
    ```
    
1. サインインしたら、[docker push](https://docs.docker.com/engine/reference/commandline/push/) コマンドを使用して Docker Hub にイメージをプッシュします。ここでも、`<docker_id>` は実際の Docker ID に置き換えてください。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. ネットワーク速度によっては、イメージの初回プッシュに数分かかる場合があります (それ以降に行う変更のプッシュは、はるかに短い時間で済みます)。 待っている間、次のセクションに進んで、別のターミナルで Azure リソースを作成することができます。

## <a name="create-supporting-azure-resources-for-your-function"></a>関数用の関連 Azure リソースを作成する

関数コードを Azure にデプロイするには、3 つのリソースを作成する必要があります。

- リソース グループ。関連リソースの論理コンテナーです。
- Azure ストレージ アカウント。プロジェクトについての状態とその他の情報を保持します。
- 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

Azure CLI コマンドを使用して、これらの項目を作成しましょう。 それぞれのコマンドからは、完了時に JSON 出力が返されます。

1. [az login](/cli/azure/reference-index#az-login) コマンドを使用して、Azure にサインインします。

    ```azurecli
    az login
    ```
    
1. [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、`AzureFunctionsContainers-rg` という名前のリソース グループを `westeurope` リージョンに作成します。 (リソース グループとリソースは通常、近くのリージョンに作成します。`az account list-locations` コマンドから返される利用可能なリージョンを使用してください。)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux と Windows のアプリを同じリソース グループ内でホストすることはできません。 Windows の関数アプリまたは Web アプリで `AzureFunctionsContainers-rg` という名前のリソース グループが存在する場合、別のリソース グループを使用する必要があります。
    
1. [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使用して、リソース グループとリージョン内に汎用ストレージ アカウントを作成します。 次の例の `<storage_name>` は適宜、グローバルに一意の名前に置き換えてください。 名前は 3 文字から 24 文字とし、小文字のみを使用する必要があります。 `Standard_LRS` では、標準的な汎用アカウントを指定します。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    このチュートリアルでは、ストレージ アカウントに関して数セントの料金が発生します。
    
1. コマンドを使用して、Azure Functions 用の Premium プランを `myPremiumPlan` という名前で作成します。価格レベルは **Elastic Premium 1** (`--sku EP1`)、リージョンは西ヨーロッパ (`-location westeurope`) または最寄りのリージョン、作成先は Linux コンテナー (`--is-linux`) とします。

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    ここでは、必要に応じてスケーリングできる Premium プランを使用します。 ホスティングについて詳しくは、「[Azure Functions のホスティング プランの比較](functions-scale.md)」をご覧ください。 コストを計算するには、[Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

    また、このコマンドを実行すると、関連する Azure Application Insights インスタンスが同じリソース グループにプロビジョニングされます。このインスタンスを使用することで、関数アプリを監視したりログを確認したりすることができます。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。 このインスタンスは、アクティブにするまでコストが発生しません。

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>イメージを使用して Azure 上の関数アプリを作成、構成する

Azure 上の関数アプリでは、ホスティング プランで関数の実行を管理します。 このセクションでは、前のセクションの Azure リソースを使用して、Docker Hub 上のイメージから関数アプリを作成し、Azure Storage への接続文字列を使用してそれを構成します。

1. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用して関数アプリを作成します。 次の例の `<storage_name>` は、前のセクションで使用したストレージ アカウントの名前に置き換えてください。 また、`<app_name>` は適宜グローバルに一意の名前に、`<docker_id>` は実際の Docker ID に置き換えます。

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    *deployment-container-image-name* パラメーターでは、関数アプリに使用するイメージを指定します。 デプロイに使用されているイメージに関する情報は、[az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) コマンドを使用して表示できます。 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) コマンドを使用して、別のイメージからデプロイすることもできます。

1. 作成したストレージ アカウントの接続文字列を [az storage account show-connection-string](/cli/azure/storage/account) コマンドで表示します。 `<storage-name>` を、上で作成したストレージ アカウントの名前に置き換えます。

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドを使用して、この設定を関数アプリに追加します。 次のコマンドで、`<app_name>` を関数アプリの名前に置き換え、`<connection_string>` を前の手順の接続文字列 ("DefaultEndpointProtocol=" で始まる長いエンコード文字列) に置き換えます。
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > Bash では、クリップボードではなく、シェル変数を使用して接続文字列をキャプチャできます。 まず、次のコマンドを使用して、接続文字列の変数を作成します。
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > 次に、2 つ目のコマンドで変数を参照します。
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. 関数でこの接続文字列を使用してストレージ アカウントにアクセスできるようになりました。

> [!NOTE]    
> カスタム イメージをプライベート コンテナー アカウントに発行する場合は、接続文字列に Dockerfile 内の環境変数を使用する必要があります。 詳細については、[ENV の手順](https://docs.docker.com/engine/reference/builder/#env)を参照してください。 また、変数 `DOCKER_REGISTRY_SERVER_USERNAME` および `DOCKER_REGISTRY_SERVER_PASSWORD` も設定する必要があります。 これらの値を使用するには、イメージをリビルドしてそのイメージをレジストリにプッシュした後、Azure で関数アプリを再起動する必要があります。

## <a name="verify-your-functions-on-azure"></a>Azure 上で関数を確認する

Azure 上の関数アプリにイメージをデプロイしたら、HTTP 要求を使用して関数を呼び出すことができます。 *function.json* 定義に `"authLevel": "function"` プロパティが存在するので、まずアクセス キー ("関数キー" とも呼ばれます) を取得して、それを URL パラメーターとしてエンドポイントへの要求に含める必要があります。

1. Azure portal を使用するか、または Azure CLI と `az rest` コマンドを使用して、アクセス (関数) キーを含む関数の URL を取得します。

    # <a name="portal"></a>[ポータル](#tab/portal)

    1. Azure portal にサインインし、"**関数アプリ**" を検索して選択します。

    1. 検証する関数を選択します。

    1. 左側のナビゲーション パネルで **[関数]** を選択し、検証する関数を選択します。

        ![Azure portal で関数を選択する](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. **[関数の URL の取得]** を選択します。

        ![Azure portal から関数 URL を取得する](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. ポップアップ ウィンドウで、 **[default (function key)]\(既定 (関数キー)\)** を選択し、URL をクリップボードにコピーします。 `?code=` に続く文字列がキーです

        ![既定の関数アクセス キーを選択する](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > 関数アプリはコンテナーとしてデプロイされるため、ポータルでその関数コードを変更することはできません。 ローカル イメージでプロジェクトを更新して、再度イメージをレジストリにプッシュした後、Azure に再デプロイする必要があります。 後続のセクションで継続的デプロイを設定することができます。
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. 次の形式で URL 文字列を構成します。`<subscription_id>`、`<resource_group>`、`<app_name>` はそれぞれ、実際の Azure サブスクリプション ID、関数アプリのリソース グループ、関数アプリの名前に置き換えてください。

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        たとえば、URL は次のようなアドレスになります。

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > 便宜上、URL を環境変数に代入して `az rest` コマンドで使用することもできます。
    
    1. 次の `az rest` コマンド (Azure CLI バージョン 2.0.77 以降で利用可能) を実行します。`<uri>` は、直前の手順の URI 文字列 (引用符を含む) に置き換えてください。

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. コマンドの出力は関数キーです。 関数の完全な URL は `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` です。`<app_name>`、`<function_name>`、`<key>` は、お客様固有の値に置き換えてください。
    
        > [!NOTE]
        > ここで取得したキーは、関数アプリに含まれるすべての関数で使用できる "*ホスト*" キーです。一方、ポータル用に示した方法で取得されるのは、1 つの関数についてのキーだけです。

    ---

1. 関数 URL をブラウザーのアドレス バーに貼り付けます。この URL の末尾にパラメーター `&name=Azure` を追加してください。 "Hello, Azure" のようなテキストがブラウザーに表示されます。

    ![ブラウザーでの関数の応答。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. 承認をテストするために、URL から `code=` パラメーターを取り除き、関数から応答がないことを確認します。


## <a name="enable-continuous-deployment-to-azure"></a>Azure への継続的デプロイを有効にする

レジストリ内のイメージを更新するたびに、イメージのデプロイを Azure Functions に自動的に更新させることができます。

1. [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) コマンドを使用して継続的デプロイを有効にします。`<app_name>` は、実際の関数アプリの名前に置き換えてください。

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    このコマンドは、継続的デプロイを有効にして、デプロイの Webhook URL を返します。 (この URL は、後から [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) コマンドを使用していつでも取得できます。)

1. デプロイの Webhook URL をクリップボードにコピーします。

1. [[Docker Hub]](https://hub.docker.com/) を開いてサインインし、ナビゲーション バーの **[Repositories]\(リポジトリ\)** を選択します。 イメージを検索して選択し、 **[Webhooks]** タブを選択します。次に、**Webhook の名前** を指定して、 **[Webhook URL]** に URL を貼り付け、 **[Create]\(作成\)** を選択します。

    ![DockerHub リポジトリに Webhook を追加する](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Webhook の設定後は、Docker Hub でイメージを更新するたびに、それが Azure Functions によって再デプロイされます。

## <a name="enable-ssh-connections"></a>SSH 接続を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 SSH が有効になっている場合は、App Service Advanced Tools (Kudu) を使用してコンテナーに接続できます。 SSH を使用してコンテナーに簡単に接続できるように、Azure Functions には、SSH が既に有効になっている基本イメージが用意されています。 必要な作業は、Dockerfile を編集してから、イメージをリビルドして再デプロイするだけです。 その後、[高度なツール (Kudu)] を使用してコンテナーに接続することができます。

1. Dockerfile で、`FROM` 命令の基本イメージに文字列 `-appservice` を追加します。

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. もう一度 `docker build` コマンドを使用してイメージをリビルドします。`<docker_id>` は、実際の Docker ID に置き換えてください。

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 更新済みのイメージを Docker Hub にプッシュします。アップロードする必要があるのは、イメージの更新済みセグメントのみであるため、初回プッシュ時よりもはるかに短時間で済みます。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions によってイメージが自動的に関数アプリに再デプロイされます。この処理の所要時間は 1 分未満です。

1. ブラウザーで `https://<app_name>.scm.azurewebsites.net/` を開きます。`<app_name>` は、一意の名前に置き換えてください。 この URL は、関数アプリ コンテナーの [高度なツール (Kudu)] エンドポイントです。

1. 自分の Azure アカウントにサインインし、 **[SSH]** を選択して、コンテナーとの接続を確立します。 Azure によるコンテナー イメージの更新がまだ行われている場合、接続に少し時間がかかる場合があります。

1. コンテナーとの接続が確立されたら、`top` コマンドを実行して、現在実行中のプロセスを表示します。 

    ![SSH セッションで実行されている Linux top コマンド](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Azure Storage キューに書き込む

Azure Functions を使用すると、独自の統合コードを記述することなく他の Azure サービスやリソースに関数を接続できます。 これらの *バインド* は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 "*トリガー*" は、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

このセクションでは、関数と Azure Storage キューを統合する方法について説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>出力バインディングを使用するコードを追加する

キュー バインディングが定義されたら、`msg` 出力パラメーターを受け取ってメッセージをキューに書き込むように関数を更新することができます。
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>レジストリ内のイメージを更新する

1. ルート フォルダーで `docker build` コマンドを再度実行します。今回は、タグ内のバージョンを `v1.0.1` に更新します。 以前と同様に、`<docker_id>` を Docker Hub アカウント ID に置き換えてください。

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. `docker push` を使用して、更新済みのイメージをリポジトリにプッシュして戻します。

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 継続的デリバリーを構成したため、レジストリにあるイメージを再度更新すると、Azure にある関数アプリが自動的に更新されます。

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage キューのメッセージを確認する

ブラウザーで、これまでと同じ URL を使用して自分の関数を呼び出します。 以前と同じ応答がブラウザーに表示されるはずです。その部分については、関数コードに変更を加えていないためです。 ただし追加したコードでは、URL パラメーター `name` を使用して、`outqueue` ストレージ キューにメッセージを書き込みました。

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースを使用して、引き続き Azure 関数に取り組む場合は、それらのリソースをすべてそのままにしてかまいません。 Azure Functions 用の Premium プランを作成したため、継続するためのコストとして、1 日につき 1 ドルまたは 2 ドルの料金がかかります。

継続コストを避けるためには、`AzureFunctionsContainer-rg` リソース グループを削除して、そのグループのリソースをすべてクリーンアップしてください。 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>次のステップ

+ [関数の監視](functions-monitoring.md)
+ [スケールとホスティングのオプション](functions-scale.md)
+ [Kubernetes ベースのサーバーレス ホスティング](functions-kubernetes-keda.md)
