---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 4ec9c847158c9b8a1160cb6f4510912ed83a9fae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98947123"
---
まず、`KeywordRecognitionModel` を返す `FromFile()` 静的関数を使用して、キーワード モデル ファイルを読み込みます。 Speech Studio からダウンロードした `.table` ファイルへのパスを使用します。 また、既定のマイクを使用して `AudioConfig` を作成し、オーディオ構成を使用して新しい `KeywordRecognizer` をインスタンス化します。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

次に、モデル オブジェクトを渡すことによって、`RecognizeOnceAsync()` を 1 回呼び出すだけでキーワード認識が実行されます。 これにより、キーワードが認識されるまで継続されるキーワード認識セッションが開始されます。 そのため、このデザイン パターンは、一般的にはマルチスレッド アプリケーションや、ウェイクワードを無期限に待ち続けるような場合に使用します。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> ここで示す例では、ローカル キーワード認識を使用しています。これは、認証コンテキストに `SpeechConfig` オブジェクトを必要とせず、バックエンドに接続しないためです。 ただし、[直接バックエンド接続を利用](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)することで、キーワード認識と検証の両方を実行できます。

### <a name="continuous-recognition"></a>継続的認識

Speech SDK の他のクラスでは、キーワード認識を使用した継続的認識 (音声認識と意図認識の両方) がサポートされています。 これにより、継続的認識に通常使用するものと同じコードを使用でき、キーワード モデルの `.table` ファイルを参照できます。

音声変換の場合、[クイックスタート](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition)に示されているデザイン パターンに従って、継続的認識を設定します。 次に、`recognizer.StartContinuousRecognitionAsync()` の呼び出しを `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` に置き換え、`KeywordRecognitionModel` オブジェクトを渡します。 キーワード認識を使用した継続的認識を停止するには、`recognizer.StopContinuousRecognitionAsync()` ではなく `recognizer.StopKeywordRecognitionAsync()` を使用します。

意図認識では、[`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) および [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) 関数と同じパターンを使用します。
