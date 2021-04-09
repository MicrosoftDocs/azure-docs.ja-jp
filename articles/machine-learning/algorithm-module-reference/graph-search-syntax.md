---
title: グラフ検索のクエリ構文
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーの検索クエリ構文を使用して、パイプライン グラフ内のノードを検索する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420769"
---
# <a name="graph-search-query-syntax"></a>グラフ検索のクエリ構文

この記事では、Azure Machine Learning でのグラフ検索のクエリ構文について説明します。 グラフ検索機能を使用すると、ノードを名前とプロパティで検索できます。 

 ![グラフ検索エクスペリエンスの例を示すアニメーション化されたスクリーンショット](media/search/graph-search.gif)

グラフ検索では、ノード名とコメントに対するフルテキストのキーワード検索がサポートされています。 runStatus、duration、computeTarget などのノード プロパティでフィルター処理を行うこともできます。 キーワード検索は Lucene クエリに基づいています。 完全な検索クエリは次のようになります。  

**[Lucene クエリ |[フィルター クエリ]** 

Lucene クエリとフィルター クエリのどちらも使用できます。 両方を使用するには、区切り記号 **|** を使用します。 フィルター クエリの構文は、Lucene クエリよりも厳密です。 したがって、顧客の入力が両方に解析できる場合、フィルター クエリが適用されます。

 

## <a name="lucene-query"></a>Lucene クエリ

グラフ検索では、ノード "name" と "comment" に対して Lucene の単純なクエリをフルテキスト検索構文として使用します。 次の Lucene 演算子がサポートされています。

 
- AND/OR
- **?** および **\*** 演算子を使用したワイルドカードでの照合。

### <a name="examples"></a>例

- 単純な検索: `JSON Data`

- AND/OR: `JSON AND Validation`

- 複数の AND/OR: `(JSON AND Validation) OR (TSV AND Training)`

 
- ワイルドカードでの照合: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Lucene クエリを "*" 文字で開始することはできません。

##  <a name="filter-query"></a>フィルター クエリ

 
フィルター クエリでは次のパターンを使用します。
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
次のノード プロパティをキーとして使用できます。

- runStatus
- compute
- duration
- reuse

使用できる演算子は次のとおりです。

- 以上: `>=`
- 以下: `<=`
- より大きい: `>`
- より小さい: `<`
- 等しい: `==`
- 次を含む: `=`
- 等号否定: `!=`
- In: `in`

 
 

### <a name="example"></a>例

- duration > 100;
- status in { Failed,NotStarted}
- compute in {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>テクニカル ノート

- 複数のフィルター間の関係は "AND" です
- `>=,  >,  <, or  <=` を選択した場合、値は自動的に数値型に変換されます。 それ以外の場合は、文字列型が比較に使用されます。
- すべての文字列型の値について、比較では大文字と小文字は区別されません。
- 演算子 "In" は、値としてコレクションを想定しており、コレクション構文は `{name1, name2, name3}` です
- キーワード間のスペースは無視されます