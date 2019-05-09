---
title: データ形式 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API のデータ形式について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532169"
---
# <a name="data-format"></a>データ形式

データ ファイルには、インデックスを作成するオブジェクトの一覧が記述されます。
ファイル内の各行は、オブジェクトの属性値を UTF-8 エンコードされた [JSON 形式](https://json.org/)で指定します。
[スキーマ](SchemaFormat.md)で定義されている属性に加えて、各オブジェクトには、オブジェクト間での相対的な対数確率を指定する省略可能な "logprob" 属性もあります。
サービスがオブジェクトを確率の降順で返す場合、"logprob" を使用して、一致するオブジェクトの戻り値の順序を指定できます。
0 から 1 の間の確率 *p* を指定した場合、対応する対数確率は log(*p*) として計算できます。ここで、log() は自然対数関数です。
logprob の値が指定されていない場合は、既定値の 0 が使用されます。

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

文字列、GUID、BLOB の属性について、値は引用符で囲まれた JSON 文字列として表されます。  数値属性 (Int32、Int64、Double) の場合、値は JSON の数値として表されます。  複合属性の場合、値はサブ属性値を指定する JSON オブジェクトです。  インデックスを高速に構築するには、対数確率の降順でオブジェクトを事前に並べ替えます。

一般に、属性には 0 個以上の値があります。  属性に値がない場合は、単純にその属性を JSON から削除します。  属性に 2 つ以上の値がある場合は、JSON オブジェクト内の属性値のペアを繰り返すことができます。  または、複数の値を含む JSON 配列を属性に割り当てることができます。

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
