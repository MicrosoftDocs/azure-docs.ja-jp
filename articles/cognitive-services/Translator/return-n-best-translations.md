---
title: N-Best の翻訳結果を返す - Translator Text API
titlesuffix: Azure Cognitive Services
description: Microsoft Translator Text API を使用して N-Best の翻訳結果を返します。
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 62992122dc16003078bb80ecd87c2bd3692586dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918316"
---
# <a name="how-to-return-n-best-translations"></a>N-Best の翻訳結果を返す方法

> [!NOTE]
> このメソッドは非推奨です。 これは Translator Text API の V3.0 では使用できません。

Microsoft Translator API の GetTranslations() メソッドと GetTranslationsArray() メソッドには、オプションのブール型フラグ "IncludeMultipleMTAlternatives" が含まれます。
トランスレーター エンジンの N-Best 一覧からデルタが提供されるメソッドでは、最大で maxTranslations の候補数まで値が返されます。

シグネチャは次のとおりです。

**構文**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**パラメーター**

| パラメーター | 説明 |
|:---|:---|
| appId | **必須**: Authorization ヘッダーを使用する場合は、appid フィールドを空白のままにするか、"Bearer" + " " + アクセス トークンを含む文字列を指定します。|
| text | **必須**: 翻訳するテキストを表す文字列。 テキストのサイズは、10,000 文字を超えてはいけません。|
| from | **必須**: 翻訳するテキストの言語コードを表す文字列。 |
| to | **必須**: テキストを翻訳するターゲットの言語コードを表す文字列。 |
| maxTranslations | **必須**: 返す翻訳結果の最大数を表す整数。 |
| options | **省略可能**: 以下に示す値を格納している TranslateOptions オブジェクト。 これらはすべて省略可能で、最も一般的な設定が既定値です。

* カテゴリ:唯一サポートされていて、既定値となっているオプションは "general" です。
* ContentType:唯一サポートされていて、既定値となっているオプションは "text/plain" です。
* 状態:要求と応答を関連付けるのに役立つユーザー状態。 応答では同じ内容が返されます。
* IncludeMultipleMTAlternatives: MT エンジンから複数の候補訳を返すかどうかを決定するフラグ。 既定値は false で、翻訳候補が 1 つだけ含まれます。

## <a name="ratings"></a>Ratings
評価は、次のように適用されます。最善の自動翻訳は評価 5 になります。
自動的に生成された (N-Best) 翻訳候補の評価は 0 になり、一致度は 100 になります。

## <a name="number-of-alternatives"></a>翻訳候補の数
返される翻訳候補の数は最大で maxTranslations までですが、それより少ない場合もあります。

## <a name="language-pairs"></a>言語ペア
この機能は、簡体字中国語と繁体字中国語の間では、翻訳方向を問わず使用できません。 Microsoft Translator がサポートしている他のすべての言語ペアで使用できます。
