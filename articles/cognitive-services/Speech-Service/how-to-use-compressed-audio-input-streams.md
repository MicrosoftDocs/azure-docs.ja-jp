---
title: Speech SDK による圧縮オーディオのストリーミング - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して圧縮オーディオを Azure Speech サービスにストリーミングする方法について説明します。 Linux 用の C++、C#、および Java で使用可能です。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 2066dc3e20ab9fc92b23fd071728ea6a920d3324
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "59011554"
---
# <a name="stream-compressed-audio-with-the-speech-sdk"></a>Speech SDK による圧縮オーディオのストリーミング

Speech SDK の**圧縮オーディオ入力ストリーム** API では、PullStream または PushStream を使用して、圧縮オーディオを Speech サービスにストリーミングできます。

> [!IMPORTANT]
> 圧縮オーディオのストリーミングは、Linux (Ubuntu 16.04 または Ubuntu 18.04) 用の C++、 C#、および Java でのみサポートされます。
> サポートは、MP3 および OPUS/OGG に制限されています。

## <a name="prerequisites"></a>前提条件

Linux 用の Speech SDK で圧縮オーディオ入力を使用するには、次の依存関係ファイルをインストールする必要があります。

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="streaming-compressed-audio"></a>圧縮オーディオのストリーミング

圧縮オーディオ形式で Speech サービスにストリーミングするには、`PullAudioInputStream` または `PushAudioInputStream` を作成します。 次に、ストリームの圧縮形式を指定して、ストリーム クラスのインスタンスから `AudioConfig` を作成します。

`myPushStream` という入力ストリーム クラスがあり、OPUS/OGG を使用しているものと想定します。 コードの外観を次に示します。 

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

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
