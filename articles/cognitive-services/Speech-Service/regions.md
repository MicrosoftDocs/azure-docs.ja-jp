---
title: リージョン - Speech サービス
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換、テキスト読み上げ、音声翻訳など、Speech サービスで利用可能なリージョンとエンドポイントのリスト。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786793"
---
# <a name="speech-service-supported-regions"></a>Speech サービスがサポートされているリージョン

Speech Service を使用すると、アプリケーションで音声をテキストに変換し、音声翻訳を実行し、テキストを音声に変換することができます。 このサービスは、Speech SDK および REST API に固有のエンドポイントを使用して複数のリージョンで利用できます。

すべてのリージョンの音声エクスペリエンスにカスタム構成を実行するための Speech ポータルについては、 https://speech.microsoft.com を参照してください

リージョンを検討するとき、次のポイントを念頭に置きます。

* アプリケーションで [Speech SDK](speech-sdk.md) を使用する場合、Speech の構成を作成するときに `westus` などのリージョン ID を指定します。
* アプリケーションで Speech Service の [REST API](./overview.md#reference-docs) のいずれかを使用する場合、要求を行うときに使用するエンドポイント URI にリージョンが含まれます。
* あるリージョン用に作成されたキーはそのリージョンでのみ有効です。 別のリージョンで使用すると認証エラーが発生します。

## <a name="speech-sdk"></a>Speech SDK

[Speech SDK](speech-sdk.md) では、リージョンは文字列として (たとえば、C# 用の Speech SDK では `SpeechConfig.FromSubscription` へのパラメーターとして) 指定されます。

### <a name="speech-to-text-text-to-speech-and-translation"></a>音声変換、テキスト読み上げ、翻訳

音声カスタマイズ ポータルについては、 https://speech.microsoft.com を参照してください

Speech サービスは、以下のリージョンで **音声認識**、**テキスト読み上げ**、**翻訳** に利用できます。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

[Speech SDK](speech-sdk.md) を使用する場合、リージョンは **リージョン識別子** によって (たとえば、`SpeechConfig.FromSubscription` へのパラメーターとして) 指定されます。 リージョンがサブスクリプションのリージョンと一致していることを確認してください。

オーディオ データを使用してカスタム モデルをトレーニングする場合は、トレーニングを高速化するために[専用のハードウェアを備えたリージョン](custom-speech-overview.md#set-up-your-azure-account)のいずれかを使用します。 [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) を使用して、完全にトレーニングされたモデルを後から別のリージョンにコピーできます。

### <a name="intent-recognition"></a>意図認識

Speech SDK を介して **意図認識** を使用できるリージョンは以下の通りです。

| グローバル リージョン | リージョン           | リージョン識別子 |
| ------------- | ---------------- | -------------------- |
| アジア          | 東アジア        | `eastasia`           |
| アジア          | 東南アジア   | `southeastasia`      |
| オーストラリア     | オーストラリア東部   | `australiaeast`      |
| ヨーロッパ        | 北ヨーロッパ     | `northeurope`        |
| ヨーロッパ        | 西ヨーロッパ      | `westeurope`         |
| 北米 | 米国東部          | `eastus`             |
| 北米 | 米国東部 2        | `eastus2`            |
| 北米 | 米国中南部 | `southcentralus`     |
| 北米 | 米国中西部  | `westcentralus`      |
| 北米 | 米国西部          | `westus`             |
| 北米 | 米国西部 2        | `westus2`            |
| 南アメリカ | ブラジル南部     | `brazilsouth`        |

これは、[Language Understanding サービス (LUIS)](../luis/luis-reference-regions.md) でサポートされている公開リージョンのサブセットです。

### <a name="voice-assistants"></a>音声アシスタント

[Speech SDK](speech-sdk.md) は、以下のリージョンで [Direct Line Speech](./direct-line-speech.md) による **音声アシスタント** 機能をサポートしています。

| グローバル リージョン | リージョン           | リージョン識別子    |
| ------------- | ---------------- | -------------------- |
| 北米 | 米国西部          | `westus`             |
| 北米 | 米国西部 2        | `westus2`            |
| 北米 | 米国東部          | `eastus`             |
| 北米 | 米国東部 2        | `eastus2`            |
| 北米 | 米国中西部  | `westcentralus`      |
| 北米 | 米国中南部 | `southcentralus`     |
| ヨーロッパ        | 西ヨーロッパ      | `westeurope`         |
| ヨーロッパ        | 北ヨーロッパ     | `northeurope`        |
| アジア          | 東アジア        | `eastasia`           |
| アジア          | 東南アジア   | `southeastasia`      |
| インド         | インド中部    | `centralindia`       |

### <a name="speaker-recognition"></a>Speaker Recognition

現在、Speaker Recognition は `westus` リージョンでのみ使用できます。

## <a name="rest-apis"></a>REST API

Speech Service は、音声テキスト変換要求とテキスト読み上げ要求用の REST エンドポイントも公開しています。

### <a name="speech-to-text"></a>音声テキスト変換

音声テキスト変換のリファレンス ドキュメントについては、「[Speech to Text REST API](rest-speech-to-text.md)」を参照してください。

REST API のエンドポイントの形式は次のとおりです。

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

次の表に示す、ご利用のサブスクリプションのリージョンと一致する識別子で `<REGION_IDENTIFIER>` を置き換えてください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 4xx HTTP エラーを受け取らないためには、URL に言語パラメーターを付加する必要があります。 たとえば、米国西部エンドポイントを使用する米国英語に設定される言語は `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` です。

### <a name="text-to-speech"></a>テキスト読み上げ

テキスト読み上げのリファレンス ドキュメントについては、「[Text to Speech REST API](rest-text-to-speech.md)」を参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]