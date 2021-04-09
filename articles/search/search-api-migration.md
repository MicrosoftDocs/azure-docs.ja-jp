---
title: REST API のバージョンをアップグレードする
titleSuffix: Azure Cognitive Search
description: API バージョンの違いを確認し、既存のコードを最新の Azure Cognitive Search サービス REST API バージョンに移行するために必要なアクションについて学習します。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929644"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Azure Cognitive Search で最新の REST API にアップグレードする

以前のバージョンの [**Search REST API**](/rest/api/searchservice/) を使用している場合は、一般公開された最新の API バージョン **2020-06-30** を使用するようにアプリケーションをアップグレードするのに、この記事が役立ちます。

バージョン 2020-06-30 には重要な新機能 ([ナレッジ ストア](knowledge-store-concept-intro.md)) が含まれており、いくつかの小さな動作変更が導入されています。 このバージョンのほとんどの部分は下位互換性があるため、以前のバージョン (2019-05-06) からアップグレードする場合、コードの変更は最小限で済むはずです。

> [!NOTE]
> 検索サービスでは、以前のものを含む、広範な REST API のバージョンがサポートされています。 それらの API バージョンを引き続き使用することもできますが、最新のバージョンに移行して、新機能を利用できるようにすることをお勧めします。 時間が経つと、REST API の最も古いバージョンは非推奨になり、[サポートされなくなります](search-api-versions.md#unsupported-versions)。

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>アップグレードする方法

新しいバージョンにアップグレードするときは、バージョン番号の変更以外、コードを大幅に変更する必要はおそらくありません。 次の状況に該当する場合のみ、コードを変更する必要があります。

* API 応答で認識できないプロパティが返されるとコードが失敗する。 既定では、アプリケーションは、認識できないプロパティを無視する必要があります。

* コードが API 要求を保持し、新しい API バージョンへの再送信を試行する。 たとえば、この状況は、アプリケーションが Search API から返される継続トークンを保持する場合に発生する可能性があります (詳細については、[Search API リファレンス](/rest/api/searchservice/Search-Documents)のページの `@search.nextPageParameters` を参照してください)。

* コードで 2019-05-06 以前の API バージョンを参照していて、そのリリースで 1 つ以上の破壊的変更の対象となっている。 「[2019-05-06 へのアップグレード](#upgrade-to-2019-05-06)」セクションに詳細が記載されています。 

これらの状況のいずれかに該当する場合は、それに応じてコードを変更する必要が生じる可能性があります。 それ以外の場合は、新しいバージョンで追加された新機能の使用を開始するのであっても、変更は不要です。

## <a name="upgrade-to-2020-06-30"></a>2020-06-30 へのアップグレード

バージョン 2020-06-30 は、REST API の新しい一般公開リリースです。 1 つの破壊的変更と複数の動作の違いがあります。 

この API バージョンで一般公開されるようになった機能には、以下が含まれます。

* [ナレッジ ストア](knowledge-store-concept-intro.md)は、スキルセットを通して作成される充実したコンテンツから成る永続的ストレージで、ダウンストリーム分析や他のアプリケーションを通した処理のために作成されます。 この機能を使用すると、インデクサー駆動型の AI エンリッチメント パイプラインにより、検索インデックスに加えてナレッジ ストアにデータを格納できます。 この機能のプレビュー バージョンを使用していた場合、それは一般公開されたバージョンと同等です。 必要とされるコード変更は、API バージョンの変更のみです。

### <a name="breaking-change"></a>互換性に影響する変更点

以前の API バージョンに基づいて記述された既存のコードは、コードに以下の機能が含まれる場合、api-version=2020-06-30 以降では使用できなくなります。

* フィルター式のすべての Edm.Date リテラル (`2020-12-12` のような年月日で構成される日付) は、Edm.DateTimeOffset 形式 `2020-12-12T00:00:00Z` に従う必要があります。 タイムゾーンの違いによる間違ったクエリ結果や予期しないクエリ結果を処理するために、この変更が必要でした。

### <a name="behavior-changes"></a>動作の変更

* [BM25 ランク付けアルゴリズム](index-ranking-similarity.md)では、前のランク付けアルゴリズムがより新しいテクノロジに置き換えられます。 新しいサービスはこのアルゴリズムを自動的に使用します。 既存のサービスについては、新しいアルゴリズムを使用するようにパラメーターを設定する必要があります。

* このバージョンでは、順序付けされた null 値の結果が変更されていて、null 値は、並べ替えが `asc` の場合は最初に、並べ替えが `desc` の場合は最後に表示されます。 null 値を並べ替える方法を扱うコードを記述した場合は、この変更に注意してください。

## <a name="upgrade-to-2019-05-06"></a>2019-05-06 へのアップグレード

バージョン 2019-05-06 は、前に一般公開された REST API のリリースです。 この API バージョンで一般公開された機能には、以下が含まれます。

* [オートコンプリート](index-add-suggesters.md)。用語の一部を入力するだけでその用語全体が表示される先行入力機能です。
* [複合型](search-howto-complex-data-types.md)。検索インデックス内で構造化オブジェクト データがネイティブでサポートされます。
* [JsonLines 解析モード](search-howto-index-json-blobs.md)。Azure BLOB インデックスの一部で、JSON エンティティごとに 1 つの検索ドキュメントが新規行として作成されます。
* [AI エンリッチメント](cognitive-search-concept-intro.md)では、Cognitive Services の AI エンリッチメント エンジンを利用してインデックスが提供されます。

### <a name="breaking-changes"></a>重大な変更

以前の API バージョンに基づいて記述された既存のコードは、コードに以下の機能が含まれる場合、api-version=2019-05-06 以降では使用できなくなります。

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Azure Cosmos DB のインデクサー - 現在のデータソースは "type": "cosmosdb"

[Cosmos DB インデクサー](search-howto-index-cosmosdb.md )を使用している場合は、 `"type": "documentdb"` を `"type": "cosmosdb"` に変更する必要があります。

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>インデクサーの実行結果のエラー状態が表示されなくなりました

以前、インデクサー実行のエラー構造には、`status` 要素が含まれていました。 この要素は、有用な情報を提供しないため、削除されました。

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>インデクサーのデータ ソース API で接続文字列が返されなくなりました

API バージョン 2019-05-06 および 2019-05-06 プレビュー以降、データ ソース API では、REST 操作の応答として、接続文字列が返されなくなりました。 以前の API バージョンで、データ ソースの作成に POST が使用されていた場合は、Azure Cognitive Search で **201** に続いて、プレーンテキストの接続文字列を含む OData 応答が返されました。

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>名前付きエンティティの認識コグニティブ スキルは廃止されました

コード内で[名前付きエンティティの認識](cognitive-search-skill-named-entity-recognition.md)スキルを呼び出した場合、その呼び出しは失敗します。 これに代わる機能は、[エンティティの認識](cognitive-search-skill-entity-recognition.md)です。 他の変更を行うことなく、スキルの参照を置換できる必要があります。 API の署名は、両方のバージョンで同じです。 

### <a name="upgrading-complex-types"></a>複合型のアップグレード

API バージョン 2019-05-06 では、複合型の正式なサポートが追加されました。 コードで、2017-11-11-Preview または 2016-09-01-Preview での複合型の等価性に関する以前の推奨事項を実装した場合、バージョン 2019-05-06 以降では、注意が必要な、変更された新しい制限がいくつか追加されています。

+ サブ フィールドの深さとインデックスあたりの複合コレクションの数に関する制限が低くなりました。 プレビュー版の API を使用してこれらの制限を超えるインデックスを作成した場合、API バージョン 2019-05-06 を使用してインデックスを更新または再作成しようとしても失敗します。 これが当てはまる場合は、新しい制限内に収まるようにスキーマを再設計してから、インデックスをリビルドする必要があります。

+ API バージョン 2019-05-06 以降では、ドキュメントあたりの複合コレクションの要素数について、新しい制限があります。 プレビュー版の API を使用して、これらの制限を超えるドキュメントのインデックスを作成した場合、API バージョン 2019-05-06 を使用してそのデータのインデックスを再作成しようとしても失敗します。 これが当てはまる場合は、ドキュメントあたりの複合コレクション要素の数を減らしてから、データのインデックスを再作成する必要があります。

詳細については、[Azure Cognitive Search サービスの制限](search-limits-quotas-capacity.md)に関する記事を参照してください。

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>以前の複合型の構造をアップグレードする方法

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

## <a name="next-steps"></a>次のステップ

Search REST API リファレンス ドキュメントを確認します。 問題が発生する場合は、[Stack Overflow](https://stackoverflow.com/) で助けを求めるか、[サポートに問い合わせ](https://azure.microsoft.com/support/community/?product=search)てください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス](/rest/api/searchservice/)
