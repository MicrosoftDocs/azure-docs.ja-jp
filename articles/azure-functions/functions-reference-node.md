---
title: "Azure Functions 用 JavaScript 開発者向けリファレンス | Microsoft Docs"
description: "JavaScript を使用して関数を開発する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: a20f6db8bbbc9b7936cf102e8cd2ff1b2a995fb2
ms.contentlocale: ja-jp
ms.lasthandoff: 09/29/2017

---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions の JavaScript 開発者向けガイド
> [!div class="op_single_selector"]
> * [C# スクリプト](functions-reference-csharp.md)
> * [F# スクリプト](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

Azure Functions の JavaScript エクスペリエンスを利用すると、ランタイムと通信したり、バインディングを介してデータの送受信を行ったりする場合に `context` オブジェクトとして渡される関数を簡単にエクスポートできます。

この記事では、「 [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)」を既に読んでいることを前提としています。

## <a name="exporting-a-function"></a>関数のエクスポート
すべての JavaScript 関数では、ランタイムが関数を見つけて実行するために、`module.exports` を使用して `function` を 1 つエクスポートする必要があります。 この関数には、常に `context` オブジェクトを含める必要があります。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

`direction === "in"` のバインディングが関数の引数と一緒に渡されます。つまり、[`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) を使用して、新しい入力を動的に処理できます (たとえば、`arguments.length` を使用して、すべての入力を繰り返し処理できます)。 この機能は、トリガーのみがあり、追加の入力がない場合に便利です。これは、`context` オブジェクトを参照しなくてもトリガーのデータに予測どおりにアクセスできるためです。

引数は、exports ステートメントで順序を指定していなくても、*function.json*に出現する順序で常に関数に渡されます。 たとえば、`function(context, a, b)` があり、それを `function(context, a)` に変更しても、`arguments[3]` を参照することで、関数コードの `b` の値を取得できます。

すべてのバインディングも、方向に関係なく、`context` オブジェクトと一緒に渡されます (以下のスクリプトを参照)。 

## <a name="context-object"></a>context オブジェクト
ランタイムでは、`context` オブジェクトを使用して、関数との間でデータをやり取りし、ユーザーがランタイムと通信できるようにします。

context オブジェクトは、常に関数の最初のパラメーターとし、必ず含める必要があります。context オブジェクトには、ランタイムを適切に使用するために必要な `context.done` や `context.log` などのメソッドが用意されているためです。 オブジェクトには、任意の名前 (`ctx` や `c` など) を付けることができます。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>context.bindings プロパティ

```
context.bindings
```
すべての入力データと出力データを含む名前付きオブジェクトを返します。 たとえば、*function.json* に次のバインディング定義が含まれている場合は、`context.bindings.myInput` オブジェクトからキューの内容にアクセスできます。 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>context.done メソッド
```
context.done([err],[propertyBag])
```

コードが完了したことをランタイムに通知します。 ユーザーは `context.done` を呼び出す必要があります。そうしないと、ランタイムに関数の完了が通知されず、実行がタイムアウトになります。 

`context.done` メソッドを使用すると、ユーザー定義のエラーに加えて、`context.bindings` オブジェクトのプロパティを上書きするプロパティのプロパティ バッグをランタイムに渡すことができます。

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>context.log メソッド  

```
context.log(message)
```
既定のトレース レベルでストリーミング コンソール ログに書き込むことができます。 `context.log` で利用可能な、他のトレース レベルでコンソール ログに書き込むことができるログ記録方法が他にあります。


| メソッド                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | エラー レベルのログ、またはそれ以下に書き込みます。   |
| **warn(_message_)**    | 警告レベルのログ、またはそれ以下に書き込みます。 |
| **info(_message_)**    | 情報レベルのログ、またはそれ以下に書き込みます。    |
| **verbose(_message_)** | 詳細なレベルのログに書き込みます。           |

次の例は、警告トレース レベルでコンソールに書き込みます。

```javascript
context.log.warn("Something has happened."); 
```
host.json ファイルにログを記録する場合のトレース レベルのしきい値を設定したり、それを無効にしたりできます。  ログに書き込む方法の詳細については、次のセクションを参照してください。

## <a name="binding-data-type"></a>バインド データ型

入力バインドのデータ型を定義するには、バインド定義の `dataType` プロパティを使用します。 たとえば、バイナリ形式で HTTP 要求のコンテンツを読み取るには、`binary` 型を使用します。

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` のその他のオプションは、`stream` と `string` です。

## <a name="writing-trace-output-to-the-console"></a>トレース出力をコンソールに書き込む 

関数で、`context.log` メソッドを使用してトレース出力をコンソールに書き込みます。 この時点では、`console.log` を使用してコンソールに書き込むことはできません。

`context.log()` を呼び出すと、既定のトレース レベルである、_情報_ トレース レベルでコンソールにメッセージが書き込まれます。 次のコードは、情報トレース レベルでコンソールに書き込みます。

```javascript
context.log({hello: 'world'});  
```

上記のコードは次のコードと同等です。

```javascript
context.log.info({hello: 'world'});  
```

次のコードは、エラー レベルでコンソールに書き込みます。

```javascript
context.log.error("An error has occurred.");  
```

_エラー_ は最高のトレース レベルであるため、ログ記録が有効になっている限り、このトレースはすべてのトレース レベルで出力に書き込まれます。  


すべての `context.log` メソッドは、Node.js の [util.format メソッド](https://nodejs.org/api/util.html#util_util_format_format)でサポートされているのと同じパラメーター形式をサポートしています。 既定のトレース レベルを使用してコンソールに出力する次のコードについて検討してください。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

同じコードを次の形式で記述することもできます。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>コンソール ログのトレース レベルを構成する

関数を使用して、コンソールに書き込むためのしきい値のトレース レベルを定義できます。これによって、関数からコンソールにトレースを書き込む方法を簡単に制御できます。 コンソールに書き込まれるすべてのトレースのしきい値を設定するには、host.json ファイルの `tracing.consoleLevel` プロパティを使用します。 この設定は、関数アプリのすべての関数に適用されます。 次の例では、詳細ログ記録が有効になるようにトレースのしきい値を設定します。

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

**consoleLevel** の値は、`context.log` メソッドの名前に対応します。 コンソールへのすべてのトレース ログ記録を無効にするには、**consoleLevel** を _off_ に設定します。 詳細については、[host.json](functions-host-json.md) のリファレンスを参照してください。

## <a name="http-triggers-and-bindings"></a>HTTP トリガーとバインディング

HTTP、webhook トリガー、および HTTP 出力バインディングでは、要求オブジェクトと応答オブジェクトを使用して HTTP メッセージングを表します。  

### <a name="request-object"></a>要求オブジェクト

`request` オブジェクトには、次のプロパティがあります。

| プロパティ      | 説明                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 要求の本文を格納するオブジェクト。               |
| _headers_     | 要求ヘッダーを格納するオブジェクト。                   |
| _method_      | 要求の HTTP メソッド。                                |
| _originalUrl_ | 要求の URL。                                        |
| _params_      | 要求のルーティング パラメーターを格納するオブジェクト。 |
| _query_       | クエリ パラメーターを格納するオブジェクト。                  |
| _rawBody_     | 文字列としてのメッセージの本文。                           |


### <a name="response-object"></a>応答オブジェクト

`response` オブジェクトには、次のプロパティがあります。

| プロパティ  | 説明                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 応答の本文を格納するオブジェクト。         |
| _headers_ | 応答ヘッダーを格納するオブジェクト。             |
| _isRaw_   | 応答の書式設定をスキップすることを示します。    |
| _状態_  | 応答の HTTP 状態コード。                     |

### <a name="accessing-the-request-and-response"></a>要求と応答へのアクセス 

HTTP トリガーを使用する場合、HTTP 要求オブジェクトと応答オブジェクトにアクセスする方法は 3 つあります。

+ 名前付きの入力バインディングと出力バインディングから。 この方法で、HTTP トリガーとバインディングは他のバインディングと同じように動作します。 次の例では、名前付き `response` バインディングを使用して、応答オブジェクトを設定します。 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ `context` オブジェクトの `req` プロパティと `res` プロパティから。 この方法で、完全な `context.bindings.name` パターンを使用する代わりに、従来のパターンを使用して context オブジェクトから HTTP データにアクセスできます。 次の例では、`context` の `req` オブジェクトと `res` オブジェクトにアクセスする方法を示します。

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ `context.done()` の呼び出しで。 これは、`context.done()` メソッドに渡される応答を返す特殊な種類の HTTP バインディングです。 次の HTTP 出力バインディングで、`$return` 出力パラメーターを定義します。

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    この出力バインディングでは、次のように `done()` を呼び出すときにユーザーに応答を返すことを想定しています。

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Node のバージョンとパッケージの管理
Node のバージョンは、現在、 `6.5.0`にロックされています。 現在、さまざまなバージョンのサポートを追加して構成できるようにするために、調査しています。

次の手順で、関数アプリにパッケージを含めることができます。 

1. `https://<function_app_name>.scm.azurewebsites.net` にアクセスします。

2. **[デバッグ コンソール]** > **[CMD]** をクリックします。

3. `D:\home\site\wwwroot` に移動し、ページの上半分にある **wwwroot** フォルダーに package.json ファイルをドラッグします。  
    関数アプリにファイルをアップロードする方法は、他にもあります。 詳細については、「[関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」を参照してください。 

4. package.json ファイルがアップロードされたら、**Kudu リモート実行コンソール**で `npm install` コマンドを実行します。  
    この操作によって、package.json ファイルに示されているパッケージがダウンロードされ、関数アプリが再起動されます。

必要なパッケージがインストールされたら、次の例に示すように `require('packagename')` を呼び出すことで、インストールされたパッケージを関数にインポートします。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

関数アプリのルートに `package.json` ファイルを定義する必要があります。 このファイルを定義することによって、アプリのすべての関数を同じキャッシュされたパッケージで共有し、最高クラスのパフォーマンスを得ることができます。 バージョンの競合がある場合は、個別の関数のフォルダーに `package.json` ファイルを追加することで競合を解決できます。  

## <a name="environment-variables"></a>環境変数
環境変数またはアプリ設定値を取得するには、次のコード例のように、 `process.env`を使用します。

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>JavaScript 関数に関する考慮事項

JavaScript 関数を使用するときは、以下の 2 つのセクションに記載されている事柄に注意する必要があります。

### <a name="choose-single-core-app-service-plans"></a>シングルコア App Service プランを選択する

App Service プランを使用する関数アプリを作成するときは、複数のコアを持つプランではなく、シングルコア プランを選択することをお勧めします。 今日では、関数を使用して、シングルコア VM で JavaScript 関数をより効率的に実行できるようになりました。そのため、大規模な VM を使用しても、期待以上にパフォーマンスが向上することはありません。 必要な場合は、シングルコア VM インスタンスを追加することで手動でスケールアウトするか、自動スケールを有効にすることができます。 詳細については、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)」を参照してください。    

### <a name="typescript-and-coffeescript-support"></a>TypeScript と CoffeeScript のサポート
ランタイムによる TypeScript/CoffeeScript の自動コンパイルはまだ直接サポートされていません。そのため、デプロイ時にランタイムの外部ですべて処理する必要があります。 

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions F# 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)


