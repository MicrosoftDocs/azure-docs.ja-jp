---
title: BLEU スコアとは - Custom Translator
titleSuffix: Azure Cognitive Services
description: BLEU は、同じ原文の自動翻訳と 1 つまたは複数の人間が作成した参考翻訳との違いの測定単位です。 BLEU アルゴリズムでは、自動翻訳の連続するフレーズと、参考翻訳に含まれる連続するフレーズとが比較され、一致数がカウントされ、重み付けされた形式で表示されます。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: 8d249d29841466c40c0fd96df92cccecdce2dc62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760343"
---
# <a name="what-is-a-bleu-score"></a>BLEU スコアとは

[BLEU (Bilingual Evaluation Understudy)](https://en.wikipedia.org/wiki/BLEU) は、同じ原文の自動翻訳と 1 つまたは複数の人間が作成した参考翻訳との違いの測定単位です。

## <a name="scoring-process"></a>スコアリング プロセス

BLEU アルゴリズムでは、自動翻訳の連続するフレーズと、参考翻訳に含まれる連続するフレーズとが比較され、一致数がカウントされ、重み付けされた形式で表示されます。 これらの一致は、位置とは独立しています。 一致度が高くなるほど、参考翻訳との類似度が高くなり、スコアも高くなります。 明瞭さと文法上の正確さは考慮されていません。

## <a name="how-bleu-works"></a>BLEU のしくみ

BLEU の長所は、すべての文に対して正確な人間の判断を下そうと試みるのではなく、テスト コーパス上の個々の文の誤判断を平均化することによって、人間の判断との相関性が高くなることです。

BLEU スコアの詳細なディスカッションについては、[こちら](https://youtu.be/-UqDljMymMg)を参照してください。

BLEU の結果は、ドメインの広さ、テスト データとトレーニングおよびチューニング データとの一貫性、トレーニングに使用できるデータの量によって大きく左右されます。 狭いドメインに対してモデルがトレーニングされ、トレーニング データとテスト データとの一貫性がある場合は、BLEU スコアが高くなることが予想されます。

>[!NOTE]
>BLEU スコア間の比較は、BLEU 結果が同じテスト セット、同じ言語ペア、および同じ MT エンジンと比較された場合にのみ、正当と認められます。 異なるテスト セットの BLEU スコアは、別と見なす必要があります。
