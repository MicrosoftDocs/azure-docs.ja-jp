---
title: Speech SDK のオーディオ入力ストリームの概念
titleSuffix: Azure Cognitive Services
description: Speech SDK のオーディオ入力ストリーム API の機能の概要です。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: f34504dddaa21cc162476d1fdb462e7be5c48629
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877305"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>Speech SDK のオーディオ入力ストリーム API について

Speech SDK の**オーディオ入力ストリーム** API では、マイクまたは入力ファイルの API を使用する代わりに、認識エンジンにオーディオ ストリームをストリーミングする方法を提供します。

オーディオ入力ストリームを使用するときは、次の手順が必要です。

- オーディオ ストリームの形式を識別します。 この形式は、Speech SDK と音声認識サービスでサポートされている必要があります。 現在、次の構成のみがサポートされています。

  PCM 形式のオーディオ サンプル、1 つのチャンネル、16,000 サンプル/秒、32,000 バイト/秒、2 つのブロック配列 (1 つのサンプルのパディングを含む 16 ビット)、16 ビット/サンプル。

  オーディオ形式を作成するための SDK の対応するコードは次のようになります。

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- これらの仕様に従って、コードが RAW オーディオ データを提供できることを確認します。 オーディオ ソース データが、サポートされている形式と一致しない場合、オーディオは、必要な形式にトランスコードする必要があります。

- `PullAudioInputStreamCallback` から派生された独自のオーディオ入力ストリーム クラスを作成します。 `Read()` メンバーと `Close()` メンバーを実装します。 正確な関数シグネチャは言語に依存しますが、そのコードは以下のサンプル コードのようになります。

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- オーディオ形式と入力ストリームに基づいてオーディオ構成を作成します。 認識エンジンを作成するときは、通常の音声構成とオーディオ入力構成の両方を渡します。 例: 

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
