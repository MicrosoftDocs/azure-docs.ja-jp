---
title: Java と Maven を使用して Azure で初めての関数を作成する | Microsoft Docs
description: Java と Maven を使用して、HTTP によってトリガーされる簡単な関数作成し、Azure に発行します。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 関数, イベント処理, コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: d25fbfc058337c7a96414cf41f321e039ebc2258
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58801846"
---
# <a name="create-your-first-function-with-java-and-maven"></a>Java と Maven を使用して初めての関数を作成する

この記事では、Maven コマンド ライン ツールを使用して Java 関数を作成し、Azure Functions に公開する方法を説明します。 完了すると、関数コードは Azure の[従量課金プラン](functions-scale.md#consumption-plan)で実行され、HTTP 要求を使用してトリガーできるようになります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Java を使用して関数を開発するには、以下のものがインストールされている必要があります。

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) バージョン 8。
- [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core Tools](functions-run-local.md#v2) (**.NET Core 2.x SDK** が必要)

> [!IMPORTANT]
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

## <a name="generate-a-new-functions-project"></a>新しい Functions プロジェクトを生成する

空のフォルダーで次のコマンドを実行して、[Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)から Functions プロジェクトを生成します。

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> コマンドの実行で問題が発生した場合は、使用されている `maven-archetype-plugin` のバージョンを確認します。 `.pom` ファイルがない空のディレクトリでコマンドを実行しているため、Maven を古いバージョンからアップグレードした場合は、`~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` から古いバージョンのプラグインを使用しようとしている可能性があります。 その場合は、`maven-archetype-plugin` ディレクトリを削除した後、コマンドを再実行します。

### <a name="windows"></a> Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven によって、プロジェクトの生成を終了するために必要な値の入力を求めるメッセージが表示されます。 _groupId_、_artifactId_、_version_ の値については、[Maven の名前付け規則](https://maven.apache.org/guides/mini/guide-naming-conventions.html)をご覧ください。 _appName_ の値は Azure 全体で一意である必要があるため、Maven は既定値として前に入力された _artifactId_ を基にしてアプリ名を生成します。 _packageName_ の値により、生成される関数コードの Java パッケージが決まります。

以下の `com.fabrikam.functions` および `fabrikam-functions` 識別子は例として使用されており、このクイック スタートの後の方の手順を読みやすくしています。 この手順では、Maven に独自の値を指定することをお勧めします。

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven は、この例 `fabrikam-functions` の場合、_artifactId_ という名前の新しいフォルダーにプロジェクト ファイルを作成します。 プロジェクトで生成される、すぐに実行できるコードは、[HTTP によってトリガーされる](/azure/azure-functions/functions-bindings-http-webhook)、要求の本文をエコーする簡単な関数です。

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
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

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

新しいターミナル ウィンドウで curl を使用して、コマンド ラインから関数をトリガーします。

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

関数のコードを停止するには、ターミナルで `Ctrl-C` を使います。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure Functions へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 続行する前に、[Azure CLI にログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)します。

```azurecli
az login
```

`azure-functions:deploy` Maven ターゲットを使用して、新しい関数アプリにコードをデプロイします。

> [!NOTE]
> Visual Studio Code を使用して関数アプリをデプロイするときは、無料サブスクリプション以外のサブスクリプションを選択してください。そうでないと、エラーが発生します。 IDE の左側で自分のサブスクリプションを確認することができます。

```
mvn azure-functions:deploy
```

デプロイが完了すると、Azure 関数アプリへのアクセスに使うことができる URL が表示されます。

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

`cURL` を使用して、Azure で実行している関数アプリをテストします。 前の手順でデプロイされた独自の関数アプリの URL と一致するように、下のサンプルの URL を変更する必要があります。

> [!NOTE]
> **アクセス権**を `Anonymous` に設定していることを確認します。 `Function` の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](../azure-functions/functions-bindings-http-webhook.md#authorization-keys)を提示する必要があります。

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>変更を加えて再デプロイする

関数アプリから返されるテキストに変更を加えるために、生成されたプロジェクトの `src/main.../Function.java` ソース ファイルを編集します。 変更するのは次の行です。

```java
return request.createResponse(200, "Hello, " + name);
```

これを次のように変更してください。

```java
return request.createResponse(200, "Hi, " + name);
```

変更内容を保存し、ターミナルから `azure-functions:deploy` を実行して再デプロイします。 関数アプリが更新されます。次の要求を実行してみましょう。

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

出力結果が更新されていることがわかります。

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>次の手順

簡単な HTTP トリガーを使って Java 関数アプリを作成し、Azure Functions にデプロイしました。

- 「[Azure Functions Java developer guide](functions-reference-java.md)」(Azure Functions Java 開発者ガイド) で、Java 関数の開発の詳細について確認します。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md)、[Eclipse](functions-create-maven-eclipse.md) を使って関数を作成し、ローカルでデバッグします。 
- Azure にデプロイされた関数を Visual Studio Code でデバッグします。 その手順については、Visual Studio Code の[サーバーレス Java アプリケーション](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)に関するドキュメントを参照してください。
