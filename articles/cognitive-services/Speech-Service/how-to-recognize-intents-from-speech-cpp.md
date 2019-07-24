---
title: Speech SDK for C++ を使用して音声から意図を認識する
titleSuffix: Azure Cognitive Services
description: Speech SDK for C++ を使用してファイルまたはマイクの音声から意図を認識するさまざまな方法を学習します。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 8298f5e3b4f04574d7123da4a6c55f169a0ab299
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867955"
---
# <a name="recognize-intents-from-speech-by-using-the-speech-sdk-for-c"></a>Speech SDK for C++ を使用して音声から意図を認識する

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone.md)]

[!code-cpp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!INCLUDE [Introduction to using a microphone and language](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone-language.md)]

[!code-cpp[Intent recognition by using a microphone in a specified language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-continuous.md)]

[!code-cpp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
この記事で使用されているコードを samples/cpp/windows/console フォルダーから探します。

## <a name="next-steps"></a>次の手順

- [音声を認識する方法](how-to-recognize-speech-cpp.md)
- [音声を翻訳する方法](how-to-translate-speech-cpp.md)
