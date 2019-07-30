---
title: Azure Active Directory を使用して Azure Batch サービス ソリューションを認証する | Microsoft Docs
description: Batch は、Batch サービスからの認証に Azure AD をサポートしています。
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: lahugh
ms.openlocfilehash: 64921a2ab69306df0b7c3d968055e698dd6995e7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323938"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch サービスの認証に Active Directory を使用する

Azure Batch は、[Azure Active Directory][aad_about] (Azure AD) を使用した認証をサポートしています。 Azure AD は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。 Azure AD は、顧客、サービス管理者、組織のユーザーを認証するために Azure 自体で使用されています。

Azure AD 認証に Azure Batch を使用する場合は、次の 2 つの方法のいずれかで認証できます。

- **統合認証**を使用して、アプリケーションと対話するユーザーを認証します。 統合認証を使用するアプリケーションでは、ユーザーの資格情報を収集し、それらの資格情報を使用して Batch リソースへのアクセスを認証します。
- **サービス プリンシパル**を使用して無人のアプリケーションを認証します。 サービス プリンシパルは、実行時のリソースへのアクセスの際にアプリケーションを表すための、アプリケーション用のポリシーとアクセス許可を定義します。

Azure AD の詳細については、「[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory/)」を参照してください。

## <a name="endpoints-for-authentication"></a>認証のためのエンドポイント

Azure AD で Batch アプリケーションを認証するには、コードによく知られているエンドポイントをいくつか含める必要があります。

### <a name="azure-ad-endpoint"></a>Azure AD エンドポイント

基本 Azure AD 機関のエンドポイントは次のとおりです。

`https://login.microsoftonline.com/`

Azure AD を使用して認証するには、このエンドポイントをテナント ID (ディレクトリ ID) と共に使用します。 テナント ID は、認証に使用する Azure AD テナントを識別します。 テナント ID を取得するには、「[Azure Acitve Directory のテナント ID を取得する](#get-the-tenant-id-for-your-active-directory)」で概要を説明する手順に従ってください。

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> サービス プリンシパルを使用して認証する場合はテナント固有のエンドポイントが必要です。 
> 
> テナント固有のエンドポイントは、統合認証を使用して認証する場合はオプションですが使用が推奨されています。 ただし、Azure AD の共通のエンドポイントを使用することもできます。 特定のテナントが提供されていない場合は、インターフェイスを収集する汎用的な資格情報が共通のエンドポイントから提供されます。 共通のエンドポイントは、`https://login.microsoftonline.com/common` です。
>
>

Azure AD エンドポイントの詳細については、「[Azure AD の認証シナリオ][aad_auth_scenarios]」を参照してください。

### <a name="batch-resource-endpoint"></a>Batch リソース エンドポイント

**Azure Batch リソース エンドポイント**は、Batch サービスへの要求を認証するトークンを取得するために次のように使用します。

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>アプリケーションをテナントに登録する

Azure AD を使用して認証する最初の手順は、アプリケーションを Azure AD テナントに登録することです。 アプリケーションを登録すると、Azure [Active Directory Authentication Library][aad_adal] (ADAL) をコードから呼び出すことができます。 ADAL は、Azure AD で認証するための API をアプリケーションから提供します。 使用プランが統合認証かサービス プリンシパルかに関わらず、アプリケーションの登録は必須です。

アプリケーションの登録では、使用するアプリケーションに関する情報を Azure AD に提供します。 これで、Azure AD から、実行時にアプリケーションを Azure AD と関連付ける際に使用するアプリケーション ID (*クライアント ID とも呼ばれます*) が提供されます。 アプリケーション ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

Batch アプリケーションを登録するには、「[Azure Active Directory とアプリケーションの統合][aad_integrate]」の「[アプリケーションの追加](../active-directory/develop/quickstart-register-app.md)」の手順に従います。 ネイティブ アプリケーションとしてアプリケーションを登録する場合は、**リダイレクト URI** 用に任意の有効な URI を指定できます。 実際のエンドポイントである必要はありません。

アプリケーションを登録すると、次のようにアプリケーション ID が表示されます。

![Azure AD に Batch アプリケーションを登録する](./media/batch-aad-auth/app-registration-data-plane.png)

Azure AD にアプリケーションを登録する詳細については、「[Azure AD の認証シナリオ](../active-directory/develop/authentication-scenarios.md)」を参照してください。

## <a name="get-the-tenant-id-for-your-active-directory"></a>Active Directory のテナント ID を取得する

テナント ID は、アプリケーションに認証サービスを提供する Azure AD テナントを識別します。 テナント ID を取得するには、次の手順に従います。

1. Azure Portal で、使用している Active Directory を選択します。
2. **[プロパティ]** をクリックします。
3. **ディレクトリ ID** 用に提供されている GUID 値をコピーします。 この値は、テナント ID とも呼ばれます。

![ディレクトリ ID をコピーする](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>統合認証を使用する

統合認証を使用して認証するには、Batch サービス API に接続するために、アプリケーションのアクセス許可を付与する必要があります。 この手順では、アプリケーションで Azure AD を使用して、Batch サービスの API 呼び出しを認証できます。

アプリケーションを登録したら、Azure portal で次の手順に従って、Batch サービスへのアクセス権をアプリケーションに付与します。

1. Azure Portal の左側のナビゲーション ウィンドウで、 **[すべてのサービス]** を選択します。 **[アプリの登録]** をクリックします。
2. アプリケーション登録の一覧で、アプリケーションの名前を検索します。

    ![アプリケーションの名前を検索する](./media/batch-aad-auth/search-app-registration.png)

3. アプリケーションをクリックし、 **[設定]** をクリックします。 **[API アクセス]** セクションで、 **[必要なアクセス許可]** を選択します。
4. **[必要なアクセス許可]** ブレードで、 **[追加]** ボタンをクリックします。
5. **[API の選択]** で、Batch API を検索します。 API が見つかるまで、次の各文字列を検索します。
    1. **MicrosoftAzureBatch**。
    2. **Microsoft Azure Batch**。 新しい Azure AD テナントでは、この名前が使用される場合があります。
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** は Batch API の ID です。 
6. Batch API を見つけたら、それを選択して **[選択]** をクリックします。
7. **[アクセス許可の選択]** で、 **[Access Azure Batch Service]\(Azure Batch サービスへのアクセス)** の横のチェック ボックスをオンにし、 **[選択]** をクリックします。
8. **[Done]** をクリックします。

これで、 **[必要なアクセス許可]** ウィンドウに、ADAL と Batch サービス API の両方へのアクセスが Azure AD アプリケーションに許可されたことが示されます。 Azure AD を使用したアプリの初回登録時に、ADAL へのアクセス許可が自動的に付与されます。

![API のアクセス許可を付与する](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>サービス プリンシパルを使用する 

無人で実行するアプリケーションを認証するには、サービス プリンシパルを使用します。 アプリケーションの登録後、Azure Portal で次の手順に従って、サービス プリンシパルを構成します。

1. アプリケーションの秘密キーを要求します。
2. アプリケーションに RBAC のロールを割り当てます。

### <a name="request-a-secret-key-for-your-application"></a>アプリケーションの秘密キーを要求する

サービス プリンシパルでアプリケーションを認証すると、Azure AD にアプリケーション ID と秘密キーの両方が送信されます。 コードから使用するために、秘密キーを作成およびコピーする必要があります。

Azure Portal で次の手順に従います。

1. Azure Portal の左側のナビゲーション ウィンドウで、 **[すべてのサービス]** を選択します。 **[アプリの登録]** をクリックします。
2. アプリの登録の一覧から、アプリケーションの名前を検索します。
3. アプリケーションをクリックし、 **[設定]** をクリックします。 **[API アクセス]** セクションで、 **[キー]** を選択します。
4. キーを作成するために、キーの説明を入力します。 次に、キーの有効期間を 1 年または 2 年から選択します。 
5. **[保存]** ボタンをクリックしてキーを作成および表示します。 キーの値を安全な場所にコピーしてください。ブレードを離れた後は再度アクセスすることはできません。 

    ![秘密キーを作成する](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>アプリケーションに RBAC ロールを割り当てる

サービス プリンシパルを使用して認証するには、アプリケーションに RBAC ロールを割り当てる必要があります。 次の手順に従います。

1. Azure Portal ポータルで、アプリケーションで使用する Batch アカウントに移動します。
2. Batch アカウントの **[設定]** ブレードで、 **[Access Control (IAM)]** を選択します。
3. **[ロールの割り当て]** タブをクリックします。
4. **[ロールの割り当ての追加]** ボタンをクリックします。 
5. **[ロール]** ドロップダウン リストで、アプリケーションに _[共同作成者]_ または _[リーダー]_ のいずれかのロールを選択します。 これらのロールの詳細については、「[Azure Portal でのロールベースのアクセス制御の基礎を確認する](../role-based-access-control/overview.md)」を参照してください。  
6. **[選択]** フィールドに、アプリケーションの名前を入力します。 リストからアプリケーションを選択し、 **[保存]** をクリックします。

この時点で、RBAC ロールが割り当てられたアプリケーションがアクセス制御の設定に表示されている必要があります。 

![アプリケーションに RBAC ロールを割り当てる](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Azure Active Directory のテナント ID を取得する

テナント ID は、アプリケーションに認証サービスを提供する Azure AD テナントを識別します。 テナント ID を取得するには、次の手順に従います。

1. Azure Portal で、使用している Active Directory を選択します。
2. **[プロパティ]** をクリックします。
3. **ディレクトリ ID** 用に提供されている GUID 値をコピーします。 この値は、テナント ID とも呼ばれます。

![ディレクトリ ID をコピーする](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>コード例

このセクションのコード例では、Azure AD での認証について、統合認証を使用する方法と、サービス プリンシパルを使用する方法を示します。 これらのコード例の大半では .NET を使用しますが、他の言語でも概念はほぼ同じです。

> [!NOTE]
> Azure AD 認証トークンの有効期間は 1 時間です。 有効期間が長い **BatchClient** オブジェクトを使用するときは、要求ごとに ADAL からトークンを取得して、常に有効なトークンを持つようにすることをお勧めします。 
>
>
> .NET でそうするには、Azure AD からトークンを取得するメソッドを記述し、このメソッドをデリゲートとして **BatchTokenCredentials** オブジェクトに渡します。 このデリゲート メソッドが要求ごとに Batch サービスに呼び出されるため、有効なトークンが確実に提供されます。 既定では ADAL がトークンをキャッシュするため、新しいトークンは必要な場合にのみ Azure AD から取得されます。 Azure AD のトークンの詳細については、「[Azure AD の認証シナリオ][aad_auth_scenarios]」を参照してください。
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>コード例:Azure AD 統合認証を Batch .NET で使用する

Batch .NET の統合認証で認証するには、[Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) パッケージと [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) パッケージを参照します。

次の `using` ステートメントをコードに含めます。

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

コードで、テナント ID を含む Azure AD エンドポイントを参照します。 テナント ID を取得するには、「[Azure Acitve Directory のテナント ID を取得する](#get-the-tenant-id-for-your-active-directory)」で概要を説明する手順に従ってください。

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Batch サービスのリソース エンドポイントを次のように参照します。

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch アカウントを次のように参照します。

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

アプリケーションのアプリケーション ID (クライアント ID) を指定します。 アプリケーション ID は、Azure Portal のアプリの登録から入手できます。

```csharp
private const string ClientId = "<application-id>";
```

アプリケーションをネイティブ アプリケーションとして登録した場合は、指定したリダイレクト URI のコピーも行います。 コードに指定するリダイレクト URI は、アプリケーションを登録したときに指定したリダイレクト URI と一致している必要があります。

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Azure AD から認証トークンを取得するコールバック メソッドを記述します。 次に示す **GetAuthenticationTokenAsync** コールバック メソッドで ADAL を呼び出し、アプリケーションを操作しているユーザーを認証します。 ADAL で指定した **AcquireTokenAsync** メソッドへの呼び出しにより、資格情報の入力画面が表示され、ユーザーが資格情報を入力すると (既にキャッシュされている資格情報でない場合)、アプリケーションが使用できるようになります。

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

デリゲートをパラメーターとして受け取る **BatchTokenCredentials** オブジェクトを作成します。 これらの資格情報を使用して **BatchClient** オブジェクトを開きます。 この **BatchClient** オブジェクトを使って Batch サービスに対する残りの操作を行えます。

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>コード例:Azure AD サービス プリンシパルを Batch .NET で使用する

Batch .NET からサービス プリンシパルで認証するには、[Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) パッケージと [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) パッケージを参照します。

次の `using` ステートメントをコードに含めます。

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

コードで、テナント ID を含む Azure AD エンドポイントを参照します。 サービス プリンシパルを使用する場合は、テナント固有のエンドポイントを指定する必要があります。 テナント ID を取得するには、「[Azure Acitve Directory のテナント ID を取得する](#get-the-tenant-id-for-your-active-directory)」で概要を説明する手順に従ってください。

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Batch サービスのリソース エンドポイントを次のように参照します。  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Batch アカウントを次のように参照します。

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

アプリケーションのアプリケーション ID (クライアント ID) を指定します。 アプリケーション ID は、Azure Portal のアプリの登録から入手できます。

```csharp
private const string ClientId = "<application-id>";
```

Azure Portal からコピーした秘密キーを次のように指定します。

```csharp
private const string ClientKey = "<secret-key>";
```

Azure AD から認証トークンを取得するコールバック メソッドを記述します。 次に示す **GetAuthenticationTokenAsync** コールバック メソッドで、自動認証用の ADAL を呼び出します。

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

デリゲートをパラメーターとして受け取る **BatchTokenCredentials** オブジェクトを作成します。 これらの資格情報を使用して **BatchClient** オブジェクトを開きます。 この **BatchClient** オブジェクトを使用して、Batch サービスに対する残りの操作を行います。

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>コード例:Azure AD サービス プリンシパルを Batch Python で使用する

Batch Python からサービス プリンシパルで認証するには、[azure-batch](https://pypi.org/project/azure-batch/) モジュールと [azure-common](https://pypi.org/project/azure-common/) モジュールをインストールして参照します。


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

サービス プリンシパルを使用する場合は、テナント ID を指定する必要があります。 テナント ID を取得するには、「[Azure Acitve Directory のテナント ID を取得する](#get-the-tenant-id-for-your-active-directory)」で概要を説明する手順に従ってください。

```python
TENANT_ID = "<tenant-id>"
```

Batch サービスのリソース エンドポイントを次のように参照します。  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Batch アカウントを次のように参照します。

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

アプリケーションのアプリケーション ID (クライアント ID) を指定します。 アプリケーション ID は、Azure Portal のアプリの登録から入手できます。

```python
CLIENT_ID = "<application-id>"
```

Azure Portal からコピーした秘密キーを次のように指定します。

```python
SECRET = "<secret-key>"
```

**ServicePrincipalCredentials** オブジェクトを作成します。

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

サービス プリンシパルの資格情報を使用して、**BatchServiceClient** オブジェクトを開きます。 この **BatchServiceClient** オブジェクトを使用して、Batch サービスに対する残りの操作を行います。

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>次の手順

* Azure AD の詳細については、「[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory/)」を参照してください。 ADAL の使用方法の詳細な例については、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory) ライブラリを参照してください。

* サービス プリンシパルの詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。 Azure Portal を使用してサービス プリンシパルを作成するには、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。 PowerShell または Azure CLI を使用してサービス プリンシパルを作成することもできます。

* Azure AD を使用して Batch 管理アプリケーションを認証するには、「[Batch 管理ソリューションの認証に Active Directory を使用する](batch-aad-auth-management.md)」を参照してください。

* Azure AD トークンを使用して認証される Batch クライアントを作成する方法を示す Python の例については、サンプルの「[Python スクリプトを使用して Azure Batch のカスタム イメージをデプロイする](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)」を参照してください。

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Azure Active Directory とは"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD の認証シナリオ"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Azure Active Directory とアプリケーションの統合"
[azure_portal]: https://portal.azure.com
