---
title: 意図認識の概要 - Speech サービス
titleSuffix: Azure Cognitive Services
description: 意図認識を使用すると、事前に定義したユーザーの目的を認識することができます。 この記事は、意図認識サービスの利点と機能の概要です。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: 意図認識
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018703"
---
# <a name="what-is-intent-recognition"></a>意図認識とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

この概要では、意図認識の利点と機能について説明します。 Cognitive Services Speech SDK は Language Understanding サービス (LUIS) と統合して意図認識機能を提供します。 意図は、航空機の予約や天気のチェック、あるいは電話を掛けるなどのユーザーが実行したい行動です。
意図認識を使用すると、アプリケーション、ツール、およびデバイスは、LUIS で定義したオプションに基づいて、ユーザーが何を開始または実行したいかを判断することができます。

## <a name="luis-key-required"></a>必要な LUIS キー

* LUIS は音声から意図を認識するために Speech サービスと統合しています。 Speech サービスのサブスクリプションは不要で、LUIS だけでかまいません。
* 音声意図認識は、SDK と統合されています。 Speech Service では LUIS キーを利用できます。
* Speech SDK を使用した意図認識は、[LUIS によってサポートされるリージョンのサブセットで提供](./regions.md#intent-recognition)されています。

## <a name="get-started"></a>はじめに

意図認識の使用を開始するには、[クイックスタート](quickstarts/intent-recognition.md)を参照してください。

## <a name="sample-code"></a>サンプル コード

意図認識のサンプル コードについては、次を参照してください。

* [クイック スタート:事前構築済みの Home Automation アプリを使用する](../luis/luis-get-started-create-app.md)
* [C# 用の Speech SDK を使用して音声の意図を認識する](./how-to-recognize-intents-from-speech-csharp.md)
* [C# の Unity を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Python 用 Speech SDK を使用して意図を認識する](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Windows で C++ 用 Speech SDK を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Windows または Linux で Java 用 Speech SDK を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Web ブラウザーで JavaScript 用 Speech SDK を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>次のステップ

* 意図認識の[クイックスタート](quickstarts/intent-recognition.md)を完了する
* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [Speech SDK を取得する](speech-sdk.md)