---
title: Speech SDK を使用してコーデック圧縮オーディオをストリーミングする - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して圧縮オーディオを Azure Speech サービスにストリーミングする方法について説明します。 Linux の場合は C++、C#、Java で、Android の場合は Java で、iOS の場合は Objective-C で使用できます。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 8f2896a6289ecaf4046d705da106636258cdadc5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802240"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK でコーデック圧縮オーディオを使用する

Speech SDK の**圧縮オーディオ入力ストリーム** API では、PullStream または PushStream を使用して、圧縮オーディオを Speech サービスにストリーミングできます。

> [!IMPORTANT]
> 現在、圧縮入力オーディオのストリーミングは、Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9) では C++、C#、Java でサポートされています。 また、[Android プラットフォームでは Java](how-to-use-codec-compressed-audio-input-streams-android.md)、[iOS プラットフォームでは Objective-C](how-to-use-codec-compressed-audio-input-streams-ios.md) でもサポートされています。
> Speech SDK バージョン 1.7.0 以降が必要です。

wav/PCM については、メインライン音声のドキュメントを参照してください。  wav/PCM 以外では、次のコーデック圧縮入力形式がサポートされています。

- MP3
- OPUS/OGG
- FLAC
- wav コンテナー内の ALAW
- wav コンテナー内の MULAW

## <a name="prerequisites"></a>前提条件

圧縮オーディオの処理は、[GStreamer](https://gstreamer.freedesktop.org) を使用して実装されます。 ライセンスの理由から、Speech SDK では Gstreamer バイナリはコンパイルおよびリンクされません。 そのため、圧縮入力オーディオを使用するには、アプリケーション開発者は 18.04、16.04、Debian 9 に次のものをインストールする必要があります。

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>コーデック圧縮オーディオ入力を使用するコード例

圧縮オーディオ形式で Speech サービスにストリーミングするには、`PullAudioInputStream` または `PushAudioInputStream` を作成します。 次に、ストリームの圧縮形式を指定して、ストリーム クラスのインスタンスから `AudioConfig` を作成します。

`myPushStream` という入力ストリーム クラスがあり、OPUS/OGG を使用しているものと想定します。 コードは次のようになります。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
