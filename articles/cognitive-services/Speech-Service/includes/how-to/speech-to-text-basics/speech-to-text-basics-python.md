---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 891d86f9429031be48ed17f83a3a5005cadb1ec1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947342"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して文字起こしをする機能です (多くの場合、音声テキスト変換と呼ばれます)。 このクイックスタートでは、アプリや製品で Speech SDK を使用し、高品質の音声テキスト変換を実行する方法について説明します。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [Python クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事では、以下のことを前提としています。

* Azure アカウントと Speech Service のサブスクリプションをお持ちになっています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-and-import-the-speech-sdk"></a>Speech SDK をインストールしてインポートする

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。

```Python
pip install azure-cognitiveservices-speech
```

macOS を使用していて、インストールの問題が発生した場合は、まず次のコマンドを実行することが必要な場合があります。

```Python
python3 -m pip install --upgrade pip
```

Speech SDK がインストールされたら、それを Python プロジェクトにインポートします。

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。 キーとリージョンを使用して [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) を作成します。 キーとリージョンのペアを見つけるには、「[キーとリージョンを見つける](../../../overview.md#find-keys-and-region)」ページを参照してください。

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
```

[`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) を初期化するには、他にも次に示すようないくつかの方法があります。

* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

## <a name="recognize-from-microphone"></a>マイクから認識する

デバイス マイクを使用して音声を認識するには、`AudioConfig` を渡さずに `SpeechRecognizer` を作成し、`speech_config` を渡します。

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

"*特定の*" オーディオ入力デバイスを使用したい場合、`AudioConfig` でデバイス ID を指定し、それを `SpeechRecognizer` コンストラクターの `audio_config` パラメーターに渡す必要があります。 自分のオーディオ入力デバイスの[デバイス ID を取得する方法](../../../how-to-select-audio-input-devices.md)をご覧ください。

## <a name="recognize-from-file"></a>ファイルから認識する

マイクを使用するのでなくオーディオ ファイルから音声を認識する場合、[`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig) を作成し、`filename` パラメーターを使用します。

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>エラー処理

これまでの例では単に、認識されたテキストを `result.text` から取得していましたが、エラーやその他の応答を処理するためには、結果を処理するなんらかのコードを記述する必要があります。 以下のコードでは、[`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason) プロパティを評価したうえで、次の処理を行っています。

* 認識結果を出力します: `speechsdk.ResultReason.RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `speechsdk.ResultReason.NoMatch `
* エラーが検出された場合は、エラー メッセージを出力します: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>継続的認識

これまでの例では、1 つの発話を認識する単発の認識を使用してきました。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。

一方、認識を停止するタイミングを **制御** したい場合は、継続的認識を使用します。 認識結果を取得するには `EventSignal` に接続する必要があります。認識を停止するには、[stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) または [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition-async--) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

入力を定義し、[`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer) を初期化することから始めましょう。

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

次に、音声認識の状態を管理する変数を作成してみましょう。 開始するには、これを `False` に設定します。認識の開始時に、それが間違いなく完了していないと見なすことができるからです。

```Python
done = False
```

次に、`evt` が受信されたときに、継続的認識を停止するコールバックを作成します。 注意すべき点がいくつかあります。

* `evt` が受信されると、`evt` メッセージが出力されます。
* `evt` が受信されたら、認識を停止するために [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#stop-continuous-recognition--) が呼び出されます。
* 認識状態が `True` に変更されます。

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

以下のコード サンプルでは、[`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#start-continuous-recognition--) から送信されたイベントにコールバックを接続する方法を示します。

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognizing): 中間的な認識結果を含むイベントのシグナル。
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#recognized): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-started): 認識セッション (操作) の開始を示すイベントのシグナル。
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#canceled): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

すべてが設定されると、[start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer#session-stopped) を呼び出すことができます。

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) 上で [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#enable-dictation--) メソッドを使用します。

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をドイツ語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の SpeechConfig を見つけて、そのすぐ下に次の行を追加します。

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-recognition-language) は、引数として文字列を取るパラメーターです。 サポートされている[ロケールまたは言語](../../../language-support.md)のリストに任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズのリストを提供することにより、音声認識の精度を向上させることができます。

例として、"Move to" という指示があり、会話で考えられる目的地として "Ward" がある場合、"Move to Ward" というエントリーを追加できます。 フレーズを追加すると、音声認識の実行時に、"Move toward" ではなく "Move to Ward" が認識される可能性が高くなります。

フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識中、フレーズのリスト内のエントリは、発話の途中にエントリが現れた場合でも単語またはフレーズの認識を強化するために使用されます。 

> [!IMPORTANT]
> フレーズのリストの機能は、en-US、de-DE、en-AU、en-CA、en-GB、es-ES、es-MX、fr-CA、fr-FR、it-IT、ja-JP、ko-KR、pt-BR、zh-CN の言語で使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) オブジェクトを作成します。次に、[`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar#addphrase-phrase--str-) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

ご利用の語句リストをクリアする必要がある場合は、次のようにします。 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>認識の精度を向上させるためのその他のオプション

フレーズ リストは、認識の精度を向上させるためのオプションの 1 つにすぎません。 次のこともできます。 

* [Custom Speech を使用して精度を向上させる](../../../custom-speech-overview.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)
