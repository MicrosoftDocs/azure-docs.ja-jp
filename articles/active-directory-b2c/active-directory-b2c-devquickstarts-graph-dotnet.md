---
title: Azure Active Directory B2C で Graph API を使用する | Microsoft Docs
description: アプリケーション ID を使用して B2C テナント用の Graph API を呼び出してプロセスを自動化する方法。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 88b1d05a47f4a8267ab936a922ac190a925bd5ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510180"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C:Azure AD Graph API を使用する

>[!NOTE]
> Azure AD B2C ディレクトリのユーザーを管理するには、[Azure AD Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) を使用する必要があります。 これは、Microsoft Graph API とは異なります。 [こちら](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/)をご覧ください。

Azure Active Directory (Azure AD) B2C テナントは非常に大規模になる傾向があります。 これは、多くの一般的なテナント管理タスクをプログラムで実行する必要があることを意味します。 主な例にはユーザーの管理があります。 たとえば、既存のユーザー ストアを B2C テナントに移行することがあります。 その場合、自分のページでユーザー登録をホストし、バックグラウンドで Azure AD B2C ディレクトリのユーザー アカウントを作成することがあります。 この種のタスクでは、ユーザー アカウントの作成、読み取り、更新、削除を実行する機能が必要です。 Azure AD Graph API を使用してこれらの操作を実行できます。

B2C テナントでは、主に 2 つのモードで Graph API と通信します。

* 対話型の一度だけ実行されるタスクでは、タスクの実行時に B2C テナントの管理者アカウントとして実行する必要があります。 このモードでは、Graph API を呼び出す前に管理者が資格情報でサインインする必要があります。
* 自動化された継続的なタスクでは、必要な権限を付与した何らかの種類のサービス アカウントを使用し、管理タスクを実行する必要があります。 Azure AD では、アプリケーションを登録して、Azure AD に認証することでそれを実行できます。 その際、 **OAuth 2.0 クライアント資格情報付与** を利用する [アプリケーション ID](../active-directory/develop/service-to-service.md)を使用します。 この場合、アプリケーションはユーザーとしてではなくアプリケーション自体として Graph API を呼び出します。

この記事では、自動化された事例を実行する方法を示します。 ユーザーの CRUD (作成、読み取り、更新、削除) 操作を実行する .NET 4.5 `B2CGraphClient` を構築します。 クライアントにはさまざまなメソッドを呼び出すことができる Windows コマンド ライン インターフェイス (CLI) を与えます。 ただし、コードは、非対話型の自動化された方法で動作するように記述されます。

## <a name="get-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを取得する
アプリケーションまたはユーザーを作成する前に、Azure AD B2C テナントが必要です。 テナントがまだない場合は、 [Azure AD B2C の使用開始](active-directory-b2c-get-started.md)に関するページを参照してください。

## <a name="register-your-application-in-your-tenant"></a>アプリケーションをテナントに登録する
B2C テナントを取得後、[Azure Portal](https://portal.azure.com) を通じてアプリケーションを登録する必要があります。

> [!IMPORTANT]
> B2C テナントで Graph API を使うには、Azure AD B2C の *[アプリケーション]* メニュー**ではなく**、Azure Portal の *[アプリの登録]* サービスを使ってアプリケーションを登録する必要があります。 次の手順では、適切なメニューを示します。 Azure AD B2C の *[アプリケーション]* メニューに登録済みの既存の B2C アプリケーションを再利用することはできません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD B2C テナントを選択します。
3. 左側のナビゲーション ウィンドウで **[すべてのサービス]** を選択し、 **[アプリの登録]** 、 **[追加]** の順にクリックします。
4. 画面の指示に従い、新しいアプリケーションを作成します。 
    1. アプリケーション タイプとして **[Web App / API]** (Web アプリ/API) を選択します。    
    2. これはこの例に関係がないので、**任意のサインオン URL** (例: `https://B2CGraphAPI`) を指定します。  
5. この時点でアプリケーションの一覧に表示されたアプリケーションをクリックして、**アプリケーション ID** (クライアント ID とも呼ばれます) を取得します。 後のセクションで必要になるため、この ID をコピーします。
6. [設定] メニューで **[キー]** をクリックします。
7. **[パスワード]** セクションにキーの説明を入力し、期間を選択して、 **[保存]** をクリックします。 後のセクションで使用するために、キーの値 (クライアント シークレットとも呼ばれます) をコピーします。

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>アプリケーション用に作成、読み取り、および更新アクセス許可を構成する
ここでは、ユーザーの作成、読み取り、更新、および削除に必要なすべてのアクセス許可を取得するようにアプリケーションを構成する必要があります。

1. Azure Portal の [アプリの登録] メニューで手順を続行し、アプリケーションを選択します。
2. [設定] メニューで **[必要なアクセス許可]** をクリックします。
3. [必要なアクセス許可] メニューで、 **[Windows Azure Active Directory]** をクリックします。
4. [アクセスの有効化] メニューで、 **[アプリケーション アクセス許可]** から **[ディレクトリ データの読み取りと書き込み]** を選択し、 **[保存]** をクリックします。
5. 最後に、[必要なアクセス許可] メニューに戻り、 **[アクセス許可の付与]** をクリックします。

これで B2C テナントに対してユーザーの作成、読み取り、更新を実行する権限を持つアプリケーションが用意されました。

> [!NOTE]
> アクセス許可を付与する処理は、完了するまでに数分間かかることがあります。
> 
> 

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>アプリケーションに対するパスワードの削除または更新のアクセス許可を構成する
現在、 *[ディレクトリ データの読み取りと書き込み]* アクセス許可には、ユーザーを削除したり、ユーザー パスワードを更新したりする権限は含まれて**いません**。 アプリケーションにユーザーを削除したり、パスワードを更新したりする権限を付与する場合は、PowerShell に関連した次の追加の手順を実行する必要があります。そうでない場合は、次のセクションにスキップできます。

まず、[Azure AD PowerShell v1 モジュール (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0) をまだインストールしていない場合は、それをインストールします。

```powershell
Install-Module MSOnline
```

PowerShell モジュールをインストールした後、Azure AD B2C テナントに接続します。

> [!IMPORTANT]
> B2C テナントに**固有**の B2C テナントの管理者アカウントを使用する必要があります。 これらのアカウントは、次のようになりますmyusername@myb2ctenant.onmicrosoft.com。

```powershell
Connect-MsolService
```

ここで、下のスクリプトにある**アプリケーション ID** を使用して、アプリケーションにユーザー アカウント管理者のロールを割り当てます。 これらのロールには、よく知られた識別子が用意されているため、下記スクリプトに**アプリケーション ID** を入力するだけです。

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

これで、アプリケーションには B2C テナントからユーザーを削除したり、パスワードを更新したりするアクセス許可も付与されました。

## <a name="download-configure-and-build-the-sample-code"></a>サンプル コードをダウンロード、構成、ビルドする
まず、サンプル コードをダウンロードして実行します。 次に、それを詳しく観察します。  [サンプル コードは .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)できます。 選択したディレクトリで複製することもできます。

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

`B2CGraphClient\B2CGraphClient.sln` Visual Studio ソリューションを Visual Studio で開きます。 `B2CGraphClient` プロジェクトで、`App.config` ファイルを開きます。 アプリの 3 つの設定を独自の値に置き換えます。

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

次に、 `B2CGraphClient` ソリューションを右クリックしてサンプルをリビルドします。 リビルドできると、`B2CGraphClient\bin\Debug` に `B2C.exe` 実行可能ファイルが表示されます。

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Graph API を使用してユーザーの CRUD 操作をビルドする
B2CGraphClient を使用するには、`cmd` Windows コマンド プロンプトを開き、`Debug` ディレクトリに移動します。 `B2C Help` コマンドを実行します。

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

各コマンドの短い説明が表示されます。 これらのコマンドのいずれかを呼び出すたびに、 `B2CGraphClient` は Azure AD Graph API に要求を行います。

### <a name="get-an-access-token"></a>アクセス トークンを取得する
Graph API に要求するには、認証のためのアクセス トークンが必要になります。 `B2CGraphClient` では、オープン ソースの Active Directory Authentication Library (ADAL) を利用し、アクセス トークンを取得します。 ADAL は、単純な API を提供し、アクセス トークンのキャッシュなどのいくつかの重要な詳細を処理することで、トークンの取得を容易にします。 ADAL を使用してトークンを取得する必要はありません。 HTTP 要求を作成してトークンを取得することもできます。

> [!NOTE]
> このコード サンプルでは、Graph API と通信するために ADAL v2 を使用しています。  Azure AD Graph API で使用できるアクセス トークンを取得するためには、ADAL v2 または v3 を使用する必要があります。
> 
> 

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

例として、 `B2C Get-User` コマンドを使用します。 `B2C Get-User` がそれ以外の入力なしで呼び出されると、CLI は `B2CGraphClient.GetAllUsers(...)` メソッドを呼び出します。 このメソッドは、HTTP GET 要求を Graph API に送信する `B2CGraphClient.SendGraphGetRequest(...)`を呼び出します。 `B2CGraphClient.SendGraphGetRequest(...)` は GET 要求を送信する前に、まず、ADAL を使用してアクセス トークンを取得します。

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

ADAL の `AuthenticationContext.AcquireToken(...)` メソッドを呼び出すことで、Graph API 用のアクセス トークンを取得できます。 ADAL はアプリケーションの ID を表す `access_token` を返します。

### <a name="read-users"></a>ユーザーを読み取る
Graph API からユーザーの一覧または特定のユーザーを取得するには、HTTP `GET` 要求を `/users` エンドポイントに送信します。 テナントのすべてのユーザーを要求した場合、次のようになります。

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

この要求を確認するには、次のコマンドを実行します。

 ```cmd
 B2C Get-User
 ```

注意すべき重要な点が 2 つあります。

* ADAL 経由で取得されたアクセス トークンは、`Bearer` スキームを使用して `Authorization` ヘッダーに追加されます。
* B2C テナントに対しては、クエリ パラメーター `api-version=1.6` を使用する必要があります。

これらの詳細は、どちらも `B2CGraphClient.SendGraphGetRequest(...)` メソッド内で処理されます。

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
B2C テナントにユーザー アカウントを作成するときは、HTTP `POST` 要求を `/users` エンドポイントに送信します。

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

この要求に含まれるプロパティのほとんどは、コンシューマー ユーザーの作成に必要です。 詳細については、 [ここ](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)をクリックしてください。 説明用のコメント ( `//` 部分) が追加されているので注意してください。 実際の要求には追加しないでください。

要求を確認するには、次のコマンドのいずれかを実行します。

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` コマンドは、入力パラメーターとして .json ファイルを受け取ります。 これには JSON 表記のユーザー オブジェクトが含まれます。 サンプル コードには 2 つのサンプル .json ファイル、`usertemplate-email.json` と `usertemplate-username.json` があります。 ニーズに合わせてこれらのファイルを変更できます。 上記の必須フィールドに加えて、利用できるいくつかの任意フィールドがこれらのファイルに含まれています。 任意フィールドの詳細については、 [Azure AD Graph API エンティティ リファレンス](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity)のページをご覧ください。

POST 要求が `B2CGraphClient.SendGraphPostRequest(...)`内でどのように構成されているかを確認できます。

* アクセス トークンを要求の `Authorization` ヘッダーにアタッチします。
* `api-version=1.6`を設定します。
* 要求の本文に JSON ユーザー オブジェクトを追加します。

> [!NOTE]
> 既存のユーザー ストアから移行するアカウントのパスワード強度が [Azure AD B2C によって適用された強力なパスワード強度](/previous-versions/azure/jj943764(v=azure.100))より低い場合は、`passwordPolicies` プロパティの `DisableStrongPassword` 値を使用して強力なパスワード要件を無効にすることができます。 たとえば、前述したユーザーの作成要求を、 `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`のように変更できます。
> 
> 

### <a name="update-consumer-user-accounts"></a>コンシューマー ユーザー アカウントを更新する
ユーザー オブジェクトの更新プロセスはユーザー オブジェクトの作成プロセスと同じです。 ただし、HTTP `PATCH` メソッドが使用されます。

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

JSON ファイルを新しいデータで更新することでユーザーを更新してください。 それから `B2CGraphClient` を利用し、これらのコマンドのいずれかを実行できます。

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

`B2CGraphClient.SendGraphPatchRequest(...)` メソッドを調べて、この要求がどのように送信されるかの詳細を確認してください。

### <a name="search-users"></a>ユーザーの検索
B2C テナントではいくつかの方法でユーザーを検索することができます。 ユーザーのオブジェクト ID を使用する方法や、ユーザーのサインイン識別子 (つまり、 `signInNames` プロパティ) を使用する方法があります。

特定のユーザーを検索するには、次のコマンドのいずれかを実行します。

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

いくつかの例を次に示します。

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>ユーザーを削除する
ユーザーの削除プロセスは簡単です。 HTTP `DELETE` メソッドを使用し、正しいオブジェクト ID で URL を構成します。

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

例を確認するには、このコマンドを実行し、コンソールに出力される DELETE 要求を観察します。

```cmd
B2C Delete-User <object-id-of-user>
```

`B2CGraphClient.SendGraphDeleteRequest(...)` メソッドを調べて、この要求がどのように送信されるかの詳細を確認してください。

ユーザー管理に加え、Azure AD Graph API では他にもさまざまなアクションを実行できます。 [Azure AD Graph API リファレンス](/previous-versions/azure/ad/graph/api/api-catalog) には、各アクションの詳細が要求の例と共に記載されています。

## <a name="use-custom-attributes"></a>カスタム属性を使用する
ほとんどのコンシューマー アプリケーションは、何らかの種類のカスタム ユーザー プロファイル情報を格納する必要があります。 その方法の 1 つとして、B2C テナントにカスタム属性を定義できます。 定義後、ユーザー オブジェクトの他のプロパティと同様にその属性を処理できます。 属性を更新したり、属性を削除したり、属性に基づいてクエリを実行したり、サインイン トークンの中で要求として属性を送信したりできます。

B2C テナント内でカスタム属性を定義するには、 [B2C のカスタム属性に関するリファレンス](active-directory-b2c-reference-custom-attr.md)を参照してください。

B2C テナント内に定義されたカスタム属性は、 `B2CGraphClient`を使用して表示できます。

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

これらの関数の出力によって、次のような各カスタム属性の詳細が明らかになります。

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

ユーザー オブジェクトのプロパティとして、 `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`などの完全名を使用できます。  新しいプロパティとその値で .json ファイルを更新し、次を実行します。

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

`B2CGraphClient`を使用することで、B2C テナント ユーザーをプログラムで管理できるサービス アプリケーションが得られます。 `B2CGraphClient` は、それ自体のアプリケーション ID を Azure AD Graph API への認証に使用します。 また、クライアント シークレットを使用してトークンが取得されます。 アプリケーションにこの機能を組み込むときは、B2C アプリに関するいくつかの重要な点に注意してください。

* テナントの適切なアクセス許可をアプリケーションに付与する必要があります。
* 現時点では、ADAL (MSAL ではありません) を使用してアクセス トークンを取得する必要があります。 (ライブラリを使用せず、プロトコル メッセージを直接送信することもできます。)
* Graph API を呼び出す場合は、`api-version=1.6` を使用します。
* コンシューマー ユーザーを作成し、更新するとき、上述のようにいくつかのプロパティが必要になります。

B2C テナントで Graph API を利用して実行するアクションに関するご質問やご要望がございましたら、この記事にコメントを投稿するか、GitHub コード サンプル リポジトリで問題を提出してください。

