---
title: 構成素解析 - Linguistic Analysis API
titlesuffix: Azure Cognitive Services
description: 構成素解析 ("語句構文解析" とも呼ばれます) で、テキスト内の語句を特定する方法について説明します。
services: cognitive-services
author: richardsunms
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 7611f5f16111b5d8b0d2d293750f658125e50837
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60535452"
---
# <a name="constituency-parsing"></a>構成素解析

> [!IMPORTANT]
> Linguistic Analysis プレビューは、2018 年 8 月 9 日に使用停止になりました。 テキスト処理と分析には、[Azure Machine Learning テキスト解析モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)の使用をお勧めします。

構成素解析 ("語句構文解析" とも呼ばれます) の目的は、テキスト内の語句を特定することです。
これはテキストから情報を抽出するときに便利です。
たとえば、大量のテキストから機能名や主要な語句を見つけて、そのような各語句を囲む修飾語や動作を確認することができます。

## <a name="phrases"></a>語句

言語学者にとって、*語句*とは単なる一連の単語ではありません。
語句であるためには、単語のグループが、まとまって文の中で特定の役割を果たす必要があります。
単語のグループは一緒に移動したり、全体として置き換えたりすることもできますが、文は流ちょうで正しい文法のままである必要があります。

次の文を考えてみましょう

> I want to find a new hybrid automobile with Bluetooth. (私は Bluetooth が搭載された新しいハイブリッド自動車を探したい。)

この文には、名詞句 "a new hybrid automobile with Bluetooth" (Bluetooth が搭載された新しいハイブリッド自動車) が含まれています。
これが語句であることは、どうすればわかるでしょうか。
この文は、語句全体を最初に移動して書き換えることができます (やや詩的になります)。

> A new hybrid automobile with Bluetooth I want to find. (私は探したい Bluetooth が搭載された新しいハイブリッド自動車を。)

または、この語句を、似た機能と意味を持つ別の語句で置き換えることができます。たとえば、"素晴らしい新車" などです。

> I want to find a fancy new car. (私は素晴らしい新車を探したい。)

代わりに、異なるサブセットの単語を選択した場合、このような置換タスクの結果、奇妙な、または判読不能な文章になります。
"find a new" (新しい見つける) を先頭に移動するとどうなるでしょうか。

> Find a new I want to hybrid automobile with Bluetooth. (Bluetooth が搭載されたハイブリッド自動車に必要な新しいものを見つけよう。)

結果は、読んで理解することが非常に困難になります。

パーサーの目的は、そのような語句をすべて見つけることです。
興味深いことに、自然言語では、語句は相互の内側に入れ子にされる傾向があります。
このような語句の自然な表現は、次のようなツリーです。

![ツリー](./Images/tree.png)

このツリーで、"NP" と記載されている枝は名詞句 (noun phrase) です。
いくつかの語句があります。たとえば、*I* (私)、*a new hybrid automobile* (新しいハイブリッド自動車)、*Bluetooth*、*a new hybrid automobile with Bluetooth* (Bluetooth が搭載された新しいハイブリッド自動車) です。

## <a name="phrase-types"></a>語句の種類

| Label | 説明 | 例 |
|-------|-------------|---------|
|ADJP   | 形容詞句 | "so rude" (あまりにも乱暴な) |
|ADVP   | 副詞句 | "clear through" (明確に) |
|CONJP  | 結合語句 | "as well as" (だけでなく) |
|FRAG   | 不完全なまたは断片的な入力に使用されるフラグメント | "Highly recommended..." (とてもお勧めの...) |
|INTJ   | 間投詞 | "Hooray" (フレー) |
|LST    | 句読点を含むリスト マーカー | "#4)" |
|NAC    | Not A Constituent (構成素ではない)。構成素ではない語句の範囲を示すために使用されます。 |  "you get things and for a good deal" (物事を入手し、そして良い取引のために) に含まれる "and for a good deal" (そして良い取引のために) |
|NP | 名詞句 | "a tasty potato pancake" (おいしいポテト パンケーキ) |
|NX | 特定の複雑な NP 内で先頭をマークするために使用されます| |
|PP | 前置詞句| "in the pool" (プール内で) |
|PRN    | かっこ内| "(so called)" (いわゆる) |
|PRT    | 不変化詞| "ripped out" (破り取る) の "out" |
|QP | 名詞句内の数量句 (つまり、複雑な尺度/金額)| "around $75" (約 $75) |
|RRC    | 縮約関係詞節| "many issues still unresolved" (まだ解決されていない多くの問題) の "still unresolved" (まだ解決されていない) |
|S  | 文または節。 | "This is a sentence." (これは文です。)
|SBAR   | 従属節 (多くの場合、従属接続詞によって導入されます) | "I looked around as I left." (出るときに周りを見た) の "as I left" (出るときに)|
|SBARQ  | wh から始まる単語または語句によって導入される直接疑問 | "What was the point?" (要点は何ですか。) |
|SINV   | 反転平叙文 | "At no time were they aware." (すぐだった、彼らが気づいたのは。) (通常の主語 "they" が動詞 "were" の後に移動している点に注目してください) |
|SQ | 逆のはい/いいえの疑問、または wh から始まる疑問の主節 | "Did they get the car?" (彼らは車を手に入れましたか。) |
|UCP    | 異なる調整された語句| "small and with bugs" (小さくてバグがある) (形容詞句と前置詞句が "and" で結合されている点に注目してください)|
|VP | 動詞句 | "ran into the woods" (森に駆け込む) |
|WHADJP | Wh で始まる形容詞句 | "how uncomfortable" (どのように不快か) |
|WHADVP | Wh で始まる副詞句| "when" (いつ) |
|WHNP   | Wh で始まる名詞句| "which potato" (どのポテト)、"how much soup" (どのくらいのスープ)|
|WHPP   | Wh で始まる前置詞句| "in which country" (どの国で)|
|X  | 不明、不確実、または解体不能| "the... the soup" (その...そのスープ) の最初の "the" |


## <a name="specification"></a>仕様

このドキュメントのツリーは、[Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) の S 式を使用しています。
