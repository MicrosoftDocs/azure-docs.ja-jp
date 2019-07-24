---
title: クエリ式構文 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API のクエリ式の構文の使用方法を説明します｡
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872035"
---
# <a name="query-expression-syntax"></a>クエリ式の構文

**解釈**要求への応答にはクエリ式が含まれることを確認しました。 ユーザーのクエリを解釈した文法では、解釈ごとにクエリ式が作成されました。 クエリ式を使用して、**評価**要求を発行し、エンティティの検索結果を取得できます。

独自のクエリ式を作成し、それらを**評価**要求で使用することもできます。 これは、ユーザーのアクションへの応答でクエリ式を作成する独自のユーザー インターフェイスを構築する場合に役立ちます。 これを行うには、クエリ式の構文を知っている必要があります。  

クエリ式に含めることのできる各エンティティ属性には、特定のデータ型と、使用できるクエリ演算子のセットがあります。 エンティティ属性および各属性でサポートされている演算子のセットが、[エンティティ属性](EntityAttributes.md)で指定されます。 単一値クエリには、*Equals* 演算をサポートするための属性が必要です。 プレフィックス クエリには、*StartsWith* 演算をサポートするための属性が必要です。 数値範囲クエリには、*IsBetween* 演算をサポートするための属性が必要です。

エンティティ データの一部は、属性名のドット '.' で示される複合属性として格納されます。 たとえば、著者/所属情報は、複合属性として表されます。 これには 4 つのコンポーネントが含まれます。AuN、AuId、AfN、AfId です。 これらのコンポーネントは、単一のエンティティ属性値を形成するデータの個別の部分です。


**文字列属性:単一値** (シノニムとの一致を含む)  
Ti='indexing by latent semantic analysis'  
Composite(AA.AuN='sue dumais')

**文字列属性:厳密に 1 つの値** (基準の値のみと一致)  
Ti=='indexing by latent semantic analysis'  
Composite(AA.AuN=='susan t dumais')
     
**文字列属性:プレフィックス値**   
Ti='indexing by latent seman'...  
Composite(AA.AuN='sue du'...)

**数値属性:単一値**  
Y=2010
 
**数値属性:範囲値**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010、2012\) (左の境界値のみを含む:2010、2011)  
Y=\[2010, 2012\] (両方の境界値を含む:2010、2011、2012)
 
**数値属性:プレフィックス値**  
Y='19'... (19 で始まる任意の数値) 
 
**日付属性:単一値**  
D='2010-02-04'

**日付属性:範囲値**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**And/Or クエリ:**  
And(Y=1985, Ti='disordered electronic systems')  
Or(Ti='disordered electronic systems', Ti='fault tolerance principles and practice')  
And(Or(Y=1985,Y=2008), Ti='disordered electronic systems')
 
**複合クエリ:**  
複合属性のコンポーネントのクエリを実行するには、Composite() 関数内の複合属性を参照するクエリ式の部分を囲む必要があります。 

たとえば、著者名による論文のクエリを実行するには、次のクエリを使用します。
```
Composite(AA.AuN='mike smith')
```
<br>特定の著者が特定の機関に所属していたときのその著者による論文のクエリを実行するには、次のクエリを使用します。
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Composite() 関数は、複合属性の 2 つの部分を結び付けます。 つまり、著者の 1 人が Harvard に所属していた "Mike Smith" の場合にのみ論文を取得します。 

特定の機関の (他の) 著者と協力している特定の著者による論文のクエリを実行するには、次のクエリを使用します。
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>このバージョンでは、Composite() が And() より前に著者と所属に個別に適用されるので、著者の 1 人が "Mike Smith" で、著者の 1 人の所属が "Harvard" であるすべての論文を取得します。 これは、前のクエリ例と同じに思えますが、同じものではありません。

一般的な例として、次の例を考えてみましょう。A と B の 2 つのコンポーネントを持つ複合属性 C があります。エンティティには、C に対して複数の値がある場合があります。これらは次のエンティティです。
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>次のクエリは、 
```
Composite(And(C.A=1, C.B=2))
```

<br>コンポーネント C.A が 1 でコンポーネント C.B が 2 である C の値を持つエンティティのみと一致します。 E1 のみがこのクエリと一致します。

次のクエリは、 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>C.A が 1 の C の値を持つエンティティと C.B が 2 の C の値を持つエンティティとも一致します。 E1 と E2 の両方がこのクエリと一致します。

注意:  
- Composite() 関数の外側にある複合属性の部分を参照することはできません。
- 同じ Composite() 関数の内側にある 2 つの異なる複合属性の部分を参照することはできません。
- Composite() 関数の内側にある複合属性の部分ではない属性を参照することはできません。
