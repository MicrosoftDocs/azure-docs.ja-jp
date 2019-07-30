---
title: インデックスの定義と概念を作成する - Azure Search
description: 構成要素や物理的構造など、Azure Search でのインデックスの用語と概念の概要を説明します。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 0a6a5b0e3957141b9ea17a378a7cbeff33a0124e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485200"
---
# <a name="create-a-basic-index-in-azure-search"></a>Azure Search で基本的なインデックスを作成する

Azure Search における*インデックス*とは、Azure Search サービスでのフィルターされたおよびフル テキストの検索に使用される*ドキュメント*などの構成要素の永続的なストアです。 概念的に、ドキュメントはインデックス内で検索可能なデータの 1 つの単位です。 たとえば、eコマースの小売業者であれば販売品目ごとにドキュメントがあり、報道機関であれば記事ごとにドキュメントがあります。 これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*インデックス*は概念的には*テーブル*に似ており、*ドキュメント*はテーブルにおける*行*とほぼ同じです。

インデックスを追加またはアップロードすると、ユーザーが提供したスキーマを基に Azure Search によって物理構造が作成されます。 たとえば、インデックス内のフィールドが検索可能としてマークされていると、そのフィールドの転置インデックスが作成されます。 後で、ドキュメントを追加またはアップロードするとき、または検索クエリを Azure Search に送信するときは、検索サービス内の特定のインデックスに対して要求を送信していることになります。 フィールドにドキュメントの値を読み込むことを、"*インデックス付け*" またはデータ インジェストと呼びます。

インデックスの作成は、ポータル、[REST API](search-create-index-rest-api.md)、または [.NET SDK](search-create-index-dotnet.md) で行うことができます。

## <a name="recommended-workflow"></a>推奨されるワークフロー

適切なインデックス設計は、通常は複数回のイテレーションを経て得られます。 ツールと API の組み合わせを使用すれば、設計をすばやく完了することができます。

1. [インデクサー](search-indexer-overview.md#supported-data-sources)を使用できるかどうかを判断します。 外部データがサポートされているデータソースのものである場合、[**データのインポート**](search-import-data-portal.md) ウィザードを使用してインデックスのプロトタイプ作成と読み込みを行うことができます。

2. **データのインポート**を使用できない場合でも、[ポータルで初期インデックスを作成](search-create-index-portal.md)し、フィールドとデータ型を追加し、 **[インデックスの追加]** ページのコントロールを使用して属性を割り当てることができます。 ポータルでは、さまざまなデータ型に対してどの属性が使用できるかが表示されます。 インデックスの作成が初めての場合、これが役立ちます。

   ![データ型ごとに属性が表示されるインデックス追加ページ](media/search-create-index-portal/field-attributes.png "データ型ごとに属性が表示されるインデックス追加ページ")
  
   **[作成]** をクリックすると、このインデックスをサポートしているすべての物理構造が検索サービスに作成されます。

3. [Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) や、[Postman](search-get-started-postman.md) などの Web テスト ツールを使用してインデックス スキーマをダウンロードします。 これで、ポータルで作成したインデックスの JSON 表現ができました。 

   ここからはコード ベースのアプローチに切り替えます。 既に作成されているインデックスは編集できないため、ポータルはイテレーションにあまり適していません。 ただし、残りのタスクには Postman や REST を使用できます。

4. [インデックスにデータを読み込みます](search-what-is-data-import.md)。 Azure Search は JSON ドキュメントを受け入れます。 プログラムによってデータを読み込むために、Postman を使用し、要求ペイロードに JSON ドキュメントを含めることができます。 データを JSON で表現することが簡単でない場合、この手順に最も多くの労力がかかります。

5. インデックスをクエリし、結果を確認し、期待した結果が得られるようになるまでインデックス スキーマをさらに反復処理します。 インデックスのクエリには [**Search エクスプローラー**](search-explorer.md)または Postman を使用できます。

6. 引き続き、コードを使用して設計を反復処理します。  

物理構造はサービス内で作成されるため、既存のフィールド定義に重要な変更を加えるたびに[インデックスの削除と再作成](search-howto-reindex.md)が必要です。 つまり、開発中は頻繁なリビルドを計画する必要があります。 リビルドを高速化するために、データのサブセットを使って作業することを検討してもよいでしょう。 

反復的な設計には、ポータルを使う方法よりもコードのほうをお勧めします。 インデックス定義にポータルを使用する場合、リビルドのたびにインデックス定義を入力する必要があります。 開発プロジェクトがまだ初期段階の場合、代わりに [Postman や REST API](search-get-started-postman.md) などのツールを使用すると、概念実証テストに役立ちます。 要求本文のインデックス定義に増分的変更を加えてから要求をサービスに送信し、更新されたスキーマを使用してインデックスを再作成できます。

## <a name="components-of-an-index"></a>インデックスのコンポーネント

スキーマ的には、Azure Search のインデックスは次の要素で構成されます。 

"[*フィールド コレクション*](#fields-collection)" は通常、インデックスの最大の部分であり、各フィールドには、名前、型、および使用方法を決定する許容される動作を示す属性が設定されます。 他の要素としては、[suggester](#suggesters)、[スコアリング プロファイル](#scoring-profiles)、カスタマイズをサポートする構成要素を含む[アナライザー](#analyzers)、[CORS](#cors)、および[暗号化キー](#encryption-key) オプションがあります。

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>フィールド コレクションとフィールド属性

スキーマを定義する際に、インデックスの各フィールドの名前、型、属性を指定する必要があります。 フィールドの型によって、そのフィールドに格納されているデータが分類されます。 属性は個々のフィールドに設定されてフィールドの使用方法を指定します。 次の表に、指定できる型と属性をまとめます。

### <a name="data-types"></a>データの種類
| Type | 説明 |
| --- | --- |
| *Edm.String* |フルテキスト検索 (単語区切り、ステミングなど) のために必要に応じてトークン化できるテキスト。 |
| *Collection(Edm.String)* |フルテキスト検索のために必要に応じてトークン化することのできる一連の文字列。 コレクション内の項目の数に理論上の上限はありませんが、ペイロードのサイズに対する 16 MB の上限がコレクションに適用されます。 |
| *Edm.Boolean* |true または false の値が含まれます。 |
| *Edm.Int32* |32 ビット整数値です。 |
| *Edm.Int64* |64 ビット整数値です。 |
| *Edm.Double* |倍精度数値データです。 |
| *Edm.DateTimeOffset* |OData V4 形式で表された日時の値です (例: `yyyy-MM-ddTHH:mm:ss.fffZ` または `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。 |
| *Edm.GeographyPoint* |地球上の地理的な場所を表すポイントです。 |

Azure Search のサポートされるデータ型の詳細については、[このページ](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)を参照してください。

### <a name="index-attributes"></a>インデックスの属性

インデックス内の 1 つのフィールドのみが、各ドキュメントを一意に識別する**キー** フィールドとして指定されている必要があります。

他の属性によって、フィールドがアプリケーション内でどのように使用されるかが決まります。 たとえば、フルテキスト検索に含める必要があるすべてのフィールドには、**searchable** 属性が割り当てられています。 

インデックスの作成に使用する API には、さまざまな既定の動作があります。 [REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Index) の場合、ほとんどの属性は既定で有効であり (たとえば、文字列フィールドの **searchable** および **retrievable** は true です)、無効にする場合は、単にそれらを設定するだけです。 .NET SDK の場合は、逆のことが言えます。 明示的に設定していないプロパティの場合、既定では、特に有効にしない限り、対応する検索動作は無効にされています。

| Attribute | 説明 |
| --- | --- |
| `key` |ドキュメント検索に使用される各ドキュメントの一意の ID を提供する文字列です。 各インデックスに、1 つのキーが必要です。 1 つのフィールドだけをキーにすることができ、その型を Edm.String に設定する必要があります。 |
| `retrievable` |検索結果でフィールドを返すことができるかどうかを設定します。 |
| `filterable` |フィルター クエリでフィールドを使用できるようにします。 |
| `Sortable` |このフィールドを使ってクエリで検索結果を並べ替えられるようにします。 |
| `facetable` |ユーザー自律フィルター処理の [ファセット ナビゲーション](search-faceted-navigation.md) 構造でフィールドを使用できるようにします。 通常は、複数のドキュメント (たとえば、1 つのブランドやサービス カテゴリに属する複数のドキュメント) をまとめてグループ化するために使用できる、反復する値があるフィールドが、ファセットとして最適です。 |
| `searchable` |フィールドをフルテキスト検索可能としてマークします。 |


## <a name="storage-implications"></a>ストレージへの影響

選択した属性はストレージに影響を与えます。 次のスクリーンショットは、属性のさまざまな組み合わせの結果であるインデックス格納パターンを示しています。

インデックスは[組み込みの real estateサンプル](search-get-started-portal.md) データ ソースに基づいており、このデータ ソースはポータルでインデックスを作成したりクエリを実行したりできます。 インデックスのスキーマは表示されませんが、インデックス名に基づいて属性を推測できます。 たとえば、*realestate-searchable* インデックスでは **searchable** 属性が選択されていて他には何もなく、*realestate-retrievable* インデックスでは **retrievable** 属性が選択されていて他には何もなく、以下同様です。

![属性選択に基づいたインデックスのサイズ](./media/search-what-is-an-index/realestate-index-size.png "属性選択に基づいたインデックスのサイズ")

これらのインデックスのバリエーションは人為的なものですが、属性がストレージに与える影響の広範な比較のために参照できます。 設定 **retrievable** はインデックスのサイズを増加させますか? いいえ。 **Suggester** にフィールドを追加するとインデックスのサイズが増加しますか? はい。

フィルターと並べ替えをサポートするインデックスは、フルテキスト検索しかサポートしないインデックスに比べて大きくなります。 フィルターと並べ替えは完全一致をクエリするので、ドキュメントがそのまま格納されることがその理由です。 これに対し、フルテキスト検索とあいまい検索をサポートする検索可能フィールドでは転置インデックスを使用します。転置インデックスはトークン化された語句によって事前設定され、使用するディスク容量はドキュメント全体よりも少なくなります。

> [!Note]
> ストレージ アーキテクチャは Azure Search の実装の詳細と考えられており、予告なく変更されることがあります。 現在の動作が将来も変わらないという保証はありません。

## <a name="suggesters"></a>サジェスター
suggester は、検索においてオートコンプリートまたは先行入力クエリをサポートするために使用されるインデックス内のフィールドが定義されている、スキーマのセクションです。 通常、ユーザーが検索クエリを入力している間に部分的な検索文字列が [Suggestions (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) に送信されて、API は検索候補の語句のセットを返します。 

サジェスターに追加されるフィールドは、先行入力検索語句の構築に使用されます。 すべての検索語句はインデックス作成時に作成され、個別に格納されます。 サジェスター構造の作成の詳細については、[サジェスターの追加](index-add-suggesters.md)に関する記事を参照してください。

## <a name="scoring-profiles"></a>スコアリング プロファイル

[スコアリング プロファイル](index-add-scoring-profiles.md)は、検索結果での項目の表示順序を変更できるカスタム スコアリング動作が定義されている、スキーマのセクションです。 スコアリング プロファイルは、フィールドの重みと関数で構成されます。 使用するには、クエリ文字列にプロファイル名を指定します。

既定のスコアリング プロファイルはバックグラウンドで実行され、検索セットの各項目の検索スコアがコンピューティングされます。 内部的な名前のないスコアリング プロファイルを使用できます。 または、クエリ文字列にカスタム プロファイルが指定されていない場合に常に呼び出される既定値として、カスタム プロファイルを使用するように **defaultScoringProfile** を設定します。

## <a name="analyzers"></a>アナライザー

アナライザー要素では、フィールドに対して使用される言語アナライザーの名前が設定されます。 利用可能なアナライザーの範囲の詳細については、[Azure Search インデックスへのアナライザーの追加](search-analyzers.md)に関する記事を参照してください。 アナライザーは検索可能フィールドでのみ使用できます。 フィールドに割り当てられたアナライザーは、インデックスを再構築しない限り変更できません。

## <a name="cors"></a>CORS

ブラウザーではすべてのクロスオリジン要求が禁止されるので、既定ではクライアント側 JavaScript で API を呼び出すことはできません。 インデックスに対するクロスオリジン クエリを許可するには、**corsOptions** 属性を設定することによって、CORS (クロスオリジン リソース共有) を有効にします。 セキュリティ上の理由から、CORS がサポートされているのはクエリ API だけです。 

CORS に対しては以下のオプションを設定できます。

+ **allowedOrigins** (必須):インデックスへのアクセスが許可されるオリジンのリストです。 つまり、これらのオリジンから提供されるすべての JavaScript コードは、インデックスのクエリを許可されます (正しい api-key を提供する場合)。 各オリジンの標準的な形式は `protocol://<fully-qualified-domain-name>:<port>` ですが、多くの場合 `<port>` は省略されます。 詳しくは、「[Cross-origin resource sharing (クロスオリジン リソース共有)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)」(Wikipedia) をご覧ください。

  すべてのオリジンにアクセスを許可する場合は、**allowedOrigins** 配列の唯一の要素として `*` を指定します。 "*運用環境の検索サービスに対してはこれは推奨されません*" が、開発およびデバッグでは役に立つことがよくあります。

+ **maxAgeInSeconds** (省略可能):ブラウザーでは、この値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) が決定されます。 負ではない整数を指定する必要があります。 この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。 これを設定しないと、既定の期間として 5 分が使用されます。

## <a name="encryption-key"></a>暗号化キー

既定では、Azure Search のすべてのインデックスは、Microsoft のマネージド キーを使用して暗号化されますが、Key Vault 内で**顧客管理のキー**を使用してインデックスを暗号化するように構成することもできます。 詳細については、[Azure Search での暗号化キーの管理に関するページ](search-security-manage-encryption-keys.md)を参照してください。

## <a name="next-steps"></a>次の手順

インデックスの構成を理解したなら、続けてポータルで最初のインデックスを作成できます。

> [!div class="nextstepaction"]
> [インデックスを追加する (ポータル)](search-create-index-portal.md)
