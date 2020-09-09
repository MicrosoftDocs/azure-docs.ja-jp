---
title: Speech SDK で音声認識モードを選択する
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用する際に最適な認識モードを選択する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 29dcd3ddc32ac92f7543e5d7648680e36f50dcfb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934601"
---
# <a name="choose-a-speech-recognition-mode"></a>音声認識モードを選択する

音声テキスト変換の認識操作に関して、[Speech SDK](speech-sdk.md) には、音声を処理するためのモードが複数用意されています。 概念上、"*認識モード*" と呼ばれることもあります。 この記事では、さまざまな認識モードを比較します。

## <a name="recognize-once"></a>1 回認識

それぞれの発話を一度に 1 "文" ずつ処理したい場合、"1 回認識" 機能を使用します。 この方法では、検出された音声の冒頭から次の休止までの間に認識された発話が入力から検出されます。 通常、文または思考の終わりは、休止によって示されます。

認識された 1 回の発話の最後に、その要求からの音声の処理が停止されます。 認識の対象となる文の長さの上限は 20 秒です。

::: zone pivot="programming-language-csharp"

`RecognizeOnceAsync` 関数の詳しい使用方法については、[.NET Speech SDK のドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)を参照してください。

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

`RecognizeOnceAsync` 関数の詳しい使用方法については、[C++ Speech SDK のドキュメント](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)を参照してください。

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

`recognizeOnceAsync` 関数の詳しい使用方法については、[Java Speech SDK のドキュメント](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)を参照してください。

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

`recognize_once` 関数の詳しい使用方法については、[Python Speech SDK のドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)を参照してください。

```python
result = speech_recognizer.recognize_once()
```

::: zone-end

::: zone pivot="programming-language-javascript"

`recognizeOnceAsync`関数の使用方法の詳細については、[JavaScript の Speech SDK のドキュメント](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-)を参照してください。

```JavaScript
recognizer.recognizeOnceAsync((result)=>{}, (error)=>{}));
```

::: zone-end

::: zone pivot="programming-language-more"

その他の言語については、[Speech SDK リファレンス ドキュメント](speech-to-text.md#speech-sdk-reference-docs)を参照してください。

::: zone-end

## <a name="continuous"></a>継続的

長時間にわたる認識を必要とする場合、開始関数とそれに対応する停止関数を使用して継続的認識を行います。 開始関数は、すべての発話の処理を開始し、その後、停止関数が呼び出されるか、無音状態で所定の時間が経過するまで、処理を継続します。 継続モードを使用する際は、発生時に作動する各種イベントに登録してください。 たとえば "recognized" イベントは、音声認識が行われたときに作動します。 認識を処理するには、イベント ハンドラーを設定する必要があります。

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync(()=>{}, (error)=>{});

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync(()=>{}, (error)=>{});
```

::: zone-end

::: zone pivot="programming-language-more"

その他の言語については、[Speech SDK リファレンス ドキュメント](speech-to-text.md#speech-sdk-reference-docs)を参照してください。

::: zone-end

## <a name="dictation"></a>ディクテーション

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

::: zone pivot="programming-language-csharp"

`EnableDictation` 関数の詳しい使用方法については、[.NET Speech SDK のドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)を参照してください。

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

`EnableDictation` 関数の詳しい使用方法については、[C++ Speech SDK のドキュメント](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)を参照してください。

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

`enableDictation` 関数の詳しい使用方法については、[Java Speech SDK のドキュメント](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)を参照してください。

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

`enable_dictation` 関数の詳しい使用方法については、[Python Speech SDK のドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)を参照してください。

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end

::: zone pivot="programming-language-javascript"

`enableDictation` 関数の使用方法の詳細については、[JavaScript の Speech SDK のドキュメント](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--)を参照してください。

```JavaScript
// Enable diction
speechConfig.enableDictation();
```

::: zone-end

::: zone pivot="programming-language-more"

その他の言語については、[Speech SDK リファレンス ドキュメント](speech-to-text.md#speech-sdk-reference-docs)を参照してください。

::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [その他の Speech SDK サンプルを GitHub で探す](https://aka.ms/csspeech/samples)
