---
title: filters 句と order-by 句のための OData 式の構文 - Azure Search
description: Azure Search クエリのための filters 式と order-by 式の OData 構文。
ms.date: 03/27/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: ab98c3be75fb59603be66ee84e0d288de56cdc91
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648505"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Azure Search の filters 句と order-by 句のための OData 式の構文

Azure Search では、**$filter** 式と **$orderby** 式に OData 式の構文のサブセットを利用できます。 filter 式はクエリの構文解析中に評価され、検索が特定のフィールドに制約されるか、インデックス スキャン中に使用される一致条件が追加されます。 order-by 式は結果セットに対して後処理の手順で適用されます。 filters 式も order-by 式もクエリ要求に含まれ、**search** パラメーターで使用されている [simple](query-simple-syntax.md) または [full](query-lucene-syntax.md) クエリ構文に関係なく、OData の構文に準拠します。 この記事では、filters 式と order-by 式で使用される OData 式の参照ドキュメントを提供します。

## <a name="filter-syntax"></a>フィルターの構文

**$filter** 式では、完全に表現されたクエリとしてスタンドアロン実行するか、クエリにパラメーターを追加して微調整できます。 次は主要なシナリオの例です。 最初の例では、フィルターはクエリの内容です。


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

もう 1 つの一般的なユース ケースは、フィルターを組み合わせてファセットを作ることです。ユーザーが動かすファセット ナビゲーション選択に基づき、フィルターによってクエリ領域を減らします。

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>フィルターの演算子  

- 論理演算子 (and、or、not)。  

- 比較式 (`eq, ne, gt, lt, ge, le`)。 文字列比較では大文字と小文字が区別されます。  

- サポートされている [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) 型の制約 (サポートされている型の一覧については、「[Supported data types (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)」(サポートされているデータ型 &#40;Azure Search&#41;) を参照してください)。 コレクション型の制約はサポートされていません。  

- フィールド名の参照。 `filterable` フィールドのみをフィルター式で使用できます。  

- パラメーターなしの `any`。 型 `Collection(Edm.String)` のフィールドに要素が含まれているかどうかがこれでテストされます。  

- ラムダ式のサポートが限定された `any` と `all`。 
    
  -   `any/all` は型 `Collection(Edm.String)` のフィールドでサポートされています。 
    
  -   `any` は単純な等値式と共に使用するか、`search.in` 関数と共に使用することのみが可能です。 単純な式は、`Title eq 'Magna Carta'` のように、単一フィールドとリテラル値の比較で構成されます。
    
  -   `all` は単純な不等値式と共に使用するか、`not search.in` と共に使用することのみ可能です。   

- 地理空間関数の `geo.distance` と `geo.intersects`。 `geo.distance` 関数により、フィルターの一部として、1 つはフィールドで、もう 1 つは定数で渡される 2 つの点の間の距離が、キロメートル単位で返されます。 `geo.intersects` 関数は、指定された点が指定された多角形の内部にある場合は true を返します。点はフィールドとして、多角形は定数として指定されて、フィルターの一部として渡されます。  

  多角形は、境界リングを定義するポイント シーケンスとして格納される 2 次元の表面です (下の例を参照してください)。 多角形は閉じられている必要があります。つまり、最初と最後のポイント セットを同じにする必要があります。 [多角形のポイントは反時計回りにする必要があります](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

  `geo.distance` により Azure Search でキロメートル単位の距離が返されます。 これは、距離を通常はメートルで返す、OData 地理空間操作をサポートする他のサービスとは異なります。  

  > [!NOTE]  
  >  フィルターで geo.distance を使用するとき、`lt`、`le`、`gt`、`ge` を使用し、関数で返される距離と定数を比較する必要があります。 演算子の `eq` と `ne` は、距離を比較するときは使用できません。 たとえば、`$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` では、geo.distance が正しく使用されています。  

- `search.in` 関数によって、与えられた文字列フィールドが与えられた値リストのそれと等しいかどうかがテストされます。 これを any または all で使用し、与えられた値リストと文字列コレクション フィールドの単一値を比較することもできます。 フィールドとリストの各値の比較は、`eq` 演算子の場合と同様に、大文字と小文字を区別して決定されます。 そのため、`search.in(myfield, 'a, b, c')` のような式は、`search.in` の方がパフォーマンスが良いという点を除き、`myfield eq 'a' or myfield eq 'b' or myfield eq 'c'` に等しくなります。 

   `search.in` 関数の最初のパラメーターは文字列フィールド参照です (あるいは、`search.in` が `any` または `all` 式で使用されている場合、文字列コレクション フィールドの範囲変数)。 
  
   2 つ目のパラメーターは、スペースまたはコンマで区切られた、値リストが含まれる文字列です。 
  
   3 つ目のパラメーターは、2 つ目のパラメーターの値リストを構文解析するとき、文字列の各文字、またはこの文字列のサブセットが区切り文字として処理される文字列です。 値にスペースやコンマが含まれるため、それ以外の区切り文字を使用する必要がある場合、任意で 3 つ目のパラメーターを `search.in` に指定できます。 

  > [!NOTE]   
  > 大量の定数値に対して 1 つのフィールドを比較する必要がある場合もあります。 たとえば、セキュリティによるトリミングと共にフィルターを実装する場合、要求しているユーザーに読み取りアクセスを与える ID リストに対してドキュメントの ID フィールドを比較する必要があります。 このようなシナリオでは、等値式の複雑な論理和演算より、`search.in` 関数を使用することを強くお勧めします。 たとえば、`Id eq 123 or Id eq 456 or ....` の代わりに `search.in(Id, '123, 456, ...')` を使用します。 
  >
  > `search.in` を使用する場合で、2 つ目のパラメーターに数百または数千単位の値リストが含まれるとき、応答時間が 1 秒以下になることが期待できます。 `search.in` に渡すことができる項目の数は、最大要求サイズにより制限されますが、明示的な上限はありません。 ただし、値の数が増えると、それだけ待ち時間が長くなります。

- `search.ismatch` 関数はフィルター式の一部として検索クエリを評価します。 検索クエリに一致するドキュメントは結果セットで返されます。 この関数の次のオーバーロードが利用できます。
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  各値の説明: 
  
  - `search`: 検索クエリ ([simple](query-simple-syntax.md) または [full](query-lucene-syntax.md) クエリ構文)。 
  - `queryType`: "simple" または "full"。既定値は "simple" です。 `search` パラメーターで使用されたクエリ言語を指定します。
  - `searchFields`: 検索する検索可能フィールドのコンマ区切りリスト。既定値はインデックスのすべての検索可能フィールドになります。    
  - `searchMode`: "any" または "all"。既定値は "any" です。 ドキュメントを一致としてカウントするために、任意の検索語句またはすべての検索語句が一致する必要があるかどうかが示されます。

  上記のパラメーターはすべて、それに対応する[検索要求パラメーター](https://docs.microsoft.com/rest/api/searchservice/search-documents)に等しくなります。

- `search.ismatchscoring` 関数により、`search.ismatch` 関数と同様に、パラメーターとして渡された検索クエリに一致したドキュメントに true が返されます。 両者の違いは、`search.ismatchscoring` クエリに一致するドキュメント スコアは全体的なドキュメント スコアに貢献するが、`search.ismatch` の場合、ドキュメント スコアは変わらないという点にあります。 この関数の次のオーバーロードは `search.ismatch` のそれと等しいパラメーターで利用できます。
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  `search.ismatch` 関数と `search.ismatchscoring` 関数は互いに、またはフィルター代数と完全に直交です。 つまり、いずれの関数も同じフィルター式で使用できます。 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>180 度経線に広がる地理空間のクエリと多角形  
 180 度経線 (日付変更線の近く) を含むクエリを作成する多くの地理空間クエリ ライブラリは、使用禁止か、多角形を 2 つに分割して経線のそれぞれの側に 1 つずつ配置するなどの回避策が必要とします。  

 Azure Search では、クエリの形状が長方形で、(`geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'` のように) 経度と緯度に沿ってグリッド レイアウトに合わせて座標の位置が調整される場合、180 度経線を含む地理空間クエリは予想どおり動作します。 それ以外の場合、長方形ではない形状や整列されていない形状については、多角形を分割する手法を検討してください。  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>フィルター サイズの制限 

 Azure Search に送信できるフィルター式のサイズと複雑性には制限があります。 この上限はおおよそ、フィルター式の句の数に基づきます。 目安として数百単位の句がある場合、上限に達する危険性があります。 サイズが無制限のフィルターを生成しない方法でアプリケーションを設計することをお勧めします。  


## <a name="filter-examples"></a>フィルターの例  

 基本料金が $200 未満で 4 つ星以上のホテルをすべて探します。  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 "Roach Motel" 以外で 2010 年以降に改装されているホテルをすべて探します。  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 2010 年以降に改装されているホテルで基本料金が $200 未満のホテルをすべて探します。datetime には太平洋標準時のタイム ゾーン情報を含めます。  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 駐車場を備え、禁煙のホテルをすべて探します。  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- または -  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 高級ホテルをすべて探すか、駐車場を備えた 5 つ星以上のホテルをすべて探します。  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 "wifi" というタグが付いたホテルをすべて探します (各ホテルの Collection(Edm.String) フィールドにタグが格納されています):  

```
$filter=tags/any(t: t eq 'wifi')
```

 "motel" というタグがないホテルをすべて探します。  

```
$filter=tags/all(t: t ne 'motel')
```

 何らかのタグが付いているホテルをすべて探します。  

```
$filter=tags/any()
```

タグの付いていないホテルをすべて探します。  

```
$filter=not tags/any()
```


 与えられた参照ポイントの 10 キロメートル内にあるホテルをすべて探します (場所は型 Edm.GeographyPoint のフィールドです)。  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 多角形として表現された所与のビューポート内にあるホテルをすべて探します (場所は型 Edm.GeographyPoint のフィールドです)。 多角形が閉じられていること (最初のポイントと最後のポイントのセットを同じにする必要があります)、[ポイントが時計回りで一覧表示されている](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)ことに注意してください。

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 "説明" フィールドに値がないか、値が明示的に null に設定されているホテルをすべて探します。  

```
$filter=description eq null
```

'Roach motel' または 'Budget hotel' という名前が付いているホテルをすべて探します。 語句には、既定の区切り記号であるスペースを含めます。 3 番目の文字列パラメーターとして、単一引用符の中に別の区切り記号を指定できます。  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

'Roach motel' または 'Budget hotel' という名前が与えられたホテルをすべて探します。'|' で区切ります。  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

'wifi' または 'pool' というタグが付いているホテルをすべて探します。  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

タグ内の 'heated towel racks' (タオル ウォーマー ラック) や 'hairdryer included' (ヘアドライヤーあり) など、コレクション内の語句との一致を探します。 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

"motel" と 'cabin' というタグがないホテルをすべて探します。  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

"waterfront" という言葉の付いたドキュメントを探します。 このフィルターは `search=waterfront` を指定した[検索要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)と同じになります。

```
$filter=search.ismatchscoring('waterfront')
```

"hostel" という言葉を含み、評価が 4 以上のドキュメントを探すか、"motel" という言葉を含み、評価が 5 のドキュメントを探します。 この要求は `search.ismatchscoring` 関数なしでは表現できないことに注意してください。

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

"luxury" という言葉のないドキュメントを探します。

```
$filter=not search.ismatch('luxury') 
```

"ocean view" というフレーズを含むか、評価が 5 のドキュメントを探します。 `search.ismatchscoring` クエリは `hotelName` フィールドと `description` フィールドに対してのみ実行されます。
論理和演算の 2 つ目の句にのみ一致するドキュメントも返されることに注意してください。評価が 5 のホテルです。 式のスコア部分にこれらのドキュメントが一致しなかったことをはっきりさせるため、スコア 0 で返されます。

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

"hotel" という言葉と "airport" という言葉がホテルの説明で互いに 5 単語以内にあり、喫煙が許可されないドキュメントを探します。 このクエリでは、[完全 Lucene クエリ言語](query-lucene-syntax.md)が使用されます。

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>order-by 構文

**$orderby** パラメーターは、最大 32 個の式をコンマで区切ったリストを受け取ります。形式は `sort-criteria [asc|desc]` です。 並べ替え基準は `sortable` フィールドの名前か、`geo.distance` 関数または `search.score` 関数の呼び出しになります。 `asc` または `desc` を使用し、並べ替え順序を明示的に指定できます。 既定の順序は昇順です。

複数のドキュメントで並べ替え基準が同じであり、`search.score` 関数が使用されない場合 (たとえば、数値の `rating` フィールドで並べ替えるとき、3 つすべてのドキュメントの評価が 4 である場合)、同点にはドキュメント スコアで順位が付けられ、降順で表示されます。 ドキュメント スコアが同じ場合 (たとえば、要求にフルテキスト検索クエリが指定されていない)、同点のドキュメントには相対的な順序付けが確定しません。
 
複数の並べ替え基準を指定できます。 式の順序によって最終的な並べ替え順序が決められます。 たとえば、スコア別に降順で並べ替えた後、評価で並べ替える場合、構文は `$orderby=search.score() desc,rating desc` のようになります。

**$orderby** の `geo.distance` の構文は **$filter** の場合と同じになります。 **$orderby** で `geo.distance` を使用するとき、それが適用されるフィールドは `Edm.GeographyPoint` 型にする必要があり、また `sortable` である必要があります。  

**$orderby** の `search.score` の構文は `search.score()` です。 関数 `search.score` には、いかなるパラメーターも取得されません。  
 

## <a name="order-by-examples"></a>order by の例

基本料金別にホテルを昇順で並べ替えます。

```
$orderby=baseRate asc
```

評価別にホテルを降順で並べ替え、その後、基本料金別に昇順で並べ替えます (昇順は既定値です)。

```
$orderby=rating desc,baseRate
```

評価別にホテルを降順で並べ替え、その後、特定の座標からの距離別に昇順で並べ替えます。

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

search.score と評価に基づいて降順でホテルを並べ替え、その後、所与の座標からの距離別に昇順で並べ替え、2 つのホテルで評価が同じ場合、距離的に近い方が先に表示されるようにします。

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>サポートされていない OData 構文

-   算術式  

-   関数 (距離と交差の地理空間関数を除く)  

-   `any/all` と任意のラムダ式  

## <a name="see-also"></a>関連項目  

+ [Azure Search のファセット ナビゲーション](search-faceted-navigation.md) 
+ [Azure Search のフィルター](search-filters.md) 
+ [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene クエリ構文](query-lucene-syntax.md)
+ [Azure Search の単純なクエリ構文](query-simple-syntax.md)   
