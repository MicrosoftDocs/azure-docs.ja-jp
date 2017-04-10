---
title: "Azure Functions 用 JavaScript 開発者向けリファレンス | Microsoft Docs"
description: "JavaScript を使用して Azure Functions を開発する方法について説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 2c2d352a8aaf572612e64bd69e6e45616c15891d
ms.lasthandoff: 03/22/2017


---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions の JavaScript 開発者向けガイド
> [!div class="op_single_selector"]
> * [C# スクリプト](functions-reference-csharp.md)
> * [F# スクリプト](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

Azure Functions の JavaScript エクスペリエンスを利用すると、ランタイムと通信したり、バインドを介してデータの送受信を行ったりする場合に `context` オブジェクトが渡される関数を簡単にエクスポートできます。

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

`direction === "in"` のバインドが関数の引数と一緒に渡されます。つまり、[`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) を使用して、新しい入力を動的に処理できます (たとえば、`arguments.length` を使用して、すべての入力を繰り返し処理できます)。 この機能は、トリガーのみがあり、追加の入力がない場合に非常に便利です。これは、`context` オブジェクトを参照しなくてもトリガーのデータに予測どおりにアクセスできるためです。

引数は、exports ステートメントで順序を指定していなくても、 *function.json*に出現する順序で常に関数に渡されます。 たとえば、`function(context, a, b)` があり、それを `function(context, a)` に変更しても、`arguments[3]` を参照することで、関数コードの `b` の値を取得できます。

すべてのバインドも、方向に関係なく、 `context` オブジェクトと一緒に渡されます (以下を参照)。 

## <a name="context-object"></a>context オブジェクト
ランタイムでは、`context` オブジェクトを使用して、関数との間でデータをやり取りし、ユーザーがランタイムと通信できるようにします。

context オブジェクトは、常に関数の最初のパラメーターで、必ず含める必要があります。context オブジェクトには、ランタイムを適切に使用するために必要な `context.done` や `context.log` などのメソッドが用意されているためです。 オブジェクトには、任意の名前 (`ctx` や `c` など) を付けることができます。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>context.bindings
`context.bindings` オブジェクトは、すべての入出力データを収集します。 データは、バインドの `name` プロパティを介して `context.bindings` オブジェクトに追加されます。 たとえば、*function.json* に次のバインド定義が含まれている場合は、`context.bindings.myInput` でキューの内容にアクセスできます。 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`
`context.done` 関数は、実行が完了したことをランタイムに通知します。 この関数は、関数が完了したときに呼び出す必要があります。そうしないと、ランタイムは関数が完了したことを認識しません。 

`context.done` 関数を使用すると、ユーザー定義のエラーに加えて、`context.bindings` オブジェクトのプロパティを上書きするプロパティのプロパティ バッグをランタイムに渡すことができます。

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>context.log(message)
`context.log` メソッドでは、ログ記録の目的で、相互に関連付けられているログ ステートメントを出力できます。 `console.log`を使用した場合、指定したメッセージはプロセス レベルのログのみに対して表示されますが、これはあまり便利ではありません。

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

`context.log` メソッドでは、Node の [util.format メソッド](https://nodejs.org/api/util.html#util_util_format_format) でサポートしているのと同じパラメーター形式をサポートしています。 たとえば、次のようなコードがあるとします。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

このコードは、次のように記述できます。

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>HTTP トリガー: context.req と context.res
HTTP トリガーでは、HTTP の要求オブジェクトと応答オブジェクトに `req` と `res` を使用するパターンをよく見かけるため、完全な `context.bindings.name` パターンを使用しなくても、context オブジェクトで簡単にアクセスできるようにしました。

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Node のバージョンとパッケージの管理
Node のバージョンは、現在、 `6.5.0`にロックされています。 現在、さまざまなバージョンのサポートを追加して構成できるようにするために、調査しています。

関数アプリのファイル システムの関数のフォルダーに *package.json* ファイルをアップロードすることで関数にパッケージを追加できます。 ファイルをアップロードする方法については、「 **Azure Functions developer reference (Azure Functions 開発者向けリファレンス)** 」の「 [関数アプリ ファイルを更新する方法](functions-reference.md#fileupdate)」セクションを参照してください。 

関数アプリの SCM (Kudu) コマンド ライン インターフェイスで `npm install` を使用することもできます。

1. `https://<function_app_name>.scm.azurewebsites.net`に移動します。
2. **[デバッグ コンソール]、[CMD]** の順にクリックします。
3. `D:\home\site\wwwroot\<function_name>` に移動します。
4. `npm install` を実行します。

必要なパッケージがインストールされたら、普段の方法でそれを関数にインポートします。たとえば、`require('packagename')` を利用します。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

キャッシュされたパッケージを関数が共有できるように、ノードの Function App のルートに `package.json` が必要です。 バージョンの競合がある場合は、関数レベルで `package.json` を追加できますが、 パフォーマンス上の理由でこれを実行することはお勧めしません。 

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

## <a name="typescriptcoffeescript-support"></a>TypeScript/CoffeeScript のサポート
ランタイムによる TypeScript/CoffeeScript の自動コンパイルはまだ直接サポートされていません。そのため、デプロイ時にランタイムの外部ですべて処理する必要があります。 

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions F# 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)


