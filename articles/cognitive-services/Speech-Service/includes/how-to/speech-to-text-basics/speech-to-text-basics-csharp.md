---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 7c4b368c6b02304a6d73e7929ff2d7a93f0921ee
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445589"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して文字起こしをする機能です (多くの場合、音声テキスト変換と呼ばれます)。 このクイックスタートでは、アプリや製品で Speech SDK を使用し、高品質の音声テキスト変換を実行する方法について説明します。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [C# クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

このパッケージ名の使用を開始する場合は、NuGet コンソールで `Install-Package Microsoft.CognitiveServices.Speech` を実行します。

プラットフォームごとのインストール手順については、次のリンクを参照してください。

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。 キーとリージョンを使用して [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) を作成します。 キーとリージョンのペアを見つけるには、「[キーとリージョンを見つける](../../../overview.md#find-keys-and-region)」ページを参照してください。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

[`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) を初期化するには、他にも次に示すようないくつかの方法があります。

* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

## <a name="recognize-from-microphone"></a>マイクから認識する

デバイス マイクを使用して音声を認識するには、`FromDefaultMicrophoneInput()` を使用して `AudioConfig` を作成します。 次に、`audioConfig` と `speechConfig` を渡して [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) を初期化します。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

"*特定の*" オーディオ入力デバイスを使用したい場合、`AudioConfig` でデバイス ID を指定する必要があります。 自分のオーディオ入力デバイスの[デバイス ID を取得する方法](../../../how-to-select-audio-input-devices.md)をご覧ください。

## <a name="recognize-from-file"></a>ファイルから認識する

マイクではなくオーディオ ファイルから音声を認識する場合でも、`AudioConfig` を作成する必要があります。 ただし、`FromDefaultMicrophoneInput()` を呼び出さずに、[`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) を作成する場合は、`FromWavFileInput()` を呼び出してファイル パスを渡します。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>インメモリ ストリームから認識する

対象となる音声データが Blob Storage に格納されていたり、既存のメモリ内に `byte[]` や同様の生データ構造として存在していたりすることは、多くのユースケースで予想されます。 以下の例では、[`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream) という、実質的に抽象化されたメモリ ストリームを使用して音声を認識します。 サンプル コードは、次の処理を実行します。

* `byte[]` を引数として受け取る `Write()` 関数を使用して、生の音声データ (PCM) を `PushAudioInputStream` に書き込みます。
* デモンストレーションとして、`FileReader` を使用して `.wav` ファイルを読み取ります。ただし、`byte[]` 形式の音声データが既にある場合は、これをスキップして直接そのコンテンツを入力ストリームに書き込むことができます。
* 既定の形式は、16 ビット、16 kHz のモノラル PCM です。 この形式をカスタマイズしたい場合は、静的関数 `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)` を使用して、`CreatePushStream()` に [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat) オブジェクトを渡してください。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

プッシュ ストリームを入力として使用する場合、音声データは生の PCM であることが前提となります (ヘッダーをスキップするなど)。
場合によっては、ヘッダーがスキップされていなくても、API は正しく機能します。しかし、最良の結果を得るためには、`byte[]` が "*音声データの開始*" 位置から始まるよう、ヘッダーを読み取るロジックの実装を検討してください。

## <a name="error-handling"></a>エラー処理

これまでの例では単に、認識されたテキストを `result.text` から取得していましたが、エラーやその他の応答を処理するためには、結果を処理するなんらかのコードを記述する必要があります。 以下のコードでは、[`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason) プロパティを評価したうえで、次の処理を行っています。

* 認識結果を出力します: `ResultReason.RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `ResultReason.NoMatch`
* エラーが検出された場合は、エラー メッセージを出力します: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
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

## <a name="continuous-recognition"></a>継続的認識

これまでの例では、1 つの発話を認識する単発の認識を使用してきました。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。

一方、認識を停止するタイミングを **制御** したい場合は、継続的認識を使用します。 この場合は、認識結果を取得するために、`Recognizing`、`Recognized`、`Canceled` の各イベントをサブスクライブする必要があります。 認識を停止するには、[`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

まず、入力を定義し、[`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) を初期化します。

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

次に、音声認識の状態を管理する `TaskCompletionSource<int>` を作成します。

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

次に、[`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) から送信されたイベントをサブスクライブします。

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing): 中間的な認識結果を含むイベントのシグナル。
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

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

すべての設定が完了したら、`StartContinuousRecognitionAsync` を呼び出して認識を開始します。

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) 上で [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation) メソッドを使用します。

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) を見つけて、そのすぐ下に次の行を追加します。

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage) プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている [ロケールまたは言語](../../../language-support.md)のリストの **ロケール** 列に任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズのリストを提供することにより、音声認識の精度を向上させることができます。

例として、"Move to" という指示があり、会話で考えられる目的地として "Ward" がある場合、"Move to Ward" というエントリーを追加できます。 フレーズを追加すると、音声認識の実行時に、"Move toward" ではなく "Move to Ward" が認識される可能性が高くなります。

フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識中、フレーズのリスト内のエントリは、発話の途中にエントリが現れた場合でも単語またはフレーズの認識を強化するために使用されます。 

> [!IMPORTANT]
> フレーズのリストの機能は、en-US、de-DE、en-AU、en-CA、en-GB、es-ES、es-MX、fr-CA、fr-FR、it-IT、ja-JP、ko-KR、pt-BR、zh-CN の言語で使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) オブジェクトを作成します。次に、[`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

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

* [Custom Speech を使用して精度を向上させる](../../../custom-speech-overview.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)
