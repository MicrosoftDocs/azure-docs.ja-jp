---
title: Azure Functions アプリの開発および構成 - Azure SignalR
description: Azure Functions と Azure SignalR Service を使用してサーバーレスのリアルタイム アプリケーションを開発し構成する方法の詳細
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858707"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure SignalR Service を使用した Azure Functions の開発と構成

Azure Functions アプリケーションは、[Azure SignalR Service バインド](../azure-functions/functions-bindings-signalr-service.md)を活用して、リアルタイム機能を追加できます。 クライアント アプリケーションは、複数の言語で使用可能なクライアント SDK を使用して、Azure SignalR Service に接続し、リアルタイム メッセージを受信します。

この記事では、SignalR Service と統合されている Azure 関数アプリを開発および構成するための概念について説明します。

## <a name="signalr-service-configuration"></a>SignalR Service の構成

Azure SignalR Service はさまざまなモードで構成できます。 Azure Functions と使用する場合、*サーバーレス* モードでサービスを構成する必要があります。

Azure portal で、SignalR Service リソースの *[設定]* ページを見つけます。 *[サービス モード]* を *[サーバーレス]* に設定します。

![SignalR Service モード](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions の開発

Azure Functions および Azure SignalR Service で構築されたサーバーレスのリアルタイム アプリケーションは、通常、2 つの Azure Functions が必要です。

* 有効な SignalR Service アクセス トークンおよびサービスエンドポイント URL を取得するためにクライアントが呼び出す「negotiate」関数
* SignalR Service からのメッセージを処理し、メッセージを送信したり、グループ メンバーシップを管理したりする 1 つ以上の関数

### <a name="negotiate-function"></a>negotiate 関数

クライアント アプリケーションでは、Azure SignalR Service に接続するために、有効なアクセス トークンが必要になります。 アクセス トークンは匿名か、または特定のユーザー ID に認証済みのものを使用できます。 サーバーレスの SignalR Service アプリケーションでは、トークンと、SignalR Service エンドポイント URL などの他の接続情報を取得するために「negotiate」という名前の HTTP エンドポイントが必要です。

HTTP によってトリガーされる Azure 関数と *SignalRConnectionInfo* 入力バインドを使用して、接続情報オブジェクトを生成します。 関数には、`/negotiate` で終わる HTTP ルートが必要です。

C# の [クラス ベース モデル](#class-based-model)では、*SignalRConnectionInfo* 入力バインドは必要なく、カスタム要求をより簡単に追加できます。 「[クラス ベース モデルでの negotiate エクスペリエンス](#negotiate-experience-in-class-based-model)」を参照してください。

negotiate 関数を作成する方法の詳細については、[*SignalRConnectionInfo* 入力バインドのリファレンス](../azure-functions/functions-bindings-signalr-service-input.md)に関するページを参照してください。

認証トークンを作成する方法の詳細については、[App Service 認証の使用](#using-app-service-authentication)に関するページを参照してください。

### <a name="handle-messages-sent-from-signalr-service"></a>SignalR Service から送信されたメッセージを処理する

SignalR Service から送信されたメッセージを処理するには、"*SignalR トリガー*" バインドを使用します。 クライアントがメッセージを送信したり、クライアントが接続または切断されたりすると、通知を受け取ることがあります。

詳細については、"[*SignalR トリガー*" バインドのリファレンス](../azure-functions/functions-bindings-signalr-service-trigger.md)を参照してください。

また、クライアントからのメッセージがあった場合に関数がサービスによってトリガーされるように、関数エンドポイントをアップストリームとして構成する必要もあります。 アップストリームを構成する方法の詳細については、この[ドキュメント](concept-upstream.md)を参照してください。

### <a name="sending-messages-and-managing-group-membership"></a>メッセージの送信とグループ メンバーシップの管理

*SignalR* 出力バインドを使用して、Azure SignalR Service に接続したクライアントにメッセージを送信します。 すべてのクライアントにメッセージをブロードキャストすることも、特定のユーザー ID で認証されたか、特定のグループに追加されたクライアントのサブセットにそれらを送信することもできます。

ユーザーは、1 つ以上のグループに追加できます。 *SignalR* 出力バインドを使用して、グループに対してユーザーを追加または削除することもできます。

詳細については、[*SignalR* 出力バインド リファレンス](../azure-functions/functions-bindings-signalr-service-output.md)に関するページを参照してください。

### <a name="signalr-hubs"></a>SignalR Hubs

SignalR には「ハブ」の概念があります。 各クライアント接続と Azure Functions から送信される各メッセージは、特定のハブに範囲指定されます。 接続およびメッセージを論理名前空間に分割する方法としてハブを使用できます。

## <a name="class-based-model"></a>クラス ベース モデル

クラス ベース モデルは C# 専用です。 クラス ベース モデルでは、一貫性のある SignalR サーバー側プログラミング エクスペリエンスを得ることができます。 これには、次の機能があります。

* 構成作業が少ない:クラス名は `HubName` として使用され、メソッド名は `Event` として使用され、`Category` はメソッド名にしたがって自動的に決定されます。
* 自動パラメーター バインディング: `ParameterNames` も属性 `[SignalRParameter]` も必要ありません。 パラメーターは、Azure Function メソッドの引数に順番に自動的にバインドされます。
* 便利な出力と negotiate エクスペリエンス。

次のコードは、これらの機能を示しています。

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

クラス ベース モデルを利用するすべての関数は、**ServerlessHub** から継承するクラスのメソッドである必要があります。 サンプルのクラス名 `SignalRTestHub` はハブ名です。

### <a name="define-hub-method"></a>ハブ メソッドを定義する

すべてのハブ メソッドには、`[SignalRTrigger]` 属性で装飾された `InvocationContext` の引数が必要であり、パラメーターなしのコンストラクターを使用する **必要があります**。 **メソッド名** は、パラメーター **イベント** として扱われます。

既定では、メソッド名以外の `category=messages` は次のいずれかの名前になります。

* **OnConnected**: `category=connections, event=connected` として扱われます
* **OnDisconnected**: `category=connections, event=disconnected` として扱われます

### <a name="parameter-binding-experience"></a>パラメーター バインディング エクスペリエンス

クラス ベース モデルでは、次の状況を除き、すべての引数は既定で `[SignalRParameter]` としてマークされているため、`[SignalRParameter]` は必要ありません。

* 引数が、バインド属性によって修飾されている。
* 引数の型が `ILogger` または `CancellationToken` である。
* 引数が属性 `[SignalRIgnore]` によって修飾されている。

### <a name="negotiate-experience-in-class-based-model"></a>クラス ベース モデルでの negotiate エクスペリエンス

SignalR の入力バインド `[SignalR]` を使用する代わりに、クラス ベース モデルでのネゴシエーションをより柔軟に行うことができます。 基底クラス `ServerlessHub` にはメソッドがあります。

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

この機能のユーザーは、関数の実行中に `userId` または `claims` をカスタマイズします。

## <a name="use-signalrfilterattribute"></a>`SignalRFilterAttribute` を使用します

ユーザーは、抽象クラス `SignalRFilterAttribute` を継承して実装できます。 `FilterAsync` で例外がスローされると、`403 Forbidden` がクライアントに返されます。

次のサンプルは、`admin` が `broadcast` を呼び出すことだけが許可される顧客フィルターを実装する方法を示しています。

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

属性を利用して関数を承認します。

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>クライアント開発

SignalR クライアント アプリケーションでは、複数の言語のいずれかで SignalR クライアント SDK を活用して、簡単に Azure SignalR Service に接続してメッセージを受信できます。

### <a name="configuring-a-client-connection"></a>クライアント接続の構成

SignalR Service に接続するには、クライアントで、以下の手順から構成される正常な接続ネゴシエーションを完了する必要があります。

1. 有効な接続情報を取得するために上記で説明した *negotiate* HTTP エンドポイントに要求を行います
1. *negotiate* エンドポイントから取得したサービス エンドポイント URL およびアクセス トークンを使用して、SignalR Service に接続します

SignalR クライアント SDK には、ネゴシエーション ハンドシェイクを実行するために必要なロジックが既に含まれます。 `negotiate` セグメントを除いた、negotiate エンドポイントの URL を SDK の `HubConnectionBuilder` に渡します。 次に JavaScript の例を示します。

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

慣例により、SDK が `/negotiate` を URL に自動的に付加し、ネゴシエーションを開始するために使用します。

> [!NOTE]
> ブラウザーで JavaScript/TypeScript SDK を使用している場合、Function App 上で[クロス オリジン リソース共有 (CORS) を有効にする](#enabling-cors)必要があります。

SignalR クライアント SDK を使用する方法の詳細については、言語に応じたドキュメントを参照してください。

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>クライアントからサービスへのメッセージの送信

SignalR リソースに対して[アップストリーム](concept-upstream.md)を構成している場合は、任意の SignalR クライアントを使用して、クライアントから Azure Functions にメッセージを送信できます。 次に JavaScript の例を示します。

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Functions の構成

Azure SignalR Service と統合する Azure 関数アプリは、[継続的なデプロイ](../azure-functions/functions-continuous-deployment.md)、[zip デプロイ](../azure-functions/deployment-zip-push.md)、[パッケージから実行](../azure-functions/run-functions-from-deployment-package.md)などのテクニックを使用して、一般的な Azure 関数アプリのようにデプロイできます。

ただし、SignalR Service のバインドを使用するアプリに対する、いくつかの特別な考慮事項があります。 クライアントがブラウザーで実行する場合、CORS が有効になっている必要があります。 また、アプリが認証を必要とする場合、negotiate エンドポイントを Azure App Service の認証と統合できます。

### <a name="enabling-cors"></a>CORS の有効化

JavaScript/TypeScript クライアントは、接続ネゴシエーションを開始するように、negotiate 関数に HTTP 要求を行います。 クライアント アプリケーションは、Azure 関数アプリと異なるドメイン上でホストされる場合、関数アプリでクロス オリジン リソース共有 (CORS) が有効になっている必要があるか、ブラウザーは要求をブロックします。

#### <a name="localhost"></a>Localhost

関数アプリをローカル コンピューター上で実行する場合、`Host` セクションを *local.settings.json* に追加して CORS を有効にできます。 `Host` セクションで、次の 2 つのプロパティを追加します。

* `CORS` - 元のクライアントアプリケーションであるベース URL を入力します
* `CORSCredentials` - `true` に設定して、「withCredentials」要求を許可します

例:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>クラウド - Azure Functions CORS

Azure 関数アプリ上で CORS を有効にするには、Azure portal の関数アプリの *[プラットフォーム機能]* タブの下にある CORS 構成画面に移動します。

> [!NOTE]
> CORS の構成は、Azure Functions Linux 従量課金プランではまだ使用できません。 [Azure API Management](#cloud---azure-api-management) を使用して、CORS を有効にします。

SignalR クライアントが negotiate 関数を呼び出すために、Access-Control-Allow-Credentials を持つ CORS を有効にする必要があります。 有効にするにはチェックボックスをオンにします。

*[許可されたオリジン]* セクションで、Web アプリケーションの元のベース URL を持つエントリを追加します。

![CORS の構成](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>クラウド - Azure API Management

Azure API Management は、既存のバックエンド サービスに機能を追加する API ゲートウェイを提供します。 これを使用して、CORS を関数アプリに追加することができます。 アクションごとの支払い価格と月あたりの無料提供を含む従量課金レベルを提供します。

[Azure 関数アプリをインポートする](../api-management/import-function-app-as-api.md)方法については、API Management のドキュメントを参照してください。 インポートした後、インバウンド ポリシーを追加して、Access-Control-Allow-Credentials をサポートする CORS を有効にすることができます。

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

API Management URL を使用するように SignalR クライアントを構成します。

### <a name="using-app-service-authentication"></a>App Service 認証の使用

Azure Functions には認証が組み込まれており、Facebook、Twitter、Microsoft アカウント、Google、Azure Active Directory などの一般的なプロバイダーをサポートしています。 この機能を *SignalRConnectionInfo* バインドと統合して、ユーザー ID に認証された Azure SignalR Service への接続を作成できます。 アプリケーションは、そのユーザー ID を対象とする *SignalR* 出力バインドを使用してメッセージを送信できます。

Azure portal の関数アプリの *[プラットフォーム機能]* タブで、 *[認証/承認]* 設定ウィンドウを開きます。 [[App Service 認証]](../app-service/overview-authentication-authorization.md) のドキュメントに従って、選択した ID プロバイダーを使用して認証を構成します。

構成すると、認証された HTTP 要求には、認証された ID のユーザー名とユーザー ID をそれぞれ含んだ `x-ms-client-principal-name` および `x-ms-client-principal-id` ヘッダーが収められます。

*SignalRConnectionInfo* バインド構成でこれらのヘッダーを使用して、認証された接続を作成できます。 次に、`x-ms-client-principal-id` ヘッダーを使用する C# negotiate 関数の例を示します。

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

この場合、SignalR メッセージの `UserId` プロパティを設定することにより、そのユーザーにメッセージを送信できます。

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

その他の言語の詳細については、Azure Functions のリファレンスの [Azure SignalR Service のバインド](../azure-functions/functions-bindings-signalr-service.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Functions を使用して、サーバーレスの SignalR Service アプリケーションを開発および構成する方法について説明しました。 [SignalR Service 概要ページ](index.yml)のクイック スタートまたはチュートリアルのいずれかを使用して自身でアプリケーションを作成してみてください。
