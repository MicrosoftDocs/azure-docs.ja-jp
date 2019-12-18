---
title: Azure Functions における Microsoft Graph のバインド
description: Azure Functions で Microsoft Graph のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: 77915409e3c44a01e26c35c0facb2d577c331fd8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914930"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Azure Functions における Microsoft Graph のバインド

この記事では、Azure Functions で Microsoft Graph のトリガーとバインドを構成および操作する方法について説明します。 これらと Azure Functions を使用して、[Microsoft Graph](https://developer.microsoft.com/graph) からのデータ、分析情報、およびイベントを処理できます。

Microsoft Graph の拡張機能には、次のバインドが用意されています。
- [認証トークンの入力バインド](#token-input)では、任意の Microsoft Graph API とやり取りできます。
- [Excel テーブルの入力バインド](#excel-input)では、Excel からデータを読み取れます。
- [Excel テーブルの出力バインド](#excel-output)では、Excel データを変更できます。
- [OneDrive ファイルの入力バインド](#onedrive-input)では、OneDrive からファイルを読み取ることができます。
- [OneDrive ファイルの出力バインド](#onedrive-output)では、OneDrive のファイルに書き込むことができます。
- [Outlook メッセージの出力バインド](#outlook-output)では、Outlook で電子メールを送信できます。
- [Microsoft Graph の webhook トリガーとバインド](#webhooks)のコレクションを使用すると、Microsoft Graph からのイベントに応答できます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph のバインドは、Azure Functions バージョン 2.x 以降では現在プレビューの段階です。 これらは Functions バージョン 1.x ではサポートされていません。

## <a name="packages"></a>パッケージ

認証トークンの入力バインディングは [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet パッケージで提供されます。 他の Microsoft Graph バインディングは [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) パッケージで提供されます。 パッケージのソース コードは、[azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>拡張機能の設定

Microsoft Graph のバインドは、_バインド拡張機能_ から入手できます。 バインド拡張機能は、Azure Functions ランタイム向けのオプション コンポーネントです。 このセクションでは、Microsoft Graph と認証トークンの拡張機能を設定する方法を示します。

### <a name="enabling-functions-20-preview"></a>Functions 2.0 プレビューを有効にする

バインド拡張機能は、Azure Functions 2.0 プレビューでのみ使用できます。 

Functions ランタイムのプレビュー版の 2.0 バージョンを使用するように関数アプリを設定する方法については、「[How to target Azure Functions runtime versions](set-runtime-version.md)」(Azure Functions ランタイム バージョンをターゲットにする方法) を参照してください。

### <a name="installing-the-extension"></a>拡張機能のインストール

Azure Portal から拡張機能をインストールするには、それを参照するテンプレートまたはバインドのいずれかに移動します。 新しい関数を作成し、[テンプレートの選択] 画面で、"Microsoft Graph" のシナリオを選択します。 このシナリオからテンプレートを 1 つ選択します。 または、既存の関数の [統合] タブに移動して、この記事で説明するバインドを 1 つ選択できます。

どちらの場合も、インストールする拡張機能の指定に関する警告が表示されます。 **[インストール]** をクリックして拡張機能を取得します。 それぞれの拡張機能は、関数アプリごとに 1 回のみインストールする必要があります。 

> [!Note] 
> ポータル内のインストール プロセスは、従量課金プランで最大 10 分かかります。

Visual Studio を使用している場合は、[この記事の前半で説明した NuGet パッケージ](#packages)をインストールして、拡張機能を取得できます。

### <a name="configuring-authentication--authorization"></a>認証と承認の構成

この記事で説明するバインドでは、ID を使用する必要があります。 これにより、Microsoft Graph でアクセス許可と監査の相互作用を適用できます。 ID には、アプリケーションにアクセスするユーザー、またはアプリケーションそのものを指定できます。 この ID を構成するには、Azure Active Directory で [App Service の認証と承認](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)を設定します。 また、関数で必要なリソースのアクセス許可を要求する必要があります。

> [!Note] 
> Microsoft Graph の拡張機能は、Azure AD 認証のみをサポートします。 ユーザーは、職場または学校のアカウントでログインする必要があります。

Azure Portal を使用している場合は、拡張機能のインストールを求めるプロンプトの下に警告が表示されます。 この警告では、App Service の認証および承認の構成と、テンプレートまたはバインドで必要なアクセス許可の要求が求められます。 必要に応じて、 **[Configure Azure AD now]\(Azure AD を今すぐ構成\)** または **[アクセス許可を今すぐ追加]** をクリックします。



<a name="token-input"></a>
## <a name="auth-token"></a>認証トークン

認証トークンの入力バインドは、特定のリソースに対する Azure AD トークンを取得し、コードに文字列として表示します。 アプリケーションがアクセス許可を持つ任意のリソースを指定できます。 

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#auth-token---example)
* [属性](#auth-token---attributes)
* [構成](#auth-token---configuration)
* [使用方法](#auth-token---usage)

### <a name="auth-token---example"></a>認証トークン - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>認証トークン - C# スクリプトの例

次の例では、ユーザー プロファイル情報を取得します。

*function.json* ファイルは、トークンの入力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、トークンを使用して Microsoft Graph への HTTP 呼び出しを行い、結果を返します。

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>認証トークン - JavaScript の例

次の例では、ユーザー プロファイル情報を取得します。

*function.json* ファイルは、トークンの入力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript スクリプト コードは、トークンを使用して Microsoft Graph への HTTP 呼び出しを行い、結果を返します。

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>認証トークン - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) 属性を使用します。

### <a name="auth-token---configuration"></a>認証トークン - 構成

次の表は、*function.json* ファイルと `Token` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - 認証トークンの関数コードで使用される変数名。 「[コードから認証トークンの入力バインドを使用する](#token-input-code)」をご覧ください。|
|**type**||必須 - `token` に設定する必要があります。|
|**direction**||必須 - `in` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId**|**UserId**  |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**リソース**|**resource**|必須 - トークンが要求される Azure AD リソース URL です。|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>認証トークン - 使用方法

バインド自体には Azure AD のアクセス許可は必要ありませんが、トークンの使用方法によっては、追加のアクセス許可を要求する必要がある場合があります。 トークンを使用してアクセスするリソースの要件を確認してください。

トークンは、常に文字列としてコードに表示されます。

> [!Note]
> `userFromId`、`userFromToken`、`userFromRequest` オプションのいずれかを使用してローカルで開発するときは、必要なトークンを[手動で取得](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857)し、呼び出し元のクライアント アプリケーションから `X-MS-TOKEN-AAD-ID-TOKEN` 要求ヘッダーで指定できます。


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel の入力

Excel テーブルの入力バインドは、OneDrive に格納されている Excel テーブルの内容を読み取ります。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#excel-input---example)
* [属性](#excel-input---attributes)
* [構成](#excel-input---configuration)
* [使用方法](#excel-input---usage)

### <a name="excel-input---example"></a>Excel の入力 - example

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel の入力 - C# スクリプトの例

次の *function.json* ファイルは、Excel の入力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

次の C# スクリプト コードは、指定したテーブルの内容を読み取って、ユーザーに返します。

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel の入力 - JavaScript の例

次の *function.json* ファイルは、Excel の入力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

次の JavaScript スクリプト コードは、指定したテーブルの内容を読み取って、ユーザーに返します。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel の入力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) 属性を使用します。

### <a name="excel-input---configuration"></a>Excel の入力 - 構成

次の表は、*function.json* ファイルと `Excel` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - Excel テーブルの関数コードで使用される変数名。 「[コードから Excel テーブルの入力バインドを使用する](#excel-input-code)」をご覧ください。|
|**type**||必須 - `excel` に設定する必要があります。|
|**direction**||必須 - `in` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId**|**UserId**  |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|**パス**|必須 - OneDrive での Excel ブックへのパスです。|
|**worksheetName**|**WorksheetName**|テーブルが検出されたワークシートです。|
|**tableName**|**TableName**|テーブルの名前。 指定しないと、ワークシートの内容が使用されます。|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel の入力 - 使用方法

このバインドには、Azure AD の次のアクセス許可が必要です。

|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルの読み取り|

バインドは、.NET 関数に次の種類を公開します。
- string[][]
- Microsoft.Graph.WorkbookTable
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel の出力

Excel の出力バインドは、OneDrive に格納されている Excel テーブルの内容を変更します。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#excel-output---example)
* [属性](#excel-output---attributes)
* [構成](#excel-output---configuration)
* [使用方法](#excel-output---usage)

### <a name="excel-output---example"></a>Excel の出力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel の出力 - C# スクリプトの例

次の例では、Excel テーブルに行を追加します。

*function.json* ファイルは、Excel の出力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、クエリ文字列からの入力に基づいて、テーブル (列が 1 つであると仮定) に新しい行を追加します。

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel の出力 - JavaScript の例

次の例では、Excel テーブルに行を追加します。

*function.json* ファイルは、Excel の出力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

次の JavaScript スクリプト コードは、クエリ文字列からの入力に基づいて、テーブル (列が 1 つであると仮定) に新しい行を追加します。

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel の出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) 属性を使用します。

### <a name="excel-output---configuration"></a>Excel の出力 - 構成

次の表は、*function.json* ファイルと `Excel` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - 認証トークンの関数コードで使用される変数名。 「[コードから Excel テーブルの出力バインドを使用する](#excel-output-code)」をご覧ください。|
|**type**||必須 - `excel` に設定する必要があります。|
|**direction**||必須 - `out` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**UserId** |**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|**パス**|必須 - OneDrive での Excel ブックへのパスです。|
|**worksheetName**|**WorksheetName**|テーブルが検出されたワークシートです。|
|**tableName**|**TableName**|テーブルの名前。 指定しないと、ワークシートの内容が使用されます。|
|**updateType**|**UpdateType**|必須 - テーブルに対して行われる変更の種類です。 次のいずれかの値を指定できます。<ul><li><code>update</code> - OneDrive 内のテーブルの内容を置き換えます。</li><li><code>append</code> - 新しい行を作成することで、OneDrive 内のテーブルの末尾にペイロードを追加します。</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel の出力 - 使用方法

このバインドには、Azure AD の次のアクセス許可が必要です。

|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルへのフル アクセスを持つ|

バインドは、.NET 関数に次の種類を公開します。
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)





<a name="onedrive-input"></a>
## <a name="file-input"></a>ファイルの入力

OneDrive ファイルの入力バインドは、OneDrive に格納されているファイルの内容を読み取ります。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#file-input---example)
* [属性](#file-input---attributes)
* [構成](#file-input---configuration)
* [使用方法](#file-input---usage)

### <a name="file-input---example"></a>ファイルの入力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>ファイルの入力 - C# スクリプトの例

次の例では、OneDrive に格納されているファイルを読み取ります。

*function.json* ファイルは、OneDrive ファイルの入力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、クエリ文字列で指定されたファイルを読み取って、その長さをログに記録します。

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>ファイルの入力 - JavaScript の例

次の例では、OneDrive に格納されているファイルを読み取ります。

*function.json* ファイルは、OneDrive ファイルの入力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

次の JavaScript コードは、クエリ文字列で指定されたファイルを読み取って、その長さを返します。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>ファイルの入力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) 属性を使用します。

### <a name="file-input---configuration"></a>ファイルの入力 - 構成

次の表は、*function.json* ファイルと `OneDrive` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - ファイルの関数コードで使用される変数名。 「[コードから OneDrive ファイルの入力バインドを使用する](#onedrive-input-code)」をご覧ください。|
|**type**||必須 - `onedrive` に設定する必要があります。|
|**direction**||必須 - `in` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId**|**UserId**  |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|**パス**|必須 - OneDrive でのファイルへのパスです。|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>ファイルの入力 - 使用方法

このバインドには、Azure AD の次のアクセス許可が必要です。

|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルの読み取り|

バインドは、.NET 関数に次の種類を公開します。
- byte[]
- ストリーム
- string
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>ファイルの出力

OneDrive ファイルの出力バインドは、OneDrive に格納されているファイルの内容を読み取ります。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#file-output---example)
* [属性](#file-output---attributes)
* [構成](#file-output---configuration)
* [使用方法](#file-output---usage)

### <a name="file-output---example"></a>ファイルの出力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>ファイルの出力 - C# スクリプトの例

次の例では、OneDrive に格納されているファイルに書き込みます。

*function.json* ファイルは、OneDrive の出力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、クエリ文字列からテキストを取得し、呼び出し元の OneDrive のルートにあるテキスト ファイル (前の例で FunctionsTest.txt と定義されている) にそれを書き込みます。

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>ファイルの出力 - JavaScript の例

次の例では、OneDrive に格納されているファイルに書き込みます。

*function.json* ファイルは、OneDrive の出力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript コードは、クエリ文字列からテキストを取得し、呼び出し元の OneDrive のルートにあるテキスト ファイル (上記の構成で FunctionsTest.txt と定義されている) にそれを書き込みます。

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>ファイルの出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) 属性を使用します。

### <a name="file-output---configuration"></a>ファイルの出力 - 構成

次の表は、*function.json* ファイルと `OneDrive` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - ファイルの関数コードで使用される変数名。 「[コードから OneDrive ファイルの出力バインドを使用する](#onedrive-output-code)」をご覧ください。|
|**type**||必須 - `onedrive` に設定する必要があります。|
|**direction**||必須 - `out` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**UserId** |**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|**パス**|必須 - OneDrive でのファイルへのパスです。|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>ファイルの出力 - 使用方法

このバインドには、Azure AD の次のアクセス許可が必要です。

|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルへのフル アクセスを持つ|

バインドは、.NET 関数に次の種類を公開します。
- byte[]
- ストリーム
- string
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook の出力

Outlook メッセージの出力バインドは、Outlook でメール メッセージを送信します。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#outlook-output---example)
* [属性](#outlook-output---attributes)
* [構成](#outlook-output---configuration)
* [使用方法](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook の出力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook の出力 - C# スクリプトの例

次の例では、Outlook で電子メールを送信します。

*function.json* ファイルは、Outlook メッセージの出力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードでは、クエリ文字列で指定された受信者に、呼び出し元からメールが送信されます。

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook の出力 - JavaScript の例

次の例では、Outlook で電子メールを送信します。

*function.json* ファイルは、Outlook メッセージの出力バインドを使用して HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

JavaScript コードでは、クエリ文字列で指定された受信者に、呼び出し元からメールが送信されます。

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook の出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) 属性を使用します。

### <a name="outlook-output---configuration"></a>Outlook の出力 - 構成

次の表は、*function.json* ファイルと `Outlook` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**||必須 - `outlook` に設定する必要があります。|
|**direction**||必須 - `out` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId**|**UserId**  |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook の出力 - 使用方法

このバインドには、Azure AD の次のアクセス許可が必要です。

|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザーとしてのメールの送信|

バインドは、.NET 関数に次の種類を公開します。
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)






## <a name="webhooks"></a>Webhooks

Webhook を使用すると、Microsoft Graph でのイベントに応答できます。 Webhook をサポートするには、_webhook サブスクリプション_ の作成、更新、および対応のための関数が必要です。 webhook の完全なソリューションには、次のバインドの組み合わせが必要です。
- [Microsoft Graph の webhook トリガー](#webhook-trigger)を使用すると、受信した webhook に応答できます。
- [Microsoft Graph webhook サブスクリプションの入力バインド](#webhook-input)を使用すると、既存のサブスクリプションを一覧表示し、必要に応じて更新できます。
- [Microsoft Graph webhook サブスクリプションの出力バインド](#webhook-output)を使用すると、webhook サブスクリプションを作成または削除できます。

バインド自体には Azure AD のアクセス許可は必要ありませんが、対応するリソースの種類に関連した、アクセス許可を要求する必要がある場合があります。 それぞれのリソースの種類で必要なアクセス許可の一覧については、[サブスクリプションのアクセス許可](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0)をご覧ください。

webhook について詳しくは、「[Microsoft Graph の Webhooks での作業]」をご覧ください。





## <a name="webhook-trigger"></a>webhook トリガー

Microsoft Graph webhook トリガーを使用すると、関数は、Microsoft Graph から受信した webhook に応答できます。 このトリガーの各インスタンスは、それぞれ Microsoft Graph リソースの 1 つの種類に応答できます。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#webhook-trigger---example)
* [属性](#webhook-trigger---attributes)
* [構成](#webhook-trigger---configuration)
* [使用方法](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>webhook トリガー - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>webhook トリガー - C# スクリプトの例

次の例では、Outlook の受信メッセージの webhook を処理します。 webhook トリガーを使用するには、[サブスクリプションを作成](#webhook-output---example)して、期限切れにならないように[サブスクリプションを更新](#webhook-subscription-refresh)します。

*function.json* ファイルは、webhook トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードでは、受信メール メッセージに応答し、受信者によって送信された、件名に "Azure Functions" が含まれるメール メッセージの本文がログに記録されます。

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>webhook トリガー - JavaScript の例

次の例では、Outlook の受信メッセージの webhook を処理します。 webhook トリガーを使用するには、[サブスクリプションを作成](#webhook-output---example)して、期限切れにならないように[サブスクリプションを更新](#webhook-subscription-refresh)します。

*function.json* ファイルは、webhook トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

JavaScript コードでは、受信メール メッセージに応答し、受信者によって送信された、件名に "Azure Functions" が含まれるメール メッセージの本文がログに記録されます。

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>webhook トリガー - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) 属性を使用します。

### <a name="webhook-trigger---configuration"></a>webhook トリガー - 構成

次の表は、*function.json* ファイルと `GraphWebhookTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**||必須 - `graphWebhook` に設定する必要があります。|
|**direction**||必須 - `trigger` に設定する必要があります。|
|**resourceType**|**ResourceType**|必須 - この関数が webhook に応答する必要がある、グラフ リソースです。 次のいずれかの値を指定できます。<ul><li><code>#Microsoft.Graph.Message</code> - Outlook メッセージに行われた変更です。</li><li><code>#Microsoft.Graph.DriveItem</code> - OneDrive ルート項目に行われた変更です。</li><li><code>#Microsoft.Graph.Contact</code> - Outlook の個人用連絡先に行われた変更です。</li><li><code>#Microsoft.Graph.Event</code> - Outlook の予定表アイテムに行われた変更です。</li></ul>|

> [!Note]
> 関数アプリは、指定された `resourceType` 値に対して登録されている関数を、1 つだけ持つことができます。

### <a name="webhook-trigger---usage"></a>webhook トリガー - 使用方法

バインドは、.NET 関数に次の種類を公開します。
- リソースの種類に関連がある、Microsoft Graph の SDK の種類 (`Microsoft.Graph.Message`、`Microsoft.Graph.DriveItem` など)
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>webhook の入力

Microsoft Graph webhook の入力バインドを使用すると、この関数アプリで管理されているサブスクリプションの一覧を取得できます。 バインドは関数アプリの記憶域から読み取りを行うため、アプリの外部から作成されたその他のサブスクリプションは反映されません。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#webhook-input---example)
* [属性](#webhook-input---attributes)
* [構成](#webhook-input---configuration)
* [使用方法](#webhook-input---usage)

### <a name="webhook-input---example"></a>webhook の入力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>webhook の入力 - C# スクリプトの例

次の例では、呼び出し元のユーザーのすべてのサブスクリプションを取得し、それらを削除します。

*function.json* ファイルは、サブスクリプションの入力バインドと、削除アクションを使用するサブスクリプションの出力バインドを使用して、HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、サブスクリプションを取得し、それらを削除します。

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>webhook の入力 - JavaScript の例

次の例では、呼び出し元のユーザーのすべてのサブスクリプションを取得し、それらを削除します。

*function.json* ファイルは、サブスクリプションの入力バインドと、削除アクションを使用するサブスクリプションの出力バインドを使用して、HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript コードは、サブスクリプションを取得した後に、それらを削除します。

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>webhook の入力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) 属性を使用します。

### <a name="webhook-input---configuration"></a>webhook の入力 - 構成

次の表は、*function.json* ファイルと `GraphWebhookSubscription` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**||必須 - `graphWebhookSubscription` に設定する必要があります。|
|**direction**||必須 - `in` に設定する必要があります。|
|**filter**|**Filter**| `userFromRequest` に設定すると、バインドは呼び出し元のユーザーが所有するサブスクリプションのみを取得します ([HTTP トリガー]でのみ有効)。| 

### <a name="webhook-input---usage"></a>webhook の入力 - 使用方法

バインドは、.NET 関数に次の種類を公開します。
- string[]
- カスタム オブジェクトの種類の配列
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>webhook の出力

webhook サブスクリプションの出力バインドを使用すると、Microsoft Graph で webhook サブスクリプションを作成、削除、および更新できます。

このセクションには、次のようにいくつかのサブセクションがあります。

* [例](#webhook-output---example)
* [属性](#webhook-output---attributes)
* [構成](#webhook-output---configuration)
* [使用方法](#webhook-output---usage)

### <a name="webhook-output---example"></a>webhook の出力 - 例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>webhook の出力 - C# スクリプトの例

次の例では、サブスクリプションを作成します。 期限切れにならないように[サブスクリプションを更新](#webhook-subscription-refresh)できます。

*function.json* ファイルは、作成アクションを使用するサブスクリプションの出力バインドを使用して、HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、呼び出し元のユーザーが Outlook メッセージを受信するとこの関数アプリに通知する、webhook を登録します。

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>webhook の出力 - JavaScript の例

次の例では、サブスクリプションを作成します。 期限切れにならないように[サブスクリプションを更新](#webhook-subscription-refresh)できます。

*function.json* ファイルは、作成アクションを使用するサブスクリプションの出力バインドを使用して、HTTP トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript コードは、呼び出し元のユーザーが Outlook メッセージを受信するとこの関数アプリに通知する、webhook を登録します。

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>webhook の出力 - 属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) 属性を使用します。

### <a name="webhook-output---configuration"></a>webhook の出力 - 構成

次の表は、*function.json* ファイルと `GraphWebhookSubscription` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**name**||必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**||必須 - `graphWebhookSubscription` に設定する必要があります。|
|**direction**||必須 - `out` に設定する必要があります。|
|**identity**|**ID**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId**|**UserId**  |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|**UserToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**action**|**アクション**|必須 - バインドが実行する必要があるアクションを指定します。 次のいずれかの値を指定できます。<ul><li><code>create</code> - 新しいサブスクリプションを登録します。</li><li><code>delete</code> - 指定したサブスクリプションを削除します。</li><li><code>refresh</code> - 指定したサブスクリプションが期限切れにならないように更新します。</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|_action_ が `create` に設定された場合にのみ必要です。 変更を監視する Microsoft Graph リソースを指定します。 「[Microsoft Graph の Webhooks での作業]」をご覧ください。 |
|**changeType**|**ChangeType**|_action_ が `create` に設定された場合にのみ必要です。 サブスクライブしているリソースで、通知が表示される変更の種類を表します。 サポートされる値は `created`、`updated`、`deleted` です。 コンマ区切りのリストを使用して複数の値を組み合わせることができます。|

### <a name="webhook-output---usage"></a>webhook の出力 - 使用方法

バインドは、.NET 関数に次の種類を公開します。
- string
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>webhook サブスクリプションの更新

サブスクリプションを更新するには、次の 2 つの方法があります。

- アプリケーション ID を使用して、すべてのアプリケーションを処理します。 この場合、Azure Active Directory 管理者から同意を得る必要があります。この方法は、Azure Functions でサポートされているすべての言語で使用できます。
- 各ユーザー ID を手動でバインドすることで、各サブスクリプションに関連付けられている ID を使用します。 この場合、バインドを実行するためにカスタム コードが必要になります。 この方法は、.NET 関数でのみ使用できます。

このセクションでは、それぞれの方法の例を示します。

* [アプリケーション ID の例](#webhook-subscription-refresh---app-identity-example)
* [ユーザー ID の例](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>webhook サブスクリプションの更新 - アプリケーション ID の例

言語固有の例をご覧ください。

* [C# スクリプト (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>アプリケーション ID の更新 - C# スクリプトの例

次の例では、アプリケーション ID を使用してサブスクリプションを更新します。

*function.json* ファイルは、サブスクリプションの入力バインドとサブスクリプションの出力バインドを使用して、タイマー トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、サブスクリプションを更新します。

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>アプリケーション ID の更新 - C# スクリプトの例

次の例では、アプリケーション ID を使用してサブスクリプションを更新します。

*function.json* ファイルは、サブスクリプションの入力バインドとサブスクリプションの出力バインドを使用して、タイマー トリガーを定義します。

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

JavaScript コードは、サブスクリプションを更新します。

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>webhook サブスクリプションの更新 - ユーザー ID の例

次の例では、ユーザー ID を使用してサブスクリプションを更新します。

*function.json* ファイルは、タイマー トリガーを定義し、関数コードへのサブスクリプションの入力バインドを延期します。

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードは、各ユーザーの ID を使用してサブスクリプションを更新し、出力バインドをコードで作成します。

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

[HTTP トリガー]: functions-bindings-http-webhook.md
[Microsoft Graph の Webhooks での作業]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
