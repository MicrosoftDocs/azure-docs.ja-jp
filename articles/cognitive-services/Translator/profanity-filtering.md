---
title: 不適切な表現のフィルター - Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Translator で不適切な表現のフィルターを使用して、翻訳テキストの不適切な表現のレベルを判断します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896801"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Translator で不適切な表現のフィルターを追加する

通常、Translator サービスでは、ソースに存在する不適切な表現は翻訳でも保たれます。 不適切な表現の程度と、単語を不適切にしているコンテキストは、文化により異なります。 その結果、対象言語における不適切な表現の程度は、増幅されたり低減されることがあります。

翻訳で不適切な表現が表示されるのを防ぐには、ソース テキストに不適切な表現が存在する場合でも、Translate() メソッドで使用可能な不適切な表現のフィルター処理オプションを使用します。 このオプションを使用すると、不適切な表現が削除されているか、適切なタグでマークされているかどうかを確認する、あるいはアクションを起こさないかを選択できます。

Translate() メソッドは "options" パラメーターを取り、ここには "ProfanityAction" の新しい要素が含まれます。 ProfanityAction に指定できる値は、"NoAction"、"Marked"、および "Deleted" です。

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction に指定できる値と例
|ProfanityAction 値 | アクション | 例:ソース - 日本語 | 例:ターゲット - 英語|
| :---|:---|:---|:---|
| NoAction | 既定値。 オプションを設定しない場合と同じです。 不適切な表現はソースからターゲットに渡されます。 | 彼は変態です。 | He is a jerk. |
| Marked | 不適切な表現は XML タグ \<profanity> ... \</profanity> で囲まれます。 | 彼は変態です。 | He is a \<profanity>jerk\</profanity>. |
| Deleted | 不適切な単語は、置換されずに出力から削除されます。 | 彼は。 | He is a. |

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Translator 呼び出しで不適切な表現のフィルター処理を適用する](reference/v3-0-translate.md)
