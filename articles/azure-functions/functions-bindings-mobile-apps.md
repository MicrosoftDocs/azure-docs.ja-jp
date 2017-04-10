---
title: "Azure Functions における Mobile Apps のバインド | Microsoft Docs"
description: "Azure Functions で Azure Mobile Apps のバインドを使用する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: c5e1c02984f9773b263c0bee7685c7d5ff62e658


---
# <a name="azure-functions-mobile-apps-bindings"></a>Azure Functions における Mobile Apps のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) のバインドを構成したりコーディングしたりする方法について説明します。 Azure Functions は、Mobile Apps の入力および出力バインドをサポートしています。

Mobile Apps の入力および出力バインドを使用すると、モバイル アプリで[データ テーブルの読み取り/書き込み](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)を行うことができます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Mobile Apps 入力バインド
Mobile Apps 入力バインドは、モバイル テーブル エンドポイントからレコードを読み込んで関数に渡します。 C# および F# 関数では、レコードに加えられた変更は、関数が正常に終了したときに、テーブルに自動的に送り返されます。

関数への Mobile Apps 入力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

以下の点に注意してください。

* `id` は、静的にすることも、関数を呼び出すトリガーに基づいて設定することもできます。 たとえば、関数に[キュー トリガー]()を使用した場合、`"id": "{queueTrigger}"` は、キュー メッセージの文字列値を、取得するレコード ID として使用します。
* `connection` には、モバイル アプリの URL が含まれている Function App のアプリ設定の名前が含まれている必要があります。 この関数は、この URL を使用して、モバイル アプリに対して必要な REST 操作を作成します。 モバイル アプリの URL (たとえば、`http://<appname>.azurewebsites.net`) を含む[アプリ設定を Function App で作成]()し、入力バインドの `connection` プロパティでアプリ設定の名前を指定します。 
* [API キーを Node.js モバイル アプリのバックエンドに実装する](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)場合や [API キーを .NET モバイル アプリのバックエンドに実装する](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)場合は、`apiKey` を指定する必要があります。 そのためには、API キーを含む[アプリ設定を Function App で作成]()し、アプリ設定の名前を指定した `apiKey` プロパティを入力バインドに追加します。 
  
  > [!IMPORTANT]
  > この API キーは、モバイル アプリ クライアント間で共有しないでください。 この API キーは、Azure Functions のようなサービス側のクライアントにのみ、セキュリティで保護された方法で配布する必要があります。 
  > 
  > [!NOTE]
  > Azure Functions では接続情報と API キーがアプリ設定として保存されるため、これらはソース管理リポジトリにチェックインされません。 これにより、機密情報が保護されます。
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>入力の使用方法
このセクションでは、Mobile Apps 入力バインドを関数のコードで使用する方法について説明します。 

指定したテーブルとレコード ID を持つレコードが検出されると、レコードは名前付き [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) パラメーターに渡されます (Node.js の場合は `context.bindings.<name>` オブジェクトに渡されます)。 レコードが検出されなかった場合、パラメーターは `null` になります。 

C# および F# の関数では、入力レコード (名前付き入力パラメーター) に加えられた変更は、関数が正常に終了したときに、Mobile Apps テーブルに自動的に送り返されます。 Node.js 関数では、`context.bindings.<name>` を使用して入力レコードにアクセスします。 Node.js でレコードを変更することはできません。

<a name="inputsample"></a>

## <a name="input-sample"></a>入力サンプル
キュー トリガー メッセージの ID を持つモバイル アプリのテーブル レコードを取得する次の function.json があるとします。

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

バインドからの入力レコードを使用する、言語固有のサンプルを参照してください。 C# および F# のサンプルでは、レコードの `text` プロパティも変更します。

* [C#](#inputcsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C での入力サンプル# #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js での入力サンプル

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Mobile Apps 出力バインド
Mobile Apps 出力バインドを使用して、Mobile Apps テーブル エンドポイントにレコードを書き込みます。  

関数への Mobile Apps 出力では、function.json の `bindings` 配列内にある次の JSON オブジェクトが使用されます。

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

以下の点に注意してください。

* `connection` には、モバイル アプリの URL が含まれている Function App のアプリ設定の名前が含まれている必要があります。 この関数は、この URL を使用して、モバイル アプリに対して必要な REST 操作を作成します。 モバイル アプリの URL (たとえば、`http://<appname>.azurewebsites.net`) を含む[アプリ設定を Function App で作成]()し、入力バインドの `connection` プロパティでアプリ設定の名前を指定します。 
* [API キーを Node.js モバイル アプリのバックエンドに実装する](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)場合や [API キーを .NET モバイル アプリのバックエンドに実装する](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)場合は、`apiKey` を指定する必要があります。 そのためには、API キーを含む[アプリ設定を Function App で作成]()し、アプリ設定の名前を指定した `apiKey` プロパティを入力バインドに追加します。 
  
  > [!IMPORTANT]
  > この API キーは、モバイル アプリ クライアント間で共有しないでください。 この API キーは、Azure Functions のようなサービス側のクライアントにのみ、セキュリティで保護された方法で配布する必要があります。 
  > 
  > [!NOTE]
  > Azure Functions では接続情報と API キーがアプリ設定として保存されるため、これらはソース管理リポジトリにチェックインされません。 これにより、機密情報が保護されます。
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>出力の使用方法
このセクションでは、Mobile Apps 出力バインドを関数のコードで使用する方法について説明します。 

C# 関数では、`out object` 型の名前付き出力パラメーターを使用して出力レコードにアクセスします。 Node.js 関数では、`context.bindings.<name>` を使用して出力レコードにアクセスします。

<a name="outputsample"></a>

## <a name="output-sample"></a>出力サンプル
キューのトリガーと Mobile Apps の出力を定義する次の function.json があるとします。

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Mobile Apps テーブル エンドポイントにレコードを作成してキュー メッセージの内容を設定する、言語固有のサンプルを参照してください。

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C での出力サンプル# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js での出力サンプル

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


