---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 679104a1941e677dd78190fb80964a5d39813e61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102445081"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して他の言語に翻訳する機能です。 このクイックスタートでは、アプリと製品で Speech SDK を使用して、高品質の音声翻訳を実行する方法について説明します。 このクイックスタートでは、次のトピックについて説明します。

* 音声テキスト変換の翻訳
* 音声を複数のターゲット言語に翻訳する
* 音声間翻訳を直接実行する

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [Python クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 お使いのプラットフォームに応じて、記事「_Speech SDK について_」の「<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK を取得する</a>」セクションに記載されている手順に従ってください。

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、python コード ファイルの先頭に次の `import` ステートメントを含めます。

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>機微なデータと環境変数

この記事のソース コードの例は、Speech リソースのサブスクリプション キーやリージョンなどの機微なデータを格納する環境変数に依存します。 python コード ファイルには、ホスト コンピューター環境変数から割り当てられた 2 つの値 (`SPEECH__SUBSCRIPTION__KEY` と `SPEECH__SERVICE__REGION`) が含まれています。 これらの変数はどちらもグローバル スコープにあるので、コード ファイルの関数定義内でアクセスできます。 環境変数の詳細については、「[環境変数とアプリケーション構成](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)」を参照してください。

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>ソース言語を変更する

音声翻訳の一般的なタスクの 1 つは、入力 (またはソース) 言語を指定することです。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 コードで、[`SpeechTranslationConfig`][config] インスタンスと対話して、`speech_recognition_language` プロパティに割り当てます。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

[`speech_recognition_language`][recognitionlang] プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている [ロケールまたは言語](../../../language-support.md)のリストの **ロケール** 列に任意の値を指定できます。

## <a name="add-translation-language"></a>翻訳言語を追加する

音声翻訳のもう 1 つの一般的なタスクは、対象の翻訳言語を指定することです。少なくとも 1 つが必要ですが、複数もサポートされています。 次のコード スニペットでは、フランス語とドイツ語の両方が翻訳言語ターゲットとして設定されています。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

[`add_target_language`][addlang] を呼び出すたびに、新しいターゲット翻訳言語が指定されます。 つまり、音声がソース言語から認識される場合は、各ターゲット翻訳を結果の翻訳操作の一部として利用できます。

## <a name="initialize-a-translation-recognizer"></a>翻訳認識エンジンを初期化する

[`SpeechTranslationConfig`][config] を作成したら、次の手順として、[`TranslationRecognizer`][recognizer] を初期化します。 [`TranslationRecognizer`][recognizer] を初期化する場合は、それを自分の `translation_config` に渡す必要があります。 構成オブジェクトにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`TranslationRecognizer`][recognizer] は次のようになります。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`][audioconfig] を作成し、[`TranslationRecognizer`][recognizer] を初期化するときに `audio_config` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

まず、`AudioConfig` オブジェクトを次のように参照します。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、[`AudioConfig`][audioconfig] を作成する場合は、`use_default_microphone=True` を呼び出す代わりに `filename="path-to-file.wav"` を呼び出して `filename` パラメーターを提供します。

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>音声を翻訳する

音声を翻訳するために、Speech SDK はマイクまたはオーディオ ファイル入力に依存します。 音声認識は、音声翻訳の前に行われます。 すべてのオブジェクトが初期化されたら、recognize-once 関数を呼び出して結果を取得します。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

音声テキスト変換の詳細については、[音声認識の基礎](../../../get-started-speech-to-text.md)に関する記事を参照してください。

## <a name="synthesize-translations"></a>翻訳の合成

音声認識と翻訳が正常に完了すると、結果にはディクショナリ内のすべての翻訳が含まれます。 [`translations`][translations] ディクショナリ キーはターゲットの翻訳言語で、値は翻訳されたテキストです。 認識された音声は翻訳可能で、別の言語で合成できます (音声間)。

### <a name="event-based-synthesis"></a>イベントベースの合成

`TranslationRecognizer` オブジェクトによって `Synthesizing` イベントが公開されます。 イベントは複数回発生し、合成されたオーディオを翻訳認識結果から取得するためのメカニズムが提供されます。 複数の言語に翻訳する場合は、「[手動合成](#manual-synthesis)」を参照してください。 合成音声を指定するには、[`voice_name`][voicename] を割り当て、`Synthesizing` イベントのイベント ハンドラーを提供して、オーディオを取得します。 次の例では、翻訳されたオーディオを *.wav* ファイルとして保存します。

> [!IMPORTANT]
> イベントベースの合成は、1 つの翻訳でのみ機能し、複数のターゲット翻訳言語を追加 **しません**。 また、[`voice_name`][voicename] は、ターゲット翻訳言語と同じ言語である必要があります。たとえば、`"de"` は `"de-DE-Hedda"` にマップされる可能性があります。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>手動合成

[`translations`][translations] ディクショナリを使用すると、翻訳テキストからオーディオを合成できます。 各翻訳を反復処理し、翻訳を合成します。 `SpeechSynthesizer` インスタンスを作成する場合、`SpeechConfig` オブジェクトの [`speech_synthesis_voice_name`][speechsynthesisvoicename] プロパティを目的の音声に設定する必要があります。 次の例では、5 つの言語に翻訳し、各翻訳を対応するニューラル言語のオーディオ ファイルに合成します。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

音声合成の詳細については、[音声合成の基礎](../../../get-started-text-to-speech.md)に関する記事を参照してください。

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name