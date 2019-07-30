---
title: Azure Active Directory を使用して認証を行い、BLOB やキュー データにクライアント アプリケーションからアクセスする
description: Azure Active Directory を使用して、クライアント アプリケーション内から認証し、OAuth 2.0 トークンを取得し、および Azure Blob Storage および Queue Storage への要求を承認します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ffae7467e9f94c68cf004b74c9791f2d9cda3171
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250007"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>BLOB やキューにアクセスするためにアプリケーションから Azure Active Directory で認証を行う

Azure Blob Storage または Queue Storage で Azure Active Directory (Azure AD) を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 その代わりに、Microsoft ID プラットフォーム (以前の Azure AD) から OAuth 2.0 アクセス トークンを要求することができます。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Blob Storage および Queue Storage への要求を承認できます。

この記事では、Microsoft ID プラットフォーム 2.0 による認証を行うためにネイティブ アプリケーションまたは Web アプリケーションを構成する方法について説明します。 コード例で取り上げられているのは .NET ですが、他の言語でも同様の方法が使用されます。 Microsoft ID プラットフォーム 2.0 の詳細については、「[Microsoft ID プラットフォーム (v2.0) の概要](../../active-directory/develop/v2-overview.md)」を参照してください。

OAuth 2.0 コード付与フローの概要については、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../../active-directory/develop/v2-oauth2-auth-code-flow.md)」を参照してください。

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD のセキュリティ プリンシパルにロールを割り当てる

Azure Storage アプリケーションからセキュリティ プリンシパルの認証を行うには、最初に、そのセキュリティ プリンシパルのロールベースのアクセス制御 (RBAC) 設定を構成します。 コンテナーとキューのアクセス許可を含む組み込みの RBAC ロールは、Azure Storage によって定義されます。 RBAC ロールがセキュリティ プリンシパルに割り当てられると、そのセキュリティ プリンシパルはそのリソースへのアクセス権を付与されます。 詳細については、[RBAC を使用した Azure BLOB とキューのデータへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>アプリケーションを Azure AD テナントに登録する

Azure AD を使用してストレージ リソースへのアクセスを承認する最初の手順は、[Azure portal](https://portal.azure.com) からクライアント アプリケーションを Azure AD テナントに登録することです。 クライアント アプリケーションの登録では、使用するアプリケーションに関する情報を Azure AD に提供します。 これで Azure AD から、実行時にアプリケーションを Azure AD と関連付ける際に使用するクライアント ID (*アプリケーション ID とも呼ばれます*) が提供されます。 クライアント ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

Azure Storage アプリケーションを登録するための手順については、「[クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)」を参照してください。 次の図は、Web アプリケーションを登録するための一般的な設定を示します。

![ストレージ アプリケーションを Azure AD に登録する方法を示すスクリーンショット](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> アプリケーションをネイティブ アプリケーションとして登録する場合は、**リダイレクト URI** 用に任意の有効な URI を指定できます。 ネイティブ アプリケーションの場合、この値が実際の URL である必要はありません。 Web アプリケーションの場合、リダイレクト URI はトークンが提供される URL を指定するため、有効な URI である必要があります。

アプリケーションを登録すると、 **[設定]** にアプリケーション ID (クライアント ID) が表示されます。

![クライアント ID を示すスクリーンショット](./media/storage-auth-aad-app/app-registration-client-id.png)

Azure AD へのアプリケーションの登録について詳しくは、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v2-register-an-app.md)」を参照してください。

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>登録済みのアプリに Azure Storage へのアクセス許可を付与する

次に、Azure Storage API を呼び出すためのアクセス許可をアプリケーションに付与します。 この手順によって、アプリケーションで Azure AD を使用して Azure Storage への要求を承認できるようになります。

1. 登録済みアプリケーションの **[概要]** ページで、 **[API アクセス許可の表示]** を選択します。
1. **[API のアクセス許可]** セクションで、 **[アクセス許可の追加]** を選択し、 **[Microsoft API]** を選択します。
1. 結果の一覧から **[Azure Storage]** を選択して、 **[Request API permissions]\(API のアクセス許可を要求する\)** ウィンドウを選択します。
1. **[アプリケーションに必要なアクセス許可の種類]** で、使用可能なアクセス許可の種類が **[委任されたアクセス許可]** になっていることを確認してください。 このオプションは既定で選択されています。
1. **[API アクセス許可の要求]** ウィンドウの **[アクセス許可の選択]** セクションで、 **[user_impersonation]** の横のチェック ボックスをオンにし、 **[アクセス許可の追加]** をクリックします。

    ![ストレージのアクセス許可を示すスクリーンショット](media/storage-auth-aad-app/registered-app-permissions-1.png)

これで、 **[API のアクセス許可]** ウィンドウに、登録済み Azure AD アプリケーションに Microsoft Graph と Azure Storage の両方へのアクセス権があることが示されます。 アプリを Azure AD に最初に登録する際に、Microsoft Graph へのアクセス許可が自動的に付与されます。

![登録済みのアプリのアクセス許可を示すスクリーンショット](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>クライアント シークレットの作成

アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを追加するには、次の手順のようにします。

1. Azure portal でアプリの登録に移動します。
1. **[証明書とシークレット]** の設定を選択します。
1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** をクリックして新しいシークレットを作成します。
1. シークレットの説明を入力し、適切な有効期限の間隔を選択します。
1. すぐに新しいシークレットの値を安全な場所にコピーします。 完全な値は 1 回だけしか表示されません。

    ![クライアント シークレットを示すスクリーンショット](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>トークン取得のためのクライアント ライブラリ

アプリケーションを登録し、Azure Blob Storage や Queue Storage 内のデータにアクセスするためのアクセス許可をこれに付与したら、セキュリティ プリンシパルを認証して OAuth 2.0 トークンを取得するためのコードをアプリケーションに追加できます。 認証してトークンを取得するには、[Microsoft ID プラットフォームの認証ライブラリ](../../active-directory/develop/reference-v2-libraries.md)または OpenID Connect 1.0 をサポートする別のオープンソース ライブラリのいずれかを使用することができます。 その後、アプリケーションはアクセス トークンを使用して、Azure Blob Storage や Queue Storage に対する要求を承認することができます。

トークンの取得がサポートされるシナリオの一覧は、[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub リポジトリの[シナリオ](https://aka.ms/msal-net-scenarios)のセクションを参照してください。

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD による認証の既知の値

Azure AD でセキュリティ プリンシパルの認証を行うには、いくつかの既知の値をコードに含める必要があります。

### <a name="azure-ad-authority"></a>Azure AD 機関

Microsoft パブリック クラウドの場合、基本 Azure AD 機関は次のとおりです。ここで、*tenant-id* は実際の Active Directory テナント ID (またはディレクトリ ID) です。

`https://login.microsoftonline.com/<tenant-id>/`

テナント ID は、認証に使用する Azure AD テナントを識別します。 ディレクトリ ID とも呼ばれます。 テナント ID を取得するには、Azure portal でアプリ登録の **[概要]** ページに移動し、そこから値をコピーします。

### <a name="azure-storage-resource-id"></a>Azure Storage リソース ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB を作成する

このコード例では、Azure AD からアクセス トークンを取得する方法を示します。 アクセス トークンは、指定されたユーザーの認証を行って、ブロック BLOB を作成する要求を承認するために使用されます。 このサンプルを動作させるには、まず前のセクションで説明されている手順に従ってください。

トークンを要求するには、アプリの登録から次の値が必要です。

- Azure AD ドメインの名前。 この値は、Azure Active Directory の **[概要]** ページから取得します。
- テナント (またはディレクトリ) ID。 この値は、アプリ登録の **[概要]** ページから取得します。
- クライアント (またはアプリケーション) ID。 この値は、アプリ登録の **[概要]** ページから取得します。
- クライアント リダイレクト URI。 この値は、アプリ登録の **[認証]** の設定から取得します。
- クライアント シークレットの値。 この値は、前にコピーした場所から取得します。

### <a name="create-a-storage-account-and-container"></a>ストレージ アカウントとコンテナーの作成

コード サンプルを実行するには、Azure Active Directory と同じサブスクリプション内にストレージ アカウントを作成します。 次に、そのストレージ アカウント内にコンテナーを作成します。 サンプル コードでは、このコンテナーにブロック BLOB が作成されます。

次に、サンプル コードを実行するユーザー アカウントに、**ストレージ BLOB データ共同作成者**ロールを明示的に割り当てます。 Azure portal でこのロールを割り当てる方法については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-portal.md)」をご覧ください。

> [!NOTE]
> Azure ストレージ アカウントを作成するとき、Azure AD を介してデータにアクセスするためのアクセス許可は自動的に割り当てられません。 Azure Storage の RBAC ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーまたはキューのレベルで割り当てることができます。

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD で Blob Storage へのアクセスを承認する Web アプリケーションを作成する

お使いのアプリケーションで Azure Storage にアクセスするときは、ユーザーの代理として行われます。つまり、BLOB またはキュー リソースには、ログインしているユーザーのアクセス許可を使ってアクセスされます。 このコード例を試すには、ユーザーが Azure AD の ID を使用してサインインすることを示すプロンプトを表示する Web アプリケーションが必要です。 独自に作成することも、Microsoft によって提供されるサンプル アプリケーションを使うこともできます。

トークンを取得し、それを使って Azure Storage に BLOB を作成する完全なサンプル Web アプリケーションは、[GitHub](https://aka.ms/aadstorage) で入手できます。 完全なサンプルをレビューして実行すると、コード例の理解に役立つことがあります。 完全なサンプルを実行する方法については、「[完全なサンプルを表示して実行する](#view-and-run-the-completed-sample)」セクションをご覧ください。

#### <a name="add-references-and-using-statements"></a>参照と using ステートメントを追加する  

Visual Studio から Azure Storage クライアント ライブラリをインストールします。 **[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** 、 **[パッケージ マネージャー コンソール]** の順に選択します。 コンソール ウィンドウに次のコマンドを入力して、.NET 用 Azure Storage クライアント ライブラリから必要なパッケージをインストールします。

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

次に、以下の using ステートメントを HomeController.cs ファイルに追加します。

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>ブロック BLOB を作成する

次のコード スニペットを追加してブロック BLOB を作成します。

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> OAuth 2.0 トークンを使用して BLOB とキューの操作を承認するには、HTTPS を使用する必要があります。

上の例では、.NET クライアント ライブラリによって、ブロック BLOB を作成するための要求の認可が処理されます。 他の言語の Azure Storage クライアント ライブラリでも、要求の認可が処理されます。 ただし、REST API を使って OAuth トークンを指定して Azure Storage の操作を呼び出す場合は、OAuth トークンを使って要求を認可する必要があります。

OAuth アクセス トークンを使って Blob service および Queue サービスの操作を呼び出すには、**ベアラー** スキームを使って **Authorization** ヘッダーでアクセス トークンを渡し、2017-11-09 以降のサービス バージョンを指定します。次の例をご覧ください。

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD から OAuth トークンを取得する

次に、ユーザーの代わりに Azure AD からトークンを要求するメソッドを追加します。 このメソッドは、アクセス許可が付与されるスコープを定義します。 アクセス許可とスコープの詳細については、「[Microsoft ID プラットフォーム エンドポイントでのアクセス許可と同意](../../active-directory/develop/v2-permissions-and-consent.md)」を参照してください。

リソース ID を使用して、トークンを取得するスコープを構築します。 この例では、(ユーザーの代わりにトークンが要求されることを示す) 組み込みの `user_impersonation` スコープと共にリソース ID を使用してスコープを構築します。

注意点としては、自分の代わりにトークンを要求することにユーザーが同意するためのインターフェイスをユーザーに提示することが必要な場合があります。 同意が必要な場合、例では **MsalUiRequiredException** をキャッチし、別のメソッドを呼び出して同意の要求を促進します。

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

同意は、保護されたリソースにアプリケーションが代理でアクセスする認証を、ユーザーが許可するプロセスです。 Microsoft ID プラットフォーム 2.0 では増分同意がサポートされています。これは、セキュリティ プリンシパルで最初は最小限のアクセス許可のセットを要求し、必要に応じて時間の経過と共にアクセス許可を追加できることを意味します。 コードがアクセス トークンを要求する場合、特定の時点でアプリが必要とするアクセス許可のスコープを、`scope` パラメーターに指定します。 増分同意の詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)」の「**増分および動的な同意**」セクションをご覧ください。

次のメソッドは、増分同意を要求する認証プロパティを作成します。

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>完全なサンプルを表示して実行する

サンプル アプリケーションを実行するには、最初に [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal) から複製またはダウンロードします。 その後、以下のセクションで説明するようにアプリケーションを更新します。

### <a name="provide-values-in-the-settings-file"></a>設定ファイルで値を提供する

次のように、独自の値で *appsettings.json* ファイルを更新します。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>ストレージ アカウントとコンテナー名を更新する

*HomeController.cs* ファイルで、自分のストレージ アカウントとコンテナーの名前を使うように、ブロック BLOB を参照する URI を更新します。

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>暗黙的な許可のフローを有効にする

サンプルを実行するには、アプリの登録に対して暗黙的な許可のフローを構成することが必要な場合があります。 次の手順に従います。

1. Azure portal でアプリの登録に移動します。
1. [管理] セクションで、 **[認証]** 設定を選択します。
1. **[詳細設定]** の **[暗黙の付与]** セクションで、次の図のように、チェック ボックスをオンにしてアクセス トークンと ID トークンを有効にします。

    ![暗黙的な許可のフローの設定を有効にする方法を示すスクリーンショット](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>localhost によって使用されるポートを更新する

サンプルを実行するとき、実行時に割り当てられる *localhost* ポートを使うように、アプリの登録で指定されているリダイレクト URI を更新することが必要な場合があります。 割り当てられたポートを使用するようリダイレクト URI を更新するには、次の手順のようにします。

1. Azure portal でアプリの登録に移動します。
1. [管理] セクションで、 **[認証]** 設定を選択します。
1. **[リダイレクト URI]** で、次の図のように、ポートを編集して、サンプル アプリケーションで使われているものと一致させます。

    ![アプリの登録のリダイレクト URI を示すスクリーンショット](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>次の手順

- Microsoft ID プラットフォームの詳細については、「[Microsoft ID プラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/)」を参照してください。
- Azure Storage の RBAC ロールについては、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。
- Azure Storage を使用して Azure リソースのマネージド ID を使用する方法については、「[Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する](storage-auth-aad-msi.md)」を参照してください。
