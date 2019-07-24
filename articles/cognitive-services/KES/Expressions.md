---
title: 構造化クエリ式 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API で構造化クエリ式を使用する方法について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860136"
---
# <a name="structured-query-expression"></a>構造化クエリ式

構造化クエリ式で、データ インデックスに対して評価する一連の演算を指定します。  この式は、属性クエリ式と上位レベルの関数で構成されます。  [*evaluate*](evaluateMethod.md) を評価するメソッドを使用して、式と一致するオブジェクトを計算します。  以下は、2013 年以降に Jaime Teevan が著した出版物を返す学術出版ドメインの例です。

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

構造化クエリ式は、[*interpret*](interpretMethod.md) 要求から取得できます。各解釈のセマンティック出力は、入力の自然言語クエリに一致するインデックス オブジェクトを返す構造化クエリ式です。  または、このセクションで説明する構文を使用して手動で作成することもできます。

## <a name="attribute-query-expression"></a>属性クエリ式

属性クエリ式は、特定の属性との照合に基づいてオブジェクトのセットを識別します。  [スキーマ](SchemaFormat.md)で指定された属性の種類およびインデックス付けされた操作に応じて、異なる照合操作がサポートされます。

| type | Operation | 例 |
|------|-------------|------------|
| String | equals | Title='latent semantic analysis'  (正規 + シノニム) |
| String | equals | Author.Name=='susan t dumais'  (正規のみ)|
| String | starts_with | Title='latent s'... |
| Int32/Int64/Double | equals | Year=2000 |
| Int32/Int64/Double | starts_with | Year='20'... (any decimal value starting with "20") |
| Int32/Int64/Double | is_between | Year&lt;2000 <br/> Year&lt;=2000 <br/> Year&gt;2000 <br/> Year&gt;=2000 <br/> Year=[2010,2012) *(左の境界値のみを含む: 2010、2011)* <br/> Year=[2000,2012] *(両方の境界値を含む: 2010、2011、2012)* |
| Date | equals | BirthDate='1984-05-14' |
| Date | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


たとえば、"Title='latent s'..." はタイトルが文字列 "latent s" で始まるすべての学術出版物に一致します。  この式を評価するために、属性 Title で、インデックスの構築に使用されるスキーマの "starts_with" 演算を指定する必要があります。

関連するシノニムを持つ属性の場合、クエリ式で、"==" 演算子を使用して指定された文字列に一致する正規値を持つオブジェクト、またはその正規値/シノニム値のいずれかが "=" 演算子を使用して一致しているオブジェクトを指定することができます。  どちらの場合も、属性定義で equals 演算子を指定する必要があります。


## <a name="functions"></a>Functions

基本的な属性クエリから、より洗練されたクエリ式を構築することができる組み込み関数が用意されています。

### <a name="and-function"></a>And 関数

`And(expr1, expr2)`

2 つの入力クエリ式の共通部分を返します。

次の例は、2000 年に発行された、情報取得に関する学術的刊行物を返します。

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Or 関数

`Or(expr1, expr2)`

2 つの入力クエリ式の和集合を返します。

次の例は、2000 年に発行された、情報取得またはユーザー モデリングに関する学術出版物を返します。

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Composite 関数

`Composite(expr)`

共通複合属性のサブ属性に対するクエリで構成される内部式をカプセル化する式を返します。  カプセル化では、一致するデータ オブジェクトの複合属性に、内側の式を個別に満たす値が少なくとも 1 つ存在する必要があります。  複合属性のサブ属性のクエリ式は、他のクエリ式と組み合わせる前に Composite() 関数を使用してカプセル化する必要があります。

たとえば、次の式は、"harry shum" が "microsoft" に在籍時に著した学術的刊行物を返します。

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

一方、次の式は、著者の 1 人が "harry shum" であり、所属のいずれかが "microsoft" である学術的刊行物を返します。

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
