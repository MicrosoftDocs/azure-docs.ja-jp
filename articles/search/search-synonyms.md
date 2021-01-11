---
title: 検索インデックス上でのクエリ拡張のシノニム
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックス上での検索クエリの範囲を拡張するシノニム マップを作成します。 一覧で指定した同等の語句を含むように範囲が拡大されます。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: dce3c484a16918931df80d5201fad090b2c0cb2e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922635"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure Cognitive Search でのシノニム

検索エンジンのシノニムは、ユーザーが実際に用語を提供する必要がなく、クエリのスコープを暗黙的に拡張する同等の用語を関連付けます。 たとえば、用語 "dog" と、"canine" と "puppy" のシノニムの関連付けを指定すると、"dog"、"canine"、または "puppy" を含むすべてのドキュメントがクエリのスコープ内に収まります。

Azure Cognitive Search では、シノニムの拡張は、クエリ時に行われます。 既存の処理を停止させることなく、シノニム マップをサービスに追加できます。 インデックスを再構築する必要なく、**synonymMaps** プロパティをフィールド定義に追加できます。

## <a name="create-synonyms"></a>シノニムを作成する

シノニムの作成はポータルでサポートされませんが、REST API または .NET SDK を使用できます。 REST を使い始めるときは、[Postman](search-get-started-postman.md)と、[シノニム マップの作成](/rest/api/searchservice/create-synonym-map) API を使用する公式化された要求を使用することをお勧めします。 C# 開発者は、[C# を使用した Azure Cognitive Search へのシノニムの追加](search-synonyms-tutorial-sdk.md)に関するページから始めることができます。

サービス側の暗号化の保存で[顧客管理のキー](search-security-manage-encryption-keys.md)を使用している場合は、その保護をシノニム マップのコンテンツに適用することもできます。

## <a name="use-synonyms"></a>同義語を使用する

Azure Cognitive Search でのシノニムのサポートは、定義してサービスにアップロードされるシノニム マップに基づいています。 これらのマップは独立したリソース (インデックスやデータ ソースなど) を構成し、検索サービスで、任意のインデックスの任意の検索可能フィールドで使用できます。

シノニム マップとインデックスは独立して保持されます。 シノニム マップを定義して、サービスにアップロードしたら、フィールド定義に **synonymMaps** という新しいプロパティを追加することによって、フィールドでシノニム機能を有効にできます。 シノニム マップの作成、更新、および削除は、常にドキュメント全体の操作になります。つまり、シノニム マップの一部を段階的に作成、更新、または削除することはできません。 1 つのエントリの更新でも、再読み込みが必要になります。

検索アプリケーションにシノニムを組み込むことは、2 段階のプロセスです。

1.  次の API によって、検索サービスにシノニム マップを追加します。  

2.  インデックス定義でシノニム マップを使用するように、検索可能フィールドを構成します。

検索アプリケーションでは、複数のシノニム マップを作成できます (アプリケーションで多言語の顧客ベースをサポートしている場合に言語別など)。 現在、フィールドではそれらのうちの 1 つしか使用できません。 フィールドの synonymMaps プロパティは、いつでも更新できます。

### <a name="synonymmaps-resource-apis"></a>SynonymMaps リソース API

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>POST または PUT を使用して、サービスでシノニム マップを追加または更新します。

シノニム マップは、POST または PUT を使用してサービスにアップロードします。 各規則は、改行文字 ('\n') で区切る必要があります。 無料サービスでシノニム マップあたり最大 5,000 規則、およびその他のすべての SKU でマップあたり 20,000 規則を定義できます。 各規則には、最大 20 個の拡張を設定できます。

シノニム マップは、次に説明する Apache Solr 形式である必要があります。 別の形式の既存のシノニム辞書があり、それを直接使用する場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) でお知らせください。

次の例のように、HTTP POST を使用して、新しいシノニム マップを作成できます。

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

または、PUT を使用し、URI にシノニム マップ名を指定できます。 シノニム マップが存在しない場合、作成されます。

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Apache Solr シノニム形式

Solr 形式は同等の明示的なシノニム マッピングをサポートします。 マッピング規則は、このドキュメントで説明している Apache Solr のオープンソース シノニム フィルター仕様[SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter) に準拠しています。 同等のシノニムのサンプル規則を以下に示します。

```
USA, United States, United States of America
```

上の規則では、検索クエリ "USA" が "USA" または "United States" または "United States of America" に拡張されます。

明示的なマッピングは、"=>" 矢印で示します。 指定した場合、"=>" の左側に一致する検索クエリの用語のシーケンスが、右側の代替で置き換えられます。 次の規則を指定した場合、検索クエリ "Washington"、"Wash."、 または "WA" はすべて "WA" に書き換えられます。 明示的なマッピングは指定した方向でのみ適用され、この例では、クエリ "WA" が "Washington" に書き換えられることはありません。

```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>サービスのシノニム マップを一覧表示します。

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>サービスのシノニム マップを取得します。

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>サービスのシノニム マップを削除します。

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>インデックス定義でシノニム マップを使用するように、検索可能フィールドを構成します。

新しいフィールド プロパティ **synonymMaps** を使用して、検索可能フィールドに使用するシノニム マップを指定できます。 シノニム マップは、サービス レベル リソースであり、そのサービスに属するインデックスの任意のフィールドによって参照できます。

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }
```

**synonymMaps** は型 'Edm.String' または 'Collection(Edm.String)' の検索可能フィールドに指定できます。

> [!NOTE]
> フィールドあたり 1 つのシノニム マップのみを指定できます。 複数のシノニム マップを使用する必要がある場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) でお知らせください。

## <a name="impact-of-synonyms-on-other-search-features"></a>他の検索機能へのシノニムの影響

シノニム機能は、OR 演算子によって元のクエリをシノニムに書き換えます。 このため、検索結果の強調表示とスコアリング プロファイルは元の用語とシノニムを同等として処理します。

シノニム機能は、検索クエリに適用され、フィルターやファセットには適用されません。 同様に、検索候補は元の用語にのみ基づき、シノニムの一致は応答に示されません。

シノニムの拡張は、ワイルドカード検索語句には適用されません。プレフィックス、あいまい検索、および正規表現語句は拡張されません。

シノニムの拡張とワイルドカード検索、正規表現検索、またはあいまい検索を 1 つのクエリで適用する必要がある場合は、OR 構文を使用してクエリを組み合わせることができます。 たとえば、クエリ構文が単純になるようにシノニムとワイルドカードを組み合わせる場合は、用語は `<query> | <query>*` のようになります。

開発 (非運用) 環境に既存のインデックスがある場合は、小さな辞書で実験して、シノニムの追加によって、スコアリング プロファイル、検索結果の強調表示、検索候補への影響など、検索操作がどのように変わるかを確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シノニム マップの作成](/rest/api/searchservice/create-synonym-map)