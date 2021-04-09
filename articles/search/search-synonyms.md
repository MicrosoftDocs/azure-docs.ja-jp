---
title: 検索インデックス上でのクエリ拡張のシノニム
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックス上での検索クエリの範囲を拡張するシノニム マップを作成します。 一覧で指定した同等の語句を含むように範囲が拡大されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251626"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure Cognitive Search でのシノニム

シノニム マップを使用すると、ユーザーが実際に用語を提供する必要がなく、クエリのスコープを拡張する同等の用語を関連付けられます。 たとえば、"dog"、"canine"、"puppy" がシノニムであると仮定すると、"canine" に対するクエリは "dog" を含むドキュメントで一致します。

## <a name="create-synonyms"></a>シノニムを作成する

シノニム マップは、一度作成すると多数のインデックスで使用できるアセットです。 [サービス レベル](search-limits-quotas-capacity.md#synonym-limits)によって、作成できるシノニム マップの数が決定されます。これは、Free および Basic レベルでの 3 つのシノニム マップから、Standard レベルでの 20 までの範囲です。 

英語版やフランス語版など、言語によって別々のシノニム マップを複数作成したり、コンテンツに技術用語やあいまいな用語が含まれている場合は、辞書を作成したりすることができます。 検索サービスで複数のシノニム マップを作成できますが、フィールドではそのうちの 1 つしか使用できません。

シノニム マップは、シノニム マップ エントリとして機能する名前、形式、および規則で構成されます。 サポートされている形式は `solr` のみで、`solr` 形式によってルールの構成が決まります。

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

シノニム マップを作成するには、「[シノニム マップの作成 (REST API)](/rest/api/searchservice/create-synonym-map)」または Azure SDK を使用します。 C# 開発者には、「[C# で Azure Cognitive Search にシノニムを追加する](search-synonyms-tutorial-sdk.md)」から始めることをお勧めします。

## <a name="define-rules"></a>ルールを定義する

マッピング規則は、このドキュメントで説明している Apache Solr のオープンソース シノニム フィルター仕様[SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter) に従います。`solr` 形式では、次の 2 種類の規則がサポートされています。

+ 同義性 (用語はクエリで同等の代用語になります)

+ 明示的なマッピング (クエリ実行の前に、用語は 1 つの明示的な用語にマップされます)

各規則は、改行文字 (`\n`) で区切る必要があります。 無料サービスでシノニム マップあたり最大 5,000 個の規則、その他のレベルでマップあたり最大 20,000 個の規則を定義できます。 各規則には、最大 20 個の拡張 (つまり規則内の項目) を設定できます。 詳細については、「[シノニムの制限](search-limits-quotas-capacity.md#synonym-limits)」をご覧ください。

クエリ パーサーでは大文字の用語や大文字と小文字が混在した用語は小文字に変換されますが、コンマやダッシュなどの特殊文字を文字列で保持する場合は、シノニム マップの作成時に適切なエスケープ文字を追加します。

### <a name="equivalency-rules"></a>同義性規則

同義語の規則は、同じ規則内でコンマで区切られます。 最初の例では、`USA` に対するクエリが `USA`、`"United States"`、または `"United States of America"` に拡張されます。 句で一致させる場合は、クエリ自体を引用符で囲まれた句のクエリにする必要があることに注意してください。

同義性の場合、`dog` のクエリは、`puppy` や `canine` も含めるようにクエリを拡張します。

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>明示的なマッピング

明示的なマッピングの規則は、矢印 `=>` によって示されます。 指定した場合、`=>` の左側に一致する検索クエリの用語のシーケンスが、クエリ時に右側の代替語で置き換えられます。

明示的な場合、`Washington`、`Wash.`、または `WA` に対するクエリは `WA` に書き換えられ、クエリ エンジンは `WA` の用語でのみ一致を検索します。 明示的なマッピングは指定した方向でのみ適用され、この例では、クエリ `WA` が `Washington` に書き換えられることはありません。

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>特殊文字のエスケープ

シノニムは、クエリ処理中に分析されます。 コンマや他の特殊文字を含むシノニムを定義する必要がある場合は、次の例のように円記号でエスケープできます。

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

JSON や C# などの他の言語では、円記号自体が特殊文字であるため、通常、二重にエスケープする必要があります。 たとえば、上記のシノニム マップに対して REST API に送信される JSON は、次のようになります。

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>シノニム マップをアップロードして管理する

前述のように、クエリおよびインデックス作成ワークロードを中断せずにシノニム マップを作成または更新することができます。 シノニム マップはスタンドアロン オブジェクト (インデックスやデータ ソースなど) であり、これを使用しているフィールドがない限り、更新によってインデックス作成やクエリが失敗することはありません。 ただし、シノニム マップをフィールド定義に追加した後でシノニム マップを削除すると、該当するフィールドを含むクエリが 404 エラーで失敗します。

シノニム マップの作成、更新、および削除は、常にドキュメント全体の操作になります。つまり、シノニム マップの一部を段階的に更新または削除することはできません。 1 つの規則の更新でも、再読み込みが必要になります。

## <a name="assign-synonyms-to-fields"></a>フィールドにシノニムを割り当てる

シノニム マップをアップロードした後、`"searchable":true` を含むフィールドで `Edm.String` または `Collection(Edm.String)` 型のフィールドに対してシノニムを有効にすることができます。 前述のように、フィールド定義で使用できるシノニム マップは 1 つだけです。

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>同義またはマップされたフィールドに対するクエリ

シノニムを追加しても、新しい要件はクエリ構造に適用されません。 シノニムの追加前と同じようにして、用語と語句のクエリを発行できます。 唯一の違いは、シノニム マップにクエリ用語が存在する場合、クエリ エンジンは規則に応じて、用語または句を拡張したり、書き換えたりするという点です。

## <a name="how-synonyms-are-used-during-query-execution"></a>クエリの実行中にシノニムが使用される方法

シノニムは、インデックスの内容を同等の用語で補完するクエリ拡張の手法ですが、シノニムの割り当てがあるフィールドでのみ機能します。 フィールド スコープ クエリでシノニム対応フィールドが *除外* されている場合、シノニム マップからの一致は表示されません。

シノニム対応フィールドの場合、シノニムは、関連付けられているフィールドと同じテキスト分析に従います。 たとえば、フィールドが標準の Lucene アナライザーを使用して分析される場合は、シノニム用語もクエリ時に標準の Lucene アナライザーに従います。 シノニム用語に句読点 (ピリオドやダッシュなど) を保持したい場合は、フィールドでコンテンツ保持アナライザーを適用します。

内部的には、シノニム機能により、シノニムを含む元のクエリが OR 演算子で書き換えられます。 このため、検索結果の強調表示とスコアリング プロファイルは元の用語とシノニムを同等として処理します。

シノニムは自由形式のテキスト クエリにのみ適用され、フィルター、ファセット、オートコンプリート、または提案ではサポートされません。 オートコンプリートと提案は、元の用語にのみ基づき、シノニムの一致は応答に示されません。

シノニムの拡張は、ワイルドカード検索語句には適用されません。プレフィックス、あいまい検索、および正規表現語句は拡張されません。

シノニムの拡張とワイルドカード検索、正規表現検索、またはあいまい検索を 1 つのクエリで適用する必要がある場合は、OR 構文を使用してクエリを組み合わせることができます。 たとえば、クエリ構文が単純になるようにシノニムとワイルドカードを組み合わせる場合は、用語は `<query> | <query>*` のようになります。

開発 (非運用) 環境に既存のインデックスがある場合は、小さな辞書で実験して、シノニムの追加によって、スコアリング プロファイル、検索結果の強調表示、検索候補への影響など、検索操作がどのように変わるかを確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シノニム マップの作成 (REST API)](/rest/api/searchservice/create-synonym-map)