---
title: Maven を使用して Azure Functions に Kotlin 関数を作成する
description: Kotlin と Maven を使用して、HTTP によってトリガーされる関数アプリを作成し、Azure Functions に発行します。
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 721fbaf1a3b2418677d07e8ac39fa733164c1459
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179563"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>クイック スタート:Kotlin と Maven を使用して初めての関数を作成する

この記事では、Maven コマンド ライン ツールを使用して Kotlin 関数プロジェクトを作成し、Azure Functions に発行する方法を説明します。 完了すると、関数コードは Azure の[従量課金プラン](consumption-plan.md)で実行され、HTTP 要求を使用してトリガーできるようになります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Kotlin を使用して関数を開発するには、以下のものがインストールされている必要があります。

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8
- [Apache Maven](https://maven.apache.org)、バージョン 3.0 以降
- [Azure CLI](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.6.666 以降

> [!IMPORTANT]
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

## <a name="generate-a-new-azure-functions-project"></a>新しい Azure Functions のプロジェクトを生成する

空のフォルダーで次のコマンドを実行して、[Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)から Azure Functions プロジェクトを生成します。

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> コマンドの実行で問題が発生した場合は、使用されている `maven-archetype-plugin` のバージョンを確認します。 `.pom` ファイルがない空のディレクトリでコマンドを実行しているため、Maven を古いバージョンからアップグレードした場合は、`~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` から古いバージョンのプラグインを使用しようとしている可能性があります。 その場合は、`maven-archetype-plugin` ディレクトリを削除した後、コマンドを再実行します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven により、プロジェクトの生成を終了するために必要な値が要求されます。 _groupId_、_artifactId_、_version_ の値については、[Maven の名前付け規則](https://maven.apache.org/guides/mini/guide-naming-conventions.html)をご覧ください。 _appName_ の値は Azure 全体で一意である必要があるため、Maven は既定値として前に入力された _artifactId_ を基にしてアプリ名を生成します。 _packageName_ の値により、生成された関数コードの Kotlin パッケージが決まります。

以下の `com.fabrikam.functions` および `fabrikam-functions` 識別子は例として使用されており、このクイック スタートの後の方の手順を読みやすくしています。 この手順では、Maven に独自の値を指定することをお勧めします。

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven は、この例 `fabrikam-functions` の場合、_artifactId_ という名前の新しいフォルダーにプロジェクト ファイルを作成します。 プロジェクトで生成される、すぐに実行できるコードは、[HTTP によってトリガーされる](./functions-bindings-http-webhook.md)、要求の本文をエコーする簡単な関数です。

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>関数をローカルで実行する

新しく作成されたプロジェクト フォルダーにディレクトリを変更し、Maven で関数をビルドして実行します。

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Java 9 でこの例外、`javax.xml.bind.JAXBException` が発生している場合は、[GitHub](https://github.com/jOOQ/jOOQ/issues/6477) で回避策を参照してください。

関数がシステム上でローカルに実行されていて、HTTP 要求に応答する準備ができている場合に、次の出力が表示されます。

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

新しいターミナル ウィンドウで curl を使用して、コマンド ラインから関数をトリガーします。

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

関数のコードを停止するには、ターミナルで `Ctrl-C` を使います。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure Functions へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 続行する前に、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)します。

```azurecli
az login
```

`azure-functions:deploy` Maven ターゲットを使用して、新しい関数アプリにコードをデプロイします。

> [!NOTE]
> Visual Studio Code を使用して関数アプリをデプロイするときは、無料サブスクリプション以外のサブスクリプションを選択してください。そうでないと、エラーが発生します。 IDE の左側で自分のサブスクリプションを確認することができます。

```
mvn azure-functions:deploy
```

デプロイが完了すると、関数アプリへのアクセスに使うことができる URL が表示されます。

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

`cURL` を使用して、Azure で実行している関数アプリをテストします。 前の手順でデプロイされた独自の関数アプリの URL と一致するように、下のサンプルの URL を変更する必要があります。

> [!NOTE]
> **アクセス権** を `Anonymous` に設定していることを確認します。 `Function` の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](functions-bindings-http-webhook-trigger.md#authorization-keys)を提示する必要があります。

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>変更を加えて再デプロイする

関数アプリから返されるテキストに変更を加えるために、生成されたプロジェクトの `src/main.../Function.java` ソース ファイルを編集します。 変更するのは次の行です。

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

次のコードにします。

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

変更内容を保存し、ターミナルから `azure-functions:deploy` を実行して再デプロイします。 関数アプリが更新されます。次の要求を実行してみましょう。

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

更新された出力が表示されます。

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>参照バインディング

HTTP トリガーとタイマー トリガー以外の [Azure Functions のトリガーとバインド](functions-triggers-bindings.md)を操作するには、バインド拡張機能をインストールする必要があります。 この記事では必要ありませんが、他のバインドの種類を操作するときに拡張機能を有効にする方法について理解しておく必要があります。

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>次のステップ

簡単な HTTP トリガーを使って Kotlin 関数アプリを作成し、Azure Functions にデプロイしました。

- Java と Kotlin の関数の開発の詳細については、「[Azure Functions の Java 開発者向けガイド](functions-reference-java.md)」を参照してください。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md)、[Eclipse](functions-create-maven-eclipse.md) を使って関数を作成し、ローカルでデバッグします。 
- Azure にデプロイされた関数を Visual Studio Code でデバッグします。 その手順については、Visual Studio Code の[サーバーレス Java アプリケーション](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)に関するドキュメントを参照してください。
