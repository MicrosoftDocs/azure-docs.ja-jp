---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: cc5e306aa9677c7370d03dbb26ef3fe69293a630
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180071"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して文字起こしをする機能です (多くの場合、音声テキスト変換と呼ばれます)。 このクイックスタートでは、アプリや製品で Speech SDK を使用し、高品質の音声テキスト変換を実行する方法について説明します。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [JavaScript クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node)を参照してください。

または、ブラウザーベースの環境で Speech SDK を使用する方法については、[React サンプル](https://github.com/Azure-Samples/AzureSpeechReactSample)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何か実行するには、事前に Node.js 用 Speech SDK をインストールしておく必要があります。 パッケージ名のインストールだけが必要な場合は、`npm install microsoft-cognitiveservices-speech-sdk` を実行します。 ガイド付きのインストール手順については、[作業開始](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet%2Clinux%2Cjre%2Cnodejs&pivots=programming-language-javascript)に関する記事を参照してください。

SDK をインポートするには、次の `require` ステートメントを使用します。

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require` の詳細については、[require に関するドキュメント](https://nodejs.org/en/knowledge/getting-started/what-is-require/)を参照してください。

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。 キーとリージョンを使用して [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) を作成します。 キーとリージョンのペアを見つけるには、「[キーとリージョンを見つける](../../../overview.md#find-keys-and-region)」ページを参照してください。

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

[`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) を初期化するには、他にも次に示すようないくつかの方法があります。

* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

## <a name="recognize-from-microphone-browser-only"></a>マイクから認識する (ブラウザーのみ)

マイクからの音声認識は、**Node.js ではサポートされておらず**、ブラウザー ベースの JavaScript 環境でのみサポートされています。 [マイクからの音声変換の実装](https://github.com/Azure-Samples/AzureSpeechReactSample/blob/main/src/App.js#L29)を確認するには、GitHub の [React サンプル](https://github.com/Azure-Samples/AzureSpeechReactSample)を参照してください。

> [!NOTE]
> "*特定の*" オーディオ入力デバイスを使用したい場合、`AudioConfig` でデバイス ID を指定する必要があります。 自分のオーディオ入力デバイスの[デバイス ID を取得する方法](../../../how-to-select-audio-input-devices.md)をご覧ください。

## <a name="recognize-from-file"></a>ファイルから認識する 

Node.js でオーディオ ファイルから音声を認識するには、プッシュ ストリームを使用する他の設計パターンが必要となります。JavaScript の `File` オブジェクトを Node.js ランタイムで使用することはできないためです。 コード例を次に示します。

* `createPushStream()` を使用してプッシュ ストリームを作成します
* 読み取りストリームを作成して `.wav` ファイルを開き、それをプッシュ ストリームに書き込みます
* プッシュ ストリームを使用してオーディオ構成を作成します

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

プッシュ ストリームを入力として使用する場合、音声データは生の PCM であることが前提となります (ヘッダーをスキップするなど)。
場合によっては、ヘッダーがスキップされていなくても、API は正しく機能します。しかし、最良の結果を得るためには、`fs` が "*音声データの開始*" 位置から始まるよう、ヘッダーを読み取るロジックの実装を検討してください。

## <a name="error-handling"></a>エラー処理

これまでの例では単に、認識されたテキストを `result.text` から取得していましたが、エラーやその他の応答を処理するためには、結果を処理するなんらかのコードを記述する必要があります。 以下のコードでは、[`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) プロパティを評価したうえで、次の処理を行っています。

* 認識結果を出力します: `ResultReason.RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `ResultReason.NoMatch`
* エラーが検出された場合は、エラー メッセージを出力します: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>継続的認識

これまでの例では、1 つの発話を認識する単発の認識を使用してきました。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。

一方、認識を停止するタイミングを **制御** したい場合は、継続的認識を使用します。 この場合は、認識結果を取得するために、`Recognizing`、`Recognized`、`Canceled` の各イベントをサブスクライブする必要があります。 認識を停止するには、[`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

まず、入力を定義し、[`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) を初期化します。

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
```

次に、[`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) から送信されたイベントをサブスクライブします。

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): 中間的な認識結果を含むイベントのシグナル。
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

すべての設定が完了したら、[`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) を呼び出して認識を開始します。

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) 上で [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) メソッドを使用します。

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) を見つけて、そのすぐ下に次の行を追加します。

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage) プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている [ロケールまたは言語](../../../language-support.md)のリストの **ロケール** 列に任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズのリストを提供することにより、音声認識の精度を向上させることができます。

例として、"Move to" という指示があり、会話で考えられる目的地として "Ward" がある場合、"Move to Ward" というエントリーを追加できます。 フレーズを追加すると、音声認識の実行時に、"Move toward" ではなく "Move to Ward" が認識される可能性が高くなります。

フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識中、フレーズのリスト内のエントリは、発話の途中にエントリが現れた場合でも単語またはフレーズの認識を強化するために使用されます。 

> [!IMPORTANT]
> フレーズのリストの機能は、en-US、de-DE、en-AU、en-CA、en-GB、es-ES、es-MX、fr-CA、fr-FR、it-IT、ja-JP、ko-KR、pt-BR、zh-CN の言語で使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) オブジェクトを作成します。次に、[`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

ご利用の語句リストをクリアする必要がある場合は、次のようにします。

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>認識の精度を向上させるためのその他のオプション

フレーズ リストは、認識の精度を向上させるためのオプションの 1 つにすぎません。 次のこともできます。 

* [Custom Speech を使用して精度を向上させる](../../../custom-speech-overview.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)
