---
title: Azure Cosmos DB のデータへのアクセスをセキュリティで保護する方法
description: 主キー、読み取り専用キー、ユーザー、アクセス許可など、Azure Cosmos DB のアクセス制御の概念について説明します。
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/11/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a16ecd2ee6ed939b2afd0e51e9cf531e419c8af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656399"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Azure Cosmos DB のデータへのアクセスをセキュリティで保護する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB でのデータ アクセス制御の概要について説明します。

Azure Cosmos DB には、データへのアクセスを制御する方法が 3 つあります。

| アクセス制御の種類 | 特性 |
|---|---|
| [主キー](#primary-keys) | 任意の管理操作またはデータ操作を行うことができる共有シークレット。 読み取り/書き込みと読み取り専用の両方の種類があります。 |
| [ロールベースのアクセス制御](#rbac) (プレビュー) | 認証に Azure Active Directory (AAD) の ID を使用する、きめ細かいロールベースのアクセス許可モデル。 |
| [リソース トークン](#resource-tokens)| ネイティブな Azure Cosmos DB のユーザーとアクセス許可に基づく、きめ細かいアクセス許可モデル。 |

## <a name="primary-keys"></a><a id="primary-keys"></a> 主キー

主キーは、データベース アカウントのすべての管理リソースへのアクセスを提供します。 各アカウントは、主キーとセカンダリ キーという 2 つの主キーで構成されます。 二重キーの目的は、キーを再生成 (ロール) して、アカウントとデータに継続的にアクセスできるようにすることです。 主キーの詳細については、[データベース セキュリティ](database-security.md#primary-keys)に関する記事を参照してください。

### <a name="key-rotation"></a><a id="key-rotation"></a> キーのローテーション

主キーのローテーション プロセスは単純です。 

1. Azure portal に移動してセカンダリ キーを取得します。
2. アプリケーションで、プライマリ キーをセカンダリ キーに置き換えます。 全デプロイにわたるすべての Cosmos DB クライアントが直ちに再起動され、更新されたキーの使用が開始されることを確認します。
3. Azure portal でプライマリ キーをローテーションします。
4. 新しいプライマリ キーがすべてのリソースに対して動作することを検証します。 キーのローテーション プロセスには、Cosmos DB アカウントのサイズに応じて、1 分未満から数時間かかる場合があります。
5. セカンダリ キーを新しいプライマリ キーに置き換えます。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Azure portal での主キーのローテーション - NoSQL データベースのセキュリティのデモ" border="false":::

### <a name="code-sample-to-use-a-primary-key"></a>主キーを使用するコード サンプル

次のコード サンプルは、Cosmos DB アカウントのエンドポイントと主キーを使用して、DocumentClient のインスタンス化とデータベースの作成を行う方法を示したものです。

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

次のコード サンプルは、Azure Cosmos DB アカウントのエンドポイントと主キーを使用して、`CosmosClient` オブジェクトのインスタンス化を行う方法を示したものです。

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="role-based-access-control-preview"></a><a id="rbac"></a> ロールベースのアクセス制御 (プレビュー)

Azure Cosmos DB では、次のことを可能にする組み込みのロールベースのアクセス制御 (RBAC) システムを公開しています。

- Azure Active Directory (AAD) の ID を使用してデータ要求を認証する。
- きめ細かいロールベースのアクセス許可モデルを使用してデータ要求を認可する。

Azure Cosmos DB RBAC は、次のような状況で理想的なアクセス制御方法です。

- 主キーなどの共有シークレットを使用するのではなく、トークンベースの認証メカニズムを利用する
- Azure AD の ID を使用して要求を認証する
- 各 ID で実行が許可されるデータベース操作を厳密に制限する、きめ細かなアクセス許可モデルが必要である
- 複数の ID に割り当てることができる "ロール" としてアクセス制御ポリシーを具現化する。

Azure Cosmos DB RBAC の詳細については、[Azure Cosmos DB アカウントのロールベースのアクセス制御の構成](how-to-setup-rbac.md)に関するページを参照してください。

## <a name="resource-tokens"></a><a id="resource-tokens"></a> リソース トークン

リソース トークンは、データベース内のアプリケーション リソースへのアクセスを提供します。 リソース トークン:

- コンテナー、パーティション キー、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、UDF へのアクセスを提供します。
- [ユーザー](#users)が特定のリソースへの[アクセス許可](#permissions)を付与されたときに作成されます。
- アクセス許可リソースが POST、GET、または PUT 呼び出しで動作するときに作成されます。
- ユーザー、リソース、およびアクセス許可用に特別に構築されたハッシュ リソース トークンを使用します。
- カスタマイズ可能な有効期間による時間の拘束があります。 既定の有効期間は 1 時間です。 ただし、トークンの有効期間は、最大 5 時間まで、明示的に指定することができます。
- 主キーの代わりに使用できる安全な代替手段を提供します。
- クライアントが、付与されているアクセス許可に従って、Cosmos DB アカウント内のリソースを読み取り、書き込み、および削除できるようにします。

リソース トークンは、自分の主キーを知らせたくないクライアントに、自分の Cosmos DB アカウント内のリソースへのアクセスを許可する場合に (Cosmos DB ユーザーとアクセス許可を作成することによって) 使用できます。  

Cosmos DB リソース トークンにより、付与されたアクセス許可に従って、主キーや読み取り専用キーなしでクライアントによる Cosmos DB アカウント内のリソースの読み取り、書き込み、および削除を可能にする安全な代替手段が提供されます。

リソース トークンの要求、生成、およびクライアントへの配信に使用されることがある一般的な設計パターンを次に示します。

1. 中間層サービスは、ユーザーの写真を共有するモバイル アプリケーションを提供するために設定します。
2. 中間層サービスは、Cosmos DB アカウントの主キーを持ちます。
3. 写真アプリは、エンド ユーザーのモバイル デバイスにインストールされます。
4. ログイン時に、写真アプリは、中間層サービスを使用してユーザー ID を確立します。 この ID 確立のしくみは完全にアプリケーションに依存します。
5. いったん ID が確立されると、中間層サービスはその ID に基づいてアクセス許可を要求します。
6. 中間層サービスから、リソース トークンが電話アプリに返されます。
7. 電話アプリはリソース トークンを引き続き使用し、リソース トークンによって定義されたアクセス許可を使用して、リソース トークンによって許可された間隔で Cosmos DB リソースに直接アクセスすることができます。
8. リソース トークンの期限が切れると、それ以降の要求は、401 (承認されていない例外) を受け取ります。  この時点で、電話アプリは ID を再度確立し、新しいリソース トークンを要求します。

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB リソース トークンのワークフロー" border="false":::

リソース トークンの生成と管理は、ネイティブ Cosmos DB クライアント ライブラリによって処理されます。ただし、REST を使用する場合は、要求/認証ヘッダーを構築する必要があります。 REST 用の認証ヘッダーの作成については、[Cosmos DB リソースのアクセス制御](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)に関するページや、[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/Authorization/AuthorizationHelper.cs) または [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts) のソース コードを参照してください。

ブローカー リソース トークンを生成するために使用する中間層サービスの例については、[ResourceTokenBroker アプリ](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)に関するページを参照してください。

### <a name="users"></a>ユーザー<a id="users"></a>

Azure Cosmos DB ユーザーは、Cosmos データベースに関連付けらていれます。  各データベースには、0 人以上の Cosmos DB ユーザーを含めることができます。 次のコード サンプルは、[Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement) を使用して Cosmos DB ユーザーを作成する方法を示しています。

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> 各 Cosmos DB ユーザーには、ユーザーに関連付けられた[アクセス許可](#permissions)の一覧を取得するために使用できる ReadAsync() メソッドがあります。

### <a name="permissions"></a>アクセス許可<a id="permissions"></a>

アクセス許可リソースはユーザーに関連付けられ、コンテナーおよびパーティション キー レベルで割り当てられます。 各ユーザーには、0 個以上のアクセス許可を含めることができます。 アクセス許可リソースによって、ユーザーが特定のパーティション キー内の特定のコンテナーまたはデータにアクセスするときに必要なセキュリティ トークンへのアクセスが提供されます。 アクセス許可リソースによって提供できるアクセス レベルは 2 つあります。

- All:ユーザーはリソースに対して完全なアクセス許可を持ちます。
- Read:ユーザーは、リソースの内容の読み取りのみを行えますが、リソースへの書き込み、更新、または削除の操作を実行することはできません。

> [!NOTE]
> ストアド プロシージャを実行するには、ストアド プロシージャを実行するコンテナーの All 権限を持つ必要があります。

[データ プレーン要求に関する診断ログ](cosmosdb-monitor-resource-logs.md)を有効にすると、アクセス許可に対応する次の 2 つのプロパティがログに記録されます。

* **resourceTokenPermissionId** - このプロパティは、指定したリソース トークンのアクセス許可 ID を示します。 

* **resourceTokenPermissionMode** - このプロパティは、リソース トークンの作成時に設定したアクセス許可モードを示します。 アクセス許可モードとして指定できるのは、"all" や "read" などの値です。

#### <a name="code-sample-to-create-permission"></a>アクセス許可を作成するコード サンプル

次のコード サンプルは、アクセス許可リソースを作成し、アクセス許可リソースのリソース トークンを読み取って、先ほど作成した[ユーザー](#users)にアクセス許可を関連付ける方法を示しています。

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

#### <a name="code-sample-to-read-permission-for-user"></a>ユーザーのアクセス許可を読み取るコード サンプル

次のコード スニペットは、上で作成したユーザーに関連付けられているアクセス許可を取得し、1 つのパーティション キーにスコープを指定して、ユーザーに代わって新しい CosmosClient をインスタンス化する方法を示しています。

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="differences-between-rbac-and-resource-tokens"></a>RBAC とリソース トークンの違い

| サブジェクト | RBAC | リソース トークン |
|--|--|--|
| 認証  | Azure Active Directory (Azure AD) を使用。 | ネイティブな Azure Cosmos DB のユーザーに基づく<br>リソース トークンを Azure AD と統合するには、Azure AD の ID と Azure Cosmos DB のユーザーをブリッジするための追加の作業が必要となります。 |
| 承認 | ロールベース: ロールの定義によって、許可されるアクションがマップされ、複数の ID に割り当てることができます。 | アクセス許可ベース: Azure Cosmos DB のユーザーごとに、データのアクセス許可を割り当てる必要があります。 |
| トークンのスコープ | AAD トークンでは、要求元の ID が保持されます。 この ID は、割り当てられているすべてのロールの定義と照合され、認可が実行されます。 | リソース トークンでは、特定の Azure Cosmos DB ユーザーに付与された特定の Azure Cosmos DB リソースに対するアクセス許可が保持されます。 異なるリソースに対する認可要求では、異なるトークンが必要になる場合があります。 |
| トークンの更新 | AAD トークンは、有効期限が切れると Azure Cosmos DB SDK によって自動的に更新されます。 | リソース トークンの更新はサポートされていません。 リソース トークンの有効期限が切れた場合は、新しいリソース トークンを発行する必要があります。 |

## <a name="add-users-and-assign-roles"></a>ユーザーの追加とロールの割り当てを行う

ユーザー アカウントに Azure Cosmos DB アカウントの閲覧者アクセス権を追加するには、サブスクリプションの所有者が Azure Portal で以下の手順を実行します。

1. Azure Portal を開き、Azure Cosmos DB アカウントを選択します。
2. **[アクセス制御 (IAM)]** タブをクリックし、 **[+ ロール割り当ての追加]** をクリックします。
3. **[ロール割り当ての追加]** ウィンドウの **[ロール]** ボックスで、 **[Cosmos DB アカウントの閲覧者ロール]** を選択します。
4. **[アクセスの割り当て先]** ボックスで、 **[Azure AD のユーザー、グループ、またはアプリケーション]** を選択します。
5. ディレクトリで、アクセス権を付与するユーザー、グループ、またはアプリケーションを選択します。  ディレクトリは、表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索できます。
    選択したメンバー、グループ、またはアプリケーションが、選択したメンバー一覧に表示されます。
6. **[保存]** をクリックします。

そのエンティティが Azure Cosmos DB リソースを閲覧できるようになります。

## <a name="delete-or-export-user-data"></a>ユーザー データの削除またはエクスポート

データベース サービスとして、Azure Cosmos DB では、データベースまたはコンテナーにあるすべてのデータを、検索、選択、変更、削除することができます。 ただし、必要に応じて、提供されている API を使用し、個人データを検索および消去するために必要なロジックを定義することはお客様の責任です。 各マルチモデル API (SQL、MongoDB、Gremlin、Cassandra、Table) では、カスタム述語に基づいてデータを検索および削除するメソッドを含む SDK をさまざまな言語で提供しています。 [Time to Live (TTL)](time-to-live.md) 機能を有効にすると、追加のコストの発生なしに、指定した期間後に自動的にデータを削除するようにすることもできます。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次のステップ

- Cosmos データベースのセキュリティの詳細については、[Cosmos DB: データベースのセキュリティ](database-security.md)に関する記事を参照してください。
- Azure Cosmos DB 認証トークンを作成する方法については、[Azure Cosmos DB リソースのアクセス制御](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)に関するページをご覧ください。
- ユーザーとアクセス許可を使用したユーザー管理サンプルについては、[.NET SDK v3 ユーザー管理サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)を参照してください
