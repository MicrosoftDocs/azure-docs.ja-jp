---
title: Azure Active Directory B2C で Graph API を使用する
description: Azure AD Graph API を呼び出し、アプリケーション ID を使用してプロセスを自動化して、Azure AD B2C テナントのユーザーを管理する方法。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42378c4377057902937b718555489636bc5dcbaa
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900014"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C:Azure AD Graph API を使用する

Azure Active Directory B2C (Azure AD B2C) テナントには、数千または数百万人のユーザーが含まれている可能性があります。 これは、多くの一般的なテナント管理タスクをプログラムで実行する必要があることを意味します。 主な例にはユーザーの管理があります。

たとえば、既存のユーザー ストアを B2C テナントに移行することがあります。 その場合、自分のページでユーザー登録をホストし、バックグラウンドで Azure AD B2C ディレクトリのユーザー アカウントを作成することがあります。 この種のタスクでは、ユーザー アカウントの作成、読み取り、更新、削除を実行する機能が必要です。 このようなタスクを実行するには、Azure AD Graph API を使用します。

B2C テナントでは、主に 2 つのモードで Graph API と通信します。

* **対話型**の一度だけ実行されるタスクでは、タスクの実行時に B2C テナントの管理者アカウントとして実行する必要があります。 このモードでは、Graph API を呼び出す前に管理者が資格情報でサインインする必要があります。
* **自動化**された継続的なタスクでは、必要な権限を付与した何らかの種類のサービス アカウントを使用し、管理タスクを実行する必要があります。 Azure AD では、アプリケーションを登録して、Azure AD に認証することでそれを実行できます。 その際、 *OAuth 2.0 クライアント資格情報付与* を利用する [アプリケーション ID](../active-directory/develop/service-to-service.md)を使用します。 この場合、アプリケーションはユーザーとしてではなくアプリケーション自体として Graph API を呼び出します。

この記事では、自動化された事例を実行する方法を示します。 ユーザーの CRUD (作成、読み取り、更新、削除) 操作を実行する .NET 4.5 `B2CGraphClient` を構築します。 クライアントにはさまざまなメソッドを呼び出すことができる Windows コマンド ライン インターフェイス (CLI) を与えます。 しかし、コードは、非対話型の自動化された方法で動作するように記述されます。

>[!IMPORTANT]
> Azure AD B2C ディレクトリのユーザーを管理するには、[Azure AD Graph API](../active-directory/develop/active-directory-graph-api-quickstart.md) を使用する**必要があります**。 Azure AD Graph API は Microsoft Graph API とは異なります。 詳細については、次の MSDN ブログ記事を参照してください。「[Microsoft Graph または Azure AD Graph](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/)」。

## <a name="prerequisites"></a>前提条件

アプリケーションまたはユーザーを作成する前に、Azure AD B2C テナントが必要です。 まだお持ちでない場合は、[Azure Active Directory B2C テナント](tutorial-create-tenant.md)を作成します。

## <a name="register-an-application"></a>アプリケーションを登録する

Azure AD B2C テナントを作成したら、[Azure portal](https://portal.azure.com) を使用して管理アプリケーションを登録する必要があります。 また、自動化されたスクリプトまたは管理アプリケーションに代わって管理タスクを実行するために必要なアクセス許可を付与する必要もあります。

### <a name="register-application-in-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録する

B2C テナントで Azure AD Graph API を使用するには、Azure Active Directory のアプケーション登録ワークフローを使用してアプリケーションを登録する必要があります。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API アクセス許可を割り当てる

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>クライアント シークレットを作成する

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

これで Azure AD B2C テナントのユーザーの*作成*、*読み取り*、*更新*を実行するアクセス許可を持つアプリケーションが用意されました。 次のセクションに進み、ユーザーの*削除*および*パスワードの更新*のアクセス許可を追加します。

## <a name="add-user-delete-and-password-update-permissions"></a>ユーザーの削除とパスワードの更新のアクセス許可を追加する

以前に付与した*ディレクトリ データの読み取りと書き込み*アクセス許可に、ユーザーの削除またはパスワードの更新を行う機能は含まれて**いません**。

アプリケーションでユーザーの削除またはパスワードの更新を実行できるようにする場合、*ユーザー管理者*ロールを付与する必要はありません。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure AD B2C テナントが含まれているディレクトリに切り替えます。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** で **[ロールと管理者]** を選択します。
1. **[ユーザー管理者]** ロールを選択します。
1. **[割り当ての追加]** を選択します。
1. **[選択]** テキスト ボックスに、前に登録したアプリケーションの名前 (たとえば、*managementapp1*) を入力します。 検索結果に表示されたらアプリケーションを選択します。
1. **[追加]** を選択します。 アクセス許可が完全に反映されるまでに数分かかる場合があります。

これで、Azure AD B2C アプリケーションには、B2C テナントでユーザーの削除またはパスワードの更新を行うために必要な追加のアクセス許可が付与されました。

## <a name="get-the-sample-code"></a>サンプル コードの取得

このコード サンプルは、[Active Directory Authentication Library (ADAL)](../active-directory/develop/active-directory-authentication-libraries.md) を使用して Azure AD Graph API と対話する .NET コンソール アプリケーションです。 このコードは、API を呼び出して、Azure AD B2C テナント内のユーザーをプログラムで管理する方法を示しています。

[サンプル アーカイブ (\*.zip) をダウンロード](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)するか、GitHub リポジトリを複製することができます。

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

コード サンプルを入手したら、環境に合わせて構成し、プロジェクトをビルドします。

1. Visual Studio で `B2CGraphClient\B2CGraphClient.sln` ソリューションを開きます。
1. **B2CGraphClient** プロジェクトで *App.config* ファイルを開きます。
1. `<appSettings>` セクションを次の XML に置き換えます。 次に、`{your-b2c-tenant}` をテナントの名前に、`{Application ID}` と `{Client secret}` を前にメモした値に置き換えます。

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. ソリューションをビルドします。 ソリューション エクスプローラーで **B2CGraphClient** ソリューションを右クリックし、 **[ソリューションのリビルド]** を選択します。

ビルドが成功すると、`B2C.exe` コンソール アプリケーションは `B2CGraphClient\bin\Debug` にあります。

## <a name="review-the-sample-code"></a>サンプル コードを確認する

B2CGraphClient を使用するには、コマンド プロンプト (`cmd.exe`) を開き、プロジェクトの `Debug` ディレクトリに変更します。 次に `B2C Help` コマンドを実行します。

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

`B2C Help` コマンドを実行すると、使用できるサブコマンドの簡単な説明が表示されます。 サブコマンドの 1 つを呼び出すたびに、`B2CGraphClient` によって Azure AD Graph API に要求が送信されます。

以下のセクションでは、アプリケーションのコードで Azure AD Graph API を呼び出す方法について説明します。

### <a name="get-an-access-token"></a>アクセス トークンを取得する

Azure AD Graph API に要求するには、認証のためのアクセス トークンが必要になります。 `B2CGraphClient` では、オープンソースの Active Directory 認証ライブラリ (ADAL) を使用して、アクセス トークンの取得を支援します。 ADAL では、ヘルパー API が用意され、アクセス トークンのキャッシュなどのいくつかの重要な詳細が処理されるため、トークンを簡単に取得できます。 しかし、ADAL を使用してトークンを取得する必要はありません。 代わりに、手動で HTTP 要求を作成してトークンを取得することもできます。

> [!NOTE]
> Azure AD Graph API で使用できるアクセス トークンを取得するには、ADAL v2 以降を使用する必要があります。 ADAL v1 を使用することはできません。

`B2CGraphClient` を実行すると、`B2CGraphClient` クラスのインスタンスが作成されます。 このクラスのコンストラクターは、ADAL の認証のスキャフォールディングを設定します。

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

例として `B2C Get-User` コマンドを使用してみましょう。

追加の引数を指定せずに `B2C Get-User` を呼び出すと、アプリケーションでは `B2CGraphClient.GetAllUsers()` メソッドが呼び出されます。 次に、`GetAllUsers()` で `B2CGraphClient.SendGraphGetRequest()` が呼び出され、Azure AD Graph API に HTTP GET 要求が送信されます。 `B2CGraphClient.SendGraphGetRequest()` は GET 要求を送信する前に、まず、ADAL を使用してアクセス トークンを取得します。

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

ADAL の `AuthenticationContext.AcquireToken()` メソッドを呼び出すことで、Graph API 用のアクセス トークンを取得できます。 ADAL はアプリケーションの ID を表す `access_token` を返します。

### <a name="read-users"></a>ユーザーを読み取る

Azure AD Graph API からユーザーの一覧または特定のユーザーを取得するには、HTTP `GET` 要求を `/users` エンドポイントに送信します。 テナントのすべてのユーザーを要求した場合、次のようになります。

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

この要求を確認するには、次のコマンドを実行します。

 ```cmd
 B2C Get-User
 ```

注意すべき重要な点が 2 つあります。

* ADAL を使用して取得したアクセス トークンは、`Bearer` スキームを使用して `Authorization` ヘッダーに追加されます。
* B2C テナントに対しては、クエリ パラメーター `api-version=1.6` を使用する必要があります。

これらの詳細は、どちらも `B2CGraphClient.SendGraphGetRequest()` メソッド内で処理されます。

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>コンシューマー ユーザー アカウントを作成する

B2C テナントにユーザー アカウントを作成するときは、HTTP `POST` 要求を `/users` エンドポイントに送信します。 次の HTTP `POST` 要求は、テナントに作成されるユーザーの例を示しています。

コンシューマー ユーザーを作成するには、次の要求のほとんどのプロパティが必要です。 図に `//` のコメントが含まれています。実際の要求には含めないでください。

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

要求を確認するには、次のコマンドのいずれかを実行します。

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` コマンドは、ユーザー オブジェクトの JSON 表現を含む JSON ファイルを入力パラメーターとして受け取ります。 サンプル コードには 2 つのサンプル JSON ファイル `usertemplate-email.json` と `usertemplate-username.json` があります。 ニーズに合わせてこれらのファイルを変更できます。 上記の必須のフィールドに加えて、いくつかの省略可能なフィールドがファイルに含まれています。

必須のフィールドと省略可能なフィールドの詳細については、「[エンティティおよび複合型リファレンス | Graph API リファレンス](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference)」を参照してください。

POST 要求が `B2CGraphClient.SendGraphPostRequest()` 内でどのように構成されているかを確認できます。

* アクセス トークンを要求の `Authorization` ヘッダーにアタッチします。
* `api-version=1.6`を設定します。
* 要求の本文に JSON ユーザー オブジェクトを追加します。

> [!NOTE]
> 既存のユーザー ストアから移行するアカウントのパスワード強度が [Azure AD B2C によって適用された強力なパスワード強度](active-directory-b2c-reference-password-complexity.md)より低い場合は、`passwordPolicies` プロパティの `DisableStrongPassword` 値を使用して強力なパスワード要件を無効にすることができます。 たとえば、前のユーザー作成要求を `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"` のように変更できます。

### <a name="update-consumer-user-accounts"></a>コンシューマー ユーザー アカウントを更新する

ユーザー オブジェクトを更新する場合、プロセスはユーザー オブジェクトの作成に使用するものと似ていますが、HTTP `PATCH` メソッドを使用します。

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

JSON ファイルの一部の値を変更してユーザーを更新してみてください。次に、`B2CGraphClient` を使用して以下のいずれかのコマンドを実行します。

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

`B2CGraphClient.SendGraphPatchRequest()` メソッドを調べて、この要求がどのように送信されるかの詳細を確認してください。

### <a name="search-users"></a>ユーザーの検索

次の方法で、B2C テナント内のユーザーを検索できます。

* ユーザーの**オブジェクト ID** を参照します。
* `signInNames` プロパティであるサインイン識別子を参照します。
* 有効な OData パラメーターのいずれかを参照します。 たとえば、'givenName', 'surname', 'displayName' などです。

以下のコマンドのいずれかを実行してユーザーを検索します。

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

例:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>ユーザーを削除する

ユーザーを削除するには、HTTP `DELETE` メソッドを使用し、ユーザーのオブジェクト ID を使用して URL を作成します。

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

例を確認するには、このコマンドを実行し、コンソールに出力される DELETE 要求を観察します。

```cmd
B2C Delete-User <object-id-of-user>
```

`B2CGraphClient.SendGraphDeleteRequest()` メソッドを調べて、この要求がどのように送信されるかの詳細を確認してください。

ユーザー管理に加え、Azure AD Graph API では他にもさまざまなアクションを実行できます。 [Azure AD Graph API リファレンス](/previous-versions/azure/ad/graph/api/api-catalog) には、各アクションの詳細が要求の例と共に記載されています。

## <a name="use-custom-attributes"></a>カスタム属性を使用する

ほとんどのコンシューマー アプリケーションは、何らかの種類のカスタム ユーザー プロファイル情報を格納する必要があります。 その方法の 1 つとして、B2C テナントにカスタム属性を定義できます。 定義後、ユーザー オブジェクトの他のプロパティと同様にその属性を処理できます。 属性を更新したり、属性を削除したり、属性に基づいてクエリを実行したり、サインイン トークンの中で要求として属性を送信したりできます。

B2C テナント内でカスタム属性を定義するには、 [B2C のカスタム属性に関するリファレンス](active-directory-b2c-reference-custom-attr.md)を参照してください。

次の `B2CGraphClient` コマンドを使用して、B2C テナントで定義されているカスタム属性を表示できます。

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

出力には、各カスタム属性の詳細が表示されます。 例:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

ユーザー オブジェクトのプロパティとして、 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`などの完全名を使用できます。 新しいプロパティとそのプロパティの値を使用して JSON ファイルを更新し、次を実行します。

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>次の手順

`B2CGraphClient`を使用することで、B2C テナント ユーザーをプログラムで管理できるサービス アプリケーションが得られます。 `B2CGraphClient` は、それ自体のアプリケーション ID を Azure AD Graph API への認証に使用します。 また、クライアント シークレットを使用してトークンが取得されます。

ご自分のアプリケーションにこの機能を組み込むときは、B2C アプリケーションに関するいくつかの重要な点に注意してください。

* テナントで必要なアクセス許可をアプリケーションに付与します。
* Graph API を呼び出す場合は、`api-version=1.6` を使用します。
* コンシューマー ユーザーを作成し、更新するとき、上述のようにいくつかのプロパティが必要になります。
