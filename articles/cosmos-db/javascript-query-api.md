---
title: Azure Cosmos DB ストアド プロシージャとトリガーで JavaScript 統合クエリ API を操作する
description: この記事では、Azure Cosmos DB でストアド プロシージャとトリガーを作成するための JavaScript 言語統合クエリ API の概念について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340976"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Azure Cosmos DB の JavaScript クエリ API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の SQL API を使用してクエリを発行することに加えて、[Cosmos DB のサーバー側の SDK](https://github.com/Azure/azure-cosmosdb-js-server/) を使用すると、JavaScript インターフェイスを使用して Cosmos DB ストアド プロシージャやトリガーで最適化されたクエリを実行できます。 この JavaScript インターフェイスの使用では、SQL 言語を意識する必要はありません。 JavaScript クエリ API では、述語関数を一連の関数呼び出しに渡すことでクエリをプログラムで構築できます。構文は ECMAScript5 のアレイ ビルトインや Lodash のような人気の JavaScript ライブラリでおなじみのものです。 クエリは JavaScript ランタイムによって解析され、Azure Cosmos DB のインデックスを使用して効率的に実行されます。

## <a name="supported-javascript-functions"></a>サポートされている JavaScript 関数

| **Function** | **説明** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|連鎖呼び出しを開始します。連鎖呼び出しは value() で終わる必要があります。|
|`filter(predicateFunction [, options] [, callback])`|入力ドキュメントを結果セットに含めたり除外したりするために、true/false を返す述語関数を使用して入力をフィルター処理します。 この関数の動作は SQL の WHERE 句に似ています。|
|`flatten([isShallow] [, options] [, callback])`|各入力項目の配列を結合し、一次元配列にします。 この関数の動作は LINQ の SelectMany に似ています。|
|`map(transformationFunction [, options] [, callback])`|各入力項目を JavaScript オブジェクトまたは値にマッピングする変換関数によって返される射影を適用します。 この関数の動作は SQL の SELECT 句に似ています。|
|`pluck([propertyName] [, options] [, callback])`|この関数は map のショートカット版で、各入力項目から 1 つのプロパティの値を抽出します。|
|`sortBy([predicate] [, options] [, callback])`|指定された述語を使用して、入力ドキュメント ストリーム内のドキュメントを昇順で並べ替え、ドキュメントの新しいセットを生成します。 この関数の動作は SQL の ORDER BY 句に似ています。|
|`sortByDescending([predicate] [, options] [, callback])`|指定された述語を使用して、入力ドキュメント ストリーム内のドキュメントを降順で並べ替え、ドキュメントの新しいセットを生成します。 この関数の動作は SQL の ORDER BY x DESC 句に似ています。|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|内部配列との自己結合を実行し、両側からの結果をタプルとして結果プロジェクションに追加します。 たとえば、person ドキュメントと person.pets を結合すると、[person, pet] タプルが生成されます。 これは、.NET LINK の SelectMany に似ています。|

述語またはセレクター関数の中に含まれるとき、次の JavaScript コンストラクトは自動的に最適化され、Azure Cosmos DB インデックスで直接実行されます。

- 単純演算子： `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- オブジェクト リテラルを含むリテラル: {}
- var、return

次の JavaScript コンストラクトは Azure Cosmos DB インデックスに対して最適化されません。

- 制御フロー (if、for、while など)
- 関数呼び出し

詳細については、[Cosmos DB のサーバー側 JavaScript のドキュメント](https://github.com/Azure/azure-cosmosdb-js-server/)を参照してください。

## <a name="sql-to-javascript-cheat-sheet"></a>SQL と JavaScript のチート シート

次の表はさまざまな SQL クエリとそれに対応する JavaScript クエリをまとめたものです。 SQL クエリと同様に、プロパティ (item.id など) では大文字と小文字が区別されます。

> [!NOTE]
> `__` (2 つのアンダースコア) は JavaScript クエリ API を使用する場合の `getContext().getCollection()` のエイリアスです。

|**SQL**|**JavaScript クエリ API**|**説明**|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|結果的にすべてのドキュメントがそのまま生成されます (継続トークンで改ページ調整されます)。|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id、<br>&nbsp;&nbsp;&nbsp;docs.message AS msg、<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|すべてのドキュメントから ID、メッセージ (エイリアスは msg)、アクションを射影します。|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|述語に id = "X998_Y998" を指定して、ドキュメントに対してクエリします。|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Tags プロパティを持つドキュメントに対してクエリします。Tags は値 123 を含む配列です。|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id、<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|述語に id = "X998_Y998" を指定してドキュメントに対してクエリし、ID とメッセージ (エイリアスは msg) を射影します。|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|配列プロパティ Tags のあるドキュメントをフィルター処理し、生成されたドキュメントを _ts タイムスタンプ システム プロパティで並べ替え、射影して Tags 配列を一次元配列にします。|

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を記述および使用する方法および概念について学習します。

- [Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB のストアド プロシージャ、トリガー、およびユーザー定義関数の操作](stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を使用する方法](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript サーバー側の API リファレンス](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
