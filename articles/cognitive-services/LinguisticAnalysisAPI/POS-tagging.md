---
title: 品詞のタグ付け - Linguistic Analysis API
description: Linguistic Analysis API の品詞タグ付け機能でテキストの各単語のカテゴリまたは品詞を識別する方法について説明します。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 0269397b0f8da66d2bafecfb427ba705fdfff001
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883153"
---
# <a name="part-of-speech-tagging"></a>品詞のタグ付け

> [!IMPORTANT]
> Linguistic Analysis プレビューは、2018 年 8 月 9 日に使用停止になりました。 テキスト処理と分析には、[Azure Machine Learning テキスト解析モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)の使用をお勧めします。

## <a name="background-and-motivation"></a>背景と動機

テキストが文とトークンに分けられたら、次の分析手順は各単語のカテゴリまたは品詞を識別することです。
たとえば、*名詞* (一般的に、人、場所、物、アイデアなどを表す) や *動詞* (一般的に、アクション、状態の変化などを表す) などのカテゴリがあります。一部の単語の品詞は明白です (たとえば、*quagmire* (湿地) は単なる名詞です) が、他の多くの単語の品詞は判別が困難です。
*table* (テーブル) は座る場所 (または数値の 2 次元レイアウト) の場合もあれば、"table a discussion" (議論を棚上げする) にも使用されます。

## <a name="list-of-part-of-speech-tags"></a>品詞タグのリスト

| タグ | 説明 | 単語の例 |
|-----|-------------|---------------|
| $ | ドル | $ |
| \`\` | 開始の引用符 | \` \`\` |
| '' | 終了の引用符 | ' '' |
| ( | 開始のかっこ | ( [ { |
| ) | 終了のかっこ | ) ] } |
| , | コンマ | , |
| -- | ダッシュ | -- |
| 。 | 文の終端記号 | 。 ! ? |
| : | コロンまたは省略記号 | : ; ... |
| CC | 接続詞、等位接続詞 | and but or yet|
| CD | 数値、基数 | nine 20 1980 '96 |
| DT | 限定詞 |a the an all both neither|
| EX | 存在の there | there |
| FW | 外来語 | enfant terrible hoi polloi je ne sais quoi |
| IN | 前置詞または後置詞| in inside if upon whether |
| JJ | 形容詞、数値、序数 | ninth pretty execrable multimodal |
| JJR | 形容詞、比較級 | better faster cheaper |
| JJS | 形容詞、最上級 | best fastest cheapest |
| LS | リスト アイテム マーカー | (a) (b) 1 2 A B A. B. |
| MD | 法助動詞 | can may shall will could might should ought |
| NN | 名詞、一般名詞、単数形、質量名詞 | potato money shoe |
| NNP | 名詞、固有名詞、単数形 | Kennedy Roosevelt Chicago Weehauken |
| NNPS | 名詞、固有名詞、複数形 | Springfields Bushes |
| NNS | 名詞、一般名詞、複数形 | pieces mice fields |
| PDT | 前決定詞 | all both half many quite such sure this |
| POS | 属格マーカー | ' 's |
| PRP | 代名詞、個人 | she he it I we they you |
| PRP$ | 代名詞、所有格 | hers his its my our their your |
| RB | 副詞 | clinically only |
| RBR | 副詞、比較級 | further gloomier grander graver greater grimmer harder harsher healthier heavier higher however larger later leaner lengthier less-perfectly lesser lonelier longer louder lower more ... |
| RBS | 副詞、最上級 | best biggest bluntest earliest farthest first furthest hardest heartiest highest largest least less most nearest second tightest worst |
| RP | 不変化詞 | on off up out about |
| SYM | 記号 | % & |
| TO | 前置詞または不定詞マーカーとしての "to" | to |
| UH | 間投詞 | uh hooray howdy hello |
| VB | 動詞、基本型 | give assign fly |
| VBD | 動詞、過去形 | gave assigned flew |
| VBG | 動詞、現在分詞、動名詞 | giving assigning flying |
| VBN | 動詞、過去分詞 | given assigned flown |
| VBP | 動詞、現在形、三単現ではない | give assign fly |
| VBZ | 動詞、現在形、三単現 | gives assigns flies |
| WDT | WH 限定詞 | that what which |
| WP | WH 代名詞 | who whom |
| WP$ | WH 代名詞、所有格 | whose |
| WRB | Wh 副詞 | how however whenever where |

## <a name="specification"></a>仕様

トークン化については、[Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) の仕様を利用しています。
