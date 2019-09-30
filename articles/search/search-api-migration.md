---
title: 最新の Azure Search Service REST API バージョンへのアップグレード - Azure Search
description: API バージョンの違いを確認し、既存のコードを最新の Azure Search Service REST API バージョンに移行するために必要なアクションについて説明します。
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 6c1f7fdb1f349c9e31ba63d79a9b9e26ea9f09da
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182392"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>最新の Azure Search Service REST API バージョンへのアップグレード
[Azure Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/) の以前のバージョンを使用している場合、この記事を参考にして、一般公開された最新の API バージョン 2019-05-06 を使用するようにアプリケーションをアップグレードできます。

バージョン 2019-05-06 の REST API には、前のバージョンからの変更点がいくつか含まれています。 大半は下位互換性を保つための変更であるため、使用していたバージョンに応じて、コードの変更は最小限で済むはずです。 新機能を使用するために必要なコードの変更については、「[アップグレードの手順](#UpgradeSteps)」で概説しています。

> [!NOTE]
> Azure Search サービスのインスタンスでは、以前のバージョンを含む、特定の範囲の REST API バージョンがサポートされます。 それらの API バージョンを引き続き使用することもできますが、最新のバージョンに移行して、新機能を利用できるようにすることをお勧めします。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>バージョン 2019-05-06 の新機能
バージョン 2019-05-06 は、Azure Search Service REST API の最新の一般公開リリースです。 この API バージョンで一般公開状態に移行した機能を次に示します。

* [オートコンプリート](index-add-suggesters.md)。用語の一部を入力するだけでその用語全体が表示される先行入力機能です。

* [複合型](search-howto-complex-data-types.md)。Azure Search インデックス内で構造化オブジェクト データがネイティブでサポートされます。

* [JsonLines 解析モード](search-howto-index-json-blobs.md)。Azure BLOB インデックスの一部で、JSON エンティティごとに 1 つの検索ドキュメントが新規行として作成されます。

* [Cognitive Search](cognitive-search-concept-intro.md)。Cognitive Services の AI エンリッチメント エンジンを利用してインデックスが作成されます。

プレビュー機能リリースのいくつかが、この一般公開の更新プログラムに組み込まれています。 新しいプレビュー機能の一覧を確認するには、[Search REST api-version 2019-05-06 プレビューに関するページ](search-api-preview.md)を参照してください。

## <a name="breaking-changes"></a>重大な変更

api-version=2019-05-06 では、次の機能を含む既存のコードは中断されます。

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB のインデクサー - 現在のデータソースは "type": "cosmosdb"

[Cosmos DB インデクサー](search-howto-index-cosmosdb.md )を使用している場合は、 `"type": "documentdb"` を `"type": "cosmosdb"` に変更する必要があります。

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>インデクサーの実行結果のエラー状態が表示されなくなりました

以前、インデクサー実行のエラー構造には、`status` 要素が含まれていました。 この要素は、有用な情報を提供しないため、削除されました。

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>インデクサーのデータ ソース API で接続文字列が返されなくなりました

API バージョン 2019-05-06 および 2019-05-06 プレビュー以降、データ ソース API では、REST 操作の応答として、接続文字列が返されなくなりました。 これまでの API バージョンで、データ ソースの作成に POST が使用されていた場合は、Azure Search で **201** と、プレーンテキストの接続文字列を含む OData 応答が返されました。

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>名前付きエンティティの認識コグニティブ スキルは廃止されました

コード内で[名前付きエンティティの認識](cognitive-search-skill-named-entity-recognition.md)スキルを呼び出しても、呼び出しは失敗します。 これに代わる機能は、[エンティティの認識](cognitive-search-skill-entity-recognition.md)です。 他の変更を行うことなく、スキルの参照を置換できる必要があります。 API の署名は、両方のバージョンで同じです。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
以前の GA バージョンの 2017-11-11 または 2016-09-01 からアップグレードする場合、コードの変更は、バージョン番号の変更以外はほぼ必要ありません。 次の状況に該当する場合のみ、コードを変更する必要があります。

* API 応答で認識できないプロパティが返されるとコードが失敗する。 既定では、アプリケーションは、認識できないプロパティを無視する必要があります。

* コードが API 要求を保持し、新しい API バージョンへの再送信を試行する。 たとえば、この状況は、アプリケーションが Search API から返される継続トークンを保持する場合に発生する可能性があります (詳細については、[Search API リファレンス](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)のページの `@search.nextPageParameters` を参照してください)。

このような状況のいずれかが当てはまる場合は、それに応じてコードを変更する必要があります。 それ以外の場合は、バージョン 2019-05-06 の[新機能](#WhatsNew)の使用を開始するのでない限り、変更する必要はありません。

プレビュー版の API からアップグレードする場合も上記の説明が適用されますが、バージョン 2019-05-06 では使用できないプレビュー機能があることにも注意する必要があります。

* ["More like this" クエリ](search-more-like-this.md)
* [CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
* [Cosmos DB インデクサーの MongoDB API サポート](search-howto-index-cosmosdb.md)

コードでこれらの機能を使用している場合は、それらの使用を削除しないと、API バージョン 2019-05-06 にアップグレードすることはできません。

> [!IMPORTANT]
> プレビュー版の API は、テストと評価を目的としたものです。運用環境での使用は避けてください。
> 

### <a name="upgrading-complex-types"></a>複合型のアップグレード

以前のプレビュー版 API である 2017-11-11 プレビューまたは 2016-09-01 プレビューに対して、コードで複合型が使用されている場合は、バージョン 2019-05-06 で、いくつかの新しい制限や変更された制限があることに注意する必要があります。

+ サブ フィールドの深さとインデックスあたりの複合コレクションの数に関する制限が低くなりました。 プレビュー版の API を使用してこれらの制限を超えるインデックスを作成した場合、API バージョン 2019-05-06 を使用してインデックスを更新または再作成しようとしても失敗します。 これが当てはまる場合は、新しい制限内に収まるようにスキーマを再設計してから、インデックスをリビルドする必要があります。

+ API バージョン 2019-05-06 では、ドキュメントあたりの複合コレクションの数に関して新しい制限があります。 プレビュー版の API を使用して、これらの制限を超えるドキュメントのインデックスを作成した場合、API バージョン 2019-05-06 を使用してそのデータのインデックスを再作成しようとしても失敗します。 これが当てはまる場合は、ドキュメントあたりの複合コレクション要素の数を減らしてから、データのインデックスを再作成する必要があります。

詳細については、「[Service limits for Azure Search (Azure Search サービスの制限)](search-limits-quotas-capacity.md)」を参照してください。

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>以前の複合型の構造をアップグレードする方法

以前のいずれかのプレビュー版の API に対して、コードで複合型が使用されている場合、使用されているインデックス定義の形式は、次のようになります。

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

新しいツリーのような形式でインデックスのフィールドを定義する方式は、API バージョン 2017-11-11 プレビューで導入されました。 この新しい形式では、それぞれの複合フィールドに、サブ フィールドが定義されたフィールド コレクションがあります。 API バージョン 2019-05-06 の場合、この新しい形式は、排他的に使用されます。古い形式を使用してインデックスを作成または更新しようとすると失敗します。 古い形式を使用してインデックスを作成した場合は、API バージョン 2017-11-11 プレビューを使用してインデックスを新しい形式に更新してから、API バージョン 2019-05-06 を使用してインデックスを管理する必要があります。

"フラット" インデックスを新しい形式に更新するには、API バージョン 2017-11-11 プレビューを使用して、次の手順を実行します。

1. GET 要求を実行してインデックスを取得します。 既に新しい形式になっている場合は、作業完了です。

2. インデックスを "フラット" 形式から新しい形式に変換します。 この記事の執筆時点では、サンプル コードが用意されていないため、コードは自分で作成する必要があります。

3. PUT 要求を実行し、インデックスを新しい形式に更新します。 インデックスのその他の詳細 (フィールドの検索可能性やフィルター可能性など) が変更されていないことを確認してください。これはインデックスの更新 API で許可されていません。

> [!NOTE]
> 古い "フラット" 形式で作成されたインデックスを Azure portal から管理することはできません。 なるべく早く、インデックスを "フラット" 表現から "ツリー" 表現にアップグレードしてください。

## <a name="next-steps"></a>次の手順

Azure Search Service REST API リファレンス ドキュメントをレビューします。 問題が発生した場合は、[StackOverflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)

