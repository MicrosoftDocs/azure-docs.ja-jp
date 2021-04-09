---
title: Azure Functions 用 Java 開発者向けリファレンス
description: Java を使用して関数を開発する方法について説明します。
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96326456"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions の Java 開発者向けガイド

このガイドでは、Java を使用した Azure Functions の開発を成功させるために役立つ詳細情報について説明します。

Java 開発者が、Azure Functions を初めて使用する場合は、まず次のいずれかの記事を読むことが推奨されます。

| 作業の開始 | 概念| 
| -- | -- |  
| <ul><li>[Visual Studio Code を使用した Java 関数](./create-first-function-vs-code-java.md)</li><li>[ターミナル/コマンド プロンプトによる Java/Maven 関数](./create-first-function-cli-java.md)</li><li>[Gradle を使用した Java 関数](functions-create-first-java-gradle.md)</li><li>[Eclipse を使用した Java 関数](functions-create-maven-eclipse.md)</li><li>[IntelliJ アイデアを使用した Java 関数](functions-create-maven-intellij.md)</li></ul> | <ul><li>[開発者ガイド](functions-reference.md)</li><li>[ホスティング オプション](functions-scale.md)</li><li>[パフォーマンス&nbsp;に関する考慮事項](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Java 関数の基礎

Java 関数は、注釈 `@FunctionName` で装飾された `public` メソッドです。 このメソッドは、Java 関数のエントリを定義し、特定のパッケージ内で一意である必要があります。 パッケージには、`@FunctionName` で注釈付けされたパブリック メソッドを複数持つクラスが複数存在することもあります。 1 つのパッケージが Azure の関数アプリにデプロイされます。 関数アプリを Azure で実行すると、個々の Java 関数のデプロイ、実行、および管理コンテキストが提供されます。

## <a name="programming-model"></a>プログラミング モデル 

[トリガーとバインド](functions-triggers-bindings.md)の概念は、Azure Functions の基盤です。 トリガーによって、コードの実行が開始します。 バインドでは、データを関数に渡し、関数からデータを返す方法が提供されます。カスタム データ アクセス コードを記述する必要はありません。

## <a name="create-java-functions"></a>Java の関数を作成する

Java の関数を作成しやすくするために、Maven ベースのツールとアーキタイプが用意されています。事前に定義された Java テンプレートを使用することで、特定の関数トリガーを含むプロジェクトを簡単に作成できます。    

### <a name="maven-based-tooling"></a>Maven ベースのツール

次の開発環境には、Java 関数プロジェクトの作成に対応した Azure Functions ツールが備わっています。 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

上記リンクの記事には、好みの IDE を使用して基本的な関数を作成する方法が紹介されています。 

### <a name="project-scaffolding"></a>プロジェクトのスキャフォールディング

ターミナルからのコマンド ライン開発を希望する場合、Java ベースの関数プロジェクトをスキャフォールディングする最も簡単な方法は、`Apache Maven` アーキタイプを使用することです。 Azure Functions の Java Maven アーキタイプは、[com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/) という _groupId_:_artifactId_ で発行されています。 

次のコマンドを使用すると、このアーキタイプを使用して新しい Java 関数プロジェクトが生成されます。

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

このアーキタイプの基本的な使い方については、[Java クイックスタート](./create-first-function-cli-java.md)を参照してください。

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

共有 [host.json](functions-host-json.md) ファイルを使用して関数アプリを構成できます。 各関数には、独自のコード ファイル (.java) とバインディング構成ファイル (function.json) があります。

1 つのプロジェクトに複数の関数を含めることができます。 複数の jar に関数を分けることは避けてください。 ターゲット ディレクトリの `FunctionApp` が、Azure 内の関数アプリにデプロイされます。

## <a name="triggers-and-annotations"></a>トリガーと注釈

 関数は、HTTP 要求、タイマー、日付の更新などのトリガーで呼び出されます。 関数はそのトリガーと他の入力を処理して 1 つまたは複数の出力を生成する必要があります。

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) パッケージに含まれる Java の注釈を使用して、入力と出力をメソッドにバインドします。 詳細については、[Java リファレンス ドキュメント](/java/api/com.microsoft.azure.functions.annotation)に関するページを参照してください。

> [!IMPORTANT] 
> Azure Blob storage、Azure Queue storage、または Azure Table Storage をローカルで実行するには、[local.settings.json](./functions-run-local.md#local-settings-file) に Azure Storage アカウントを構成する必要があります。

例:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

[azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) によって生成された、対応する `function.json` を次に示します。

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

## <a name="java-versions"></a>Java のバージョン

Azure で関数を実行する関数アプリを作成する場合に使用する Java のバージョンは、pom.xml ファイルに指定します。 Maven アーキタイプでは現在、Java 8 用の pom.xml が生成され、これは、発行前に変更できます。 pom.xml の Java バージョンは、アプリをローカルで開発してテストしたバージョンと一致している必要があります。 

### <a name="supported-versions"></a>サポートされているバージョン

次の表は、Functions ランタイムの各メジャー バージョンに対して現在サポートされている Java バージョンをオペレーティング システムごとに示しています。

| Functions バージョン | Java バージョン (Windows) | Java バージョン (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | 該当なし |

デプロイのための Java バージョンを指定しない限り、Azure へのデプロイ中、Maven アーキタイプは既定で Java 8 になります。

### <a name="specify-the-deployment-version"></a>デプロイ バージョンを指定する

Maven アーキタイプが対象とする Java のバージョンは、`-DjavaVersion` パラメーターを使用して制御できます。 このパラメーターの値には `8` または `11` を指定できます。 

Maven アーキタイプでは、指定された Java バージョンを対象とする pom.xml が生成されます。 pom.xml 内の次の要素は、使用する Java バージョンを示しています。

| 要素 |  Java 8 の値 | Java 11 の値 | 説明 |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | maven-compiler-plugin によって使用される Java のバージョン。 |
| **`JavaVersion`** | 8 | 11 | Azure の関数アプリによってホストされる Java バージョン。 |

次の例に、pom.xml ファイルの関連セクションにある Java 8 の設定を示します。

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> JAVA_HOME 環境変数に、Maven を使用したコードのコンパイル時に使用される JDK ディレクトリが正しく設定されている必要があります。 JDK のバージョンが少なくとも `Java.version` 設定と同じであることを確認します。 

### <a name="specify-the-deployment-os"></a>デプロイ OS を指定する

また、Maven では、Azure で関数アプリを実行するオペレーティング システムを指定することもできます。 `os` 要素を使用して、オペレーティング システムを選択します。 

| 要素 |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | Windows | linux | docker |

次の例は、pom.xml ファイルの `runtime` セクションのオペレーティング システム設定を示しています。

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>JDK ランタイムの使用可能性とサポート 

Java 関数アプリをローカルで開発するには、[Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) から適切な [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDK をダウンロードして使用します。 Azure Functions は、関数アプリをクラウドにデプロイするときに Azul Java 8 JDK ランタイムを使用します。

JKD および関数アプリに関する問題に対する [Azure サポート](https://azure.microsoft.com/support/)は、[認定サポート プラン](https://azure.microsoft.com/support/plans/)を通じてご利用いただけます。

## <a name="customize-jvm"></a>JVM のカスタマイズ

関数を使用すると、お使いの Java 関数の実行に使用する Java 仮想マシン (JVM) をカスタマイズできます。 既定で[次の JVM オプション](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)が使用されます。

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

`JAVA_OPTS` という名前のアプリ設定には、追加の引数を指定できます。 Azure portal または Azure CLI で、Azure にデプロイされているお使いの関数アプリにアプリ設定を追加することができます。

> [!IMPORTANT]  
> 従量課金プランでは、カスタマイズを機能させるために、値を 0 にした WEBSITE_USE_PLACEHOLDER 設定も追加する必要があります。 この設定によって、Java 関数のコールド スタート時間が長くなることはありません。

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com) の [[アプリケーション設定]](functions-how-to-use-azure-function-app-settings.md#settings) タブを使用して `JAVA_OPTS` の設定を追加します。

### <a name="azure-cli"></a>Azure CLI

次の例のように、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) コマンドを使用して `JAVA_OPTS` を設定できます。

# <a name="consumption-plan"></a>[従量課金プラン](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[従量課金プラン](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[専用プラン/Premium プラン](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[専用プラン/Premium プラン](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

この例では、ヘッドレス モードが有効になります。 `<APP_NAME>` をお使いの関数アプリ名に置き換え、`<RESOURCE_GROUP>` をリソース グループに置き換えます。 

## <a name="third-party-libraries"></a>サードパーティ製ライブラリ 

Azure Functions はサード パーティ製ライブラリの使用をサポートしています。 既定で、プロジェクトの `pom.xml` ファイルで指定されているすべての依存関係は、[`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 目標の間に自動的にバンドルされます。 `pom.xml` ファイルの依存関係として指定されていないライブラリの場合は、関数のルート ディレクトリ以下の `lib` ディレクトリに配置します。 `lib` ディレクトリに配置された依存関係は、実行時にシステム クラス ローダーに追加されます。

`com.microsoft.azure.functions:azure-functions-java-library` 依存関係はクラスパスにおいて既定で提供されており、`lib` ディレクトリに含める必要はありません。 また、[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) は、[ここ](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)に示されている依存関係をクラスパスに追加します。

## <a name="data-type-support"></a>データ型のサポート

Plain old Java object (POJO)、`azure-functions-java-library` 内で定義されている型、または文字列や整数などのプリミティブなデータ型を使用して入力または力バインドにバインドできます。

### <a name="pojos"></a>POJO

入力データを POJO に変換する場合、[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) では [gson](https://github.com/google/gson) ライブラリが使用されます。 関数への入力として使用される POJO の型は `public` である必要があります。

### <a name="binary-data"></a>Binary Data

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

null 値を期待する場合、`Optional<T>` を使用します。

## <a name="bindings"></a>バインド

入出力バインドによって、コード内からデータに接続する宣言型の方法が提供されます。 関数は複数の入出力バインドを持つことができます。

### <a name="input-binding-example"></a>入力バインドの例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
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

この関数は HTTP 要求を使用して呼び出します。 
- HTTP 要求のペイロードは、引数 `inputReq` に `String` として渡されます。
- 1 つのエントリが Table storage から取得され、引数 `inputData` に `TestInputData` として渡されます。

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

この関数は、構成済みのイベント ハブに新しいデータが見つかるたびにトリガーされます。 `cardinality` が `MANY` に設定されているので、関数はイベント ハブからメッセージのバッチを受信します。 イベント ハブからの `EventData` は関数実行のために `TestEventData` に変換されます。

### <a name="output-binding-example"></a>出力バインドの例

`$return` を使用して、出力バインディングを戻り値にバインドすることができます。 

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

この関数は HttpRequest で呼び出します。 複数の値を Queue storage に書き込みます。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage と HttpResponseMessage

 これらは `azure-functions-java-library` で定義されます。 HttpTrigger 関数と共に機能するヘルパー型です。

| 特殊な型      |       移行先        | 一般的な用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP トリガー     | メソッド、ヘッダー、またはクエリを取得する |
| `HttpResponseMessage` | HTTP 出力のバインド | 200 以外の状態を返す   |

## <a name="metadata"></a>Metadata

いくつかのトリガーでは、入力データと共に[トリガー メタデータ](./functions-triggers-bindings.md)が送信されます。 注釈 `@BindingName` を使用して、トリガー メタデータにバインドできます。


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
上記の例では、`queryValue` は HTTP 要求 URL `http://{example.host}/api/metadata?name=test` 内のクエリ文字列パラメーター `name` にバインドされています。 次のもう 1 つの例では、キュー トリガー メタデータから `Id` にバインドする方法を示します。

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
> 注釈に指定されている名前がメタデータ プロパティと一致する必要があります。

## <a name="execution-context"></a>実行コンテキスト

`azure-functions-java-library` 内で定義されている `ExecutionContext` には、関数ランタイムと通信するためのヘルパー メソッドが含まれています。 詳しくは、[ExecutionContext のリファレンス記事](/java/api/com.microsoft.azure.functions.executioncontext)をご覧ください。

### <a name="logger"></a>ロガー

`ExecutionContext` 内で定義されている `getLogger` を使用して、関数コードからログを書き込みます。

例:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>ログとトレースの表示

Java の stdout と stderr ログ、その他の各種アプリケーション ログは、Azure CLI を使用してストリーム配信することができます。 

Azure CLI を使用して、アプリケーション ログを書き込むように関数アプリを構成する方法を次に示します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Azure CLI を使って関数アプリのログ出力をストリーム配信するには、コマンド プロンプト、Bash、ターミナルのいずれかのセッションを新たに開いて、次のコマンドを入力します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

[az webapp log tail](/cli/azure/webapp/log) コマンドには、`--provider` オプションを使って出力をフィルター処理するオプションが用意されています。 

Azure CLI を使ってログ ファイルを単一の ZIP ファイルとしてダウンロードするには、コマンド プロンプト、Bash、ターミナルのいずれかのセッションを新たに開いて、次のコマンドを入力します。

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

このコマンドを実行する前に、Azure portal または Azure CLI でファイル システム ログを有効にしておく必要があります。

## <a name="environment-variables"></a>環境変数

Functions では、サービス接続文字列などの[アプリ設定](functions-app-settings.md)は、実行中に環境変数として公開されます。 `System.getenv("AzureWebJobsStorage")` を使用して、これらの設定にアクセスすることができます。

次の設定では、`myAppSetting` という名前のキーの[アプリケーション設定](functions-how-to-use-azure-function-app-settings.md#settings)が取得されます。

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> 最適化されたコールド スタート エクスペリエンスを実現するには、アプリ設定 FUNCTIONS_EXTENSION_VERSION の値を ~2 または ~3 にする必要があります。

## <a name="next-steps"></a>次のステップ

Java による Azure Functions 開発の詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md)、[Eclipse](functions-create-maven-eclipse.md) を使ったローカルでの開発とデバッグ
* [Visual Studio Code を使用した Java 関数のリモート デバッグ](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven plugin for Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) (Azure Functions 用の Maven プラグイン) 
* `azure-functions:add` 目標を使って関数の作成を効率化し、[ZIP ファイル デプロイ](deployment-zip-push.md)に向けてステージング ディレクトリを準備します。
