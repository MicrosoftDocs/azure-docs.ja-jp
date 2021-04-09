---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: d0cfa7281edfca64616fc62b8af25ef4e5ec598a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444237"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して他の言語に翻訳する機能です。 このクイックスタートでは、アプリと製品で Speech SDK を使用して、高品質の音声翻訳を実行する方法について説明します。 このクイックスタートでは、次のトピックについて説明します。

* 音声テキスト変換の翻訳
* 音声を複数のターゲット言語に翻訳する
* 音声間翻訳を直接実行する

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [C++ クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 お使いのプラットフォームに応じて、記事「_Speech SDK について_」の「<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK を取得する</a>」セクションに記載されている手順に従ってください。

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、C++ コード ファイルの先頭に次の `#include` および `using` ステートメントを含めます。

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>機微なデータと環境変数

この記事のソース コードの例は、Speech リソースのサブスクリプション キーやリージョンなどの機微なデータを格納する環境変数に依存します。 C++ コード ファイルには、ホスト コンピューター環境変数から割り当てられた 2 つの文字列値 (`SPEECH__SUBSCRIPTION__KEY` と `SPEECH__SERVICE__REGION`) が含まれています。 これらのフィールドはどちらもクラス スコープにあるので、クラスのメソッド本体内でアクセスできます。 環境変数の詳細については、「[環境変数とアプリケーション構成](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)」を参照してください。

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>ソース言語を変更する

音声翻訳の一般的なタスクの 1 つは、入力 (またはソース) 言語を指定することです。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 コードでは、[`SpeechTranslationConfig`][config] インスタンスと対話して、`SetSpeechRecognitionLanguage` メソッドを呼び出します。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

[`SpeechRecognitionLanguage`][recognitionlang] プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている [ロケールまたは言語](../../../language-support.md)のリストの **ロケール** 列に任意の値を指定できます。

## <a name="add-translation-language"></a>翻訳言語を追加する

音声翻訳のもう 1 つの一般的なタスクは、対象の翻訳言語を指定することです。少なくとも 1 つが必要ですが、複数もサポートされています。 次のコード スニペットでは、フランス語とドイツ語の両方が翻訳言語ターゲットとして設定されています。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

[`AddTargetLanguage`][addlang] を呼び出すたびに、新しいターゲット翻訳言語が指定されます。 つまり、音声がソース言語から認識される場合は、各ターゲット翻訳を結果の翻訳操作の一部として利用できます。

## <a name="initialize-a-translation-recognizer"></a>翻訳認識エンジンを初期化する

[`SpeechTranslationConfig`][config] を作成したら、次の手順として、[`TranslationRecognizer`][recognizer] を初期化します。 [`TranslationRecognizer`][recognizer] を初期化する場合は、それを自分の `translationConfig` に渡す必要があります。 構成オブジェクトにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`TranslationRecognizer`][recognizer] は次のようになります。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`][audioconfig] を作成し、[`TranslationRecognizer`][recognizer] を初期化するときに `audioConfig` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

まず、`AudioConfig` オブジェクトを次のように参照します。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、`FromDefaultMicrophoneInput` を呼び出すのではなく、[`AudioConfig`][audioconfig] を作成する場合は、`FromWavFileInput` を呼び出して `filename` パラメーターを渡します。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>音声を翻訳する

音声を翻訳するために、Speech SDK はマイクまたはオーディオ ファイル入力に依存します。 音声認識は、音声翻訳の前に行われます。 すべてのオブジェクトが初期化されたら、recognize-once 関数を呼び出して結果を取得します。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

音声テキスト変換の詳細については、[音声認識の基礎](../../../get-started-speech-to-text.md)に関する記事を参照してください。

## <a name="synthesize-translations"></a>翻訳の合成

音声認識と翻訳が正常に完了すると、結果にはディクショナリ内のすべての翻訳が含まれます。 [`Translations`][translations] ディクショナリ キーはターゲットの翻訳言語で、値は翻訳されたテキストです。 認識された音声は翻訳可能で、別の言語で合成できます (音声間)。

### <a name="event-based-synthesis"></a>イベントベースの合成

`TranslationRecognizer` オブジェクトによって `Synthesizing` イベントが公開されます。 イベントは複数回発生し、合成されたオーディオを翻訳認識結果から取得するためのメカニズムが提供されます。 複数の言語に翻訳する場合は、「[手動合成](#manual-synthesis)」を参照してください。 合成音声を指定するには、[`SetVoiceName`][voicename] を割り当て、`Synthesizing` イベントのイベント ハンドラーを提供して、オーディオを取得します。 次の例では、翻訳されたオーディオを *.wav* ファイルとして保存します。

> [!IMPORTANT]
> イベントベースの合成は、1 つの翻訳でのみ機能し、複数のターゲット翻訳言語を追加 **しません**。 また、[`SetVoiceName`][voicename] は、ターゲット翻訳言語と同じ言語である必要があります。たとえば、`"de"` は `"de-DE-Hedda"` にマップされる可能性があります。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>手動合成

[`Translations`][translations] ディクショナリを使用すると、翻訳テキストからオーディオを合成できます。 各翻訳を反復処理し、翻訳を合成します。 `SpeechSynthesizer` インスタンスを作成する場合、`SpeechConfig` オブジェクトの [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] プロパティを目的の音声に設定する必要があります。 次の例では、5 つの言語に翻訳し、各翻訳を対応するニューラル言語のオーディオ ファイルに合成します。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

音声合成の詳細については、[音声合成の基礎](../../../get-started-text-to-speech.md)に関する記事を参照してください。

[config]: /cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: /cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: /cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: /cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: /cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: /cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename