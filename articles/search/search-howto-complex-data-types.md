---
title: 複合データ型をモデル化する方法 - Azure Search
description: 階層または入れ子になったデータ構造は、フラット化された行セットとコレクション データ型を使用して、Azure Search インデックスでモデル化できます。
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311985"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Azure Search で複合データ型をモデル化する方法
Azure Search インデックスの設定に使用する外部データセットの中には、下部構造が階層または入れ子となっているために、表形式の行セットに適切に分解できないものが存在します。 このような構造の例として、単一の顧客に複数の住所と電話番号が含まれるケース、単一の SKU に複数の色とサイズが含まれるケース、1 冊の書籍に複数の著者が存在するケースなどが挙げられます。 モデル化の際に使う用語では、このような構造を "*複合データ型*"、"*コンパウンド データ型*"、"*コンポジット データ型*"、"*集合体データ型*" などの用語で呼ぶことがあります。

複合データ型は Azure Search でネイティブにサポートされているわけではありませんが、実証済みの回避策では、データ構造をフラット化し、次に **コレクション** データ型を使用するという 2 段階のプロセスを用いることで、データの内部構造を再構築できます。 この記事で説明されている手法により、コンテンツの検索、ファセット化、フィルター処理、並べ替えが可能になります。

## <a name="example-of-a-complex-data-structure"></a>複合データ構造の例
通常、対象となるデータは、一連の JSON ドキュメントまたは XML ドキュメント、または Azure Cosmos DB のような NoSQL ストア内のアイテムとして存在します。 構造的に、このような問題は、検索やフィルター処理を行う必要のある子アイテムが複数存在することに起因しています。  回避策を説明する出発点として、次の JSON ドキュメントをご覧ください。このドキュメントは、例として一連の連絡先の一覧を示しています。

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

"id"、"name"、"company" というフィールドは、Azure Search インデックス内のフィールドとして一対一の関係で簡単にマップできますが、"locations" フィールドには場所の配列が含まれており、その中には場所の ID と説明の両方が含まれています。 Azure Search には、これをサポートするデータ型が存在しないため、Azure Search でこれをモデル化するには別の方法が必要になります。 

> [!NOTE]
> この手法は、Kirk Evans によるブログの投稿「[Indexing Azure Cosmos DB with Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/)」(Azure Search で Azure Cosmos DB をインデックス化する方法) でも説明されています。この手法は "データのフラット化" と呼ばれ、`locationsID` および `locationsDescription` と呼ばれるフィールドを使用します。これらのフィールドは両方とも[コレクション](https://msdn.microsoft.com/library/azure/dn798938.aspx) (または文字列の配列) です。   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>パート 1:個々のフィールドに配列をフラット化する
このデータセットを格納する Azure Search インデックスを作成するには、入れ子になった下部構造のそれぞれに対応するフィールドを作成します。ここでの下部構造とは、[コレクション](https://msdn.microsoft.com/library/azure/dn798938.aspx) データ型 (または文字列の配列) である `locationsID` と `locationsDescription` を指します。 これらのフィールドでは、John Smith の `locationsID` フィールドに値 "1" と "2" のインデックスを作成し、Jen Campbell の `locationsID` フィールドに値 "3" と "4" のインデックスを作成します。  

Azure Search では、データは次のように表示されます。 

![sample data, 2 rows](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>パート 2:インデックス定義にコレクションのフィールドを追加する
インデックス スキーマでは、フィールド定義は次の例のようになります。

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>検索動作を検証し、必要に応じてインデックスを拡張する
インデックスの作成とデータの読み込みが完了したら、ソリューションをテストして、データセットに対する検索クエリの実行を検証できるようになります。 各**コレクション** フィールドは、**検索**、**フィルター処理**、**ファセット化**が可能である必要があります。 次のようなクエリを実行できるようになります。

* "Adventureworks Headquarters" で働く人をすべて検索する。
* "Home Office" で働く人の数をカウントする。  
* "Home Office" で働く人のうち、他のオフィスでも働く人について、そのオフィス名を表示し、各オフィスで働く人数をカウントする。  

場所の ID と場所の説明の両方を組み合わせた検索を実行する必要がある場合、この手法ではうまくいきません。 例: 

* "Home Office" で働き、かつ場所の ID が "4" である人をすべて検索する。  

元の内容は次のようなものだったことを思い出してください。

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

ところが、既にデータを別々のフィールドに分割してしまったため、Jen Campbell の "Home Office" が `locationsID 3` に関連しているのか、`locationsID 4` に関連しているのかを知るすべはありません。  

このようなケースを処理するには、インデックスでもう 1 つのフィールドを定義し、そのフィールドですべてのデータを単一のコレクションに結合します。  この例では、このフィールドを `locationsCombined` と呼びます。ここでは、内容を区切るために `||` を使用しますが、内容に対して一意である文字の組み合わせであれば、どのような区切り記号でも選択できます。 例:  

![sample data, 2 rows with separator](./media/search-howto-complex-data-types/sample-data-2.png)

`locationsCombined` フィールドを使用すると、次の例のような、さらに多くのクエリに対応できるようになります。

* "Home Office" で働き、かつ場所の ID が "4" である人をカウントする。  
* "Home Office" で働き、かつ場所の ID が "4" である人を検索する。 

## <a name="limitations"></a>制限事項
この手法は多くのシナリオで役立ちますが、すべての場合に適用できるわけではありません。  例: 

1. 複合データ型に静的な一連のフィールドがなく、すべての指定可能な型を 1 つのフィールドにマップすることができない場合。 
2. 入れ子になったオブジェクトを更新する際に、Azure Search インデックスで何を更新する必要があるのかを正確に判断するための追加作業が必要になる場合。

## <a name="sample-code"></a>サンプル コード
Azure Search に複合 JSON データセットのインデックスを作成し、このデータセットに対してさまざまなクエリを実行する方法の例については、こちらの [GitHub リポジトリ](https://github.com/liamca/AzureSearchComplexTypes)を参照してください。

## <a name="next-step"></a>次のステップ
Azure Search UserVoice ページで、[複合データ型のネイティブ サポートに関する投票](https://feedback.azure.com/forums/263029-azure-search)を行ってください。機能の実装に関する要望がほかにもある場合は、入力をお願いします。 Twitter で @liamca 宛てに直接ご連絡いただいてもかまいません。

