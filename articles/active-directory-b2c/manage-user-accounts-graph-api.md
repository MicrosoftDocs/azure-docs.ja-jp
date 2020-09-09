---
title: Microsoft Graph API を使用してユーザーを管理する
titleSuffix: Azure AD B2C
description: Microsoft Graph API を呼び出し、アプリケーション ID を使用してプロセスを自動化して、Azure AD B2C テナントのユーザーを管理する方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8132eb72b3e448d7ae830b29ccb7dc51528c1250
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921403"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する

Microsoft Graph を使用すると、Microsoft Graph API で作成、読み取り、更新、および削除の各メソッドを指定することで、Azure AD B2C ディレクトリ内のユーザー アカウントを管理できます。 既存のユーザー ストアを Azure AD B2C テナントに移行し、Microsoft Graph API を呼び出すことによって、他のユーザー アカウント管理操作を実行することができます。

以下のセクションでは、Microsoft Graph API を使用した Azure AD B2C ユーザー管理の主要な側面について説明します。 ここで説明する Microsoft Graph API の操作、種類、およびプロパティは、Microsoft Graph API リファレンス ドキュメントに記載されているもののサブセットです。

## <a name="register-a-management-application"></a>管理アプリケーションを登録する

作成したユーザー管理アプリケーションやスクリプトが Azure AD B2C テナント内のリソースと対話できるようにするには、その操作を実行するためのアクセス許可を付与するアプリケーション登録が必要です。

この操作方法に関する記事の手順に従って、管理アプリケーションで使用できるアプリケーション登録を作成します。

[Microsoft Graph を使用して Azure AD B2C を管理する](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph のユーザー管理操作

[Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user) では次のユーザー管理操作を使用できます。

- [ユーザーの一覧の取得](https://docs.microsoft.com/graph/api/user-list)
- [ユーザーの作成](https://docs.microsoft.com/graph/api/user-post-users)
- [ユーザーの取得](https://docs.microsoft.com/graph/api/user-get)
- [ユーザーの更新](https://docs.microsoft.com/graph/api/user-update)
- [ユーザーの削除](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>ユーザー プロパティ

### <a name="display-name-property"></a>表示名プロパティ

`displayName` は、ユーザーに対して Azure portal ユーザー管理で表示され、Azure AD B2C がアプリケーションに返すアクセス トークンで表示される名前です。 このプロパティは必須です。

### <a name="identities-property"></a>ID プロパティ

顧客アカウント (コンシューマー、パートナー、または一般ユーザー) は、次の ID の種類に関連付けることができます。

- **ローカル** ID - ユーザー名とパスワードは、Azure AD B2C ディレクトリにローカルに格納されます。 これらの ID は、"ローカル アカウント" とよく呼ばれます。
- **フェデレーション** ID - "*ソーシャル*" または "*エンタープライズ*" アカウントとも呼ばれ、ユーザーの ID は、Facebook、Microsoft、ADFS、Salesforce などのフェデレーション ID プロバイダーによって管理されます。

顧客アカウントを持つユーザーは、複数の ID でサインインできます。 たとえば、ユーザー名、電子メール、従業員 ID、政府 ID などです。 1 つのアカウントで、同じパスワードを持つ複数の ID (ローカルとソーシャルの両方) を持つことができます。

Microsoft Graph API では、ローカル ID とフェデレーション ID の両方が、[objectIdentity][graph-objectIdentity] 型のユーザー `identities` 属性に格納されます。 `identities` コレクションは、ユーザー アカウントへのサインインに使用される一連の ID を表します。 このコレクションにより、ユーザーは、関連付けられた任意の ID を使用してユーザー アカウントにサインインできます。

| プロパティ   | Type |説明|
|:---------------|:--------|:----------|
|signInType|string| お使いのディレクトリ内のユーザー サインインの種類を指定します。 ローカル アカウントの場合: `emailAddress`、`emailAddress1`、`emailAddress2`、`emailAddress3`、`userName`、または他の任意の種類。 ソーシャル アカウントは `federated` に設定する必要があります。|
|発行者|string|ID の発行者を指定します。 ローカル アカウント (**signInType** が `federated` でない) の場合、このプロパティは、ローカル B2C テナントの既定のドメイン名 (`contoso.onmicrosoft.com` など) になります。 ソーシャル ID (**signInType** が `federated`) の場合、値は発行者の名前 (`facebook.com` など) になります。|
|issuerAssignedId|string|発行者がユーザーに割り当てる一意識別子を指定します。 **issuer** と **issuerAssignedId** の組み合わせは、テナント内で一意である必要があります。 ローカル アカウントの場合、**signInType** が `emailAddress` または `userName` に設定されているときは、ユーザーのサインイン名を表します。<br>**signInType** が次のように設定されている場合: <ul><li>`emailAddress` (または、`emailAddress1` のように `emailAddress` で始まる)。**issuerAssignedId** は有効なメール アドレスである必要があります</li><li>`userName` (または、その他の値)。**issuerAssignedId** は、[メール アドレスの有効なローカル部分](https://tools.ietf.org/html/rfc3696#section-3)である必要があります</li><li>`federated`。**issuerAssignedId** は、フェデレーション アカウントの一意識別子を表します</li></ul>|

次の **Identities** プロパティ。サインイン名を含むローカル アカウント ID、サインインとしての電子メール アドレス、およびソーシャル ID が含まれます。 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

フェデレーション ID の場合、ID プロバイダーによっては、**issuerAssignedId** は、アプリケーションごとの特定のユーザーまたは開発アカウントの一意の値です。 ソーシャル プロバイダーまたは同じ開発アカウント内の別のアプリケーションによって割り当てられたのと同じアプリケーション ID で Azure AD B2C ポリシーを構成します。

### <a name="password-profile-property"></a>パスワード プロファイル プロパティ

ローカル ID の場合、**passwordProfile** プロパティが必須であり、これにはユーザーのパスワードが含まれています。 `forceChangePasswordNextSignIn` プロパティは `false` に設定する必要があります。

フェデレーション (ソーシャル) ID の場合、**passwordProfile** プロパティは必要ありません。

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>パスワード ポリシー プロパティ

Azure AD B2C の (ローカル アカウントの) パスワード ポリシーは、Azure Active Directory の[強力なパスワード強度](../active-directory/authentication/concept-sspr-policy.md)ポリシーに基づいています。 Azure AD B2C のサインアップまたはサインインとパスワード リセットの各ポリシーでは、この強力なパスワード強度を必要とし、パスワードに有効期限がありません。

ユーザー移行シナリオでは、移行するアカウントのパスワード強度が、Azure AD B2C によって適用された[強力なパスワード強度](../active-directory/authentication/concept-sspr-policy.md)より弱い場合は、強力なパスワードという要件を無効にすることができます。 既定のパスワード ポリシーを変更するには、`passwordPolicies` プロパティを `DisableStrongPassword` に設定します。 たとえば、ユーザー作成要求を次のように変更できます。

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>拡張機能プロパティ

すべての顧客向けアプリケーションには、収集する情報についての固有の要件があります。 Azure AD B2C テナントには、名、姓、市区町村、郵便番号などのプロパティに格納された組み込みの一連の情報が用意されています。 Azure AD B2C では、各顧客アカウントに格納されている一連のプロパティを拡張できます。 カスタム属性の定義の詳細については、[カスタム属性 (ユーザー フロー)](user-flow-custom-attributes.md) および[カスタム属性 (カスタムポリシー)](custom-policy-custom-attributes.md) に関する記事を参照してください。

Microsoft Graph API では、拡張属性を使用したユーザーの作成と更新がサポートされています。 Graph API の拡張属性には、`extension_ApplicationClientID_attributename` の規則を使用して名前が付けられます。ここで `ApplicationClientID` は `b2c-extensions-app` アプリケーションの**アプリケーション (クライアント) ID** です (Azure portal の **[アプリの登録]**  >  **[すべてのアプリケーション]** にあります)。 拡張属性名で表されるように、**アプリケーション (クライアント) ID** にはハイフンが含まれないことに注意してください。 次に例を示します。

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>コード サンプル:プログラムによってユーザー アカウントを管理する方法

このコード サンプルは、[Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) を使用して Microsoft Graph API と対話する .NET Core コンソール アプリケーションです。 このコードは、API を呼び出して、Azure AD B2C テナント内のユーザーをプログラムで管理する方法を示しています。
[サンプル アーカイブ (*.zip) をダウンロード](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)するか、GitHub の[リポジトリを参照する](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)か、リポジトリを複製することができます。

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

コード サンプルを入手したら、環境に合わせて構成し、プロジェクトをビルドします。

1. [Visual Studio](https://visualstudio.microsoft.com) または [Visual Studio Code](https://code.visualstudio.com) でプロジェクトを開きます。
1. `src/appsettings.json`を開きます。
1. 「`appSettings`」セクションで、`your-b2c-tenant` をテナントの名前に、`Application (client) ID` と `Client secret` を管理アプリケーション登録の値に置き換えます (この記事の「[管理アプリケーションを登録する](#register-a-management-application)」のセクションを参照してください)。
1. リポジトリのローカル複製内でコンソール ウィンドウを開き、`src` ディレクトリに切り替えてから、プロジェクトをビルドします。
    ```console
    cd src
    dotnet build
    ```
1. `dotnet` コマンドを使用してアプリケーションを実行します。

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

アプリケーションで、実行可能なコマンドの一覧が表示されます。 たとえば、すべてのユーザーの取得、単一ユーザーの取得、ユーザーの削除、ユーザーのパスワードの更新、一括インポートなどです。

### <a name="code-discussion"></a>コードの説明

このサンプル コードでは、Microsoft Graph にアクセスする高品質かつ効率的で回復性があるアプリケーションを簡単に構築できるように設計されている、[Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) を使用します。

Microsoft Graph API への要求には、認証のためのアクセス トークンが必要になります。 このソリューションでは、Microsoft Graph SDK で使用する Microsoft Authentication Library (MSAL) の認証シナリオベースのラッパーを提供する、 [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet パッケージを使用します。

_Program.cs_ ファイル内の `RunAsync` メソッド:

1. _appsettings.json_ ファイルからアプリケーション設定を読み取ります
1. [OAuth 2.0 クライアント資格情報の付与](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)フローを使用して、認証プロバイダーを初期化します。 クライアント資格情報の付与フローを使用すると、アプリは Microsoft Graph API を呼び出すためのアクセス トークンを取得できます。
1. 認証プロバイダーを使用して Microsoft Graph サービス クライアントを設定します。

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

次に、初期化された *GraphServiceClient* を _UserService.cs_ で使用して、ユーザー管理操作を実行します。 たとえば、次のようにしてテナント内のユーザー アカウントの一覧を取得します。

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Microsoft Graph SDK を使用した API 呼び出し](https://docs.microsoft.com/graph/sdks/create-requests)に関する記事には、Microsoft Graph から情報を読み書きする方法、`$select` を使用して返されるプロパティを制御する方法、カスタム クエリ パラメーターを指定する方法、`$filter` および `$orderBy` のクエリ パラメーターを使用する方法に関する情報が含まれています。

## <a name="next-steps"></a>次のステップ

Azure AD B2C リソースでサポートされている Microsoft Graph API 操作の完全なインデックスについては、「[Azure AD B2C に使用可能な Microsoft Graph 操作](microsoft-graph-operations.md)」を参照してください。

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
