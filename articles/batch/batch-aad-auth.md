---
title: "Azure Batch からの認証に Azure Active Directory を使用する | Microsoft Docs"
description: "Batch は、Batch サービスと Batch リソース プロバイダーからの認証に Azure AD をサポートしています。"
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/16/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 28665625cde9c7b164986f7b2d9c845136543aba
ms.lasthandoff: 03/18/2017

---

# <a name="authenticate-from-batch-solutions-with-active-directory"></a>Batch ソリューションからの認証に Active Directory を使用する

Azure Batch は、[Azure Active Directory][aad_about] (Azure AD) を使用した Batch サービスと Batch 管理サービスの認証をサポートしています。 Azure AD は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。 Azure AD は、顧客、サービス管理者、組織のユーザーを認証するために、Azure 自体で使用されています。

この記事では、Batch Management .NET ライブラリや Batch .NET ライブラリを使用するアプリケーションでの認証に Azure AD を使用する方法について説明します。 Batch .NET API のコンテキストでは、Azure AD の統合認証を使用して、サブスクリプション管理者や共同管理者を認証する方法を説明します。 認証されたユーザーは、Azure Batch に要求を発行できるようになります。

Azure AD を使用して、無人で実行中のアプリケーションへのアクセスを認証することもできます。 ここでは Azure AD の統合認証の使用に焦点を当てるほか、自動アプリケーションの認証に関する他のリソースを紹介します。

## <a name="use-azure-ad-with-batch-management-solutions"></a>Batch 管理ソリューションで Azure AD を使用する

Batch Management .NET ライブラリでは、Batch アカウント、アカウント キー、アプリケーション、およびアプリケーション パッケージを操作するための型を公開します。 Batch Management .NET ライブラリは Azure リソース プロバイダー クライアントであり、[Azure Resource Manager][resman_overview] と共に使用してこれらのリソースをプログラムで管理します。 

Azure AD は、Batch Management .NET ライブラリを含む Azure リソース プロバイダー クライアントや、[Azure Resource Manager][resman_overview] を通じて行われた要求の認証に必要です。

このセクションでは、GitHub で入手できる [AccountManagment][acct_mgmt_sample] サンプル プロジェクトを使用して、Batch Management .NET ライブラリで Azure AD を使用する方法について説明します。 AccountManagement サンプルは、サブスクリプションにプログラムでアクセスし、リソース グループと新しい Batch アカウントを作成し、アカウント上でいくつかの操作を実行するコンソール アプリケーションです。 

Batch Management .NET ライブラリと AccountManagement サンプルの使用の詳細については、「[.NET 用の Batch 管理クライアント ライブラリを使用した Batch アカウントとクォータの管理](batch-management-dotnet.md)」を参照してください。

### <a name="register-your-application-with-azure-ad"></a>Azure AD にアプリケーションを登録する

Azure [Active Directory Authentication Library][aad_adal] (ADAL) は、アプリケーション内で使用するためのプログラム インターフェイスを Azure AD に提供します。 アプリケーションから ADAL を呼び出すには、Azure AD テナントにアプリケーションを登録する必要があります。 アプリケーションを登録する場合は、アプリケーションに関する情報 (Azure AD テナント内でのアプリケーション名など) を Azure AD で指定します。 Azure AD により、実行時にアプリケーションを Azure AD と関連付けるために使用するアプリケーション ID が提供されます。 アプリケーション ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/active-directory-application-objects.md)」を参照してください。

AccountManagement サンプル アプリケーションを登録するには、「[Azure Active Directory とアプリケーションの統合][aad_integrate]」の「[アプリケーションの追加](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application)」の手順に従います。 アプリケーションの種類として、**[ネイティブ クライアント アプリケーション]** を指定します。 **リダイレクト URI** には、任意の有効な URI (`http://myaccountmanagementsample` など) を指定します。実際のエンドポイントである必要はありません。

![](./media/batch-aad-auth/app-registration-management-plane.png)

登録プロセスが完了すると、アプリケーションのアプリケーション ID とオブジェクト (サービス プリンシパル) ID が表示されます。  

![](./media/batch-aad-auth/app-registration-client-id.png)

### <a name="update-your-code-to-reference-your-application-id"></a>アプリケーション ID を参照するようコードを更新する 

クライアント アプリケーションはアプリケーション ID (クライアント ID とも呼ばれる) を使用して、実行時に Azure AD にアクセスします。 Azure Portal にアプリケーションを登録したら、Azure AD により登録したアプリケーションに提供されたアプリケーション ID を使用するように、コードを更新します。 AccountManagement サンプル アプリケーションで、アプリケーション ID を Azure Portal から適切な定数にコピーします。

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
また、登録プロセス時に指定したリダイレクト URI をコピーします。

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

### <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>アプリケーションに Azure Resource Manager APIへ のアクセスを許可する　

次に、Azure Resource Manager API へのアクセスをアプリケーションに委任する必要があります。 Resource Manager API の Azure AD 識別子は、**Windows Azure Service Management API** です。

Azure Portal で次の手順に従います。

1. Azure Portal の左側のナビゲーション ウィンドウで **[More Services](その他のサービス)** を選択し、**[アプリの登録]**、**[追加]** の順にクリックします。
2. アプリケーション登録の一覧で、アプリケーションの名前を検索します。

    ![アプリケーションの名前を検索](./media/batch-aad-auth/search-app-registration.png)

3. **[設定]** ブレードを表示します。 **[API アクセス]** セクションで、**[必要なアクセス許可]** を選択します。
4. **[追加]** をクリックして、新しい必要なアクセス許可を追加します。 
5. ステップ 1 で、「**Windows Azure Service Management API**」と入力し、結果の一覧からその API を選択して **[選択]** ボタンをクリックします。
6. ステップ 2 で、**[Access Azure classic deployment model as organization users](組織のユーザーとして Azure クラシック デプロイメント モデルにアクセスする)** の横のチェック ボックスをオンにして、**[選択]** ボタンをクリックします。
7. **[完了]** ボタンをクリックします。

**[必要なアクセス許可]** ブレードに、ADAL および Resource Manager API へのアプリケーションのアクセスが許可されたことが表示されます。 アプリの Azure AD への最初の登録時に、既定で ADAL へのアクセス許可が付与されます。

![Azure Resource Manager API へのアクセス許可の委任](./media/batch-aad-auth/required-permissions-management-plane.png)


### <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 認証トークンを取得する

AccountManagement サンプル アプリケーションは、Azure AD と Azure Resource Manager にエンドポイントを提供する定数を定義します。 サンプル アプリケーションではこれらの定数を使用して、Azure AD にサブスクリプション情報のクエリを行います。 これらの定数は変更しないようにします。

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure service management resource 
private const string ResourceUri = "https://management.core.windows.net/";
```

AccountManagement サンプルを Azure AD テナントに登録し、サンプル ソース コード内で必要な値を指定すると、サンプルは Azure AD を使用した認証の準備が整います。 サンプルを実行すると、ADAL は認証トークンの取得を試みます。 この段階で、Microsoft 資格情報の入力が求められます。 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

資格情報を入力すると、サンプル アプリケーションは Batch 管理サービスに対して承認された要求の発行を開始することができます。 

## <a name="use-azure-ad-with-batch-service-solutions"></a>Batch サービス ソリューションで Azure AD を使用する

Batch .NET ライブラリは、Batch サービスでの並列処理のワークフローを構築するための型を提供します。 Batch サービスは、[共有キー](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service)による認証と Azure AD を使用した認証の両方をサポートします。 このセクションでは、Azure AD を使用した認証について説明します。

>[!NOTE]
>Batch アカウントの作成時に、Batch によって管理されるサブスクリプションとユーザー サブスクリプションのどちらにプールを割り当てるかを指定できます。 ご使用のアカウントでユーザー サブスクリプションにプールを割り当てる場合は、Azure AD を使用して、アカウント内のリソースへの要求を認証する必要があります。
>
>

Azure AD を使用した Batch .NET アプリケーションの認証は、Batch Management .NET アプリケーションの認証と似ています。 いくつかの違いについては、このセクションで説明します。

### <a name="batch-service-endpoints"></a>Batch サービス エンドポイント

Batch サービス エンドポイントは、Batch Management .NET で使用するものと異なります。

Batch サービス用の Azure AD エンドポイントは次のとおりです。

`https://login.microsoftonline.com/common`

Batch サービス用のリソース エンドポイントは次のとおりです。

`https://batch.core.windows.net/`

### <a name="grant-the-batch-service-api-access-to-your-application"></a>アプリケーションに Batch サービス API へのアクセスを許可する

Azure AD を使用して Batch アプリケーションで認証できるようにするには、まずアプリケーションを Azure AD に登録し、Batch サービスAPI へのアクセスを許可する必要があります。 Batch サービス API の Azure AD 識別子は **Microsoft Azure Batch (MicrosoftAzureBatch)** です。

1. Batch アプリケーションを登録するには、「[Azure Active Directory とアプリケーションの統合][aad_integrate]」の「[アプリケーションの追加](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application)」の手順に従います。 **リダイレクト URI** には、任意の有効な URI を指定することができます。 実際のエンドポイントである必要はありません。

    アプリケーションを登録すると、アプリケーション ID とオブジェクト ID が表示されます。

    ![Azure AD に Batch アプリケーションを登録する](./media/batch-aad-auth/app-registration-data-plane.png)

2. 次に、**[設定]** ブレードを表示します。 **[API アクセス]** セクションで、**[必要なアクセス許可]** を選択します。
3. **[必要なアクセス許可]** ブレードで、**[追加]** ボタンをクリックします。
4. ステップ 1 で、**MicrosoftAzureBatch** を探して **[Microsoft Azure Batch (MicrosoftAzureBatch)]** を選択し、**[選択]** ボタンをクリックします。
5. ステップ 2 で、**[Access Azure Batch Service](Azure Batch サービスへのアクセス)** の横のチェック ボックスをオンにし、**[選択]** ボタンをクリックします。
6. **[完了]** ボタンをクリックします。

**[必要なアクセス許可]** ブレードに、Azure AD API と Azure Batch API への Azure AD アプリケーションのアクセスが許可されたことが表示されます。 

![API のアクセス許可](./media/batch-aad-auth/required-permissions-data-plane.png)

### <a name="authentication-for-batch-accounts-in-a-user-subscription"></a>ユーザー サブスクリプションでの Batch アカウントの認証

新しい Batch アカウントの作成時に、プールを割り当てるサブスクリプションを選択することができます。 この選択は、そのアカウント内のリソースに対して行われる要求を認証する方法に影響を与えます。

既定では、Batch プールは Batch サービス サブスクリプションに割り当てられます。 このオプションを選択すると、共有キーまたは Azure AD のいずれかを使用してアカウント内のリソースに対する要求を認証できます。

指定したユーザーのサブスクリプションに Batch プールが割り当てられるよう指定することもできます。 このオプションを選択すると、Azure AD で認証する必要があります。

### <a name="best-practices-for-using-azure-ad-with-batch"></a>Batch で Azure AD を使用するためのベスト プラクティス

Azure AD 認証トークンの有効期限は、1 時間後に切れます。 有効期間が長い **BatchClient** オブジェクトを使用するときは、要求ごとに ADAL からトークンを取得して、常に有効なトークンを持つようにすることをお勧めします。 

.NET でそうするには、Azure AD からトークンを取得するメソッドを記述し、このメソッドをデリゲートとして **BatchTokenCredentials** オブジェクトに渡します。 デリゲート メソッドは、有効なトークンが提供されるようにするために要求ごとに Batch サービスに呼び出されます。 既定では ADAL がトークンをキャッシュするため、新しいトークンは必要な場合にのみ Azure AD から取得されます。 例については、次のセクションの「[コード例: Batch .NET で Azure AD を使用する](#code-example-using-azure-ad-with-batch-net)」を参照してください。 Azure AD のトークンの詳細については、「[Azure AD の認証シナリオ][aad_auth_scenarios]」を参照してください。

### <a name="code-example-using-azure-ad-with-batch-net"></a>コード例: Batch .NET で Azure AD を使用する

Azure AD を使用して認証する Batch .NET コードを記述するには、[Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) パッケージと [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) パッケージを参照してください。

次の `using` ステートメントをコードに含めます。

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

コード内の Azure AD の共通エンドポイントと、Batch サービス用の Azure AD エンドポイントを参照します。  

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/common";
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch アカウントのエンドポイントを参照します。

```csharp
private const string BatchAccountEndpoint = "https://myaccount.westcentralus.batch.azure.com";
```

アプリケーションのアプリケーション ID (クライアント ID) を指定します。 アプリケーション ID は Azure Portal のアプリ登録から使用できます。アプリケーション ID の取得については、「[アプリケーションに Batch サービス API へのアクセスを許可する](#grant-the-batch-service-api-access-to-your-application)」を参照してください。 

```csharp
private const string ClientId = "<application-id>";
```

リダイレクト URI も指定しますが、これは任意の有効な URI を使用できます。

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Azure AD から認証トークンを取得するコールバック メソッドを記述します。 **AcquireTokenAsync** メソッドはユーザーに資格情報の入力を求め、これらの資格情報を新しいトークンの取得に使用します。

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

デリゲートをパラメーターとして受け取る **BatchTokenCredentials** オブジェクトを作成します。 これらの資格情報を使用して **BatchClient** オブジェクトを開きます。 **BatchClient** オブジェクトは、Batch サービスに対する後の操作で使用できます。

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountEndpoint, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

上に示した **GetAuthenticationTokenAsync** コールバック メソッドは、アプリケーションと対話するユーザーの統合認証に Azure AD を使用します。 **AcquireTokenAsync** メソッドへの呼び出しにより、資格情報の入力が求められます。資格情報が入力されると、アプリケーションが続行します。 また Azure AD を使用して、自動アプリケーションを Azure AD サービス プリンシパルを使用して認証することもできます。 詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/active-directory-application-objects.md)」と「[リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../resource-group-create-service-principal-portal.md)」を参照してください。  
 

## <a name="next-steps"></a>次のステップ

[AccountManagement サンプル アプリケーション][acct_mgmt_sample]の実行に関する詳細については、「[.NET 用の Batch 管理クライアント ライブラリを使用した Batch アカウントとクォータの管理](batch-management-dotnet.md)」を参照してください。

Azure AD の詳細については、「[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory/)」を参照してください。 ADAL の使用方法の詳細な例については、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory) ライブラリを参照してください。


[aad_about]: ../active-directory/active-directory-whatis.md "Azure Active Directory とは"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD の認証シナリオ"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Azure Active Directory とアプリケーションの統合"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

