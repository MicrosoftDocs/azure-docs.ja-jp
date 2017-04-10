---
title: "DocumentDB のデータへのアクセスをセキュリティで保護する方法 | Microsoft Docs"
description: "マスター キー、読み取り専用キー、ユーザー、アクセス許可など、DocumentDB のアクセス制御の概念について説明します。"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: e4cbc9b0c9532d56376c4fabebcde8c64cb0474b
ms.lasthandoff: 03/24/2017


---
# <a name="securing-access-to-documentdb-data"></a>DocumentDB のデータへのアクセスのセキュリティ保護
この記事では、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)に格納されたデータへのアクセスをセキュリティ保護する方法の概要を説明します。

DocumentDB では、2 種類のキーを使用してユーザーを認証し、そのデータとリソースへのアクセスを提供しています。 

|キーの種類|リソース|
|---|---|
|[マスター キー](#master-keys) |次の管理リソースで使用されます。データベース アカウント、データベース、ユーザー、およびアクセス許可|
|[リソース トークン](#resource-tokens)|次のアプリケーション リソースで使用されます。コレクション、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、および UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>マスター キー 

マスター キーは、データベース アカウントのすべての管理リソースへのアクセスを提供します。 マスター キー:  
- アカウント、データベース、ユーザー、およびアクセス許可へのアクセスを提供します。 
- コレクションとドキュメントへのきめ細かいアクセスを提供するために使用することはできません。
- アカウントの作成時に作成されます。
- いつでも再生成することができます。

各アカウントは、プライマリ キーとセカンダリ キーという 2 つのマスター キーで構成されます。 二重キーの目的は、キーを再生成 (ロール) して、アカウントとデータに継続的にアクセスできるようにするためです。 

DocumentDB アカウント用の 2 つのマスター キーに加えて、2 つの読み取り専用キーがあります。 これらの読み取り専用キーは、アカウントの読み取り操作のみを許可します。 読み取り専用キーは、アクセス許可リソースを読み取るためのアクセスを提供しません。

プライマリ、セカンダリ、読み取り専用、および読み取り/書き込みのマスター キーは、Azure Portal で取得と再生成を行うことができます。 手順については、「[アクセス キーを表示、コピー、および再生成する](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys)」を参照してください。

![Azure Portal でのアクセス制御 (IAM) - NoSQL データベースのセキュリティ](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-portal.png)

マスター キーのローテーション プロセスは単純です。 Azure Portal に移動し、セカンダリ キーを取得します。次に、アプリケーションでプライマリ キーをセカンダリ キーに置き換えます。その後、Azure Portal でプライマリ キーをローテーションします。

![Azure Portal でのマスター キーのローテーション - NoSQL データベースのセキュリティ](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>マスター キーを使用するコード サンプル

次のコード サンプルは、DocumentDB アカウントのエンドポイントとマスター キーを使用して、DocumentClient のインスタンス化とデータベースの作成を行う方法を示しています。 

```csharp
//Read the DocumentDB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the NOSQL (DocumentDB) account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>リソース トークン

リソース トークンは、データベース内のアプリケーション リソースへのアクセスを提供します。 リソース トークン:
- コレクション、パーティション キー、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、UDF へのアクセスを提供します。
- [ユーザー](#users)が特定のリソースへの[アクセス許可](#permissions)を付与されたときに作成されます。
- アクセス許可リソースが POST、GET、または PUT 呼び出しで動作するときに作成されます。
- ユーザー、リソース、およびアクセス許可用に特別に構築されたハッシュ リソース トークンを使用します。
- カスタマイズ可能な有効期間による時間の拘束があります。 既定の有効期間は 1 時間です。 ただし、トークンの有効期間は、最大 5 時間まで、明示的に指定することができます。
- マスター キーの代わりに使用できる安全な代替手段を提供します。 
- クライアントが、付与されているアクセス許可に従って、DocumentDB アカウント内のリソースを読み取り、書き込み、および削除できるようにします。

リソース トークンは、自分のマスター キーを知らせたくないクライアントに、自分の DocumentDB アカウント内のリソースへのアクセスを許可する場合に (DocumentDB ユーザーとアクセス許可を作成することによって) 使用できます。  

DocumentDB リソース トークンにより、付与されたアクセス許可に従って、マスター キーや読み取り専用キーなしでクライアントによる DocumentDB アカウント内のリソースの読み取り、書き込み、および削除を可能にする安全な代替手段が提供されます。

リソース トークンの要求、生成、およびクライアントへの配信に使用されることがある一般的な設計パターンを次に示します。

1. 中間層サービスは、ユーザーの写真を共有するモバイル アプリケーションを提供するために設定します。 
2. 中間層サービスは、DocumentDB アカウントのマスター キーを持ちます。
3. 写真アプリは、エンド ユーザーのモバイル デバイスにインストールされます。 
4. ログイン時に、写真アプリは、中間層サービスを使用してユーザー ID を確立します。 この ID 確立のしくみは完全にアプリケーションに依存します。
5. いったん ID が確立されると、中間層サービスはその ID に基づいてアクセス許可を要求します。
6. 中間層サービスから、リソース トークンが電話アプリに返されます。
7. 電話アプリはリソース トークンを引き続き使用し、リソース トークンによって定義されたアクセス許可を使用して、リソース トークンによって許可された間隔で DocumentDB リソースに直接アクセスすることができます。 
8. リソース トークンの期限が切れると、それ以降の要求は、401 (承認されていない例外) を受け取ります。  この時点で、電話アプリは ID を再度確立し、新しいリソース トークンを要求します。

    ![DocumentDB リソース トークンのワークフロー](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

リソース トークンの生成と管理は、ネイティブ DocumentDB クライアント ライブラリによって処理されます。ただし、REST を使用する場合は、要求/認証ヘッダーを構築する必要があります。 REST 用の認証ヘッダーの作成については、「[Access Control on DocumentDB Resources (DocumentDB のアクセス制御)](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources)」または[SDK のソース コード](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js)を参照してください。

ブローカー リソース トークンを生成するために使用する中間層サービスの例については、[ResourceTokenBroker アプリ](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)に関するページを参照してください。

<a id="users"></a>

## <a name="users"></a>ユーザー
DocumentDB ユーザーは DocumentDB データベースに関連付けられます。  各データベースには、0 人以上の DocumentDB ユーザーを含めることができます。  次のコード サンプルは、DocumentDB ユーザー リソースを作成する方法を示しています。

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> DocumentDB の各ユーザーは PermissionsLink プロパティを持っています。このプロパティを使用して、ユーザーに関連付けられた[アクセス許可](#permissions)の一覧を取得することができます。
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>アクセス許可
DocumentDB アクセス許可リソースは DocumentDB ユーザーに関連付けられています。  各ユーザーは、0 個以上の DocumentDB アクセス許可を持つ可能性があります。  アクセス許可リソースは、特定のアプリケーション リソースにアクセスするときに必要なセキュリティ トークンへのアクセスを提供します。
アクセス許可リソースによって提供できるアクセス レベルは 2 つあります。

* All: ユーザーはリソースに対して完全なアクセス許可を持ちます。
* Read: ユーザーは、リソースの内容を読み取りのみができますが、リソースへの書き込み、更新、または削除の操作を実行することはできません。

> [!NOTE]
> DocumentDB ストアド プロシージャを実行するには、ストアド プロシージャを実行するコレクションの All 権限を持つ必要があります。
> 
> 

### <a name="code-sample-to-create-permission"></a>アクセス許可を作成するコード サンプル

次のコード サンプルは、アクセス許可リソースを作成し、アクセス許可リソースのリソース トークンを読み取って、先ほど作成した[ユーザー](#users)にアクセス許可を関連付ける方法を示しています。

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

コレクションに対してパーティション キーを指定した場合は、コレクション、ドキュメント、添付ファイル リソースのアクセス許可にも、ResourceLink に加えて ResourcePartitionKey が含まれる必要があります。

### <a name="code-sample-to-read-permissions-for-user"></a>ユーザーのアクセス許可を読み取るコード サンプル

特定のユーザーに関連付けられているすべてのアクセス許可リソースを簡単に取得するために、DocumentDB では、各ユーザー オブジェクトのアクセス許可フィードを利用できるようになっています。  次のコード スニペットは、上記で作成したユーザーに関連付けられている権限を取得し、権限の一覧を作成して、ユーザーの代わりに新しい DocumentClient をインスタンス化する方法を示します。

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>次のステップ
* DocumentDB データベースのセキュリティの詳細については、「[DocumentDB: NoSQL データベースのセキュリティ](documentdb-nosql-database-security.md)」を参照してください。
* マスター キーと読み取り専用キーの詳細については、「[DocumentDB アカウントの管理方法](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys)」を参照してください。
* DocumentDB 認証トークンを作成する方法については、「[DocumentDB リソースのアクセス制御](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources)」を参照してください。

