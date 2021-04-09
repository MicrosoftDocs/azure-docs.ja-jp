---
title: Azure Active Directory を使用した Azure Batch サービスの認証
description: Batch は、Batch サービスからの認証に Azure AD をサポートしています。 次の 2 つの方法のいずれかで認証する方法について説明します。
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 2ceefa538c44208750da8986fcf3d161f0c0865f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180022"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch サービスの認証に Active Directory を使用する

Azure Batch は、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) を使用した認証をサポートしています。 Azure AD は、Microsoft が提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。 Azure AD は、顧客、サービス管理者、組織のユーザーを認証するために Azure 自体で使用されています。

Azure AD 認証に Azure Batch を使用する場合は、次の 2 つの方法のいずれかで認証できます。

- **統合認証** を使用して、アプリケーションと対話するユーザーを認証します。 統合認証を使用するアプリケーションでは、ユーザーの資格情報を収集し、それらの資格情報を使用して Batch リソースへのアクセスを認証します。
- **サービス プリンシパル** を使用して無人のアプリケーションを認証します。 サービス プリンシパルは、実行時のリソースへのアクセスの際にアプリケーションを表すための、アプリケーション用のポリシーとアクセス許可を定義します。

Azure AD の詳細については、「[Azure Active Directory のドキュメント](../active-directory/index.yml)」を参照してください。

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

Azure AD エンドポイントの詳細については、「[認証と承認](../active-directory/develop/authentication-vs-authorization.md)」を参照してください。

### <a name="batch-resource-endpoint"></a>Batch リソース エンドポイント

**Azure Batch リソース エンドポイント** は、Batch サービスへの要求を認証するトークンを取得するために次のように使用します。

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>アプリケーションをテナントに登録する

Azure AD を使用して認証する最初の手順は、アプリケーションを Azure AD テナントに登録することです。 アプリケーションを登録すると、Azure [Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) をコードから呼び出すことができます。 ADAL は、Azure AD で認証するための API をアプリケーションから提供します。 使用プランが統合認証かサービス プリンシパルかに関わらず、アプリケーションの登録は必須です。

アプリケーションの登録では、使用するアプリケーションに関する情報を Azure AD に提供します。 これで、Azure AD から、実行時にアプリケーションを Azure AD と関連付ける際に使用するアプリケーション ID (*クライアント ID とも呼ばれます*) が提供されます。 アプリケーション ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

Batch アプリケーションを登録するには、[クイックスタートの「**アプリケーションを登録する**」セクションの手順に従います。Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)」を参照してください。 ネイティブ アプリケーションとしてアプリケーションを登録する場合は、**リダイレクト URI** 用に任意の有効な URI を指定できます。 実際のエンドポイントである必要はありません。

アプリケーションを登録すると、次のようにアプリケーション ID が表示されます。

![Azure portal に表示されているアプリケーション ID のスクリーンショット。](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Active Directory のテナント ID を取得する

テナント ID は、アプリケーションに認証サービスを提供する Azure AD テナントを識別します。 テナント ID を取得するには、次の手順に従います。

1. Azure Portal で、使用している Active Directory を選択します。
1. **[プロパティ]** を選択します。
1. **ディレクトリ ID** 用に提供されている GUID 値をコピーします。 この値は、テナント ID とも呼ばれます。

![Azure portal のディレクトリ ID のスクリーンショット。](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>統合認証を使用する

統合認証を使用して認証するには、Batch サービス API に接続するために、アプリケーションのアクセス許可を付与する必要があります。 この手順では、アプリケーションで Azure AD を使用して、Batch サービスの API 呼び出しを認証できます。

アプリケーションを登録したら、Azure portal で次の手順に従って、Batch サービスへのアクセス権をアプリケーションに付与します。

1. Azure portal で、 **[すべてのサービス]** 、 **[アプリの登録]** の順に選択します。
1. アプリの登録の一覧から、アプリケーションの名前を検索します。
1. アプリケーションを選択して **[API のアクセス許可]** を選択します。
1. **[API のアクセス許可]** セクションで、 **[アクセス許可の追加]** を選択します。
1. **[API の選択]** で、Batch API を検索します。 API が見つかるまで、次の各文字列を検索します。
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** は Batch API の ID です。
1. Batch API が見つかったら、それを選択して **[選択]** を選びます。
1. **[アクセス許可の選択]** で、 **[Access Azure Batch Service] (Azure Batch サービスへのアクセス)** の横にあるチェック ボックスをオンにして **[アクセス許可を追加する]** を選択します。

これで、 **[API のアクセス許可]** セクションに、Azure AD アプリケーションに Microsoft Graph と Batch サービス API の両方へのアクセス権があることが示されます。 アプリを Azure AD に最初に登録する際に、Microsoft Graph へのアクセス許可が自動的に付与されます。

## <a name="use-a-service-principal"></a>サービス プリンシパルを使用する

無人で実行するアプリケーションを認証するには、サービス プリンシパルを使用します。 アプリケーションの登録後、Azure Portal で次の手順に従って、サービス プリンシパルを構成します。

1. アプリケーションのシークレットを要求します。
1. アプリケーションに Azure ロールベースのアクセス制御 (Azure RBAC) を割り当てます。

### <a name="request-a-secret-for-your-application"></a>アプリケーションのシークレットを要求する

アプリケーションは、サービス プリンシパルで認証する場合、Azure AD にアプリケーション ID とシークレットの両方を送信します。 コードから使用するために、秘密キーを作成およびコピーする必要があります。

Azure Portal で次の手順に従います。

1. Azure portal で、 **[すべてのサービス]** を選択します。 **[アプリの登録]** を選択します。
1. アプリ登録の一覧から、アプリケーションを選択します。
1. アプリケーションを選択してから、 **[証明書とシークレット]** を選択します。 **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択します。
1. シークレットを作成するには、シークレットの説明を入力します。 次に、シークレットの有効期限を 1 年、2 年、有効期限なしのいずれかから選択します。
1. **[追加]** を選択して、シークレットを作成および表示します。 このページを離れると再びアクセスすることができなくなるため、シークレット値を安全な場所にコピーします。

### <a name="assign-azure-rbac-to-your-application"></a>アプリケーションに Azure RBAC を割り当てる

サービス プリンシパルで認証するには、アプリケーションに Azure RBAC を割り当てる必要があります。 次の手順に従います。

1. Azure Portal ポータルで、アプリケーションで使用する Batch アカウントに移動します。
1. Batch アカウントの **[設定]** セクションで、 **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当て]** タブを選択します。
1. **[ロールの割り当ての追加]** を選択します。
1. **[ロール]** ドロップダウン リストで、アプリケーションに *[共同作成者]* または *[リーダー]* のいずれかのロールを選択します。 これらのロールの詳細については、[Azure portal での Azure ロールベースのアクセス制御の概要](../role-based-access-control/overview.md)に関するページを参照してください。
1. **[選択]** フィールドに、アプリケーションの名前を入力します。 一覧からアプリケーションを選択してから、 **[保存]** を選択します。

この時点で、Azure ロールが割り当てられたアプリケーションがアクセス制御の設定に表示されている必要があります。

![アプリケーションに Azure ロールを割り当てる](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>カスタム ロールを割り当てる

カスタム ロールでは、ジョブやタスクの送信などの詳細なアクセス許可をユーザーに付与します。 これにより、プールの作成やノードの変更などのコストに影響する操作をユーザーが実行できないようにすることができます。

カスタム ロールを使用して、次の Azure RBAC 操作のアクセス許可を Azure AD のユーザー、グループ、またはサービス プリンシパルに付与することができます。

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read (任意の読み取り操作)
- Microsoft.Batch/batchAccounts/listKeys/action (任意の操作)

カスタム ロールは、Batch アカウントの資格情報 (共有キー) ではなく、Azure AD によって認証されるユーザーを対象としています。 Batch アカウントの資格情報は、Batch アカウントに対する完全なアクセス許可を付与することに注意してください。 また、[autopool](nodes-and-pools.md#autopools) を使用するジョブには、プールレベルのアクセス許可が必要なことにも注意してください。

> [!NOTE]
> 特定のロールの割り当ては、[アクション] フィールドで指定する必要がありますが、それ以外は [DataAction] フィールドで指定する必要があります。 詳細については、「[Azure リソース プロバイダーの操作](../role-based-access-control/resource-provider-operations.md#microsoftbatch)」を参照してください。

カスタム ロールの定義の例を次に示します。

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

カスタム ロールの作成に関する詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Azure Active Directory のテナント ID を取得する

テナント ID は、アプリケーションに認証サービスを提供する Azure AD テナントを識別します。 テナント ID を取得するには、次の手順に従います。

1. Azure Portal で、使用している Active Directory を選択します。
1. **[プロパティ]** を選択します。
1. **ディレクトリ ID** 用に提供されている GUID 値をコピーします。 この値は、テナント ID とも呼ばれます。

![ディレクトリ ID をコピーする](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>コード例

このセクションのコード例では、Azure AD での認証について、統合認証を使用する方法と、サービス プリンシパルを使用する方法を示します。 これらのコード例の大半では .NET を使用しますが、他の言語でも概念はほぼ同じです。

> [!NOTE]
> Azure AD 認証トークンの有効期間は 1 時間です。 有効期間が長い **BatchClient** オブジェクトを使用するときは、要求ごとに ADAL からトークンを取得して、常に有効なトークンを持つようにすることをお勧めします。
>
> .NET でそうするには、Azure AD からトークンを取得するメソッドを記述し、このメソッドをデリゲートとして **BatchTokenCredentials** オブジェクトに渡します。 このデリゲート メソッドが要求ごとに Batch サービスに呼び出されるため、有効なトークンが確実に提供されます。 既定では ADAL がトークンをキャッシュするため、新しいトークンは必要な場合にのみ Azure AD から取得されます。 Azure AD のトークンの詳細については、「[セキュリティ トークン](../active-directory/develop/security-tokens.md)」を参照してください。

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
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
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
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
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
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のドキュメント](../active-directory/index.yml)を確認します。 ADAL の使用方法の詳細な例については、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory) ライブラリを参照してください。
- [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)と[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルを作成する方法](../active-directory/develop/howto-create-service-principal-portal.md)について学習します。
- [Active Directory を使用した Batch 管理ソリューションの認証](batch-aad-auth-management.md)について学習します。
- Azure AD トークンを使用して認証される Batch クライアントを作成する方法を示す Python の例については、サンプルの「[Python スクリプトを使用して Azure Batch のカスタム イメージをデプロイする](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)」を参照してください。

