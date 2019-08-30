---
title: 例:多層構造ファセット - Azure Search
description: 複数レベルの分類法を表すファセット構造を構築し、アプリケーション ページに含めることができる、入れ子になったナビゲーション構造を作成する方法について説明します。
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: chstone
ms.openlocfilehash: 5a6fda0157f0f3a4ca5861acd4bcbead7839e451
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649929"
---
# <a name="example-multi-level-facets-in-azure-search"></a>例:Azure Search の多層構造ファセット

Azure Search のスキーマでは、複数レベルの分類法によるカテゴリは明示的にサポートされていませんが、インデックス作成の前にコンテンツを操作し、その後、結果にいくつかの特殊な処理を適用することで、それに近づけることができます。 

## <a name="start-with-the-data"></a>データから開始する

この記事の例は、[AdventureWorks の在庫データベースのモデル化](search-example-adventureworks-modeling.md)に関する前の例に基づいていて、Azure Search の多層構造ファセットの実例が示されます。

AdventureWorks には、親子関係のある単純な 2 レベルの分類があります。 この構造の固定長の分類の深さであれば、単純な SQL 結合クエリを使用して、分類をグループ化できます。

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![クエリの結果](./media/search-example-adventureworks/prod-query-results.png "クエリの結果")

## <a name="indexing-to-a-collection-field"></a>コレクション フィールドへのインデックス付け

この構造体を含むインデックス内に、このデータを格納するための Azure Search スキーマの **Collection(Edm.String)** フィールドを作成し、フィールドの属性に、検索可能、フィルター可能、ファセット可能、取得可能が含まれるようにします。

ここで、特定の分類カテゴリを参照するコンテンツにインデックスを付けるときに、分類の各レベルから取得したテキストを含む配列として、分類を送信します。 たとえば、`ProductCategoryId = 5 (Mountain Bikes)` であるエンティティの場合、フィールドを `[ "Bikes", "Bikes|Mountain Bikes"]` として送信します

子カテゴリの値 "Mountain Bikes" には、親カテゴリの "Bikes" が含まれることに注目してください。 各サブカテゴリには、ルート要素を基準としたパス全体を埋め込む必要があります。 パイプ文字の区切り記号は、任意に使用できますが、一貫している必要があり、ソース テキストに出現してはいけません。 区切り記号の文字は、ファセットの結果から分類ツリーを再構築するために、アプリケーション コードで使用されます。

## <a name="construct-the-query"></a>クエリを作成する

クエリの発行時には、次のファセット仕様を含めます (taxonomy は実際のファセット可能な分類フィールドです): `facet = taxonomy,count:50,sort:value`

count は、考えられる分類の値をすべて返すのに十分大きい値にする必要があります。 AdventureWorks のデータには別個の分類値が 41 個含まれているので、`count:50` で十分です。

  ![ファセットが適用されたフィルター](./media/search-example-adventureworks/facet-filter.png "ファセットが適用されたフィルター")

## <a name="build-the-structure-in-client-code"></a>クライアント コードで構造を作成する

クライアント アプリケーション コードでは、パイプ文字で各ファセット値を分割することで、分類ツリーを再構築します。

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

これで、**categories** オブジェクトを使用して、正確な数の折りたたみ可能な分類ツリーを表示できるようになりました。

  ![多層構造ファセットのフィルター](./media/search-example-adventureworks/multi-level-facet.png "多層構造ファセットのフィルター")

 
ツリー内の各リンクでは、関連するフィルターが適用されるはずです。 例:

+ **taxonomy/any**`(x:x eq 'Accessories')` では、Accessories 分岐内のすべてのドキュメントが返されます
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` では、Accessories 分岐の下にある Bike Racks サブカテゴリのドキュメントのみが返されます。

この手法は、深い分類ツリーや、異なる親カテゴリの下に他とまったく同じサブカテゴリが現れる (たとえば `Bike Components|Forks` と `Camping Equipment|Forks`) ような、より複雑なシナリオに対応するためにスケールします。

> [!TIP]
> クエリの速度は、返されるファセットの数に影響されます。 非常に大きい分類セットをサポートする場合は、ファセット可能な、各ドキュメントの最上位の分類値を保持する **Edm.String** フィールドを追加することを検討してください。 その後は上と同じ手法を適用しますが、ユーザーが最上位ノードを拡張するときには、(ルートの分類フィールドに対するフィルター処理が行われる) collection-facet クエリのみを実行します。 または、100% の呼び戻しが必要でない場合は、ファセットの数を妥当な数まで減らし、ファセットのエントリが数で並べ替えられるようにします。

## <a name="see-also"></a>関連項目

[例:AdventureWorks の在庫データベースを Azure Search 用にモデル化する](search-example-adventureworks-modeling.md)