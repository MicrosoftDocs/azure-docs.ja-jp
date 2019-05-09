---
title: Azure Functions 用 Java 開発者向けリファレンス | Microsoft Docs
description: Java を使用して関数を開発する方法について説明します。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 9258b58783d4670620a251fef866211f7634480f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096726"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions の Java 開発者向けガイド

Azure Functions ランタイムは、[Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/) をサポートします。

このガイドには、Java で Azure 関数を記述する複雑な作業についての情報が含まれます。

Java 関数は、注釈 `@FunctionName` で装飾された `public` メソッドです。 このメソッドは、Java 関数のエントリを定義し、特定のパッケージ内で一意である必要があります。 

この記事では、「[Azure Functions の開発者向けガイド](functions-reference.md)」を既に読んでいることを前提としています。 さらに、[Visual Studio Code](functions-create-first-function-vs-code.md) を使用するか、[maven を使用](functions-create-first-java-maven.md)して、最初の関数を作成する Functions のクイック スタートも終えている必要があります。

## <a name="programming-model"></a>プログラミング モデル 

[トリガーとバインド](functions-triggers-bindings.md)の概念は、Azure Functions の基盤です。 トリガーによって、コードの実行が開始します。 バインドでは、データを関数に渡し、関数からデータを返す方法が提供されます。カスタム データ アクセス コードを記述する必要はありません。

## <a name="folder-structure"></a>フォルダー構造

Azure Functions の Java プロジェクトのフォルダー構造を次に示します。

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

関数アプリの構成に使用できる共有 [host.json](functions-host-json.md) ファイルがあります。 各関数には、独自のコード ファイル (.java) とバインディング構成ファイル (function.json) があります。

1 つのプロジェクトに複数の関数を含めることができます。 複数の jar に関数を分けることは避けてください。 ターゲット ディレクトリの FunctionApp が、Azure 内の関数アプリにデプロイされます。

## <a name="triggers-and-annotations"></a>トリガーと注釈

 Azure の関数は、HTTP 要求、タイマー、日付の更新などのトリガーで呼び出されます。 関数はそのトリガーと他の入力を処理して 1 つまたは複数の出力を生成する必要があります。

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) パッケージに含まれる Java の注釈を使用して、入力と出力をメソッドにバインドします。 詳しくは、[Java リファレンスドキュメント](/java/api/com.microsoft.azure.functions.annotation)のページをご覧ください。

> [!IMPORTANT] 
> Azure Storage Blob、Queue、または Table のトリガーをローカルに実行するには、[local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) に Azure Storage アカウントを構成する必要があります。

例:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

[azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) によって生成された、対応する `function.json` を次に示します:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>JDK ランタイムの使用可能性とサポート 

Java 関数アプリをローカルで開発するには、[Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) から [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK をダウンロードして使用します。 Azure Functions は、関数アプリをクラウドにデプロイするときに Azul Java 8 JDK ランタイムを使用します。

JKD および関数アプリに関する問題に対する [Azure サポート](https://azure.microsoft.com/en-us/support/)は、[認定サポート プラン](https://azure.microsoft.com/support/plans/)を通じてご利用いただけます。

## <a name="third-party-libraries"></a>サードパーティ製ライブラリ 

Azure Functions はサード パーティ製ライブラリの使用をサポートしています。 既定で、プロジェクトの `pom.xml` ファイルで指定されているすべての依存関係は、[`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 目標の間に自動的にバンドルされます。 `pom.xml` ファイルの依存関係として指定されていないライブラリの場合は、関数のルート ディレクトリ以下の `lib` ディレクトリに配置します。 `lib` ディレクトリに配置された依存関係は、実行時にシステム クラス ローダーに追加されます。

`com.microsoft.azure.functions:azure-functions-java-library` 依存関係はクラスパスにおいて既定で提供されており、`lib` ディレクトリに含める必要はありません。 また、[ここ](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)に示されている依存関係が、[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) によってクラスパスに追加されます。

## <a name="data-type-support"></a>データ型のサポート

Plain old Java object (POJO)、`azure-functions-java-library` 内で定義されている型、または文字列や整数などのプリミティブなデータ型を使用して入力/出力バインドにバインドできます。

### <a name="plain-old-java-objects-pojos"></a>Plain old Java object (POJO)

入力データを POJO に変換する場合、[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) では [gson](https://github.com/google/gson) ライブラリが使用されます。 関数への入力として使用される POJO の型は `public` である必要があります。

### <a name="binary-data"></a>バイナリ データ

バイナリの入力または出力を `byte[]` にバインドするには、使用する function.json 内の `dataType` フィールドを `binary` に設定します。

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

null 値が必要な場合は `Optional<T>` を使用します

## <a name="bindings"></a>バインド

入出力バインドによって、コード内からデータに接続する宣言型の方法が提供されます。 関数は複数の入出力バインドを持つことができます。

### <a name="example-input-binding"></a>入力バインディングの例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

この関数は、HTTP 要求を使用して呼び出されます。 
- HTTP 要求のペイロードは、引数 `inputReq` に `String` として渡されます
- 1 つのエントリが Azure Table Storage から取得され、引数 `inputData` に `TestInputData` として渡されます。

入力のバッチを受信するには、`String[]`、`POJO[]`、`List<String>` または `List<POJO>` にバインドできます。

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

この関数は、構成済みのイベント ハブに新しいデータが見つかるたびにトリガーされます。 `cardinality` が `MANY` に設定されているので、関数はイベント ハブからメッセージのバッチを受信します。 イベント ハブからの EventData は関数実行のために `TestEventData` に変換されます。

### <a name="example-output-binding"></a>出力バインディングの例

`$return` を使用して、出力バインディングを戻り値にバインドすることができます 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

複数の出力バインディングが存在する場合は、そのうちの 1 つにのみ戻り値を使用します。

複数の出力値を送信するには、`azure-functions-java-library` パッケージに定義されている `OutputBinding<T>` を使用します。 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

上記の関数が HttpRequest 上で呼び出され、複数の値が Azure キューに書き込まれます

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage と HttpResponseMessage

 `azure-functions-java-library` 内で定義されている HttpRequestMessage 型と HttpResponseMessage 型は、HttpTrigger 関数を操作するためのヘルパー型です

| 特殊な型      |       ターゲット        | 一般的な用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP トリガー     | メソッド、ヘッダー、またはクエリを取得する |
| `HttpResponseMessage` | HTTP 出力のバインド | 200 以外の状態を返す   |

## <a name="metadata"></a>Metadata

いくつかのトリガーでは、入力データと共に[トリガー メタデータ](/azure/azure-functions/functions-triggers-bindings)が送信されます。 注釈 `@BindingName` を使用して、トリガー メタデータにバインドできます


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
上記の例では、`queryValue` は `name`Http 要求 URL`http://{example.host}/api/metadata?name=test` 内のクエリ文字列パラメーターにバインドされています。 キュー トリガー メタデータから `Id` にバインドする別の例を次に示します

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 注釈に指定されている名前がメタデータ プロパティと一致する必要があります

## <a name="execution-context"></a>実行コンテキスト

`ExecutionContext` 内で定義されている `azure-functions-java-library` には、関数ランタイムと通信するためのヘルパー メソッドが含まれています。

### <a name="logger"></a>ロガー

`ExecutionContext` 内で定義されている `getLogger` を使用して、関数コードからログを書き込みます。

例:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>ログとトレースの表示

Java の stdout と stderr ログ、その他の各種アプリケーション ログは、Azure CLI を使用してストリーム配信することができます。 

Azure CLI を使って、アプリケーション ログを出力するための構成を関数アプリケーションに対して行います。

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI を使って関数アプリのログ出力をストリーム配信するには、コマンド プロンプト、Bash、ターミナルのいずれかのセッションを新たに開いて、次のコマンドを入力します。

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) コマンドには、`--provider` オプションを使って出力をフィルター処理するオプションが用意されています。 

Azure CLI を使ってログ ファイルを単一の ZIP ファイルとしてダウンロードするには、コマンド プロンプト、Bash、ターミナルのいずれかのセッションを新たに開いて、次のコマンドを入力します。

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

このコマンドを実行する前に、Azure portal または Azure CLI でファイル システム ログを有効にしておく必要があります。

## <a name="environment-variables"></a>環境変数

Functions では、サービス接続文字列などの[アプリ設定](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)は、実行中に環境変数として公開されます。 `System.getenv("AzureWebJobsStorage")` を使用して、これらの設定にアクセスすることができます

例:

名前が testAppSetting で値が testAppSettingValue の[AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) を追加します

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>次の手順

Java による Azure Functions 開発の詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md)、[Eclipse](functions-create-maven-eclipse.md) を使ったローカルでの開発とデバッグ 
* [Visual Studio Code を使用した Java Azure Functions のリモート デバッグ](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven plugin for Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - `azure-functions:add` ゴールを使って関数の作成を効率化し、[ZIP ファイル デプロイ](deployment-zip-push.md)に向けてステージング ディレクトリを準備します。
