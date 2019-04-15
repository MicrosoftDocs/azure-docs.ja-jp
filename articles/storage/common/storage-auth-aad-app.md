---
title: Azure Active Directory を使用して認証を行い、BLOB やキュー データにアプリケーションからアクセスする |Microsoft Docs
description: Azure Active Directory を使用してアプリケーション内から認証を行い、BLOB やキューへの要求を承認します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8fdc4445e28a420b6b4f7935443d7d991d9e8a4d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446070"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>BLOB やキューにアクセスするためにアプリケーションから Azure Active Directory で認証を行う

Azure Storage で Azure Active Directory (Azure AD) を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Azure AD に対して OAuth 2.0 アクセス トークンを要求できます。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) の認証が処理されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Storage への要求を承認できます。

この記事では、Azure AD による認証を行うためにアプリケーションを構成する方法について説明します。 コード例で取り上げられているのは .NET ですが、他の言語でも同様の方法が使用されます。

Azure Storage アプリケーションからセキュリティ プリンシパルの認証を行う前に、そのセキュリティ プリンシパルのロールベースのアクセス制御 (RBAC) 設定を構成しておいてください。 コンテナーとキューのアクセス許可を含む RBAC ロールは、Azure Storage によって定義されます。 RBAC ロールがセキュリティ プリンシパルに割り当てられると、そのセキュリティ プリンシパルはそのリソースへのアクセス権を付与されます。 詳細については、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページを参照してください。

OAuth 2.0 コード付与フローの概要については、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../../active-directory/develop/v1-protocols-oauth-code.md)」を参照してください。

OAuth トークンを使用して BLOB とキューの操作を承認するには、HTTPS を使用する必要があります。

## <a name="assign-an-rbac-role-to-an-azure-ad-security-principal"></a>Azure AD のセキュリティ プリンシパルに RBAC ロールを割り当てる

Azure Storage アプリケーションからセキュリティ プリンシパルの認証を行うには、最初に、そのセキュリティ プリンシパルのロールベースのアクセス制御 (RBAC) 設定を構成します。 コンテナーとキューのアクセス許可を含む RBAC ロールは、Azure Storage によって定義されます。 RBAC ロールがセキュリティ プリンシパルに割り当てられると、そのセキュリティ プリンシパルはそのリソースへのアクセス権を付与されます。 詳細については、[RBAC を使用した Azure BLOB とキューのデータへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>アプリケーションを Azure AD テナントに登録する

Azure AD を使用してストレージ リソースへのアクセスを承認する最初の手順は、クライアント アプリケーションを Azure AD テナントに登録することです。 アプリケーションを登録すると、Azure [Active Directory Authentication Library](../../active-directory/active-directory-authentication-libraries.md) (ADAL) をコードから呼び出すことができます。 ADAL は、Azure AD で認証するための API をアプリケーションから提供します。 さらに、アプリケーションを登録すると、アクセス トークンを使用してそのアプリケーションから Azure Storage API への呼び出しを承認できます。

アプリケーションの登録では、使用するアプリケーションに関する情報を Azure AD に提供します。 これで Azure AD から、実行時にアプリケーションを Azure AD と関連付ける際に使用するクライアント ID (*アプリケーション ID とも呼ばれます*) が提供されます。 クライアント ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

Azure Storage アプリケーションを登録するには、「[Azure Active Directory とアプリケーションの統合](../../active-directory/active-directory-integrating-applications.md)」の「[アプリケーションの追加](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md)」の手順に従います。 アプリケーションをネイティブ アプリケーションとして登録する場合は、**リダイレクト URI** 用に任意の有効な URI を指定できます。 値は実際のエンドポイントである必要はありません。

![ストレージ アプリケーションを Azure AD に登録する方法を示すスクリーンショット](./media/storage-auth-aad-app/app-registration.png)

アプリケーションを登録すると、**[設定]** にアプリケーション ID (クライアント ID) が表示されます。

![クライアント ID を示すスクリーンショット](./media/storage-auth-aad-app/app-registration-client-id.png)

Azure AD へのアプリケーションの登録について詳しくは、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」を参照してください。 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>登録済みのアプリに Azure Storage へのアクセス許可を付与する

次に、Azure Storage API を呼び出すためのアクセス許可をアプリケーションに付与する必要があります。 この手順によって、アプリケーションで Azure AD を使用して Azure Storage への呼び出しを承認できるようになります。

1. Azure portal の左側のナビゲーション ウィンドウで、**[すべてのサービス]** を選択し、**[アプリの登録]** を探します。
2. 前の手順で作成した登録済みのアプリケーションの名前を探します。
3. 登録済みのアプリを選択し、**[設定]** をクリックします。 **[API アクセス]** セクションで、**[必要なアクセス許可]** を選択します。
4. **[必要なアクセス許可]** ブレードで、**[追加]** ボタンをクリックします。
5. **[API を選択します]** で "Azure Storage" を検索して、結果の一覧から **[Azure Storage]** を選択します。

    ![ストレージのアクセス許可を示すスクリーンショット](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. **[アクセス許可の選択]** で、**[Access Azure Storage]\(Azure Storage へのアクセス\)** の横にあるチェック ボックスをオンにして、**[選択]** をクリックします。
7. **[Done]** をクリックします。

これで、**[必要なアクセス許可]** ウィンドウに、Azure AD アプリケーションに Azure Active Directory と Azure Storage の両方へのアクセス権があることが示されます。 アプリを Azure AD に最初に登録する際に、Azure AD へのアクセス許可が自動的に付与されます。

![登録済みのアプリのアクセス許可を示すスクリーンショット](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>.NET コード例: ブロック BLOB を作成する

このコード例では、Azure AD からアクセス トークンを取得する方法を示します。 アクセス トークンは、指定されたユーザーの認証を行って、ブロック BLOB を作成する要求を承認するために使用されます。 このサンプルを動作させるには、まず前のセクションで説明されている手順に従ってください。

> [!NOTE]
> Azure Storage アカウントの所有者であっても、データへのアクセス許可が自動的に割り当てられるわけではありません。 Azure Storage の RBAC ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーまたはキューのレベルで割り当てることができます。 
>
> たとえば、自分が所有者であるストレージ アカウントで、独自のユーザー ID を使ってサンプル コードを実行するには、BLOB データ共同作成者の RBAC ロールを自分自身に割り当てる必要があります。 そうしないと、BLOB を作成するための呼び出しが、HTTP 状態コード 403 (許可されていません) で失敗します。 詳細については、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページを参照してください。

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD による認証の既知の値

Azure AD でセキュリティ プリンシパルの認証を行うには、いくつかの既知の値をコードに含める必要があります。

#### <a name="azure-ad-authority"></a>Azure AD 機関

Microsoft パブリック クラウドの場合、基本 Azure AD 機関は次のとおりです。ここで、*tenant-id* は実際の Active Directory テナント ID (またはディレクトリ ID) です。

`https://login.microsoftonline.com/<tenant-id>/`

テナント ID は、認証に使用する Azure AD テナントを識別します。 テナント ID を取得するには、「**Azure Acitve Directory のテナント ID を取得する**」で説明されている手順に従ってください。

#### <a name="storage-resource-id"></a>ストレージ リソース ID

Azure Storage への要求を認証するトークンを取得するには、Azure Storage リソース ID を使用します。

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Azure Active Directory のテナント ID を取得する

テナント ID を取得するには、次の手順に従います。

1. Azure Portal で、使用している Active Directory を選択します。
2. **[プロパティ]** をクリックします。
3. **ディレクトリ ID** 用に提供されている GUID 値をコピーします。 この値は、テナント ID とも呼ばれます。

![テナント ID をコピーする方法を示すスクリーンショット](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>参照と using ステートメントを追加する  

Visual Studio から Azure Storage クライアント ライブラリをインストールします。 **[ツール]** メニューで、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。 コンソールに次のコマンドを入力して、.NET 用クライアント ライブラリの最新バージョンをインストールします。

```
Install-Package WindowsAzure.Storage
```

また、最新バージョンの ADAL をインストールします。

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

次に、以下の using ステートメントをコードに追加します。

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD から OAuth トークンを取得する

次に、Azure AD に対してトークンを要求するメソッドを追加します。 トークンを要求するには、[AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) メソッドを呼び出します。 前に実行した手順で、次の値が取得されていることを確認します。

- テナント (ディレクトリ) ID
- クライアント (アプリケーション) ID
- クライアント リダイレクト URI

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthInstance = "https://login.microsoftonline.com/{0}/";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthInstance, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>ブロック BLOB を作成する

最後に、アクセス トークンを使って新しいストレージ資格情報を作成し、それらの資格情報を使って BLOB を作成します。 OAuth トークンを使用して BLOB とキューの操作を承認するには、HTTPS を使用する必要があることにご留意ください。

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);

blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
```

> [!NOTE]
> Azure AD と Azure Storage の統合では、Azure Storage 操作に HTTPS を使用する必要があります。

上の例では、.NET クライアント ライブラリによって、ブロック BLOB を作成するための要求の認可が処理されます。 他のストレージ クライアント ライブラリでも、要求の認可が処理されます。 ただし、REST API を使って OAuth トークンを指定して Azure Storage の操作を呼び出す場合は、OAuth トークンを使って要求を認可する必要があります。   

OAuth アクセス トークンを使って Blob service および Queue サービスの操作を呼び出すには、**ベアラー** スキームを使って **Authorization** ヘッダーでアクセス トークンを渡し、2017-11-09 以降のサービス バージョンを指定します。次の例をご覧ください。

```
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

REST からの Azure Storage 操作の認可について詳しくは、「[Authenticate with Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory)」(Azure Active Directory での認証) をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。
- Azure Storage を使用して Azure リソースのマネージド ID を使用する方法については、「[Azure マネージド サービス ID から Azure AD の認証を受ける](storage-auth-aad-msi.md)」を参照してください。
- Azure AD ID で Azure CLI と PowerShell にサインインする方法については、[CLI または PowerShell での Azure AD ID を使用した Azure Storage へのアクセス](storage-auth-aad-script.md)に関するページをご覧ください。
