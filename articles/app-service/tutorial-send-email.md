---
title: 'チュートリアル: Logic Apps を使用してメールを送信する'
description: App Service アプリからビジネス プロセスを呼び出す方法について説明します。 メール、ツイート、Facebook の投稿を送信したり、メーリング リストに追加したりできます。
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95014664"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>チュートリアル:App Service からメールを送信したり他のビジネス プロセスを呼び出したりする

このチュートリアルでは、App Service アプリを各種ビジネス プロセスと統合する方法について説明します。 これは、次のような Web アプリのシナリオと共通です。

- トランザクションの確認メールを送信する
- Facebook グループにユーザーを追加する
- SAP や Salesforce などのサードパーティ製システムに接続する
- 標準的な B2B メッセージを交換する

このチュートリアルでは、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して、App Service アプリから Gmail でメールを送信します。 Web アプリからメールを送信する方法は他にもあります。たとえば、言語フレームワークによって提供される SMTP 構成などです。 ただし、Logic Apps を使用すると、コードを複雑にすることなく、App Service アプリをもっと強化できます。 Logic Apps には、最も一般的なビジネス統合用の単純な構成インターフェイスが用意されており、アプリから HTTP 要求を使用していつでも呼び出すことができます。

## <a name="prerequisite"></a>前提条件

任意の言語フレームワークを使用してアプリを App Service にデプロイします。 チュートリアルに従ってサンプル アプリをデプロイするには、以下を参照してください。

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[チュートリアル:Azure App Service での ASP.NET Core および SQL Database アプリの作成](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[チュートリアル:Azure で Node.js と MongoDB のアプリを構築する](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[チュートリアル:Azure で PHP と MySQL アプリを構築する](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[チュートリアル:Azure App Service で PostgreSQL を使用して Python (Django) Web アプリを実行する](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Azure App Service on Linux で Ruby および Postgres のアプリを構築する](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>ロジック アプリを作成する

1. [Azure portal](https://portal.azure.com) で、「[ロジック アプリを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)」の手順に従って、空のロジック アプリを作成します。 **Logic Apps デザイナー** が表示されたら、このチュートリアルに戻ります。
1. Logic Apps デザイナーのスプラッシュ ページで、 **[一般的なトリガーで開始する]** の **[HTTP 要求の受信時]** を選択します。

    ![Logic Apps Designer のスプラッシュ ページのスクリーンショット。[HTTP 要求の受信時] が強調表示されています。](./media/tutorial-send-email/receive-http-request.png)
1. **[HTTP 要求の受信時]** のダイアログで、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

    ![[HTTP 要求の受信時] ダイアログ ボックスのスクリーンショット。[サンプルのペイロードを使用してスキーマを生成する] オプションが選択されています。 ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. 次のサンプル JSON をテキスト ボックスにコピーして、 **[完了]** を選択します。

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    これで、必要な要求データに対するスキーマが生成されます。 実際には、アプリケーション コードによって生成される実際の要求データをキャプチャするだけで、Azure によって JSON スキーマが自動的に生成されます。 
1. Logic Apps デザイナーの上部にある **[保存]** を選択します。 

    HTTP 要求トリガーの URL が表示されるようになりました。 コピー アイコンを選択して、後で使用できるようにコピーしておきます。

    ![HTTP 要求トリガーの URL をコピーするためのコピー アイコンが強調表示されているスクリーンショット。](./media/tutorial-send-email/http-request-url.png)

    この HTTP 要求定義は、Gmail であるかそれ以外の何かであるかにかかわらず、このロジック アプリで実行するすべての処理のトリガーです。 後で、App Service アプリでこの URL を呼び出します。 要求トリガーの詳細については、[HTTP 要求と応答のリファレンス](../connectors/connectors-native-reqres.md)を参照してください。

1. デザイナーの下部にある **[新しいステップ]** をクリックし、アクション検索ボックスに「**Gmail**」と入力して、 **[メールの送信 (V2)]** を見つけて選択します。
    
    > [!TIP]
    > SendGrid、MailChimp、Microsoft 365、SalesForce など、他の種類の統合を検索することができます。 詳細については、[Logic Apps のドキュメント](../logic-apps/index.yml)を参照してください。

1. **[Gmail]** ダイアログで、 **[サインイン]** を選択し、メールの送信に使用する Gmail アカウントにサインインします。

    ![電子メールの送信元にする Gmail アカウントにサインインするための [Gmail] ダイアログ ボックスのスクリーンショット。](./media/tutorial-send-email/gmail-sign-in.png)

1. サインインした後、 **[To]\(対象\)** ボックス内をクリックすると、動的なコンテンツのダイアログが自動的に開かれます。

1. **[HTTP 要求の受信時]** アクションの横の **[もっと見る]** を選択します。

    ![[HTTP 要求の受信時] アクションの横にある [もっと見る] ボタンのスクリーンショット。](./media/tutorial-send-email/expand-dynamic-content.png)

    これで、前に使用したサンプル JSON データの 3 つのプロパティが表示されます。 この手順では、HTTP 要求のこれらのプロパティを使用して、メールを作成します。
1. **[To]\(対象\)** フィールドの値を選択しているため、 **[メール]** を選択します。 必要に応じて、 **[動的なコンテンツの追加]** をクリックして、動的なコンテンツのダイアログをオフに切り替えます。

    ![電子メール オプションのスクリーンショット。[動的なコンテンツの追加] オプションが強調表示されています。](./media/tutorial-send-email/hide-dynamic-content.png)

1. **[新しいパラメーターの追加]** ドロップダウンで **[件名]** と **[本文]** を選択します。

1. **[件名]** ボックス内をクリックし、同様に、 **[タスク]** を選択します。 **[件名]** ボックスにカーソルがある状態で、「*created*」と入力します。 

1. **[本文]** ボックス内をクリックし、同様に、 **[期限]** を選択します。 **[期限]** の左側にカーソルを移動し、「*This work item is due on*」と入力します。

    > [!TIP]
    > メールの本文で HTML コンテンツを直接編集する場合は、Logic Apps デザイナー ウィンドウの上部にある **[コード ビュー]** を選択します。 動的なコンテンツ コード (たとえば、`@{triggerBody()?['due']}`) が保持されていることを確認します
    >
    > ![HTML コンテンツをメール本文で直接閲覧するためのコード ビューのスクリーンショット。](./media/tutorial-send-email/edit-rich-html-email.png) 

1. 次に、HTTP トリガーに非同期 HTTP 応答を追加します。 HTTP トリガーと Gmail アクションの間の **+** 記号をクリックし、 **[並列分岐の追加]** を選択します。

    ![\+ 記号のスクリーンショット。[並列分岐の追加] オプションが強調表示されています。](./media/tutorial-send-email/add-http-response.png)

1. 検索ボックスで **応答** を検索して、**応答** アクションを選択します。

    ![検索バーのスクリーンショット。応答アクションが強調表示されています。](./media/tutorial-send-email/choose-response-action.png)

    既定では、応答アクションは HTTP 200 を送信します。 このチュートリアルでは、それで十分です。 詳細については、[HTTP 要求と応答のリファレンス](../connectors/connectors-native-reqres.md)を参照してください。

1. Logic Apps デザイナーの上部にある **[保存]** をもう一度選択します。 

## <a name="add-http-request-code-to-app"></a>アプリに HTTP 要求コードを追加する

前の手順で HTTP 要求トリガーの URL をコピーしてあることを確認します。 機密情報が含まれているため、コードに直接記述しないことをお勧めします。 代わりに、App Service ではアプリ設定を使用して、環境変数として参照できます。 

[Cloud Shell](https://shell.azure.com) で次のコマンドを使用してアプリ設定を作成します ( *\<app-name>* 、 *\<resource-group-name>* 、 *\<logic-app-url>* を置き換えます)。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

コードで、言語フレームワークで使用できる任意の HTTP クライアント言語を使用し、次の構成で URL に標準の HTTP Post を行います。

- 要求本文には、ロジック アプリに指定したものと同じ JSON 形式が含まれています。

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- 要求には、ヘッダー `Content-Type: application/json` が含まれています。 
- パフォーマンスを最適化するには、可能であれば要求を非同期的に送信します。

例を表示するには、下の該当する言語またはフレームワークのタブをクリックします。

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

ASP.NET では、[System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient) クラスを使用して HTTP Post を送信できます。 次に例を示します。

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

このコードを「[チュートリアル: SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」のサンプル アプリでテストしている場合は、そのアプリを使用して、[Create アクション](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)で、`Todo` 項目の追加後に確認メールを送信できます。 上の非同期コードを使用するには、Create アクションを非同期に変換します。

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

ASP.NET Core では、[System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient) クラスを使用して HTTP Post を送信できます。 次に例を示します。

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> このコードは、デモの簡略化を意図して記述されています。 実際には、要求ごとに `HttpClient` オブジェクトをインスタンス化しないでください。 「[IHttpClientFactory を使用して回復力の高い HTTP 要求を実装する](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)」のガイダンスに従ってください。

このコードを「[チュートリアル: Azure App Service での ASP.NET Core および SQL Database アプリの作成](tutorial-dotnetcore-sqldb-app.md)」のサンプル アプリでテストしている場合は、そのアプリを使用して、[Create アクション](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)で、`Todo` 項目の追加後に確認メールを送信できます。

### <a name="nodejs"></a>[Node.js](#tab/node)

Node.js では、[axios](https://www.npmjs.com/package/axios) のような npm パッケージを使用して、HTTP Post を簡単に送信できます。 次に例を示します。

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

このコードを「[チュートリアル: Azure で Node.js と MongoDB のアプリを構築する](tutorial-nodejs-mongodb-app.md)」のサンプル アプリでテストしている場合は、そのアプリを使用して、[create 関数](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)で、[記事が正常に保存された](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)後に確認メールを送信できます。

### <a name="php"></a>[PHP](#tab/php)

PHP では、[Guzzle](http://docs.guzzlephp.org/en/stable/index.html) を使用して、HTTP Post を簡単に送信できます。 次に例を示します。

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

このコードを「[チュートリアル: Azure で PHP と MySQL アプリを構築する](tutorial-php-mysql-app.md)」のサンプル アプリでテストしている場合は、そのアプリを使用して、[Route::post 関数](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)の return ステートメントの直前で確認メールを送信できます。

### <a name="python"></a>[Python](#tab/python)

Python では、[requests](https://pypi.org/project/requests/) を使用して、HTTP Post を簡単に送信できます。 次に例を示します。

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

このコードを「[チュートリアル: Azure App Service で PostgreSQL を使用して Python (Django) Web アプリを実行する](tutorial-python-postgresql-app.md)」のサンプル アプリでテストしている場合は、そのアプリを使用して、[Route::post 関数](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)の return ステートメントの直前で確認メールを送信できます。

### <a name="ruby"></a>[Ruby](#tab/ruby)

Ruby では、JSONClient を使用して、HTTP Post を簡単に送信できます。 次に例を示します。

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

このコードを「[Azure App Service on Linux で Ruby および Postgres のアプリを構築する](tutorial-ruby-postgres-app.md)」のサンプル アプリでテストしている場合は、そのアプリを使用して、[create](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) アクションで、[@task.save が成功した場合](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)に確認メールを送信できます。

---

## <a name="more-resources"></a>その他のリソース

[チュートリアル:Azure App Service で CORS を使用して RESTful API をホストする](app-service-web-tutorial-rest-api.md)  
[Logic Apps の HTTP 要求および応答のリファレンス](../connectors/connectors-native-reqres.md)  
[クイック スタート: Azure Logic Apps を使用して初めてのワークフローを作成する - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)