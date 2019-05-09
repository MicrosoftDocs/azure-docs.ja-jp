---
title: Analyzer の名前付け構造 - Linguistic Analysis API
titlesuffix: Azure Cognitive Services
description: Linguistic Analysis API Analyzer のアナライザーの名前付け構造によって､どのように柔軟性と精度の両方が実現されているかについて説明します。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: b2e956b099022751712f96ab0b15aa57c752b458
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873378"
---
# <a name="analyzer-names"></a>アナライザー名

> [!IMPORTANT]
> Linguistic Analysis プレビューは、2018 年 8 月 9 日に使用停止になりました。 テキスト処理と分析には、[Azure Machine Learning テキスト解析モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)の使用をお勧めします。

アナライザーでは、少し複雑な名前付け構造を使用して、柔軟性を実現します。また、名前の意味を正確に理解できるようにします。
アナライザー名は、ID、種類、仕様、実装の 4 つの部分で構成されます。
各部分の役割は以下のように定義されます。

## <a name="id"></a>ID
まずアナライザーには一意の ID、GUID があります。
このような GUID はほとんど変更されません。特定のアナライザーを一意に示す唯一の方法です。

## <a name="kind"></a>種類
次に、各アナライザーには**種類**があります。
これは、かなり大まかに、返される分析のタイプを定義します。その分析を表すために使用されるデータ構造を一意に定義する必要があります。
現在、3 つの種類があります
 - [トークン](Sentences-and-Tokens.md)
 - [POS タグ](Pos-Tagging.md)
 - [支援ツリー](constituency-parsing.md)

## <a name="specification"></a>仕様
ただし、特定の種類内で、特定の現象を分析する方法について専門家の意見が一致しないこともあります。
プログラミング言語とは異なり、この方法に関する明確で正確な定義はありません。

たとえば、"He didn't go" という英文でトークンを探していたとします。
特に、"didn't" という文字列について考えます。
1 つの可能な解釈は、これを "did" と "not" という 2 つのトークンに分割すべきということです。
このとき、代わりの文 "He did not go" には同じトークンのセットが含まれます。
もう 1 つの可能性は、"did" と "n't" というトークンに分割すべきということです。
後者のトークンは通常は単語と見なされません。ただし、このアプローチは表面的な文字列に関してより多くの情報を保持できるため、場合によっては役立つことがあります。
つまり、この短縮形を 1 語と見なす必要があるということです。

どちらを選択するにせよ、一貫して選択する必要があります。
これは、まさに、正しい表現が何であるかを定義する**仕様**の役割です。

アナライザーの出力を適正に比較することができるのは、同じ仕様に準拠するデータのみです。

## <a name="implementation"></a>実装

多くの場合、同じ結果を達成しようとする複数のモデルがありますが、パフォーマンスの特性はさまざまです。
あるモデルは処理が速いが正確さに欠け、別のモデルには別のトレードオフがある可能性があります。

アナライザー名の**実装**の部分は、ユーザーがニーズに最も適したアナライザーを選択できるように、この種類の情報を示すために使用されます。
