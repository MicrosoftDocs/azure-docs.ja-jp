---
title: Speech SDK for Java を使用して音声を認識する
titleSuffix: Azure Cognitive Services
description: Speech SDK for Java を使用して音声を認識するさまざまな方法を示します (ファイルから、マイクから、カスタマイズしたモデルで、継続的に、1 回限りで)。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 56f12c9f38b92203e2be37824b9247b321a86169
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875393"
---
# <a name="recognize-speech-by-using-the-speech-sdk-for-java"></a>Speech SDK for Java を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-microphone.md)]

[!code-java[Speech recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionWithMicrophone)]

[!INCLUDE [Introduction to using customized recognition](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-customized.md)]

[!code-java[Speech recognition by using a customized model](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionCustomized)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-continuous.md)]

[!code-java[Continuous speech recognition](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionContinuousWithFile)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
この記事で使用されているコードを samples/java/jre/console フォルダーから探します。

## <a name="next-steps"></a>次の手順

- [音声から意図を認識する方法](how-to-recognize-intents-from-speech-java.md)
- [音声を翻訳する方法](how-to-translate-speech-java.md)

