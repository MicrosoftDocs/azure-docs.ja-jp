---
title: マネージド ID を使用して Azure SQL Database への接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して Azure SQL Database へのインデクサー接続を設定する方法について説明します
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358424"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>マネージド ID を使用して Azure SQL Database へのインデクサー接続を設定する

このページでは、データ ソース オブジェクトの接続文字列に資格情報を指定する代わりに、マネージド ID を使用して Azure SQL Database へのインデクサー接続を設定する方法について説明します。

この機能について詳しく学ぶ前に、インデクサーとは何か、また、データ ソースに対してインデクサーを設定する方法について理解しておくことをお勧めします。 以下のリンクで詳しい情報を確認できます。

* [インデクサーの概要](search-indexer-overview.md)
* [Azure SQL インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>マネージド ID を使用して接続を設定する

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - システム割り当てマネージド ID をオンにする

システム割り当てマネージド ID が有効になると、Azure で検索サービスのための ID が作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 この ID はその後、インデックス作成中にデータへのアクセスを許可する Azure ロールベースのアクセス制御 (Azure RBAC) の割り当てで、使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID を有効にする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - SQL Server に対して Azure Active Directory の管理者をプロビジョニングする

次の手順でデータベースに接続する際には、検索サービス上でデータベースへのアクセスが許可されるように、データベースに対する管理者アクセス権がある Azure Active Directory (Azure AD) アカウントで接続を行う必要があります。

[こちら](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database)の手順に従って、ご自身の Azure AD アカウントにデータベースへの管理者アクセス権を付与してください。

### <a name="3---assign-the-search-service-permissions"></a>3 - 検索サービスにアクセス許可を割り当てる

次の手順に従って、検索サービスにデータベースの読み取りアクセス許可を割り当てます。

1. Visual Studio に接続します

    ![Visual Studio に接続する](./media/search-managed-identities/connect-with-visual-studio.png "Visual Studio に接続する")

2. Azure AD アカウントを使用して認証を行う

    ![認証](./media/search-managed-identities/visual-studio-authentication.png "認証")

3. 次のコマンドを実行します。

    検索サービス名をかっこで囲んで指定します。
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![新しいクエリ](./media/search-managed-identities/visual-studio-new-query.png "[新しいクエリ]")

    ![クエリを実行する](./media/search-managed-identities/visual-studio-execute-query.png "クエリの実行")

>[!NOTE]
> この手順の完了後に手順 1 からの検索サービス ID が変更された場合、ロールのメンバーシップを削除し、SQL データベース上のユーザーを削除してから、手順3 をもう一度完了してアクセス許可を再追加します。
> ロールのメンバーシップとユーザーを削除するには、次のコマンドを実行します。
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - ロールの割り当てを追加する

この手順では、Azure Cognitive Search サービスに、SQL Server からデータを読み取るためのアクセス許可を付与します。

1. Azure portal 上で、お使いの Azure SQL Server のページに移動します。
2. **[アクセス制御 (IAM)]** を選択します
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-sql-server.png "ロールの割り当ての追加")

4. 適切な **リーダー** ロールを選択します。
5. **[アクセスの割り当て先]** は **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます
6. お使いの検索サービスを検索し、それを選んでから、 **[保存]** を選択します

    ![リーダー ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "リーダー ロールの割り当てを追加する")

### <a name="5---create-the-data-source"></a>5 - データ ソースを作成する

[REST API](/rest/api/searchservice/create-data-source)、Azure portal、および [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) では、マネージド ID 接続文字列がサポートされています。 次に、[REST API](/rest/api/searchservice/create-data-source) とマネージド ID 接続文字列を使用し、Azure SQL Database のデータにインデックスを付けてデータ ソースを作成する方法例を示します。 マネージド ID 接続文字列の形式は、REST API、.NET SDK、および Azure portal において同じです。

[REST API](/rest/api/searchservice/create-data-source) を使用してデータ ソースを作成する場合、データ ソースには次の必須プロパティが必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **型** は、`azuresql` です
* **credentials**
    * マネージド ID を使用して認証する場合、**credentials** 形式は、マネージド ID を使用しない場合とは異なります。 この場合、Initial Catalog または Database の名前と、アカウント キーまたはパスワードが設定されていない ResourceId を指定します。 ResourceId には、Azure SQL Database のサブスクリプション ID、SQL データベースのリソース グループ、および SQL データベースの名前を含める必要があります。 
    * マネージド ID の接続文字列の形式:
        * *Initial Catalog|Database=**データベース名**;ResourceId=/subscriptions/**お使いのサブスクリプション ID**/resourceGroups/**お使いのリソース グループ名**/providers/Microsoft.Sql/servers/**お使いの SQL Server 名**/;Connection Timeout=**接続タイムアウトの長さ**;*
* **container** には、インデックスを作成する対象のテーブルまたはビューの名前を指定します。

[REST API](/rest/api/searchservice/create-data-source) を使用して Azure SQL データ ソース オブジェクトを作成する方法の例:

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

検索可能な `booktitle` フィールドを使用してインデックスを作成する方法を次に示します。   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

インデックスの作成の詳細については、[インデックスの作成](/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="7---create-the-indexer"></a>7 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースの作成を終えたら、次はインデクサーを作成できます。

Azure SQL インデクサーのインデクサー定義の例:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

インデクサーからデータ ソースへの接続を試みたときに、クライアントではサーバーへのアクセスが許可されていないことを示すエラーを受信した場合は、[一般的なインデクサー エラー](./search-indexer-troubleshooting.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

Azure SQL インデクサーに関する詳細を確認します。
* [Azure SQL インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)