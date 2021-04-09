---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 5efe6e9cf3227857300d649a8c2ecb4973627335
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444500"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して他の言語に翻訳する機能です。 このクイックスタートでは、アプリと製品で Speech SDK を使用して、高品質の音声翻訳を実行する方法について説明します。 このクイックスタートでは、次のトピックについて説明します。

* 音声テキスト変換の翻訳
* 音声を複数のターゲット言語に翻訳する
* 音声間翻訳を直接実行する

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [C# クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 お使いのプラットフォームに応じて、記事「_Speech SDK について_」の「<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK を取得する</a>」セクションに記載されている手順に従ってください。

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、*Program.cs* ファイルの先頭に次の `using` ステートメントを含めます。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>機微なデータと環境変数

この記事のソース コードの例は、Speech リソースのサブスクリプション キーやリージョンなどの機微なデータを格納する環境変数に依存します。 `SPEECH__SERVICE__REGION` クラスには、ホスト コンピューター環境変数から割り当てられた 2 つの `Program` 値 (`static readonly string` と `SPEECH__SUBSCRIPTION__KEY`) が含まれています。 これらのフィールドはどちらもクラス スコープにあるので、クラスのメソッド本体内でアクセスできます。 環境変数の詳細については、「[環境変数とアプリケーション構成](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)」を参照してください。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>音声翻訳構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechTranslationConfig`][config] を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!TIP]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechTranslationConfig`][config] を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

キーとリージョンを使用して [`SpeechTranslationConfig`][config] を作成する方法を見てみましょう。 「[Speech Service を無料で試す](../../../overview.md#try-the-speech-service-for-free)」の手順に従って、これらの資格情報を取得します。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>ソース言語を変更する

音声翻訳の一般的なタスクの 1 つは、入力 (またはソース) 言語を指定することです。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 コードで、[`SpeechTranslationConfig`][config] インスタンスと対話して、`SpeechRecognitionLanguage` プロパティに割り当てます。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang] プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている [ロケールまたは言語](../../../language-support.md)のリストの **ロケール** 列に任意の値を指定できます。

## <a name="add-translation-language"></a>翻訳言語を追加する

音声翻訳のもう 1 つの一般的なタスクは、対象の翻訳言語を指定することです。少なくとも 1 つが必要ですが、複数もサポートされています。 次のコード スニペットでは、フランス語とドイツ語の両方が翻訳言語ターゲットとして設定されています。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

[`AddTargetLanguage`][addlang] を呼び出すたびに、新しいターゲット翻訳言語が指定されます。 つまり、音声がソース言語から認識される場合は、各ターゲット翻訳を結果の翻訳操作の一部として利用できます。

## <a name="initialize-a-translation-recognizer"></a>翻訳認識エンジンを初期化する

[`SpeechTranslationConfig`][config] を作成したら、次の手順として、[`TranslationRecognizer`][recognizer] を初期化します。 [`TranslationRecognizer`][recognizer] を初期化する場合は、それを自分の `translationConfig` に渡す必要があります。 構成オブジェクトにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`TranslationRecognizer`][recognizer] は次のようになります。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`][audioconfig] を作成し、[`TranslationRecognizer`][recognizer] を初期化するときに `audioConfig` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

まず、`AudioConfig` オブジェクトを次のように参照します。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、`FromDefaultMicrophoneInput` を呼び出すのではなく、[`AudioConfig`][audioconfig] を作成する場合は、`FromWavFileInput` を呼び出して `filename` パラメーターを渡します。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>音声を翻訳する

音声を翻訳するために、Speech SDK はマイクまたはオーディオ ファイル入力に依存します。 音声認識は、音声翻訳の前に行われます。 すべてのオブジェクトが初期化されたら、recognize-once 関数を呼び出して結果を取得します。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

音声テキスト変換の詳細については、[音声認識の基礎](../../../get-started-speech-to-text.md)に関する記事を参照してください。

## <a name="synthesize-translations"></a>翻訳の合成

音声認識と翻訳が正常に完了すると、結果にはディクショナリ内のすべての翻訳が含まれます。 [`Translations`][translations] ディクショナリ キーはターゲットの翻訳言語で、値は翻訳されたテキストです。 認識された音声は翻訳可能で、別の言語で合成できます (音声間)。

### <a name="event-based-synthesis"></a>イベントベースの合成

`TranslationRecognizer` オブジェクトによって `Synthesizing` イベントが公開されます。 イベントは複数回発生し、合成されたオーディオを翻訳認識結果から取得するためのメカニズムが提供されます。 複数の言語に翻訳する場合は、「[手動合成](#manual-synthesis)」を参照してください。 合成音声を指定するには、[`VoiceName`][voicename] を割り当て、`Synthesizing` イベントのイベント ハンドラーを提供して、オーディオを取得します。 次の例では、翻訳されたオーディオを *.wav* ファイルとして保存します。

> [!IMPORTANT]
> イベントベースの合成は、1 つの翻訳でのみ機能し、複数のターゲット翻訳言語を追加 **しません**。 また、[`VoiceName`][voicename] は、ターゲット翻訳言語と同じ言語である必要があります。たとえば、`"de"` は `"de-DE-Hedda"` にマップされる可能性があります。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>手動合成

[`Translations`][translations] ディクショナリを使用すると、翻訳テキストからオーディオを合成できます。 各翻訳を反復処理し、翻訳を合成します。 `SpeechSynthesizer` インスタンスを作成する場合、`SpeechConfig` オブジェクトの [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] プロパティを目的の音声に設定する必要があります。 次の例では、5 つの言語に翻訳し、各翻訳を対応するニューラル言語のオーディオ ファイルに合成します。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

音声合成の詳細については、[音声合成の基礎](../../../get-started-text-to-speech.md)に関する記事を参照してください。

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename