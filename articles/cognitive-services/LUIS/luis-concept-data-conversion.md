---
title: データ変換
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) の予測の前に、発話の変更方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdf2d87e558726937fa4221a0d95179216c66051
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859202"
---
# <a name="convert-data-format-of-utterances"></a>発話のデータ形式を変換する
LUIS では、Cognitive Services の音声サービスを使用して、予測の前に音声の発話からテキストの発話に発話を変換します。 

## <a name="speech-to-intent-conversion-concepts"></a>音声から意図への変換の概念
LUIS の音声テキスト変換を使用すると、音声の発話をエンドポイントに送信し、LUIS の予測応答を受け取ることができます。 このプロセスは、[音声](https://docs.microsoft.com/azure/cognitive-services/Speech)サービスと LUIS の統合です。 音声意図判定の詳細については、[チュートリアル](../speech-service/how-to-recognize-intents-from-speech-csharp.md)で確認してください。

### <a name="key-requirements"></a>主な要件
この統合のために **Bing Speech API** キーを作成する必要はありません。 この統合には、Azure portal で作成された **Language Understanding** キーが使用されます。 LUIS スターター キーは使用しないでください。

### <a name="pricing-tier"></a>価格レベル
この統合では、通常の Language Understanding 価格レベルとは別の[価格](luis-boundaries.md#key-limits)モデルを使用します。 

### <a name="quota-usage"></a>クォータの使用量
詳細については、[主な制限](luis-boundaries.md#key-limits)に関するページを参照してください。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声テキスト変換の使用](luis-tutorial-speech-to-intent.md)

