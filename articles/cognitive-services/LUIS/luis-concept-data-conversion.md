---
title: データ変換 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) の予測の前に、発話の変更方法について説明します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 42a9caff0433808734ee853cbad90a2088bf4e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95019247"
---
# <a name="convert-data-format-of-utterances"></a>発話のデータ形式を変換する
LUIS では、予測の前に、次のユーザーの発話の変換を提供します。

* [Cognitive Services の Speech](../Speech-Service/overview.md) サービスを使用した音声テキスト変換。

## <a name="speech-to-text"></a>音声テキスト変換

音声テキスト変換は、LUIS との統合として提供されます。

### <a name="intent-conversion-concepts"></a>意図変換の概念
LUIS の音声テキスト変換を使用すると、音声の発話をエンドポイントに送信し、LUIS の予測応答を受け取ることができます。 このプロセスは、[音声](/azure/cognitive-services/Speech)サービスと LUIS の統合です。 音声意図判定の詳細については、[チュートリアル](../speech-service/how-to-recognize-intents-from-speech-csharp.md)で確認してください。

### <a name="key-requirements"></a>主な要件
この統合のために **Bing Speech API** キーを作成する必要はありません。 この統合には、Azure portal で作成された **Language Understanding** キーが使用されます。 LUIS スターター キーは使用しないでください。

### <a name="pricing-tier"></a>価格レベル
この統合では、通常の Language Understanding 価格レベルとは別の[価格](luis-limits.md#key-limits)モデルを使用します。

### <a name="quota-usage"></a>クォータの使用量
詳細については、[主な制限](luis-limits.md#key-limits)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [データの抽出](luis-concept-data-extraction.md)