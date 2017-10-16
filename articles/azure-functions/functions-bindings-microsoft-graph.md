---
title: "Azure Functions における Microsoft Graph のバインド | Microsoft Docs"
description: "Azure Functions で Microsoft Graph のトリガーとバインドを使用する方法について説明します。"
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0eb0ac63c7dbb9d6cbba093937231e93670529e
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-functions-microsoft-graph-bindings"></a>Azure Functions における Microsoft Graph のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Microsoft Graph のトリガーとバインドを構成および操作する方法について説明します。
これらと Azure Functions を使用して、[Microsoft Graph](https://graph.microsoft.io) からのデータ、分析情報、およびイベントを処理できます。

Microsoft Graph の拡張機能には、次のバインドが用意されています。
- [認証トークンの入力バインド](#token-input)では、任意の Microsoft Graph API とやり取りできます。
- [Excel テーブルの入力バインド](#excel-input)では、Excel からデータを読み取れます。
- [Excel テーブルの出力バインド](#excel-output)では、Excel データを変更できます。
- [OneDrive ファイルの入力バインド](#onedrive-input)では、OneDrive からファイルを読み取れます。
- [OneDrive ファイルの出力バインド](#onedrive-output)では、OneDrive のファイルに書き込むことができます。
- [Outlook メッセージの出力バインド](#outlook-output)では、Outlook で電子メールを送信できます。
- [Microsoft Graph の webhook トリガーとバインド](#webhooks)のコレクションを使用すると、Microsoft Graph からのイベントに応答できます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Microsoft Graph のバインドは、現在プレビューの段階です。

## <a name="setting-up-the-extensions"></a>拡張機能の設定

Microsoft Graph のバインドは、_バインド拡張機能_から入手できます。 バインド拡張機能は、Azure Functions ランタイム向けのオプション コンポーネントです。 このセクションでは、Microsoft Graph と認証トークンの拡張機能を設定する方法について説明します。

### <a name="enabling-functions-20-preview"></a>Functions 2.0 プレビューを有効にする

バインド拡張機能は、Azure Functions 2.0 プレビューでのみ使用できます。 Functions 2.0 を有効にするには、`FUNCTIONS_EXTENSION_VERSION` のアプリケーション設定を "ベータ" に設定します。  アプリケーション設定を構成する方法については、[Azure Functions のアプリケーション設定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)に関するページをご覧ください。

### <a name="installing-the-extension"></a>拡張機能のインストール

Azure Portal から拡張機能をインストールするには、それを参照するテンプレートまたはバインドのいずれかに移動する必要があります。 新しい関数を作成し、[テンプレートの選択] 画面で、"Microsoft Graph" のシナリオを選択します。 このシナリオからテンプレートを 1 つ選択します。 または、既存の関数の [統合] タブに移動して、このトピックで説明するバインドを 1 つ選択できます。

どちらの場合も、インストールする拡張機能の指定に関する警告が表示されます。 **[インストール]** をクリックして拡張機能を取得します。

> [!Note] 
> それぞれの拡張機能は、関数アプリごとに 1 回のみインストールする必要があります。 ポータル内のインストール プロセスは、従量課金プランで最大 10 分かかります。

Visual Studio を使用している場合は、次の NuGet パッケージをインストールして、拡張機能を取得できます。
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>App Service の認証および承認の構成

このトピックで説明するバインドでは、ID を使用する必要があります。 これにより、Microsoft Graph でアクセス許可と監査の相互作用を適用できます。 ID には、アプリケーションにアクセスするユーザー、またはアプリケーションそのものを指定できます。 この ID を構成するには、Azure Active Directory で [App Service の認証および承認](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)を設定する必要があります。 また、関数で必要なリソースのアクセス許可を要求する必要があります。

> [!Note] 
> Microsoft Graph の拡張機能は、AAD 認証のみをサポートします。 ユーザーは、職場または学校のアカウントでログインする必要があります。

Azure Portal を使用している場合、拡張機能のインストールを求めるメッセージの下に、App Service の認証および承認の構成と、テンプレートまたはバインドで必要なアクセス許可の要求を求める警告が表示されます。 必要に応じて、**[AAD を今すぐ構成]** または **[アクセス許可を今すぐ追加]** をクリックします。






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>認証トークンの入力バインド

このバインドは、特定のリソースに対する AAD トークンを取得し、コードに文字列として表示します。 アプリケーションがアクセス許可を持つ任意のリソースを指定できます。 

### <a name="configuring-an-auth-token-input-binding"></a>認証トークンの入力バインドの構成

バインド自体には AAD のアクセス許可は必要ありませんが、トークンの使用方法によっては、追加のアクセス許可を要求する必要がある場合があります。 トークンを使用してアクセスするリソースの要件を確認してください。

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - 認証トークンの関数コードで使用される変数名。 「[コードから認証トークンの入力バインドを使用する](#token-input-code)」をご覧ください。|
|**type**|必須 - `token` に設定する必要があります。|
|**direction**|必須 - `in` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**resource**|必須 - トークンが要求される AAD リソース URL です。|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>コードから認証トークンの入力バインドを使用する

トークンは、常に文字列としてコードに表示されます。

#### <a name="sample-getting-user-profile-information"></a>例: ユーザー プロファイル情報の取得

トークンの入力バインドを使用して HTTP トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、トークンを使用して Microsoft Graph への HTTP 呼び出しを行い、結果を返します。

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

次の JS サンプルでは、トークンを使用して Microsoft Graph への HTTP 呼び出しを行い、結果を返します。 上記の `function.json` で、まず `$return` を `res` に変更します。

```js
const rp = require('request-promise');

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





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Excel テーブルの入力バインド

このバインドは、OneDrive に格納されている Excel テーブルの内容を読み取ります。

### <a name="configuring-an-excel-table-input-binding"></a>Excel テーブルの入力バインドの構成

このバインドには、AAD の次のアクセス許可が必要です。
|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルの読み取り|

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - Excel テーブルの関数コードで使用される変数名。 「[コードから Excel テーブルの入力バインドを使用する](#excel-input-code)」をご覧ください。|
|**type**|必須 - `excel` に設定する必要があります。|
|**direction**|必須 - `in` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|必須 - OneDrive での Excel ブックへのパスです。|
|**worksheetName**|テーブルが検出されたワークシートです。|
|**tableName**|テーブルの名前。 指定しないと、ワークシートの内容が使用されます。|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>コードから Excel テーブルの入力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- string[][]
- Microsoft.Graph.WorkbookTable
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)

#### <a name="sample-reading-an-excel-table"></a>例: Excel テーブルの読み取り

Excel の入力バインドを使用して HTTP トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、指定したテーブルの内容を読み取って、ユーザーに返します。

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

次の JS のサンプルでは、指定したテーブルの内容を読み取って、ユーザーに返します。 上記の `function.json` で、まず `$return` を `res` に変更します。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Excel テーブルの出力バインド

このバインドは、OneDrive に格納されている Excel テーブルの内容を変更します。

### <a name="configuring-an-excel-table-output-binding"></a>Excel テーブルの出力バインドの構成

このバインドには、AAD の次のアクセス許可が必要です。
|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルへのフル アクセスを持つ|

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - 認証トークンの関数コードで使用される変数名。 「[コードから Excel テーブルの出力バインドを使用する](#excel-output-code)」をご覧ください。|
|**type**|必須 - `excel` に設定する必要があります。|
|**direction**|必須 - `out` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|必須 - OneDrive での Excel ブックへのパスです。|
|**worksheetName**|テーブルが検出されたワークシートです。|
|**tableName**|テーブルの名前。 指定しないと、ワークシートの内容が使用されます。|
|**updateType**|必須 - テーブルに対して行われる変更の種類です。 次のいずれかの値を指定できます。<ul><li><code>update</code> - OneDrive 内のテーブルの内容を置き換えます。</li><li><code>append</code> - 新しい行を作成することで、OneDrive 内のテーブルの末尾にペイロードを追加します。</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>コードから Excel テーブルの出力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)

#### <a name="sample-adding-rows-to-an-excel-table"></a>例: Excel テーブルへの行の追加

Excel の出力バインドを使用して HTTP トリガーを定義する、次の function.json があるとします。

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


次の C# のサンプルでは、クエリ文字列からの入力に基づいて、テーブル (列が 1 つであると仮定) に新しい行を追加します。

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
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

次の JS のサンプルでは、クエリ文字列からの入力に基づいて、テーブル (列が 1 つであると仮定) に新しい行を追加します。 上記の `function.json` で、まず `$return` を `res` に変更します。

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>OneDrive ファイルの入力バインド

このバインドは、OneDrive に格納されているファイルの内容を読み取ります。

### <a name="configuring-a-onedrive-file-input-binding"></a>OneDrive ファイルの入力バインドの構成

このバインドには、AAD の次のアクセス許可が必要です。
|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルの読み取り|

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - ファイルの関数コードで使用される変数名。 「[コードから OneDrive ファイルの入力バインドを使用する](#onedrive-input-code)」をご覧ください。|
|**type**|必須 - `onedrive` に設定する必要があります。|
|**direction**|必須 - `in` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|必須 - OneDrive でのファイルへのパスです。|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>コードから OneDrive ファイルの入力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- byte[]
- ストリーム
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>例: OneDrive からのファイルの読み取り

OneDrive の入力バインドを使用して HTTP トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、クエリ文字列で指定されたファイルを読み取って、その長さをログに記録します。

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

次の JS のサンプルでは、クエリ文字列で指定されたファイルを読み取って、その長さを返します。 上記の `function.json` で、まず `$return` を `res` に変更します。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>OneDrive ファイルの出力バインド

このバインドは、OneDrive に格納されているファイルの内容を変更します。

### <a name="configuring-a-onedrive-file-output-binding"></a>OneDrive ファイルの出力バインドの構成

このバインドには、AAD の次のアクセス許可が必要です。
|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザー ファイルへのフル アクセスを持つ|

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - ファイルの関数コードで使用される変数名。 「[コードから OneDrive ファイルの出力バインドを使用する](#onedrive-output-code)」をご覧ください。|
|**type**|必須 - `onedrive` に設定する必要があります。|
|**direction**|必須 - `out` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**path**|必須 - OneDrive でのファイルへのパスです。|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>コードから OneDrive ファイルの出力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- byte[]
- ストリーム
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>例: OneDrive 内のファイルへの書き込み

OneDrive の出力バインドを使用して HTTP トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、クエリ文字列からテキストを取得し、呼び出し元の OneDrive のルートにあるテキスト ファイル (上記の構成で FunctionsTest.txt と定義されている) にそれを書き込みます。

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
次の JS のサンプルでは、クエリ文字列からテキストを取得し、呼び出し元の OneDrive のルートにあるテキスト ファイル (上記の構成で FunctionsTest.txt と定義されている) にそれを書き込みます。 上記の `function.json` で、まず `$return` を `res` に変更します。

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Outlook メッセージの出力バインド

Outlook でメール メッセージを送信します。

### <a name="configuring-an-outlook-message-output-binding"></a>Outlook メッセージの出力バインドの構成

このバインドには、AAD の次のアクセス許可が必要です。
|リソース|アクセス許可|
|--------|--------|
|Microsoft Graph|ユーザーとしてのメールの送信|

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**|必須 - `outlook` に設定する必要があります。|
|**direction**|必須 - `out` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>コードから Outlook メッセージの出力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)

#### <a name="sample-sending-an-email-through-outlook"></a>例: Outlook で電子メールを送信する

Outlook メッセージの出力バインドを使用して HTTP トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、クエリ文字列で指定された受信者に、呼び出し元からメールが送信されます。

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
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

次の JS のサンプルでは、クエリ文字列で指定された受信者に、呼び出し元からメールが送信されます。

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





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Microsoft Graph の webhook バインド

Webhook を使用すると、Microsoft Graph でのイベントに応答できます。 Webhook をサポートするには、_webhook サブスクリプション_の作成、更新、および対応のための関数が必要です。 Webhook の完全なソリューションには、次のバインドの組み合わせが必要です。
- [Microsoft Graph の webhook トリガー](#webhook-trigger)を使用すると、受信した webhook に応答できます。
- [Microsoft Graph webhook サブスクリプションの入力バインド](#webhook-input)を使用すると、既存のサブスクリプションを一覧表示し、必要に応じて更新できます。
- [Microsoft Graph webhook サブスクリプションの出力バインド](#webhook-output)を使用すると、webhook サブスクリプションを作成または削除できます。

バインド自体には AAD のアクセス許可は必要ありませんが、対応するリソースの種類に関連した、アクセス許可を要求する必要がある場合があります。 それぞれのリソースの種類で必要なアクセス許可の一覧については、[サブスクリプションのアクセス許可](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions)をご覧ください。

Webhook について詳しくは、「[Microsoft Graph の Webhooks での作業]」をご覧ください。





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Microsoft Graph の webhook トリガー

このトリガーを使用すると、関数は、Microsoft Graph から受信した webhook に応答できます。 このトリガーの各インスタンスは、それぞれ Microsoft Graph リソースの 1 つの種類に応答できます。

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Microsoft Graph webhook トリガーの構成

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**|必須 - `graphWebhook` に設定する必要があります。|
|**direction**|必須 - `trigger` に設定する必要があります。|
|**resourceType**|必須 - この関数が webhook に応答する必要がある、グラフ リソースです。 次のいずれかの値を指定できます。<ul><li><code>#Microsoft.Graph.Message</code> - Outlook メッセージに行われた変更です。</li><li><code>#Microsoft.Graph.DriveItem</code> - OneDrive ルート項目に行われた変更です。</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> 関数アプリは、指定された `resourceType` 値に対して登録されている関数を、1 つだけ持つことができます。

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>コードから Microsoft Graph webhook トリガーを使用する

バインドは、.NET 関数に次の種類を公開します。
- リソースの種類に関連がある、Microsoft Graph の SDK の種類 (例: Microsoft.Graph.Message、Microsoft.Graph.DriveItem)
- カスタム オブジェクトの種類 (構造的なモデル バインドを使用)

このバインドのコードでの使用例については、「[Microsoft Graph の webhook サンプル](#webhook-samples)」をご覧ください。



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Microsoft Graph webhook サブスクリプションの入力バインド

このバインドを使用すると、この関数アプリで管理されているサブスクリプションの一覧を取得できます。 バインドは関数アプリの記憶域から読み取りを行いますが、アプリの外部から作成されたその他のサブスクリプションは反映されません。

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Microsoft Graph webhook サブスクリプションの入力バインドの構成

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**|必須 - `graphWebhookSubscription` に設定する必要があります。|
|**direction**|必須 - `in` に設定する必要があります。|
|**filter**| `userFromRequest` に設定すると、バインドは呼び出し元のユーザーが所有するサブスクリプションのみを取得します ([HTTP トリガー]でのみ有効)。| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>コードから Microsoft Graph webhook サブスクリプションの入力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- string[]
- カスタム オブジェクトの種類の配列
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

このバインドのコードでの使用例については、「[Microsoft Graph の webhook サンプル](#webhook-samples)」をご覧ください。


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Microsoft Graph webhook サブスクリプションの出力バインド

このバインドを使用すると、Microsoft Graph で webhook サブスクリプションを作成、削除、および更新できます。

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Microsoft Graph webhook サブスクリプションの出力バインドの構成

バインドでは以下のプロパティがサポートされています。

|プロパティ|Description|
|--------|--------|
|**name**|必須 - メール メッセージの関数コードで使用される変数名。 「[コードから Outlook メッセージの出力バインドを使用する](#outlook-output-code)」をご覧ください。|
|**type**|必須 - `graphWebhookSubscription` に設定する必要があります。|
|**direction**|必須 - `out` に設定する必要があります。|
|**identity**|必須 - 操作を実行するために使用する ID です。 次のいずれかの値を指定できます。<ul><li><code>userFromRequest</code> - [HTTP トリガー]でのみ有効です。 呼び出し元ユーザーの ID を使用します。</li><li><code>userFromId</code> - 指定された ID を使用して以前ログインしたユーザーの ID を使用します。 <code>userId</code> プロパティをご覧ください。</li><li><code>userFromToken</code> - 指定したトークンによって表される ID を使用します。 <code>userToken</code> プロパティをご覧ください。</li><li><code>clientCredentials</code> - 関数アプリの ID を使用します。</li></ul>|
|**userId** |_identity_ が `userFromId` に設定された場合にのみ必要です。 以前ログインしたユーザーに関連付けられたユーザー プリンシパル ID です。|
|**userToken**|_identity_ が `userFromToken` に設定された場合にのみ必要です。 関数アプリの有効なトークンです。 |
|**action**|必須 - バインドが実行する必要があるアクションを指定します。 次のいずれかの値を指定できます。<ul><li><code>create</code> - 新しいサブスクリプションを登録します。</li><li><code>delete</code> - 指定したサブスクリプションを削除します。</li><li><code>refresh</code> - 指定したサブスクリプションが期限切れにならないように更新します。</li></ul>|
|**subscriptionResource**|_action_ が `create` に設定された場合にのみ必要です。 変更を監視する Microsoft Graph リソースを指定します。 「[Microsoft Graph の Webhooks での作業]」をご覧ください。 |
|**changeType**|_action_ が `create` に設定された場合にのみ必要です。 サブスクライブしているリソースで、通知が表示される変更の種類を表します。 サポートされる値は `created`、`updated`、`deleted` です。 コンマ区切りのリストを使用して複数の値を組み合わせることができます。|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>コードから Microsoft Graph webhook サブスクリプションの出力バインドを使用する

バインドは、.NET 関数に次の種類を公開します。
- string
- Microsoft.Graph.Subscription

このバインドのコードでの使用例については、「[Microsoft Graph の webhook サンプル](#webhook-samples)」をご覧ください。
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Microsoft Graph の webhook サンプル

#### <a name="sample-creating-a-subscription"></a>例: サブスクリプションの作成

作成アクションを使用し、サブスクリプションの出力バインドで HTTP トリガーを定義する、次の function.json があるとします。

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
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

次の C# のサンプルでは、呼び出し元のユーザーが Outlook メッセージを受信するとこの関数アプリに通知する、webhook を登録します。

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

次の JS のサンプルでは、呼び出し元のユーザーが Outlook メッセージを受信するとこの関数アプリに通知する、webhook を登録します。

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>例: 通知の処理

Outlook メッセージを処理するための、Graph の webhook トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、受信メール メッセージに応答し、受信者によって送信された、件名に "Azure Functions" が含まれるメール メッセージの本文がログに記録されます。

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

次の JS のサンプルでは、受信メール メッセージに応答し、受信者によって送信された、件名に "Azure Functions" が含まれるメール メッセージの本文がログに記録されます。

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

#### <a name="sample-deleting-subscriptions"></a>例: サブスクリプションの削除

削除アクションを使用し、サブスクリプションの入力バインドおよびサブスクリプションの出力バインドで HTTP トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、呼び出し元のユーザーのすべてのサブスクリプションを取得した後に、それらを削除します。

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

次の JS のサンプルでは、呼び出し元のユーザーのすべてのサブスクリプションを取得した後に、それらを削除します。

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

#### <a name="sample-refreshing-subscriptions"></a>例: サブスクリプションの更新

サブスクリプションを更新するには、次の 2 つの方法があります。
- アプリケーション ID を使用して、すべてのアプリケーションを処理します。 この場合、Azure Active Directory 管理者から同意を得る必要があります。この方法は、Azure Functions でサポートされているすべての言語で使用できます。
- 各ユーザー ID を手動でバインドすることで、各サブスクリプションに関連付けられている ID を使用します。 この場合、バインドを実行するためにカスタム コードが必要になります。 この方法は、.NET 関数でのみ使用できます。

次に両方の方法について説明します。

**アプリケーション ID を使用する**

アプリケーション ID を使用して、サブスクリプションの入力バインドおよびサブスクリプションの出力バインドでタイマー トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、アプリケーション ID を使用して、タイマー上のサブスクリプションを更新します。

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

次の JS のサンプルでは、アプリケーション ID を使用して、タイマー上のサブスクリプションを更新します。

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**動的なユーザー ID を使用する**

代替オプションとしては、関数コードに対するサブスクリプションの入力バインドへのバインドを延期して、タイマー トリガーを定義する、次の function.json があるとします。

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

次の C# のサンプルでは、出力バインドをコードで作成することで、各ユーザーの ID を使用して、タイマー上のサブスクリプションを更新します。
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
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
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[HTTP トリガー]: functions-bindings-http-webhook.md
[Microsoft Graph の Webhooks での作業]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

