---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a8647e7f19b55547bbb7eff6f1f3bc1f5282c89
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934542"
---
## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

キーとリージョンを使用して [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を作成する方法を見てみましょう。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>認識エンジンを初期化する

[`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を作成したら、次の手順として、[`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) を初期化します。 [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) を初期化する場合は、それを自分の `speechConfig` に渡す必要があります。 これにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) は次のようになります。

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) を作成し、[`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) を初期化するときに `audioConfig` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

まず、次の `using` ステートメントを追加します。

```csharp
using Microsoft.CognitiveServices.Speech.Audio;
```

これで、`AudioConfig` オブジェクトを次のように参照できるようになります。

```csharp
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、`FromDefaultMicrophoneInput` を呼び出すのではなく、[`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) を作成する場合は、`FromWavFileOutput` を呼び出して `filename` パラメーターを渡します。


```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>音声を認識する

Speech SDK for C# 用の [認識エンジン](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) では、音声認識に使用できるいくつかの手法が公開されています。

* 単発の認識 (非同期) - 非ブロッキング (非同期) モードで認識を実行します。 これにより、1 つの発話が認識されます。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。
* 継続的認識 (非同期) - 継続的な認識操作を非同期に開始します。 ユーザーはイベントに登録し、さまざまなアプリケーションの状態を処理します。 非同期の継続的認識を停止するには、[`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet) を呼び出します。

> [!NOTE]
> [音声認識モードを選択](../../../how-to-choose-recognition-mode.md)する方法の詳細情報。

### <a name="single-shot-recognition"></a>単発の認識

[`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet) を使用した非同期の単発認識の例を次に示します。

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

結果を処理するコードを記述する必要があります。 このサンプルでは、[`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet) を評価します。

* 認識結果を出力します: `ResultReason.RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `ResultReason.NoMatch`
* エラーが検出された場合は、エラー メッセージを出力します: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        Console.WriteLine($"    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

### <a name="continuous-recognition"></a>継続的認識

継続的認識は、単発の認識よりも少し複雑です。 この場合は、認識結果を取得するために、`Recognizing`、`Recognized`、`Canceled` の各イベントをサブスクライブする必要があります。 認識を停止するには、[`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

入力を定義し、[`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) を初期化することから始めましょう。

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
次に、音声認識の状態を管理する変数を作成してみましょう。 まず、前の宣言の後に `TaskCompletionSource<int>` を宣言します。

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

[`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) から送信されたイベントをサブスクライブします。

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet): 中間的な認識結果を含むイベントのシグナル。
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

すべてが設定されると、[`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet) を呼び出すことができます。

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) 上で [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet) メソッドを使用します。

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を見つけて、そのすぐ下に次の行を追加します。

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet) プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている[ロケールまたは言語](../../../language-support.md)のリストの**ロケール**列に任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

Speech SDK を使用して認識の精度を向上させるには、いくつかの方法があります。 フレーズ リストを見てみましょう。 フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識時に、音声にフレーズ全体に対する完全一致が含まれる場合、フレーズ リスト内のエントリが使用されます。 フレーズに対する完全一致が見つからない場合、認識はサポートされません。

> [!IMPORTANT]
> フレーズ リスト機能は英語でのみ使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) オブジェクトを作成します。次に、[`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

ご利用の語句リストをクリアする必要がある場合は、次のようにします。 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>認識の精度を向上させるためのその他のオプション

フレーズ リストは、認識の精度を向上させるためのオプションの 1 つにすぎません。 次のこともできます。 

* [Custom Speech を使用して精度を向上させる](../../../how-to-custom-speech.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)