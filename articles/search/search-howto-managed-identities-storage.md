---
title: マネージド ID を使用してストレージ アカウントへの接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して、Azure Storage アカウントへのインデクサー接続を設定する方法を学ぶ
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f26ca04955dfa854a8ee17b7aa255a6ed991b8df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358373"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity"></a>マネージド ID を使用して、Azure Storage アカウントへの接続を設定する

このページでは、データ ソースのオブジェクト接続文字列で資格情報を指定する代わりに、マネージド ID を使用して Azure ストレージ アカウントへのインデクサー接続を設定する方法を説明します。

この機能についてさらに学ぶ前に、インデクサーとは何かについて、およびデータ ソースに対してインデクサーを設定する方法について理解しておくことをお勧めします。 以下のリンクで詳しい情報を確認できます。
* [インデクサーの概要](search-indexer-overview.md)
* [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
* [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>接続を設定する

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - システム割り当てマネージド ID をオンにする

システム割り当てマネージド ID が有効になると、Azure で検索サービスのための ID が作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 この ID はその後、インデックス作成中にデータへのアクセスを許可する Azure ロールベースのアクセス制御 (Azure RBAC) の割り当てで、使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")
 
### <a name="2---add-a-role-assignment"></a>2 - ロールの割り当てを追加する

この手順では、Azure Cognitive Search サービスに、ストレージ アカウントからデータを読み取るためのアクセス許可を付与します。

1. Azure portal で、インデックスを作成するデータを含むストレージ アカウントに移動します。
2. **[アクセス制御 (IAM)]** を選択します
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-storage.png "ロールの割り当ての追加")

4. インデックスを作成するストレージ アカウントの種類に基づいて、適切なロールを選択します。
    1. Azure BLOB ストレージでは、**ストレージ BLOB データ閲覧者** ロールに検索サービスを追加する必要があります。
    1. Azure Data Lake Storage Gen2 では、**ストレージ BLOB データ閲覧者** ロールに検索サービスを追加する必要があります。
    1. Azure テーブル ストレージでは、**閲覧者とデータ アクセス** ロールに検索サービスを追加する必要があります。
5.  **[アクセスの割り当て先]** は **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます
6.  検索サービスを検索し、それを選んでから、 **[保存]** を選択します

    Azure Blob storage と Azure Data Lake Storage Gen2 の例を次に示します。

    ![ストレージ BLOB データ閲覧者ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "ストレージ BLOB データ閲覧者ロールの割り当てを追加する")

    Azure Table Storage の例を次に示します。

    ![閲覧者とデータ アクセスのロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "閲覧者とデータ アクセスのロールの割り当てを追加する")

### <a name="3---create-the-data-source"></a>3 - データ ソースを作成する

マネージド ID 接続文字列は、[REST API](/rest/api/searchservice/create-data-source)、Azure portal、および [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) でサポートされています。 次に、[REST API](/rest/api/searchservice/create-data-source) とマネージド ID 接続文字列を使用し、ストレージ アカウントのデータにインデックスを付けてデータ ソースを作成する方法例を示します。 マネージド ID 接続文字列の形式は、REST API、.NET SDK、および Azure portal において同じです。

ストレージ アカウントからインデックスを作成する場合は、データ ソースに次の必須プロパティが必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **type**
    * Azure Blob Storage: `azureblob`
    * Azure Table Storage: `azuretable`
    * Azure Data Lake Storage Gen2: [こちらのフォーム](https://aka.ms/azure-cognitive-search/mi-preview-request)を使用してプレビューにサインアップすると、この **種類** が提供されます。
* **credentials**
    * マネージド ID を使用して認証する場合、**credentials** 形式は、マネージド ID を使用しない場合とは異なります。 ここでは、アカウント キーまたはパスワードのない ResourceId を指定します。 ResourceId には、ストレージ アカウントのサブスクリプション ID、ストレージ アカウントのリソース グループ、およびストレージ アカウント名を含める必要があります。
    * マネージド ID の形式: 
        * *ResourceId=/subscriptions/**ご利用のサブスクリプション ID**/resourceGroups/**ご利用のリソース グループの名前**/providers/Microsoft.Storage/storageAccounts/**ご利用のストレージ アカウントの名前**/;*
* **container** では、ストレージ アカウントにあるコンテナーまたはテーブルを指定します。 既定では、コンテナー内のすべての BLOB を取得できます。 特定の仮想ディレクトリにある BLOB についてのみインデックスを作成する場合は、オプションの **query** パラメーターを使用してそのディレクトリを指定できます。

[REST API](/rest/api/searchservice/create-data-source) を使用して BLOB データ ソース オブジェクトを作成する方法の例:

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

ここでは、BLOB から抽出されたテキストを格納するために、検索可能な `content` フィールドを含むインデックスを作成する方法を示します。   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

インデックスの作成の詳細については、[インデックスの作成](/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="5---create-the-indexer"></a>5 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースが作成されたら、インデクサーを作成できます。

BLOB インデクサーのインデクサー定義の例:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「[インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="accessing-secure-data-in-storage-accounts"></a>ストレージ アカウントで安全なデータにアクセスする

Azure ストレージ アカウントは、ファイアウォールと仮想ネットワークを利用することでさらにセキュリティを強化できます。 ファイアウォールや仮想ネットワークを利用してセキュリティを強化した BLOB ストレージ アカウントまたは Data Lake Gen2 ストレージ アカウントからコンテンツにインデックスを付ける場合、「[信頼されたサービスの例外を使用してストレージ アカウントのデータに安全にアクセスする](search-indexer-howto-access-trusted-service-exception.md)」の指示に従ってください。

## <a name="see-also"></a>関連項目

Azure Storage インデクサーの詳細については、以下を参照してください。

* [Azure BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 インデクサー](search-howto-index-azure-data-lake-storage.md)
* [Azure テーブル インデクサー](search-howto-indexing-azure-tables.md)