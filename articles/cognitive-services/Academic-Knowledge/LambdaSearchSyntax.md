---
title: Lambda 検索構文 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で利用できる Lambda 検索構文について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873201"
---
# <a name="lambda-search-syntax"></a>ラムダ検索の構文

各*ラムダ*検索クエリ文字列は、グラフ パターンを記述します。 クエリには、トラバーサルを開始するグラフ ノードを指定する開始ノードが少なくとも 1 つ必要です。 開始ノードを指定するには、*MAG.StartFrom()* メソッドを呼び出し、1 つまたは複数のノードの ID または検索制約を指定するクエリ オブジェクトを渡します。 *StartFrom()* メソッドには 3 つのオーバーロードがあります。 そのすべてに 2 つの引数があり、2 つ目は省略可能です。 最初の引数は、長整数、長整数型の列挙可能なコレクション、または JSON オブジェクトを表す文字列オブジェクト (セマンティクスは *json* 検索の場合と同じ) にすることができます。
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

ラムダ検索クエリの作成のプロセスは、あるノードから別のノードへのウォークです。 ウォークスルーするエッジの種類を指定するには、*FollowEdge()* を使用し、目的のエッジの種類を渡します。 *FollowEdge()* は、任意の数の文字列引数を受け取ります。
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> フォローするエッジの種類を気にしない場合は、単純に 2 つのノード間で *FollowEdge()* を省略します。クエリは、この 2 つのノード間で可能なすべてのエッジをウォークスルーします。

*VisitNode()* を使用して、ノードに対して実行するトラバーサル アクションを指定できます。つまり、このノードで停止し、結果として現在のパスに戻るか、グラフの探索を続けるかを指定します。  列挙型 *Action* では、2 種類のアクションを定義しています。*Action.Return* と *Action.Continue* です。 このような列挙型の値を *VisitNode()* に直接渡すか、ビットごとの And 演算子 '&' と組み合わせることができます。 2 つのアクションを組み合わせる場合、両方のアクションが実行されることを意味します。 注: アクションではビットごとの Or 演算子 '|' は使用しないでください。 使用すると、クエリが何もを返さずに終了する原因となります。 2 つの *FollowEdge()* 呼び出し間で *VisitNode()* をスキップすると、ノードに到達した後、クエリが無条件でグラフを探索します。

```
VisitNode(Action action, IEnumerable<string> select = null)
```

*VisitNode()* の場合、*Expression\<Func\<INode, Action\>\>* 型のラムダ式を渡すこともできます。これは *INode* を受け取り、トラバーサル アクションを返します。

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* は、"*読み取り専用*" データ アクセス インターフェイスと、ノード上のいくつかの組み込みヘルパー関数を提供します。 

### <a name="basic-data-access-interfaces"></a>基本的なデータ アクセス インターフェイス

##### <a name="long-cellid"></a>long CellID

ノードの 64 ビットの ID。 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

指定したプロパティの値を取得します。 *T* は、フィールドがこの型として解釈されると想定される目的の型です。 自動型キャストは、目的の型をフィールドの型から暗黙的に変換できない場合に試行されます。 注: プロパティが複数値を持つ場合、*GetField\<string\>* は、一覧が Json 文字列 ["val1", "val2", ...] にシリアル化される原因になります。プロパティが存在しない場合は、例外がスローされ、現在のグラフの探索が中止されます。

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

指定した名前のフィールドが現在のノードに存在するかどうかを示します。

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

*GetField\<string\>(fieldName)* と同様に動作します。 ただし、フィールドが見つからない場合に例外をスローせず、代わりに空の文字列 ("") を返します。

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

指定されたプロパティが、現在のノードに存在するかどうかを示します。 *ContainsField(fieldName)* と同じです。

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

プロパティが指定された値を持つかどうかを示します。 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

指定したプロパティの値の数を取得します。 プロパティが存在しない場合は 0 を返します。

### <a name="built-in-helper-functions"></a>組み込みヘルパー関数

##### <a name="action-returnifbool-condition"></a>Action return_if(bool condition)

条件が *true* の場合に *Action.Return* を返します。 条件が *false* で、この式がビットごとの And 演算子によって他のアクションと結合されていない場合、グラフ探索は中止されます。

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

条件が *true* の場合に *Action.Continue* を返します。 条件が *false* で、この式がビットごとの And 演算子によって他のアクションと結合されていない場合、グラフ探索は中止されます。

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

0.0 以上 1.0 未満の乱数を生成します。 この関数は、数値が *p* 以下の場合にのみ *true* を返します。

*json* 検索と比較して、*ラムダ*検索は表現力に富んでいます。クエリのパターンを指定するために、C# ラムダ式を直接使用できます。 次に 2 つの例を示します。

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
