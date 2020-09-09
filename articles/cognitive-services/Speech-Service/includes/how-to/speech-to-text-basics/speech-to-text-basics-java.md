---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 314617554abf8fee430e47eb4b0a0ca5db5bc75f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87374942"
---
## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java ランタイム<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

キーとリージョンを使用して [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) を作成する方法を見てみましょう。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>認識エンジンを初期化する

[`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) を作成したら、次の手順として、[`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) を初期化します。 [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) を初期化する場合は、それを自分の `config` に渡す必要があります。 これにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) は次のようになります。

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) を作成し、[`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) を初期化するときに `audioConfig` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

まず、次の `import` ステートメントを追加します。

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

これで、`AudioConfig` オブジェクトを次のように参照できるようになります。

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、`fromDefaultMicrophoneInput` を呼び出すのではなく、[`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) を作成する場合は、`fromWavFileOutput` を呼び出して `filename` パラメーターを渡します。

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>音声を認識する

Speech SDK for Java 用の [認識エンジン](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) では、音声認識に使用できるいくつかの手法が公開されています。

* 単発の認識 (非同期) - 非ブロッキング (非同期) モードで認識を実行します。 これにより、1 つの発話が認識されます。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。
* 継続的認識 (非同期) - 継続的な認識操作を非同期に開始します。 マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 非同期の継続的認識を停止するには、[stopContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) を呼び出します。

> [!NOTE]
> [音声認識モードを選択](../../../how-to-choose-recognition-mode.md)する方法の詳細情報。

### <a name="single-shot-recognition"></a>単発の認識

[`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable) を使用した非同期の単発認識の例を次に示します。

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

結果を処理するコードを記述する必要があります。 このサンプルでは、[`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable) を評価します。

* 認識結果を出力します: `ResultReason.RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `ResultReason.NoMatch`
* エラーが検出された場合は、エラー メッセージを出力します: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>継続的認識

継続的認識は、単発の認識よりも少し複雑です。 この場合は、認識結果を取得するために、`recognizing`、`recognized`、`canceled` の各イベントをサブスクライブする必要があります。 認識を停止するには、[`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

入力を定義し、[`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) を初期化することから始めましょう。

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

次に、音声認識の状態を管理する変数を作成してみましょう。 まず、クラス スコープで `Semaphore` を宣言します。

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

[`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) から送信されたイベントをサブスクライブします。

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): 中間的な認識結果を含むイベントのシグナル。
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

すべてが設定されると、[`startContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync) を呼び出すことができます。

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) 上で [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) メソッドを使用します。

```java
config.enableDictation();
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をフランス語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) を見つけて、そのすぐ下に次の行を追加します。

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable) は、引数として文字列を取るパラメーターです。 サポートされている[ロケールまたは言語](../../../language-support.md)のリストに任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

Speech SDK を使用して認識の精度を向上させるには、いくつかの方法があります。 フレーズ リストを見てみましょう。 フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識時に、音声にフレーズ全体に対する完全一致が含まれる場合、フレーズ リスト内のエントリが使用されます。 フレーズに対する完全一致が見つからない場合、認識はサポートされません。

> [!IMPORTANT]
> フレーズ リスト機能は英語でのみ使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) オブジェクトを作成します。次に、[`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

ご利用の語句リストをクリアする必要がある場合は、次のようにします。 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>認識の精度を向上させるためのその他のオプション

フレーズ リストは、認識の精度を向上させるためのオプションの 1 つにすぎません。 次のこともできます。 

* [Custom Speech を使用して精度を向上させる](../../../how-to-custom-speech.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)
