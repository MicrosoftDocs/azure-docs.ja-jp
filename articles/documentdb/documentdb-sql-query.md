---
title: "DocumentDB の SQL 構文と SQL クエリ | Microsoft Docs"
description: "DocumentDB (NoSQL データベース) の SQL 構文、データベースの概念、および SQL クエリについて説明します。 DocumentDB では、JSON クエリ言語として SQL を使用できます。"
keywords: "sql 構文、sql クエリ、json クエリ言語、データベースの概念と sql クエリ、集計関数"
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 5ed72d95ae258d6fa8e808cd72ab6e8a665901c9
ms.openlocfilehash: 4c72a7c7127f2d387926ac2722aeb3f1e5f7c2a6
ms.lasthandoff: 02/22/2017


---
# <a name="sql-query-and-sql-syntax-in-documentdb"></a>DocumentDB における SQL クエリと SQL 構文
Microsoft Azure DocumentDB は、JSON クエリ言語として SQL (Structured Query Language) を使用するドキュメントのクエリをサポートしています。 DocumentDB は完全にスキーマフリーです。 データベース エンジン内で JSON データ モデルを直接処理することで、明示的なスキーマやセカンダリ インデックスの作成を必要とせずに、JSON ドキュメントの自動インデックス作成を実現しています。 

マイクロソフトは、以下の&2; 点を目標に DocumentDB 向けのクエリ言語を設計しました。

* 新しい JSON クエリ言語を開発するのではなく、SQL をサポートすることにしました。 SQL は最も幅広く普及したクエリ言語の&1; つです。 DocumentDB SQL は、JSON ドキュメントに対するリッチ クエリを、正式なプログラミング モデルを通して実現します。
* マイクロソフトでは、データベース エンジン内で JavaScript を直接実行できる JSON ドキュメント データベースを設計するため、JavaScript のプログラミング モデルに基づいてクエリ言語を開発することにしました。 DocumentDB SQL は、JavaScript の型システム、式評価、関数呼び出しを基盤としています。 これによって、リレーショナル プロジェクション、JSON ドキュメント間の階層型ナビゲーション、自己結合、空間クエリ、完全に JavaScript で記述されたユーザー定義関数 (UDF) の呼び出しなどに対して、自然なプログラミング モデルが提供されます。 

マイクロソフトでは、アプリケーションとデータベース間の不整合を削減し、開発者の生産性を高めるには、こうした方針が鍵になると考えています。

最初に、Aravind Ramachandran 氏が DocumentDB のクエリ機能を紹介している次のビデオを見てから、 [クエリのプレイグラウンド](http://www.documentdb.com/sql/demo)のページで、DocumentDB を使ってデータセットに対して SQL クエリを実行する機能を試すことから始めることをお勧めします。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/DataExposedQueryingDocumentDB/player]
> 
> 

その後、この記事に戻って、いくつかのシンプルな JSON ドキュメントと SQL コマンドを使用する SQL クエリのチュートリアルを開始します。

## <a id="GettingStarted"></a>DocumentDB での SQL コマンドの概要
DocumentDB SQL の動作を確認するため、最初にシンプルな JSON ドキュメントを見てみましょう。その後このドキュメントに対して実施するシンプルなクエリについて説明します。 これら&2; つの JSON ドキュメントは、2 つの家族に関するドキュメントです。 DocumentDB では、スキーマやセカンダリ インデックスを明示的に作成する必要がありません。 必要なことは、JSON ドキュメントを DocumentDB コレクションに挿入した後、クエリを実行するだけです。 以下は、Andersen 一家に関するシンプルな JSON ドキュメントです。両親、子供 (および子供のペット)、住所、登録に関する情報が記載されています。 ドキュメントには、文字列、数値、ブール値、配列、入れ子になったプロパティがあります。 

**ドキュメント**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

2 つ目のドキュメントは、`givenName` と `familyName` が `firstName` と `lastName` の代わりに使用されている点だけが違います。

**ドキュメント**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

DocumentDB SQL の重要な項目について理解するため、このデータに対していくつかのクエリを実行してみます。 たとえば以下のクエリを実行すると、ID フィールドが `AndersenFamily`に一致するドキュメントが返されます。 `SELECT *`であるため、クエリの出力は完全な JSON ドキュメントになります。

**クエリ**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


ここで、この JSON 出力を異なる形式に変更する必要がある場合を考えてみます。 このクエリは、住所の都市名と州名が同じ場合に、2 つの特定のフィールド (Name と City) を持つ JSON オブジェクトを表現します。 この場合、"NY, NY" の一致となります。

**クエリ**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**結果**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


次のクエリでは、ID が `WakefieldFamily` と一致する家族の子供の名前がすべて返されます。

**クエリ**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**結果**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


これまでの例からわかる、DocumentDB クエリ言語の注目すべき特性を以下に示します。  

* JSON 値を利用する DocumentDB SQL は、行と列ではなくツリー形式のエンティティを処理します。 つまり、この言語では `Node1.Node2.Node3…..Nodem` のように任意の深さのツリーのノードを参照できます。これは、リレーショナル SQL が `<table>.<column>` という&2; 項目参照を実行するのと同様です。   
* 構造化照会言語ではスキーマのないデータを扱います。 このため、型システムを動的にバインドする必要があります。 同じ式でも、ドキュメントが異なれば異なる型が導出される場合があります。 このようなクエリ結果は有効な JSON 値ですが、固定スキーマの場合でも有効とは限りません。  
* DocumentDB は厳密な JSON ドキュメントだけをサポートします。 つまり、型システムおよび式は、JSON 型のみを扱うように制限されます。 詳細については、 [JSON の仕様に関するページ](http://www.json.org/) を参照してください。  
* DocumentDB コレクションは、スキーマフリーの JSON ドキュメントのコンテナーです。 コレクションにあるドキュメント内およびドキュメント間のデータ エンティティの関係はコンテインメントによって暗黙的にキャプチャされます。プライマリ キーと外部キーの関係ではキャプチャされません。 これは、この記事で後述するドキュメント間結合の点で注意すべき要素です。

## <a id="Indexing"></a>DocumentDB のインデックス作成
DocumentDB SQL の構文について詳しく説明する前に、DocumentDB のインデックス作成に関する設計について説明します。 

データベース インデックスの目的は、さまざまな形式のクエリを処理しながら、リソース (CPU、入力/出力など) の消費量を最小化し、スループットを高め、遅延時間を少なくすることです。 多くの場合、データベース クエリにおける適切なインデックスの選択には、入念な計画と長期間の試行錯誤が必要とされます。 データが厳密なスキーマに準拠せず、データ量も急速に増大するスキーマのないデータベースの場合、このアプローチは現実的ではありません。 

このため、マイクロソフトでは、以下の目標に従って DocumentDB のインデックス サブシステムを設計しました。

* スキーマを必要としないインデックス ドキュメント: インデックス作成サブシステムは、スキーマ情報を一切必要とせず、ドキュメントのスキーマを想定しません。 
* 高度な階層化に対応した、効率的なリレーショナル クエリのサポート: インデックスは DocumentDB クエリ言語を効率的にサポートします。これには、階層型かつリレーショナルなプロジェクションのサポートも含まれます。
* 一定量かつ持続的に実行される書き込みに対する、一貫性のあるクエリのサポート: 一貫性のあるクエリで書き込みスループットの負荷が高くなる場合は、一定量の書き込みが持続的に実行されたとしても、インデックスは効率的かつ段階的にオンラインで更新されます。 ユーザーがドキュメント サービスを構成した際の一貫性レベルでクエリを処理するためには、インデックスの一貫した更新が欠かせません。
* マルチテナントのサポート: テナント間のリソースは、予約ベースのモデルで制御されます。インデックスの更新は、これに従って、レプリカごとに割り当てられたシステム リソース (CPU、メモリ、および&1; 秒あたりの入力/出力操作) 量の範囲内で実行されます。 
* ストレージの効率性: コスト効率性を実現するため、インデックスのディスク上のストレージ オーバーヘッドは、有限かつ予測可能なものにします。 これは、開発者がインデックスのオーバーヘッドとクエリのパフォーマンスを比較して、コストに基づくトレードオフを DocumentDB で実施できるようになるためには必須です。  

コレクションのインデックス作成ポリシーの構成方法については、MSDN の [DocumentDB のサンプル](https://github.com/Azure/azure-documentdb-net) を参照してください。 以降は、DocumentDB SQL の構文の詳細を説明していきます。

## <a id="Basics"></a>DocumentDB SQL クエリの基礎
すべてのクエリは ANSI-SQL 標準に従って SELECT 句とオプションの FROM および WHERE 句で構成されます。 通常は、各クエリで FROM 句のソースが列挙されます。 次に WHERE 句のフィルターがソースに適用され、JSON ドキュメントのサブセットが取得されます。 最後に SELECT 句を使用して、要求された JSON 値が特定のリストにプロジェクションされます。

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>FROM 句
`FROM <from_specification>` 句はオプションです (ソースがクエリの後半でフィルター処理またはプロジェクションされる場合を除く)。 この句の目的は、クエリが動作する対象のデータ ソースを指定することです。 通常はコレクション全体がソースになりますが、コレクションのサブセットを指定することもできます。 

`SELECT * FROM Families` というクエリは、Families コレクション全体がソースとなり、このソースを対象に列挙が行われることを示します。 特別な識別子 ROOT を使うと、コレクション名の代わりにコレクションを表現することができます。 クエリごとに以下のバインド規則が強制されます。

* コレクションは、`SELECT f.id FROM Families AS f` またはシンプルに `SELECT f.id FROM Families f` のようにエイリアス化することができます。 ここで、`f` は `Families` に相当します。 `AS` は識別子をエイリアス化するためのオプションのキーワードです。
* エイリアス化されると、元のソースをバインドすることはできなくなります。 たとえば、`SELECT Families.id FROM Families f` は無効な構文となります。これは、識別子 "Families" が解決できなくなるためです。
* 参照先になる必要があるすべてのプロパティは完全修飾する必要があります。 準拠する厳密なスキーマが存在しない場合、これが強制されることによって曖昧なバインドが回避されます。 このため、`SELECT id FROM Families f` は無効な構文となります。これは、プロパティ `id` がバインドされていないためです。

### <a name="sub-documents"></a>サブドキュメント
ソースはさらに小さいサブセットに限定することができます。 たとえば各ドキュメントのサブツリーだけを列挙する場合、以下の例のようにサブルートをソースにすることができます。

**クエリ**

    SELECT * 
    FROM Families.children

**結果**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

上記の例では配列をソースとして使用していますが、以下の例のようにオブジェクトをソースとして使用することもできます。 ソースで検索できる有効なすべての JSON 値 (未定義でないもの) は、クエリ結果に含められることが想定されます。 `address.state` 値を持たない家族は、クエリ結果から除外されます。

**クエリ**

    SELECT * 
    FROM Families.address.state

**結果**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>WHERE 句
WHERE 句 (**`WHERE <filter_condition>`**) はオプションです。 WHERE 句は、ソースが提供する JSON ドキュメントを結果に含めるために満たす必要がある条件を指定します。 結果の対象となるには、指定された条件についてすべての JSON ドキュメントが "true" と評価される必要があります。 WHERE 句がインデックス レイヤーで使用されることで、結果に含めることが可能なソース ドキュメントの最小のサブセットが判断されます。 

以下のクエリは、name プロパティを含み、そのプロパティ値が `AndersenFamily`であるようなドキュメントを要求しています。 name プロパティを持たない、またはそのプロパティ値が `AndersenFamily` に一致しないドキュメントはすべて除外されます。 

**クエリ**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


上記の例では単純な等値クエリを紹介しました。 DocumentDB SQL はさまざまなスカラー式もサポートしています。 最も多く使用されるのはバイナリ式と単項式です。 ソース JSON オブジェクトからのプロパティ参照も有効な式です。 

現在サポートされている&2; 項演算子を以下に示します。これらは、以下の例のようにクエリ内で使用することができます。  

<table>
<tr>
<td>算術</td>    
<td>+、-、*、/、%</td>
</tr>
<tr>
<td>ビット</td>    
<td>|、&、^、<<, >>、>>> (0 埋め右シフト)</td>
</tr>
<tr>
<td>論理</td>
<td>AND、OR、NOT</td>
</tr>
<tr>
<td>比較</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>String</td>    
<td>|| (連結)</td>
</tr>
</table>  


2 項演算子を使用したクエリを見てみます。

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


単項演算子 (+、-、~、および NOT) もサポートされています。これらはクエリの内側で次の例のように使用することができます。

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



2 項演算子と単項演算子に加えてプロパティ参照も許可されます。 たとえば、`SELECT * FROM Families f WHERE f.isRegistered` は `isRegistered` プロパティを含む JSON ドキュメントを返し、このプロパティの値は JSON の `true` 値と等しくなります。 その他すべての値 (false、null、Undefined、`<number>`、`<string>`、`<object>`、`<array>` など) の場合、ソース ドキュメントが結果から除外されます。 

### <a name="equality-and-comparison-operators"></a>等値演算子と比較演算子
以下の表は、DocumentDB SQL の&2; つの JSON 型で等値比較を実行した結果を示しています。

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>演算子</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>String</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>String<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

その他の比較演算子 (>、>=、! =、<、および <=) については、以下のようになります。   

* 型の間の比較は Undefined になる。
* 2 つのオブジェクト間または&2; つの配列間の比較は Undefined になる。   

フィルター内のスカラー式が Undefined という結果になった場合、Undefined は論理上 "true" と等しくならないため、対応するドキュメントは結果に含まれません。

### <a name="between-keyword"></a>BETWEEN キーワード
また、ANSI SQL などの場合と同様に、値の範囲に対してクエリを表現するときに、BETWEEN キーワードを使用することができます。 BETWEEN は、文字列または数値に対して使用できます。

たとえば、次のクエリは、最初の子のレベルが 1 ～ 5 (両方の値を含む) の間であるすべての家族のドキュメントを返します。 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

ANSI-SQL の場合と異なり、次の例のように、FROM 句内に BETWEEN 句を使用することもできます。

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

クエリの実行速度を速めるには、BETWEEN 句でフィルター処理される数値プロパティやパスに対して範囲のインデックス型を使用するインデックス作成ポリシーを作成してください。 

DocumentDB と ANSI SQL の BETWEEN の使用に関する主な違いは、混合型のプロパティに対して範囲クエリを表すことができる点です。たとえば、"grade" に数値 (5) が入力されているドキュメントや、文字列 ("grade4") が入力されているドキュメントを混在させることができます。 このような場合、JavaScript の場合と同様に、2 種類を比較した結果は "undefined" になり、ドキュメントはスキップされます。

### <a name="logical-and-or-and-not-operators"></a>論理 (AND、OR、および NOT) 演算子
論理演算子は Boolean 値に対して使用されます。 これらの演算子に関する真理値表は以下のようになります。

| または | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

### <a name="in-keyword"></a>IN キーワード
IN キーワードを使用すると、指定した値がリスト内の任意の値と一致するかどうかを確認することができます。 たとえば、次のクエリは、id が "WakefieldFamily" または "AndersenFamily" のどちらかであるすべての家族のドキュメントを返します。 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

この例では、状態が指定された値のいずれかであるすべてのドキュメントを返します。

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>3 項 (?) 演算子と合体 (??) 演算子
3 項演算子と合体演算子は、一般的なプログラミング言語である C# や JavaScript と同様に、条件式の構築に使用することができます。 

3 項 (?) 演算子は、実行中に新しい JSON プロパティを構築するときに役立つ場合があります。 たとえば、次のように、初級、中級、上級など、人間が判読できる形式でクラス レベルを分類するクエリを作成できます。

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

次のクエリのように、演算子への呼び出しを入れ子にすることもできます。

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

他のクエリ演算子と同様に、すべてのドキュメントで条件付きの式の参照先のプロパティが見つからない場合、または比較対象となる型が異なる場合、これらのドキュメントはクエリの結果から除外されます。

合体 (??) 演算子を使用すると、ドキュメントにプロパティが存在するかどうか (つまり 定義されているかどうか) を効率的に確認できます。 この演算子は、半構造化されたデータや混合型のデータに対してクエリを実行するときに役立ちます。 たとえば、次のクエリは、存在する場合に "lastName" を返し、存在しない場合に "surname" を返します。

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>引用符で囲まれたプロパティのアクセサー
プロパティは、引用符で囲まれたプロパティの演算子 `[]`を使用してアクセスすることもできます。 たとえば、 `SELECT c.grade` and `SELECT c["grade"]` は同等です。 この構文はスペース、特殊文字を含むプロパティや、SQL キーワードや予約語と同じ名前を共有するプロパティをエスケープする必要がある場合に役立ちます。

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>SELECT 句
ANSI-SQL と同様に、クエリから取得される値を指定する SELECT 句 (**`SELECT <select_list>`**) は必須です。 ソース ドキュメントの先頭でフィルターされたサブセットがプロジェクション フェーズに渡され、渡された入力のそれぞれについて、指定された JSON 値が取得され、新しい JSON オブジェクトが構築されます。 

一般的な SELECT クエリの例を次に示します。 

**クエリ**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>入れ子になったプロパティ
以下の例では、 `f.address.state` and `f.address.city`の代わりに使用されている点だけが違います。

**クエリ**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


プロジェクションは、以下の例のように JSON 式もサポートします。

**クエリ**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


この `$1` のロールについて説明します。 `SELECT` 句は、JSON オブジェクトを作成する必要がありますが、キーが提供されていないため、`$1` で始まる暗黙的な引数の変数を使用しています。 たとえば、このクエリは `$1` と `$2` でラベル付けされた暗黙的な引数の変数を返します。

**クエリ**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>エイリアス化
ここで、値を明示的にエイリアス化することによって、上記の例を拡張します。 AS はエイリアス化に使用されるキーワードです。 例からわかるようにこれはオプションですが、2 つ目の値を `NameInfo` として表している点に注意してください。 

同じ名前を持つ&2; つのプロパティがクエリにある場合、エイリアス化を使ってプロパティのいずれかまたは両方の名前を変更する必要があります。こうすることで、プロジェクションの結果でこれらを区別することができます。

**クエリ**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>スカラー式
SELECT 句は、プロパティ参照に加えて、定数、算術式、論理式などのスカラー式をサポートします。例として、単純な "Hello World" クエリを以下に示します。

**クエリ**

    SELECT "Hello World"

**結果**

    [{
      "$1": "Hello World"
    }]


スカラー式を使用したより複雑な例は以下のようになります。

**クエリ**

    SELECT ((2 + 11 % 7)-2)/3    

**結果**

    [{
      "$1": 1.33333
    }]


以下の例では、スカラー式の結果は Boolean になります。

**クエリ**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**結果**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>オブジェクトと配列の作成
配列/オブジェクトの作成も、DocumentDB SQL の重要な機能です。 新しい JSON オブジェクトはこれまでの例で作成しました。 同様に、以下の例のように配列を構築することもできます。

**クエリ**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**結果**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>VALUE キーワード
**VALUE** キーワードは、JSON 値を返す方法を提供します。 たとえば以下のクエリでは、スカラー `"Hello World"` が返され、`{$1: "Hello World"}` とはなりません。

**クエリ**

    SELECT VALUE "Hello World"

**結果**

    [
      "Hello World"
    ]


以下のクエリでは、結果に `"address"` ラベルのない JSON 値が返されます。

**クエリ**

    SELECT VALUE f.address
    FROM Families f    

**結果**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

これをさらに拡張した以下の例では、JSON のプリミティブ値、つまり、JSON ツリーのリーフ レベルの値が返されます。 

**クエリ**

    SELECT VALUE f.address.state
    FROM Families f    

**結果**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* 演算子
サポートされている特別な演算子 (*) によって、ドキュメントが現状のまま表されます。 使用する場合は、この演算子が唯一のプロジェクションされるフィールドである必要があります。 `SELECT * FROM Families f` のようなクエリは有効ですが、`SELECT VALUE * FROM Families f ` および `SELECT *, f.id FROM Families f ` は無効です。

**クエリ**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>TOP 演算子
TOP キーワードを使用すると、クエリの値数を制限できます。 TOP と ORDER BY 句を併用すると、結果セットは、指定された順序で並べ替えられた値の先頭 N 個に制限されます。ORDER BY 句を使用しない場合、未定義の順序の結果の先頭 N 個が返されます。 SELECT ステートメントでは、ベスト プラクティスとして常に ORDER BY 句と TOP 句を併用することをお勧めします。 TOP の影響を受ける行を予想どおりに指定するには、併用する必要があります。 

**クエリ**

    SELECT TOP 1 * 
    FROM Families f 

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

(前述のように)、TOP には、定数、またはパラメーター化されたクエリを使用した変数を指定できます。 詳細については、後述のパラメーター化されたクエリを参照してください。

### <a id="Aggregates"></a>集計関数
`SELECT` 句で集計を実行することもできます。 集計関数は、一連の値を計算して&1; つの値を返します。 たとえば、次のクエリは、コレクション内の家族のドキュメントの数を返します。

**クエリ**

    SELECT COUNT(1) 
    FROM Families f 

**結果**

    [{
        "$1": 2
    }]

`VALUE` キーワードを使用して、集計のスカラー値を返すこともできます。 たとえば、次のクエリは、値の数を&1; つの数値として返します。

**クエリ**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**結果**

    [ 2 ]

フィルターを組み合わせて集計を実行することもできます。 たとえば、次のクエリは、住所がワシントン州のドキュメントの数を返します。

**クエリ**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**結果**

    [{
        "$1": 1
    }]

次の表は、DocumentDB でサポートされている集計関数の一覧です。 `SUM` と `AVG` は、数値に対して実行します。`COUNT`、`MIN`、`MAX` は、数値、文字列、ブール値、null 値に対して実行できます。 

| 使用法 | 説明 |
|-------|-------------|
| COUNT | 式の項目の数を返します。 |
| SUM   | 式のすべての値の合計を返します。 |
| 最小   | 式の最小値を返します。 |
| 最大   | 式の最大値を返します。 |
| 平均   | 式の値の平均を返します。 |

配列の反復処理の結果に対して集計を実行することもできます。 詳細については、[クエリでの配列の反復処理](#Iteration)の項目を参照してください。

> [!NOTE]
> Azure Portal のクエリ エクスプローラーを使用する場合は、集計クエリが&1; つのクエリ ページを集計した部分的な結果を返す可能性があります。 SDK の場合は、すべてのページにわたって累計した単一の値が生成されます。 
> 
> コードを使用して集計クエリを実行するには、.NET SDK 1.12.0、.NET Core SDK 1.1.0、または Java SDK 1.9.5 以降が必要です。    
>

## <a id="OrderByClause"></a>ORDER BY 句
ANSI SQL 同様、クエリ実行にオプションで Order By 句を含めることができます。 句に ASC/DESC 引数 (オプション) を含めて、結果を取得する順番を指定できます。

たとえば、居住都市の名前の順序で家族を取得するクエリは次のようになります。

**クエリ**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**結果**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

例として、作成日 (エポック時間、つまり、1970 年 1 月 1 日からの経過時間を秒で表す数字で格納) の順序で家族を取得するクエリを示します。

**クエリ**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**結果**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>高度なデータベースの概念と SQL クエリ

### <a id="Iteration"></a>反復
DocumentDB SQL の **IN** キーワードによる新しいコンストラクトを追加することで、JSON 配列に対する反復がサポートされています。 反復のサポートは FROM ソースが提供します。 まず、以下の例から始めます。

**クエリ**

    SELECT * 
    FROM Families.children

**結果**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

次に、コレクションの子に対する反復を実行する別のクエリを見てみます。 出力配列の違いに注目してください。 この例では、 `children` を分割し、結果を&1; つの配列にフラット化しています。  

**クエリ**

    SELECT * 
    FROM c IN Families.children

**結果**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

これをさらに発展させることで、以下の例のように、配列の個々のエントリをフィルターすることができます。

**クエリ**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**結果**  

    [{
      "givenName": "Lisa"
    }]

配列の反復処理の結果に対して集計を実行することもできます。 たとえば、次のクエリは、すべての家族の子供の数を合計します。

**クエリ**

    SELECT COUNT(child) 
    FROM child IN Families.children

**結果**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>結合
リレーショナル データベースでは、テーブル間を結合できることは非常に重要です。 このことは、正規化されたスキーマの設計においては論理的必然です。 一方 DocumentDB は、スキーマフリーのドキュメントの正規化されていないデータ モデルを扱います。 これは、論理的には "自己結合" と同義です。

この言語がサポートする構文は、<from_source1> JOIN <from_source2> JOIN ...JOIN <from_sourceN> です。 これによって、**N**-タプル (**N** 個の値を持つタプル) のセットが返されます。 それぞれのタプルは、対応するセットに対する、すべてのコレクションのエイリアスの反復によって生成された値を持ちます。 これは、結合に含まれるセットの完全なクロス積ということができます。

JOIN 句の動作を示す例をいくつか紹介します。 以下の例の場合、ソースの各ドキュメントと空集合のクロス積は空になるため、結果は空となります。

**クエリ**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**結果**  

    [{
    }]


以下の例は、ドキュメント ルートと `children` サブルートの間の結合です。 これは&2; つの JSON オブジェクトのクロス積となります。 この JOIN には、children が配列であるという事実は影響していません。これは、扱っている単一のルートがその children 配列であるためです。 そのため、結果には&2; つの結果しか含まれません。これは、その配列を持つ各ドキュメントのクロス積によってドキュメントが&1; つだけ導出されるためです。

**クエリ**

    SELECT f.id
    FROM Families f
    JOIN f.children

**結果**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


より一般的な結合の例を以下に示します。

**クエリ**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**結果**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



まず注意が必要な点は、この `from_source`JOIN** 句の ** が反復子であるという点です。 このため、この場合のフローは以下のようになります。  

* 各子要素 **c** を配列に展開します。
* ドキュメント **f** のルートと、最初の手順でフラット化された各子要素 **c** とのクロス積を適用します。
* 最後に、ルート オブジェクト **f** の name プロパティだけをプロジェクションします。 

最初のドキュメント (`AndersenFamily`) には子要素が&1; つだけ含まれているため、結果セットには、このドキュメントに対応するオブジェクトが&1; つだけ含まれます。 2 つ目のドキュメント (`WakefieldFamily`) には子が&2; つ含まれています。 このため、クロス積によってそれぞれの子で個別のオブジェクトが生成されることで、オブジェクトが&2; つ (このドキュメントに対応するそれぞれの子に&1; つずつ) になります。 クロス積の段階で想定されるとおり、これらのドキュメントのルート フィールドが同じになる点に注意してください。

JOIN の便利な点は、クロス積からタプルを生成できる点です。これ以外の形式によるプロジェクションは簡単ではありません。 さらに、以下の例でわかるとおり、すべてのタプル全体によって満たされる条件をユーザーが選択できるようなタプルの組み合わせをフィルターすることができます。

**クエリ**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**結果**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



この例は前述の例を拡張したもので、二重結合を実行しています。 このため、クロス積は以下の擬似コードのように捉えることができます。

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` には&1; 人の子供がいて、子供はペットを&1; 匹飼っています。 このため、クロス積によってこの家族から&1; 行 (1*1*1) が導出されます。 WakefieldFamily には子供が&2; 人いますが、ペットを飼っているのは "Jesse"&1; 人だけです。 ただしペットは 2 匹います。 したがって、この家族からのクロス積は 1*1*2 = 2 行となります。

次の例では、 `pet`に対するフィルターを追加します。 これによって、ペットの名前が "Shadow" ではないタプルがすべて除外されます。 配列からタプルを構築し、タプルのすべての要素に対してフィルターを実行し、要素の任意の組み合わせをプロジェクションできる点に注目してください。 

**クエリ**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**結果**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>JavaScript 統合
DocumentDB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーという点では、JavaScript ベースのアプリケーション ロジックをコレクションで直接実行することができます。 これによって以下の両方が実現されます。

* データベース エンジン内部での JavaScript ランタイムとの直接かつ緊密な統合により、高パフォーマンスなトランザクション CRUD の操作とクエリを実行する能力。 
* 制御フロー、変数のスコープ設定、割り当て、例外処理プリミティブとデータベース トランザクションの割り当てと統合の自然なモデリング。 DocumentDB による JavaScript 統合のサポートの詳細については、JavaScript のサーバー側プログラミングに関する文書を参照してください。

### <a id="UserDefinedFunctions"></a>ユーザー定義関数 (UDF)
この記事で定義した型に加えて、DocumentDB SQL ではユーザー定義関数 (UDF) のサポートを提供しています。 具体的にはスカラー UDF がサポートされています。開発者は、0 個またはいくつかの引数を渡し、1 つの引数を結果として返すことができます。 また、これらの引数のそれぞれが、有効な JSON 値であることがチェックされます。  

これらのユーザー定義関数を使用することで、DocumentDB SQL の構文を拡張し、カスタムのアプリケーション ロジックをサポートすることができます。 UDF は DocumentDB に登録し、SQL クエリの一部として参照することができます。 実際、その高度な設計に基づいて UDF はクエリから呼び出すことができます。 この選択に基づく当然の帰結として、UDF には、その他の JavaScript の型 (ストアド プロシージャおよびトリガー) とは異なり、コンテキスト オブジェクトへのアクセスはありません。 クエリは読み取り専用で実行されるため、プライマリ レプリカでもセカンダリ レプリカでも実行することができます。 このため、UDF は、その他の JavaScript の型とは異なり、セカンダリ レプリカで実行されるように設計されています。

以下は、DocumentDB データベースに、具体的にはドキュメント コレクションに、UDF を登録する方法の例です。

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

前の例では「 `REGEX_MATCH`」という名前の UDF を作成しています。 この UDF は&2; つの JSON 文字列値 `input` and `pattern` を受け取り、JavaScript の string.match() 関数を使用して、1 つ目の文字列値が&2; つ目の文字列値で指定されたパターンに一致するかどうかをチェックします。

これで、この UDF をプロジェクション内のクエリで使用できるようになりました。 UDF をクエリ内から呼び出すときは、大文字と小文字が区別されるプレフィックス "udf." で 修飾する必要があります。 

> [!NOTE]
> 2015 年 3 月 17 日以前では、SELECT REGEX_MATCH() のような、"udf." プレフィックス のない UDF 呼び出しが DocumentDB でサポートされていました。 この呼び出しパターンは廃止されました。  
> 
> 

**クエリ**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**結果**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

以下の例のように、UDF はフィルター内で使用することもできます。 この場合も、"udf." プレフィックスで修飾します。

**クエリ**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**結果**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


基本的には UDF は有効なスカラー式であり、プロジェクションとフィルターの両方で使用することができます。 

UDF の機能はさらに拡張できます。条件ロジックが使用された以下の例を見てみます。

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


この UDF を実行する例を以下に示します。

**クエリ**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**結果**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


前述の例でわかるように、UDF では、JavaScript 言語の性能と DocumentDB SQL を統合することによって、組み込みの JavaScript ランタイム機能のサポートを活用して手続き型で条件付きの複雑なロジックを実行するためのリッチ プログラミング インターフェイスを提供します。

DocumentDB は、UDF の処理の現在のステージ (WHERE 句または SELECT 句) で、ソース内の各ドキュメントについて、UDF に対する引数を提供します。 結果は、実行パイプライン全体にシームレスに組み込まれます。 UDF のパラメーターに参照されるプロパティを JSON 値で利用できない場合、パラメーターは未定義と見なされ、UDF 呼び出し全体がスキップされます。 同様に UDF の結果が未定義の場合は結果に含められません。 

複雑なビジネス ロジックをクエリで実行するには、UDF は非常に便利な手段です。

### <a name="operator-evaluation"></a>演算子の評価
JSON データベースという特性を持つ DocumentDB は、JavaScript 演算子とその評価セマンティクスに似た関係を備えています。 DocumentDB は JSON サポートという点では JavaScript のセマンティクスを可能な限り保持していますが、演算子の評価はいくつかの点で異なっています。

従来の SQL とは異なり、DocumentDB SQL では、実際に値がデータベースから取得されるまで値の型は未知です。 クエリの実行を効率化するため、大部分の演算子には厳密な型の要件があります。 

DocumentDB SQL は JavaScript とは異なり暗黙的な変換を実行しません。 たとえば、 `SELECT * FROM Person p WHERE p.Age = 21` のようなクエリは、値が 21 の Age プロパティを含むドキュメントに一致します。 Age プロパティが文字列の "21" に一致するようなドキュメントや、"021"、"21.0"、"0021"、"00021" などの無限のバリエーションに一致するドキュメントは対象外となります。 これは、(演算子 == などに基づいて) 文字列の値が暗黙的に数値にキャストされる JavaScript とは異なります。 インデックスの効率的な一致のために、DocumentDB SQL ではこのような選択が避けられないものとなっています。 

## <a name="parameterized-sql-queries"></a>パラメーター化された SQL クエリ
DocumentDB では、使い慣れた @ 表記で表されたパラメーターを使用するクエリがサポートされます。 パラメーター化された SQL により、ユーザーの入力を堅牢に処理し、流用して、SQL インジェクションによってデータが誤って開示されるリスクを回避することができます。 

たとえば、パラメーターとして姓と住所 (都道府県) を使用するクエリを記述し、ユーザーの入力に基づいて、姓と住所 (都道府県) にさまざまな値を指定して実行できます。

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

この要求は、次のように、パラメーター化された JSON クエリとして DocumentDB に送信できます。

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

以下のように、パラメーター化されたクエリを使用して TOP の引数を設定できます。

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

パラメーターの値には、有効な任意の JSON (文字列、数値、ブール値、null、配列や入れ子になった JSON も含む) を指定できます。 また DocumentDB はスキーマフリーであるため、パラメーターはどの型に対しても検証されません。

## <a id="BuiltinFunctions"></a>組み込み関数
DocumentDB では、ユーザー定義関数 (UDF) のようにクエリ内で使用できる、一般的な操作向けのいくつかの組み込み関数をサポートしています。

| 関数グループ          | 操作                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| 数学関数  | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 型チェック関数 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE                                                           |
| 文字列関数        | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER       |
| 配列関数         | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH、ARRAY_SLICE                                                                                         |
| 空間関数       | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、および ST_ISVALIDDETAILED                                                                           | 

現在ユーザー定義関数 (UDF) を使用している処理に対して組み込み関数を利用できる場合は、より高速かつ効率的な対応する組み込み関数を使用する必要があります。 

### <a name="mathematical-functions"></a>数学関数
一般に、各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。 次の表に、サポートされている組み込みの数学関数を示します。


| 使用法 | 説明 |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | 指定された数値式の絶対値 (正の値) を返します。 |
| [CEILING (num_expr)](#bk_ceiling) | 指定された数値式以上の最小の整数値を返します。 |
| [FLOOR (num_expr)](#bk_floor) | 指定された数値式未満の最大の整数を返します。 |
| [EXP (num_expr)](#bk_exp) | 指定された数値式の指数を返します。 |
| [LOG (num_expr [,base])](#bk_log) | 指定された数値式の自然対数、または指定された基数を使用して自然対数を返します。 |
| [LOG10 (num_expr)](#bk_log10) | 指定された数値式の底&10; の対数値を返します。 |
| [ROUND (num_expr)](#bk_round) | 最も近い整数値に丸められた数値を返します。 |
| [TRUNC (num_expr)](#bk_trunc) | 最も近い整数値に切り捨てられた数値を返します。 |
| [SQRT (num_expr)](#bk_sqrt) | 指定された数値式の平方根を返します。 |
| [SQUARE (num_expr)](#bk_square) | 指定された数値式の&2; 乗を返します。 |
| [POWER (num_expr, num_expr)](#bk_power) | 指定された値の指定された数値式のべき乗を返します。 |
| [SIGN (num_expr)](#bk_sign) | 指定された数値式の符号値 (-1、0、1) を返します。 |
| [ACOS (num_expr)](#bk_acos) | コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。 |
| [ASIN (num_expr)](#bk_asin) | サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。 |
| [ATAN (num_expr)](#bk_atan) | タンジェントが指定された数値式となる角度をラジアン単位で返します。 アークタンジェントとも呼ばれます。 |
| [ATN2 (num_expr)](#bk_atn2) | 正の x 軸と、原点から点 (y, x) までの斜線との間の角度をラジアン単位で返します。ここで x と y は、2 つの指定された float 型の式の値です。 |
| [COS (num_expr)](#bk_cos) | 式で指定されたラジアン単位の角度の三角関数コサインを返します。 |
| [COT (num_expr)](#bk_cot) | 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。 |
| [DEGREES (num_expr)](#bk_degrees) | ラジアン単位で指定された角度に対応する度数単位の角度を返します。 |
| [PI ()](#bk_pi) | 円周率の定数値を返します。 |
| [RADIANS (num_expr)](#bk_radians) | 数値式が度数単位で入力されると、ラジアンを返します。 |
| [SIN (num_expr)](#bk_sin) | 式で指定されたラジアン単位の角度の三角関数サインを返します。 |
| [TAN (num_expr)](#bk_tan) | 指定された式で入力された式のタンジェントを返します。 |

たとえば、次のようなクエリを実行できます。

**クエリ**

    SELECT VALUE ABS(-4)

**結果**

    [4]

DocumentDB の関数と ANSI SQL の間の主な違いとして、DocumentDB の関数はスキーマを持たないデータやスキーマが混在するデータをうまく扱えるようになっています。 たとえば、Size プロパティがないドキュメントや "unknown" のような数値以外の値を持つドキュメントの場合、エラーを返す代わりに、ドキュメントがスキップされます。

### <a name="type-checking-functions"></a>型チェック関数
型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。 型チェック関数を使用すると、ドキュメント内のプロパティの型が変数または不明の場合に型をその場で判定できます。 次の表に、サポートされている組み込みの型チェック関数を示します。

<table>
<tr>
  <td><strong>使用方法</strong></td>
  <td><strong>説明</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>値の型が配列であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>値の型がブール値であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>値の型が null であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>値の型が数値であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>値の型が JSON オブジェクトであるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>値の型が文字列であるかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>プロパティに値が代入されているかどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>値の型が文字列、数値、ブール値、null のいずれであるかを示すブール値を返します。</td>
</tr>

</table>

これらの関数を使用して、次のようなクエリを実行できます。

**クエリ**

    SELECT VALUE IS_NUMBER(-4)

**結果**

    [true]

### <a name="string-functions"></a>文字列関数
次のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。 組み込みの文字列関数を次の表に示します。

| 使用法 | 説明 |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |指定された文字列式の文字数を返します。 |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |2 つ以上の文字列値を連結した結果である文字列を返します。 |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |文字列式の一部を返します。 |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |1 つ目の文字列式が&2; つ目の文字列で終了しているかどうかを示すブール値を返します。 |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |1 つ目の文字列式が&2; つ目の文字列で終了しているかどうかを示すブール値を返します。 |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |1 つ目の文字列式に&2; つ目の文字列式が含まれているかどうかを示すブール値を返します。 |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |1 つ目に指定された文字列式内で&2; つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。 |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |指定された文字数分、文字列の左側の部分を返します。 |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |指定された文字数分、文字列の右側の部分を返します。 |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |文字列式の先頭の空白を削除して返します。 |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |文字列式のすべての後続の空白を切り捨てて返します。 |
| [LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |文字列式の大文字データを小文字に変換して返します。 |
| [UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |文字列式の小文字データを大文字に変換して返します。 |
| [REPLACE (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |指定された文字列値のすべての出現箇所をもう&1; つの文字列値に置き換えます。 |
| [REPLICATE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate) |文字列値を指定された回数だけ繰り返します。 |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |文字列値の順序を逆にして返します。 |

これらの関数を使用して、次のようなクエリを実行できます。 たとえば、次のようにすると、ファミリ名を大文字で返すことができます。

**クエリ**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**結果**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

次の例のように文字列を連結することもできます。

**クエリ**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**結果**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


文字列関数は、WHERE 句の中で使用して、次の例のように結果をフィルター処理することもできます。

**クエリ**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**結果**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>配列関数
次のスカラー関数は、配列入力値に対して演算を実行し、数値、ブール値、または配列値を返します。 組み込みの配列関数を次の表に示します。

| 使用法 | 説明 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |指定された配列式の要素の数を返します。 |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |2 つ以上の配列値を連結した結果である配列を返します。 |
| [ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |配列に指定された値が含まれているかどうかを示すブール値を返します。 |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |配列式の一部を返します。 |

配列関数を使用すると、JSON に含まれる配列を操作できます。 例として、親の&1; 人が "Robin Wakefield" であるすべてのドキュメントを返すクエリを次に示します。 

**クエリ**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**結果**

    [{
      "id": "WakefieldFamily"
    }]

ARRAY_LENGTH を使用して家族あたりの子供の数を取得する、もう&1; つの例を次に示します。

**クエリ**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**結果**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>空間関数
DocumentDB は、以下の Open Geospatial Consortium (OGC) 組み込み関数を使った地理空間検索をサポートしています。 

<table>
<tr>
  <td><strong>使用方法</strong></td>
  <td><strong>説明</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>2 つの GeoJSON Point、Polygon、または LineString 式間の距離を返します。</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>1 つ目の GeoJSON オブジェクト (Point、Polygon、または LineString) が&2; つ目の GeoJSON オブジェクト (Point、Polygon、または LineString) 内に存在するかどうかを示すブール式を返します。</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>指定された&2; つの GeoJSON オブジェクト (Point、Polygon、または LineString) が重なるかどうかを示すブール式を返します。</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>指定された GeoJSON Point、Polygon、または LineString 式が有効かどうかを示すブール値を返します。</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。</td>
</tr>
</table>

空間関数を使用すると、空間データに対して近接検索クエリを実行することができます。 指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを ST_DISTANCE 組み込み関数で取得するクエリの例を以下に示します。 

**クエリ**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

DocumentDB での地理空間のサポートの詳細については、 [Azure DocumentDB での地理空間データの操作](documentdb-geospatial.md)に関するページを参照してください。 これで空間関数の説明が終わり、DocumentDB 用の SQL 構文の説明も終わります。 次に、LINQ クエリの動作とこれまでに説明した構文の関係を見ていきましょう。

## <a id="Linq"></a>LINQ と DocumentDB SQL
LINQ は、計算処理をオブジェクトのストリームに対するクエリとして表現する .NET プログラミング モデルです。 DocumentDB は LINQ と連携するためのクライアント側ライブラリを提供しています。ここでは、JSON オブジェクトと .NET オブジェクト間の変換と、LINQ クエリのサブセットから DocumentDB クエリへのマッピングを実施します。 

DocumentDB を使用した LINQ クエリ サポートのアーキテクチャは以下の図のようになります。  開発者は DocumentDB クライアントを使用して **IQueryable** オブジェクトを作成できます。このオブジェクトが DocumentDB クエリ プロバイダーを直接照会することで、LINQ クエリが DocumentDB クエリに変換されます。 次にクエリが DocumentDB サーバーに渡されることで、結果セットが JSON 形式で取得されます。 返された結果は、クライアント側で .NET オブジェクトのストリームに逆シリアル化されます。

![DocumentDB を使用する LINQ クエリをサポートするアーキテクチャ - SQL 構文、JSON クエリ言語、データベースの概念と SQL クエリ][1]

### <a name="net-and-json-mapping"></a>.NET と JSON のマッピング
.NET オブジェクトと JSON ドキュメント間のマッピングは自然に実行されます。 各データ メンバー フィールドが JSON オブジェクトにマップされ、フィールド名がオブジェクトの "key" 部分にマップされ、"value" 部分がオブジェクトの値の部分に再帰的にマップされます。 以下の例では、作成された Family オブジェクトが JSON ドキュメントにマップされています。 同様に JSON ドキュメントから .NET オブジェクトへのマップも行われています。

**C# クラス**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ から SQL への変換
DocumentDB クエリ プロバイダーは、LINQ クエリから DocumentDB SQL クエリへのマッピングをベスト エフォートで実行します。 ここからの説明は、LINQ の基本的な知識を持つ読者向けとなります。

まず型システムについては、すべての JSON プリミティブ型 (数値型、ブール値、文字列、Null) がサポートされます。 サポートされるのはこれらの JSON 型だけです。 以下のスカラー式がサポートされます。

* 定数値 – これらには、クエリが評価された時点でのプリミティブ データ型の定数値が含まれます。
* プロパティ/配列インデックス式 – これらの式は、オブジェクトまたは配列要素のプロパティを参照します。
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n is an int variable
* 算術式 - これらには、数値およびブール値に対する共通の算術式が含まれます。 完全な一覧については、SQL 仕様を参照してください。
  
     2 * family.children[0].grade;    x + y;
* 文字列比較式 - これらには、文字列値と定数文字列値との比較が含まれます。  
  
     mother.familyName == "Smith";    child.givenName == s; //s is a string variable
* オブジェクト/配列作成式 - これらの式は、複合値の型または匿名型のオブジェクト、またはこうしたオブジェクトの配列を返します。 これらの値は入れ子にすることができます。
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //an anonymous type with 2 fields              
     new int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>サポートされている LINQ 演算子の一覧
DocumentDB .NET SDK に含まれる LINQ プロバイダーでサポートされる LINQ 演算子の一覧です。

* **Select**: オブジェクトの構築など、プロジェクションによって SQL SELECT に変換します。
* **Where**: フィルターによって SQL WHERE に変換します。また、&&、||、および ! から SQL 演算子への 変換をサポートします。
* **SelectMany**: SQL JOIN 句に対して配列をアンワインドできます。 配列要素に関してフィルターする式を連結または入れ子にするために使用できます。
* **OrderBy と OrderByDescending**: ORDER BY の昇順/降順に変換します。
* **CompareTo**: 範囲比較に変換します。 .NET では文字列を比較できないので、一般的に文字列に使用されます。
* **Take**: クエリからの結果を制限するために SQL TOP に変換します。
* **数学関数**: .NET の Abs、Acos、Asin、Atan、Ceiling、Cos、Exp、Floor、Log、Log10、Pow、Round、Sign、Sin、Sqrt、Tan、Truncate から、同等の SQL 組み込み関数への変換をサポートします。
* **文字列関数**: .NET の Concat、Contains、EndsWith、IndexOf、Count、ToLower、TrimStart、Replace、Reverse、TrimEnd、StartsWith、SubString、ToUpper から、同等の SQL 組み込み関数への変換をサポートします。
* **配列関数**: .NET のからの変換をサポートしています。NET の Concat、Contains、Count から、同等の SQL 組み込み関数への変換をサポートします。
* **地理空間の拡張関数**: スタブ メソッドの Distance、Within、IsValid、IsValidDetailed から、同等の SQL 組み込み関数への変換をサポートします。
* **ユーザー定義関数の拡張関数**: スタブ メソッドの UserDefinedFunctionProvider.Invoke から、対応するユーザー定義関数への変換をサポートします。
* **その他**: 合体演算子と条件演算子の変換をサポートします。 コンテキストに応じて、Contains から、CONTAINS、ARRAY_CONTAINS、または SQL IN に変換できます。

> [!NOTE]
> 集計演算子 **Count、Sum、Min、Max、Average** は現在サポートされていませんが、SDK の将来のバージョンでのサポートを予定しています。  
> 
> 

### <a name="sql-query-operators"></a>SQL クエリ演算子
標準 LINQ クエリ演算子が DocumentDB クエリに変換される方法を以下のいくつかの例で示します。

#### <a name="select-operator"></a>Select 演算子
構文は `input.Select(x => f(x))` です。`f` はスカラー式です。

**LINQ ラムダ式**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**LINQ ラムダ式**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**LINQ ラムダ式**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>SelectMany 演算子
構文は `input.SelectMany(x => f(x))` です。`f` はコレクション型を返すスカラー式です。

**LINQ ラムダ式**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Where 演算子
構文は `input.Where(x => f(x))` です。`f` はブーリアン値を返すスカラー式です。

**LINQ ラムダ式**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**LINQ ラムダ式**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>複合 SQL クエリ
上記の演算子を組み合わせることで、より強力なクエリを作成できます。 DocumentDB は入れ子になったコレクションをサポートするため、連結による複合も入れ子による複合も可能です。

#### <a name="concatenation"></a>連結
構文は `input(.|.SelectMany())(.Select()|.Where())*`です。 連結クエリは、オプションの `SelectMany` クエリで開始し、複数の `Select` または `Where` 演算子を追加できます。

**LINQ ラムダ式**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**LINQ ラムダ式**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**LINQ ラムダ式**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ ラムダ式**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>入れ子
構文は `input.SelectMany(x=>x.Q())` です。Q は `Select`、`SelectMany`、または `Where` 演算子です。

入れ子になったクエリでは、内側のクエリが外側のコレクションの各要素に適用されます。 重要な機能として、内側のクエリは外側のコレクションの要素のフィールドを自己結合のように参照できます。

**LINQ ラムダ式**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**LINQ ラムダ式**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**LINQ ラムダ式**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>SQL クエリの実行
DocumentDB が公開するリソースには、HTTP/HTTPS 要求機能を持つ任意の言語から REST API を呼び出すことでアクセスできます。 さらに、.NET、Node.js、JavaScript、Python など、いくつかの主要な言語のプログラミング ライブラリも DocumentDB に用意されています。 REST API と各種のライブラリはすべて SQL 経由のクエリをサポートしています。 .NET SDK は SQL に加えて LINQ クエリをサポートしています。

以下の例では、クエリを作成して DocumentDB データベース アカウントに送信する方法について説明します。

### <a id="RestAPI"></a>REST API
DocumentDB は、HTTP を介したオープンな RESTful プログラミング モデルを提供します。 データベース アカウントは Azure サブスクリプションを使用してプロビジョニングできます。 DocumentDB リソース モデルは、データベース アカウントに従属する一連のリソースから成り、個々のリソースは、不変の論理 URI アドレスを使用して参照することができます。 このドキュメントでは、そうした一連のリソースを " フィード " と呼ぶことにします。 データベース アカウントはデータベースのセットで構成され、各データベースに複数のコレクションが含まれています。これらのコレクションのそれぞれに、ドキュメント、UDF、その他のリソースが含まれます。

これらのリソースとの基本的な対話モデルでは、HTTP の動詞である GET、PUT、POST、および DELETE が標準の解釈で使用されます。 POST 動詞は、新しいリソースの作成、ストアド プロシージャの実行、または DocumentDB クエリの発行に使用されます。 クエリは常に読み取り専用の操作で、副作用はありません。

以下の例では、これまでに説明した&2; つのサンプル ドキュメントを含むコレクションに対する、DocumentDB クエリの POST を示しています。 このクエリには、JSON の名前プロパティに対するシンプルなフィルターがあります。 `x-ms-documentdb-isquery` と Content-Type の使用: `application/query+json` ヘッダーは、クエリによる操作であることを示しています。

**要求**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**結果**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


2 つ目の例は、結合から複数の結果を返す、より複雑なクエリを示しています。

**要求**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**結果**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


クエリ結果を&1; つの結果ページに収めることができない場合、REST API は `x-ms-continuation-token` 応答ヘッダーを使って継続トークンを返します。 クライアントは、このヘッダーを後続の結果に含めることで、結果を改ページすることができます。 ページあたりの結果の数も、 `x-ms-max-item-count` 番号ヘッダーで制御できます。 指定されたクエリに `COUNT` などの集計関数が含まれる場合、クエリ ページが結果ページの部分的な集計値を返す可能性があります。 最終的な結果を得るためには、クライアントがこれらの結果に対して二次的な集計 (例: 個々のページで返された数を集計して合計数を返す) を実行する必要があります。

クエリのデータ一貫性ポリシーを管理するには、すべての REST API 要求と同様に `x-ms-consistency-level` ヘッダーを使用します。 セッションの一貫性を維持するには、すべてのクエリ要求で最新の `x-ms-session-token` Cookie ヘッダーもエコーする必要があります。 クエリされたコレクションのインデックス作成ポリシーは、クエリ結果の一貫性にも影響を与える点に注意してください。 既定のインデックス作成ポリシーの設定では、インデックスはドキュメントの内容に関して常に最新の状態になり、クエリ結果はデータ用に選択された一貫性と一致します。 インデックス作成ポリシーが "遅延" に緩和されている場合、返されるクエリ結果も古いものになる可能性があります。 詳細については、[DocumentDB の一貫性レベルに関する記事][consistency-levels]を参照してください。

指定されたクエリを、コレクションで構成されたインデックス作成ポリシーがサポートできない場合、DocumentDB サーバーは 400 "Bad Request" を返します。 これは、ハッシュ (等値) 検索用に構成されたパスに対する範囲クエリと、インデックス作成から明示的に除外されたパスのために返されます。 `x-ms-documentdb-query-enable-scan` ヘッダーを指定することで、インデックスを利用できない場合のクエリによるスキャン実行を許可することができます。

### <a id="DotNetSdk"></a>C# (.NET) SDK
.NET SDK は LINQ クエリと SQL クエリの両方をサポートしています。 以下の例は、このドキュメントで前述したシンプルなフィルター クエリを実行する方法を示しています。

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


このサンプルでは、2 つのプロパティの等値比較を各ドキュメント内で実行し、匿名プロジェクションを使用しています。 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


次のサンプルは、LINQ SelectMany によって表された結合を示しています。

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



上記に示したとおり、.NET クライアントは foreach ブロックのクエリ結果のすべてのページを自動で反復処理します。 REST API のセクションで説明したクエリ オプションも .NET SDK で利用可能です。これには、`FeedOptions` および `FeedResponse` クラスを CreateDocumentQuery メソッドで使用します。 ページの数は `MaxItemCount` 設定を使用して制御できます。 

開発者は、`IDocumentQueryable` を作成することでページ設定を明示的に制御できます。これには `IQueryable` オブジェクトを使用し、次に ` ResponseContinuationToken` の値を読み取り、これらを`RequestContinuationToken` として`FeedOptions` 内で渡します。 `EnableScanInQuery` を設定することで、構成されたインデックス作成ポリシーでクエリをサポートできない場合に、スキャンを有効にすることができます。 パーティション分割コレクションの場合は、`PartitionKey` を使用すると&1; つのパーティションに対してクエリを実行でき (ただし、DocumentDB はクエリ テキストからこれを自動的に抽出できます)、`EnableCrossPartitionQuery` を使用すると複数のパーティションに対して実行することが必要な場合があるクエリを実行できます。 

クエリのその他のサンプルについては、 [DocumentDB の .NET サンプル](https://github.com/Azure/azure-documentdb-net) を参照してください。 

> [!NOTE]
> 集計クエリを実行するには、SDK 1.12.0 以降が必要です。 LINQ では集計関数はサポートされていませんが、.NET SDK 1.13.0 で提供される予定です。
>

### <a id="JavaScriptServerSideApi"></a>JavaScript のサーバー側 API
DocumentDB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーという点では、JavaScript ベースのアプリケーション ロジックをコレクションで直接実行することができます。 コレクション レベルで登録された JavaScript ロジックは、特定のコレクション内のドキュメントの操作に対してデータベース操作を発行できるようになっています。 これらの操作は周囲の ACID トランザクションにラップされます。

以下の例は、JavaScript サーバー API で queryDocuments を使用して、ストアド プロシージャとトリガーからクエリを実行する方法を示しています。

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>参考資料
1. [Azure DocumentDB の概要][introduction]
2. [DocumentDB SQL の仕様](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [DocumentDB .NET のサンプル](https://github.com/Azure/azure-documentdb-net)
4. [DocumentDB の一貫性レベル][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Javascript 仕様 [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. 大規模なデータベース向けのクエリ評価技術 [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. 「Query Processing in Parallel Relational Database Systems」(IEEE Computer Society Press、1994 年)
11. 「Query Processing in Parallel Relational Database Systems」(Lu、Ooi、Tan、IEEE Computer Society Press、1994 年)
12. 「Pig Latin: A Not-So-Foreign Language for Data Processing」(Christopher Olston、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar、Andrew Tomkins、SIGMOD、2008 年)
13. G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md

