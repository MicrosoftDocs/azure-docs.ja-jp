---
title: Azure Functions における Mobile Apps のバインド
description: Azure Functions で Azure Mobile Apps のバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5ea58cc3d9f3615a74249b36f3f9ffb79caddda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212246"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Azure Functions における Mobile Apps のバインド 

> [!NOTE]
> Azure Mobile Apps のバインドは、Azure Functions 1.x でのみ使用できます。 これらは Azure Functions 2.x 以降ではサポートされていません。

この記事では、Azure Functions で [Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) のバインドを操作する方法について説明します。 Azure Functions は、Mobile Apps の入力および出力バインドをサポートしています。

Mobile Apps のバインドを使用すると、モバイル アプリのデータ テーブルの読み取りや更新を行うことができます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Mobile Apps バインディングは [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet パッケージ、バージョン 1.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>入力

Mobile Apps 入力バインドは、モバイル テーブル エンドポイントからレコードを読み込んで関数に渡します。 C# および F# 関数では、レコードに加えられた変更は、関数が正常に終了したときに、テーブルに自動的に送り返されます。

## <a name="input---example"></a>入力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>入力 - C# スクリプトの例

次の例は、*function.json* ファイルの Mobile Apps 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、レコード識別子を含むキュー メッセージによってトリガーされます。 この関数は、指定されたレコードを読み取って、`Text` プロパティを変更します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

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

### <a name="input---javascript"></a>入力 - JavaScript

次の例は、*function.json* ファイルの Mobile Apps 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、レコード識別子を含むキュー メッセージによってトリガーされます。 この関数は、指定されたレコードを読み取って、`Text` プロパティを変更します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>入力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) 属性を使用します。

属性のプロパティについて詳しくは、[この後の構成に関するセクション](#input---configuration)をご覧ください。

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルと `MobileTable` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
| **type**| 該当なし | "mobileTable" に設定する必要があります。|
| **direction**| 該当なし |"in" に設定する必要があります。|
| **name**| 該当なし | 関数シグネチャの入力パラメーターの名前。|
|**tableName** |**TableName**|モバイル アプリのデータ テーブルの名前|
| **id**| **Id** | 取得するレコードの識別子。 静的にすることも、関数を呼び出すトリガーに基づいて設定することもできます。 たとえば、関数にキュー トリガーを使用した場合、`"id": "{queueTrigger}"` は、キュー メッセージの文字列値を、取得するレコード ID として使用します。|
|**connection**|**Connection**|モバイル アプリの URL を含むアプリ設定の名前。 この関数は、この URL を使用して、モバイル アプリに対して必要な REST 操作を作成します。 モバイル アプリの URL を含むアプリ設定を Function App で作成し、入力バインドの `connection` プロパティでアプリ設定の名前を指定します。 URL は、`http://<appname>.azurewebsites.net` のようになります。
|**apiKey**|**ApiKey**|モバイル アプリの API キーを含むアプリ設定の名前。 [API キーを Node.js モバイル アプリに実装する](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)場合や、[API キーを .NET モバイル アプリに実装する](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)場合は、API キーを指定します。 キーを指定するには、API キーを含むアプリ設定を Function App で作成し、アプリ設定の名前を指定した `apiKey` プロパティを入力バインドに追加します。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> API キーをモバイル アプリのクライアントと共有しないでください。 この API キーは、Azure Functions のようなサービス側のクライアントにのみ、セキュリティで保護された方法で配布する必要があります。 Azure Functions では接続情報と API キーがアプリ設定として保存されるため、これらはソース管理リポジトリにチェックインされません。 これにより、機密情報が保護されます。

## <a name="input---usage"></a>入力 - 使用方法

C# 関数では、指定の ID を含むレコードが検出されると、指定された [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) パラメーターに渡されます。 レコードが検出されなかった場合、パラメーター値は `null` になります。 

JavaScript 関数では、レコードは `context.bindings.<name>` オブジェクトに渡されます。 レコードが検出されなかった場合、パラメーター値は `null` になります。 

C# および F# の関数では、入力レコード (名前付き入力パラメーター) に加えられた変更は、関数が正常に終了したときに、テーブルに自動的に送り返されます。 JavaScript 関数ではレコードを変更できません。

## <a name="output"></a>出力

Mobile Apps 出力バインドを使用して、Mobile Apps テーブルにレコードを書き込みます。  

## <a name="output---example"></a>出力 - 例

言語固有の例をご覧ください。

* [C#](#output---c-example)
* [C# スクリプト (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>出力 - C# の例

次の例は、キュー メッセージによってトリガーされ、モバイル アプリ テーブルにレコードを作成する [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>出力 - C# スクリプトの例

次の例は、*function.json* ファイルの Mobile Apps 出力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、キュー メッセージによってトリガーされ、`Text` プロパティにハードコーディングされた値を使用して新しいレコードを作成します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
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
]
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>出力 - JavaScript の例

次の例は、*function.json* ファイルの Mobile Apps 出力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、キュー メッセージによってトリガーされ、`Text` プロパティにハードコーディングされた値を使用して新しいレコードを作成します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
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

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) 属性を使用します。

構成可能な属性プロパティについては、「[出力 - 構成](#output---configuration)」を参照してください。 メソッド シグネチャでの `MobileTable` 属性の例を次に示します。

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

完全な例については、「[出力 - C# の例](#output---c-example)」を参照してください。

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `MobileTable` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
| **type**| 該当なし | "mobileTable" に設定する必要があります。|
| **direction**| 該当なし |"out" に設定する必要があります。|
| **name**| 該当なし | 関数シグネチャの出力パラメーターの名前。|
|**tableName** |**TableName**|モバイル アプリのデータ テーブルの名前|
|**connection**|**MobileAppUriSetting**|モバイル アプリの URL を含むアプリ設定の名前。 この関数は、この URL を使用して、モバイル アプリに対して必要な REST 操作を作成します。 モバイル アプリの URL を含むアプリ設定を Function App で作成し、入力バインドの `connection` プロパティでアプリ設定の名前を指定します。 URL は、`http://<appname>.azurewebsites.net` のようになります。
|**apiKey**|**ApiKeySetting**|モバイル アプリの API キーを含むアプリ設定の名前。 [API キーを Node.js モバイル アプリのバックエンドに実装する](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)場合や [API キーを .NET モバイル アプリのバックエンドに実装する](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)場合は、API キーを指定します。 キーを指定するには、API キーを含むアプリ設定を Function App で作成し、アプリ設定の名前を指定した `apiKey` プロパティを入力バインドに追加します。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> API キーをモバイル アプリのクライアントと共有しないでください。 この API キーは、Azure Functions のようなサービス側のクライアントにのみ、セキュリティで保護された方法で配布する必要があります。 Azure Functions では接続情報と API キーがアプリ設定として保存されるため、これらはソース管理リポジトリにチェックインされません。 これにより、機密情報が保護されます。

## <a name="output---usage"></a>出力 - 使用方法

C# スクリプト関数では、`out object` 型の名前付き出力パラメーターを使用して出力レコードにアクセスします。 C# クラス ライブラリでは、`MobileTable` 属性を次のどの型に対しても使用できます。

* `ICollector<T>` または `IAsyncCollector<T>`。このとき、`T` は `JObject` か、`public string Id` プロパティを持つ任意の型です。
* `out JObject`
* `out T` または `out T[]`。このとき、`T` は `public string Id` プロパティを持つ任意の型です。

Node.js 関数では、`context.bindings.<name>` を使用して出力レコードにアクセスします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
