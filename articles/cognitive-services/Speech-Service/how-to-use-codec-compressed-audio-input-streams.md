---
title: Speech SDK を使用してコーデック圧縮オーディオをストリーミングする - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して圧縮オーディオを Azure Speech サービスにストリーミングする方法について説明します。 Linux 用の C++、C#、および Java で使用可能です。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: d23190dc8f7980cb8a94ba295f45ae67fc7d4678
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605083"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK でコーデック圧縮オーディオを使用する

Speech SDK の**圧縮オーディオ入力ストリーム** API では、PullStream または PushStream を使用して、圧縮オーディオを Speech サービスにストリーミングできます。

> [!IMPORTANT]
> 圧縮オーディオのストリーミングは、Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9) 用の C++、 C#、および Java でのみサポートされます。
> Speech SDK バージョン 1.4.0 以降が必要です。

wav/PCM については、メインライン音声のドキュメントを参照してください。  wav/PCM 以外では、次のコーデック圧縮入力形式がサポートされています。

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>コーデック圧縮オーディオ入力を使用するための前提条件

Linux 用の Speech SDK で圧縮オーディオ入力を使用するには、これらの依存関係ファイルをインストールします。

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
