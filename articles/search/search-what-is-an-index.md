---
title: 検索インデックスの作成
titleSuffix: Azure Cognitive Search
description: スキーマ定義や物理データ構造など、Azure Cognitive Search のインデックス作成の概念とツールについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3aa4a1917711f8997c282ba577c33e7a7f94472b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932884"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Azure Cognitive Search で基本検索インデックスを作成する

Azure Cognitive Search では、フルテキストクエリおよびフィルター適用済みクエリに使用される検索可能なコンテンツが*検索インデックス*に格納されます。 インデックスは、スキーマによって定義され、サービスに保存されます。2 番目の手順としてデータのインポートが続きます。 

インデックスには*ドキュメント*が含まれています。 概念的に、ドキュメントはインデックス内で検索可能なデータの 1 つの単位です。 小売業者であれば製品ごとにドキュメントがあり、報道機関であれば記事ごとにドキュメントがあります。 これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*検索インデックス*は*テーブル*と同じで、*ドキュメント*はテーブルにおける*行*とほぼ同じです。

インデックスの物理的な構造は、スキーマによって決定されます。フィールドは "検索可能" としてマークされ、そのフィールドに対して逆インデックスが作成されます。 

次のツールと API を使用してインデックスを作成できます。

* Azure portal で **[インデックスの追加]** または **[データのインポート]** ウィザードを使用します。
* [Create Index (REST API)](/rest/api/searchservice/create-index) の使用
* [.NET SDK](./search-get-started-dotnet.md) の使用

ポータル ツールを使用すると、簡単に学習できます。 ポータルでは、数値フィールドに対してフルテキスト検索機能を許可しないなど、特定のデータ型に対して要件とスキーマ ルールが適用されます。 実行可能なインデックスができたら、[Get Index (REST API)](/rest/api/searchservice/get-index) を使用してサービスから JSON 定義を取得し、それをソリューションに追加することにより、コードへ移行することができます。

## <a name="recommended-workflow"></a>推奨されるワークフロー

最終的なインデックス設計への到達は反復的なプロセスです。 ポータルから始めて最初のインデックスを作成してから、コードに切り替えてインデックスをソース管理下に配置することが一般的です。

1. [ **[データのインポート]** ](search-import-data-portal.md) を使用できるかどうかを判断します。 ソースデータが [Azure でサポートされているデータソース型](search-indexer-overview.md#supported-data-sources)である場合、オールインワン インデクサーベースのインデックス作成が実行されます。

1. **[データのインポート]** を使用できない場合は、 **[インデックスの追加]** から始めてスキーマを定義します。

   ![[インデックスの追加] コマンド](media/search-what-is-an-index/add-index.png "[インデックスの追加] コマンド")

1. インデックス内の各検索ドキュメントを一意に識別するために使用される名前とキーを指定します。 キーは必須で、Edm.String 型でなければなりません インポート時に、ソースデータ内の一意のフィールドをこのフィールドにマッピングすることを計画する必要があります。 

   ポータルでは、キーに対して `id` フィールドが提供されます。 既定の `id` をオーバーライドするには、新しいフィールドを作成し (たとえば、`HotelId` という名前の新しいフィールド定義)、 **[キー]** で選択します。

   ![必須プロパティを入力する](media/search-what-is-an-index//field-attributes.png "必須プロパティを入力する")

1. フィールドをさらに追加します。 ポータルでは、さまざまなデータ型に対してどの[フィールド属性](#index-attributes)が使用できるかが表示されます。 インデックスの作成が初めての場合、これが役立ちます。

   受信データ自体が階層化されている場合、入れ子構造を表すために、スキーマには[複合型](search-howto-complex-data-types.md)データ型を割り当てます。 あらかじめ登録されているサンプル データ セットである Hotels (ホテル) は、各ホテルとの一対一のリレーションシップを持つ Address (複数のサブフィールドを含む) と、各ホテルに複数の部屋が関連付けられている複合型コレクションの Rooms を使用した複合型を示しています。 

1. インデックスを作成する前に、[アナライザー](#analyzers)を文字列フィールドに割り当てます。 特定のフィールドでオートコンプリートを有効にする場合は、[サジェスター](#suggesters)に対して同じ操作を行います。

1. 検索サービスで物理構造を構築するには、 **[作成]** をクリックします。

1. インデックスが作成されたら、その他のコマンドを使用して定義を確認するか、さらに要素を追加します。

   ![データ型別に属性を示すインデックス ページを追加する](media/search-what-is-an-index//field-definitions.png "データ型別に属性を示すインデックス ページを追加する")

1. [Get Index REST API](/rest/api/searchservice/get-index) や、[Postman](search-get-started-postman.md) などの Web テスト ツールを使用してインデックス スキーマをダウンロードします。 これで、コードに対して適応できるインデックスの JSON 表現ができました。

1. [インデックスにデータを読み込みます](search-what-is-data-import.md)。 Azure Cognitive Search によって JSON ドキュメントが受け入れられます。 プログラムによってデータを読み込むために、Postman を使用し、要求ペイロードに JSON ドキュメントを含めることができます。 データを JSON で表現することが簡単でない場合、この手順に最も多くの労力がかかります。 

    インデックスがデータと共に読み込まれた後、既存のフィールドへの編集のほとんどにおいて、インデックスを削除し、再構築する必要があります。

1. インデックスをクエリし、結果を確認し、期待した結果が得られるようになるまでインデックス スキーマをさらに反復処理します。 インデックスのクエリには [**Search エクスプローラー**](search-explorer.md)または Postman を使用できます。

開発時に、頻繁な再構築を計画します。 物理構造はサービス内で作成されるため、既存のフィールド定義への変更のほとんどにおいて[インデックスの削除と再作成](search-howto-reindex.md)が必要です。 リビルドを高速化するために、データのサブセットを使って作業することを検討してもよいでしょう。 

> [!Tip]
> インデックスの設計とデータのインポートを同時に行う場合は、ポータル アプローチではなくコードを使用することをお勧めします。 開発プロジェクトがまだ初期段階の場合、代わりに [Postman や REST API](search-get-started-postman.md) などのツールを使用すると、概念実証テストに役立ちます。 要求本文のインデックス定義に増分的変更を加えてから要求をサービスに送信し、更新されたスキーマを使用してインデックスを再作成できます。

## <a name="index-schema"></a>インデックス スキーマ

インデックスでは、フィールド コレクションに名前と 1 つの指定されたキーフィールド (Edm. string) が含まれる必要があります。 "[*フィールド コレクション*](#fields-collection)" は通常、インデックスの最大の部分であり、各フィールドには、名前、型、および使用方法を決定する許容される動作を示す属性が設定されます。 

その他の要素には、アナライザーでサポートされている言語ルールやその他の特性、および [CORS (cross-origin remote scripting)](#corsoptions) 設定に従って文字列をトークンへと処理するために使用される、[サジェスター](#suggesters)、[スコアリング プロファイル](#scoringprofiles)、[アナライザー](#analyzers) があります。

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

フィールドには、名前、格納されたデータを分類する型、およびフィールドの使用方法を指定する属性があります。

### <a name="data-types"></a>データ型

| Type | 説明 |
|------|-------------|
| Edm.String |フルテキスト検索 (単語区切り、ステミングなど) のために必要に応じてトークン化できるテキスト。 |
| Collection(Edm.String) |フルテキスト検索のために必要に応じてトークン化することのできる一連の文字列。 コレクション内の項目の数に理論上の上限はありませんが、ペイロードのサイズに対する 16 MB の上限がコレクションに適用されます。 |
| Edm.Boolean |true または false の値が含まれます。 |
| Edm.Int32 |32 ビット整数値です。 |
| Edm.Int64 |64 ビット整数値です。 |
| Edm.Double |倍精度数値データです。 |
| Edm.DateTimeOffset |OData V4 形式で表された日時の値です (例: `yyyy-MM-ddTHH:mm:ss.fffZ` または `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`)。 |
| Edm.GeographyPoint |地球上の地理的な場所を表すポイントです。 |

詳細については、[サポートされているデータ型](/rest/api/searchservice/Supported-data-types)に関するページを参照してください。

<a name="index-attributes"></a>

### <a name="attributes"></a>属性

フィールド属性は、フィールドがどのように使用されるか (フルテキスト検索、ファセット ナビゲーション、並べ替えなどの操作で使用されるかどうか) を決定します。 

文字列フィールドは多くの場合、"検索可能" および "取得可能" としてマークされます。 検索結果を絞り込むために使用されるフィールドには、"並べ替え可能"、"フィルター可能"、および "ファセット可能" が含まれます。

|属性|説明|  
|---------------|-----------------|  
|"検索可能" |フルテキスト検索可能であり、インデックス作成中の単語分割などの字句解析に従います。 検索可能フィールドを "sunny day" などの値に設定した場合、その値は内部的に個別のトークン "sunny" と "day" に分割されます。 詳細については、「[フルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。|  
|"フィルター可能" |$filter クエリで参照されます。 型 `Edm.String` または `Collection(Edm.String)` のフィルター可能フィールドは単語分割されないため、比較は完全に一致するかどうかだけになります。 たとえば、このようなフィールドを "sunny day" に設定した場合、`$filter=f eq 'sunny'` では一致が見つかりませんが、`$filter=f eq 'sunny day'` では見つかります。 |  
|"並べ替え可能" |既定では、システムは結果をスコアで並べ替えますが、ドキュメント内のフィールドに基づいて並べ替えを構成できます。 型 `Collection(Edm.String)` のフィールドを "並べ替え可能" にすることはできません。 |  
|"ファセット可能" |通常、カテゴリ (たとえば、特定の市にあるホテル) ごとのヒット カウントを含む検索結果のプレゼンテーションで使用されます。 このオプションは、 `Edm.GeographyPoint`型のフィールドでは使用できません。 "フィルター可能"、"並べ替え可能"、または "ファセット可能" である型 `Edm.String` のフィールドの長さは、最大 32 キロバイトです。 詳細については、「[Create Index (REST API) (インデックスの作成 (REST API))](/rest/api/searchservice/create-index)」を参照してください。|  
|"キー" |インデックス内のドキュメントの一意識別子。 キー フィールドとして正確に 1 つのフィールドを選択する必要があり、それは型 `Edm.String` である必要があります。|  
|"取得可能" |検索結果でこのフィールドを返すことができるかどうかを決定します。 これは、あるフィールド (*利幅* など) をフィルター、並べ替え、またはスコア付けのメカニズムとして使用するが、このフィールドをエンド ユーザーには表示したくない場合に役立ちます。 `true` for `key` である必要があります。|  

いつでも新しいフィールドを追加できますが、既存のフィールド定義はインデックスの有効期間の間ロックされます。 このため、開発者は通常、単純なインデックスを作成したり、アイデアをテストしたり、ポータル ページを使用して設定を検索したりするためのポータルを使用します。 インデックスを容易に再構築できるようにコード ベースのアプローチに従う場合は、インデックス設計を頻繁に反復する方がより効率的です。

> [!NOTE]
> インデックスの作成に使用する API には、さまざまな既定の動作があります。 [REST API](/rest/api/searchservice/Create-Index) の場合、ほとんどの属性は既定で有効であり (たとえば、文字列フィールドの "searchable" および "retrievable" は true です)、無効にする場合は、単にそれらを設定するだけです。 .NET SDK の場合は、逆のことが言えます。 明示的に設定していないプロパティの場合、既定では、特に有効にしない限り、対応する検索動作は無効にされています。

## `analyzers`

アナライザー要素では、フィールドに対して使用される言語アナライザーの名前が設定されます。 利用可能なアナライザーの範囲の詳細については、[Azure Cognitive Search インデックスへのアナライザーの追加](search-analyzers.md)に関する記事を参照してください。 アナライザーは検索可能フィールドでのみ使用できます。 フィールドに割り当てられたアナライザーは、インデックスを再構築しない限り変更できません。

## `suggesters`

suggester は、検索においてオートコンプリートまたは先行入力クエリをサポートするために使用されるインデックス内のフィールドが定義されている、スキーマのセクションです。 通常、ユーザーが検索クエリを入力している間に部分的な検索文字列が [Suggestions (REST API)](/rest/api/searchservice/suggestions) に送信されて、API から検索候補のドキュメントまたは語句のセットが返されます。 

サジェスターに追加されるフィールドは、先行入力検索語句の構築に使用されます。 すべての検索語句はインデックス作成時に作成され、個別に格納されます。 サジェスター構造の作成の詳細については、[サジェスターの追加](index-add-suggesters.md)に関する記事を参照してください。

## `corsOptions`

ブラウザーではすべてのクロスオリジン要求が禁止されるので、既定ではクライアント側 JavaScript で API を呼び出すことはできません。 インデックスに対するクロスオリジン クエリを許可するには、**corsOptions** 属性を設定することによって、CORS (クロスオリジン リソース共有) を有効にします。 セキュリティ上の理由から、CORS がサポートされているのはクエリ API だけです。 

CORS に対しては以下のオプションを設定できます。

+ **allowedOrigins** (必須):インデックスへのアクセスが許可されるオリジンのリストです。 つまり、これらのオリジンから提供されるすべての JavaScript コードは、インデックスのクエリを許可されます (正しい api-key を提供する場合)。 各オリジンの標準的な形式は `protocol://<fully-qualified-domain-name>:<port>` ですが、多くの場合 `<port>` は省略されます。 詳しくは、「[Cross-origin resource sharing (クロスオリジン リソース共有)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)」(Wikipedia) をご覧ください。

  すべてのオリジンにアクセスを許可する場合は、**allowedOrigins** 配列の唯一の要素として `*` を指定します。 "*運用環境の検索サービスに対してはこれは推奨されません*" が、開発およびデバッグでは役に立つことがよくあります。

+ **maxAgeInSeconds** (省略可能):ブラウザーでは、この値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) が決定されます。 負ではない整数を指定する必要があります。 この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。 これを設定しないと、既定の期間として 5 分が使用されます。

## `scoringProfiles`

[スコアリング プロファイル](index-add-scoring-profiles.md)は、検索結果での項目の表示順序を変更できるカスタム スコアリング動作が定義されている、スキーマのセクションです。 スコアリング プロファイルは、フィールドの重みと関数で構成されます。 使用するには、クエリ文字列にプロファイル名を指定します。

既定のスコアリング プロファイルはバックグラウンドで実行され、検索セットの各項目の検索スコアがコンピューティングされます。 内部的な名前のないスコアリング プロファイルを使用できます。 または、クエリ文字列にカスタム プロファイルが指定されていない場合に常に呼び出される既定値として、カスタム プロファイルを使用するように **defaultScoringProfile** を設定します。

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>属性とインデックスのサイズ (ストレージへの影響)

インデックスのサイズは、アップロードするドキュメントのサイズと、suggester を含めるかどうかや、個々のフィールドに属性を設定する方法など、インデックス構成によって決定されます。 

次のスクリーンショットは、属性のさまざまな組み合わせの結果であるインデックス格納パターンを示しています。 インデックスは**不動産サンプルインデックス**に基づいています。これは、データのインポート ウィザードを使用して簡単に作成できます。 インデックスのスキーマは表示されませんが、インデックス名に基づいて属性を推測できます。 たとえば、*realestate-searchable* インデックスでは "searchable" 属性が選択されていて他には何もなく、*realestate-retrievable* インデックスでは "retrievable" 属性が選択されていて他には何もなく、以下同様です。

![属性の選択に基づいたインデックス サイズ](./media/search-what-is-an-index/realestate-index-size.png "属性の選択に基づいたインデックス サイズ")

これらのインデックスのバリエーションは人為的なものですが、属性がストレージに与える影響の広範な比較のために参照できます。 設定 "retrievable" はインデックスのサイズを増加させますか? いいえ。 **suggester** にフィールドを追加するとインデックスのサイズが増加しますか? はい。

フィルターと並べ替えをサポートするインデックスは、フルテキスト検索しかサポートしないインデックスに比べてその分だけ大きくなります。 これは、フィルター操作と並べ替え操作では完全一致が調べられ、逐語的テキスト文字列の存在が必要になるためです。 これに対し、フルテキスト クエリをサポートする検索可能フィールドでは転置インデックスを使用します。転置インデックスはトークン化された語句によって事前設定され、使用するディスク容量はドキュメント全体よりも少なくなります。 

> [!Note]
> ストレージ アーキテクチャは Azure Cognitive Search の実装の詳細と考えられており、予告なく変更されることがあります。 現在の動作が将来も変わらないという保証はありません。

## <a name="next-steps"></a>次のステップ

インデックスの構成を理解したなら、続けてポータルで最初のインデックスを作成できます。 **[データのインポート]** ウィザードから始め、ホステッド データ ソースの *realestate-us-sample* または *hotels-sample* を選択することをお勧めします。

> [!div class="nextstepaction"]
> [データのインポート ウィザード (ポータル)](search-get-started-portal.md)

両方のデータ セットについて、ウィザードでは、インデックス スキーマを推測し、データをインポートし、検索エクスプローラーを使用してクエリを実行できる検索可能インデックスを出力できます。 これらのデータ ソースは、 **[データのインポート]** ウィザードの **[データへの接続]** ページにあります。

   ![サンプル インデックスを作成する](media/search-what-is-an-index//import-wizard-sample-data.png "サンプル インデックスを作成する")