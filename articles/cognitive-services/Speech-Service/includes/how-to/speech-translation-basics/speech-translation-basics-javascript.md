---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-javascript
ms.openlocfilehash: 6fe89e36685a2db0bf7b570ab6e7150bacf34857
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405835"
---
## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK for JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web ブラウザー <span class="docon docon-navigate-external x-hidden-focus"></span></a>

また、ターゲット環境によっては、次のいずれかを使用します。

# <a name="script"></a>[script](#tab/script)

<a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK for JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* ファイルをダウンロードして抽出し、HTML ファイルにアクセス可能なフォルダーに配置します。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Web ブラウザーを対象としていて、`<script>` タグを使用する場合は、`sdk` プレフィックスは必要ありません。 `sdk` プレフィックスは、`require` モジュールに名前を付けるために使用されるエイリアスです。

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

`import` の詳細については、「<a href="https://javascript.info/import-export" target="_blank">export および import <span class="docon docon-navigate-external x-hidden-focus"></span></a>」を参照してください。

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require` の詳細については、「<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">require とは<span class="docon docon-navigate-external x-hidden-focus"></span></a>」を参照してください。

---

## <a name="create-a-translation-configuration"></a>翻訳構成を作成する

Speech SDK を使用して翻訳サービスを呼び出すには、[`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。
[`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

キーとリージョンを使用して [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) を作成する方法を見てみましょう。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>翻訳ツールを初期化する

[`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) を作成したら、次の手順として、[`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) を初期化します。 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) を初期化する場合は、それを自分の `speechTranslationConfig` に渡す必要があります。 これにより、翻訳サービスによってお客様の要求を検証するために必要とされる資格情報が提供されます。

ご利用のデバイスの既定のマイクから提供された音声を翻訳している場合、[`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) は次のようになります。

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) を作成し、[`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) を初期化するときに `audioConfig` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。
`AudioConfig` オブジェクトを次のように参照します。

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、これを行うことができるのは **Node.js** を対象とする場合のみであり、[`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) を作成するときに、`fromDefaultMicrophoneInput`を呼び出すのではなく、`fromWavFileOutput` を呼び出して `filename` パラメーターを渡します。

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>音声を翻訳する

Speech SDK for JavaScript の [TranslationRecognizer クラス](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest)では、音声翻訳に使用できるいくつかの手法が公開されています。

* 単発の翻訳 (非同期) - 非ブロッキング (非同期) モードで翻訳を実行します。 これにより、1 つの発話が翻訳されます。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。
* 継続的翻訳 (非同期) - 継続的な翻訳操作を非同期に開始します。 ユーザーはイベントに登録し、さまざまなアプリケーションの状態を処理します。 非同期の継続的翻訳を停止するには、[`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync) を呼び出します。

> [!NOTE]
> [音声認識モードを選択](../../../how-to-choose-recognition-mode.md)する方法の詳細情報。
### <a name="specify-a-target-language"></a>対象言語を指定する

翻訳するには、ソース言語と少なくとも 1 つのターゲット言語の両方を指定する必要があります。
[音声翻訳の一覧表](../../../language-support.md#speech-translation)に記載されたロケールを使用して、ソース言語を選択できます。 同じリンクで、翻訳対象言語のオプションを探します。 テキストを表示する場合、または合成された翻訳対象の音声を再生する場合には、ターゲット言語のオプションが異なります。 英語からドイツ語に翻訳するには、翻訳の構成オブジェクトを次のように変更します。

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>単発の認識

[`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognizeonceasync) を使用した非同期の単発翻訳の例を次に示します。

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

結果を処理するコードを記述する必要があります。 このサンプルでは、ドイツ語に翻訳するための [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?view=azure-node-latest) を評価しています。

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

コードでは、翻訳が処理されている間に提供される更新情報を扱うこともできます。
これらの更新情報を利用して、翻訳の進行状況についての視覚的なフィードバックを提供できます。
翻訳の処理中に提供された更新情報を表示するサンプル コードについては、[こちらの JavaScript Node.js の例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js)を参照してください。 次のコードでは、翻訳の処理中に生成された詳細情報も表示されています。

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>継続的翻訳

継続的翻訳は、単発の認識よりも少し複雑です。 この場合は、認識結果を取得するために、`recognizing`、`recognized`、`canceled` の各イベントをサブスクライブする必要があります。 翻訳を停止するには、[`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的翻訳を実行する方法の例を次に示します。

入力を定義し、[`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) を初期化することから始めましょう。

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

[`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) から送信されたイベントをサブスクライブします。

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognizing):中間的な翻訳結果を含むイベントのシグナル。
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognized):最終的な翻訳結果を含むイベントのシグナル (翻訳の試行が成功したことを示す)。
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#sessionstopped):翻訳セッション (操作) の終了を示すイベントのシグナル。
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#canceled):キャンセルされた翻訳結果を含むイベントのシグナル (結果としてキャンセルされた翻訳の試行または直接的なキャンセル要求、あるいは、転送またはプロトコルの失敗を示す)。

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
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

すべてが設定されると、[`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#startcontinuousrecognitionasync) を呼び出すことができます。

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>ソース言語を選択する

音声翻訳の 1 つの一般的なタスクに、入力 (またはソース) 言語の指定があります。 入力言語をイタリア語に変更する場合の方法を見てみましょう。 コード内で、使用している [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) を見つけて、そのすぐ下に次の行を追加します。

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest#speechrecognitionlanguage) プロパティには、言語ロケールの書式指定文字列が必要です。 サポートされている[ロケールまたは言語](../../../language-support.md)のリストの**ロケール**列に任意の値を指定できます。

## <a name="choose-one-or-more-target-languages"></a>1 つ以上のターゲット言語を選択する

Speech SDK によって、複数のターゲット言語を並列で翻訳することができます。 利用可能なターゲット言語は、ソース言語の一覧とはやや異なります。また、ロケールではなく言語コードを使用して、ターゲット言語を指定します。
[言語サポートのページにある音声翻訳の一覧](../../../language-support.md#speech-translation)で、テキストのターゲットに対応する言語コードの一覧を確認してください。 合成された言語への翻訳についても、そこで詳細を確認できます。

次のコードでは、ターゲット言語としてドイツ語を追加しています。

```javascript
translationConfig.addTargetLanguage("de");
```

複数のターゲット言語への翻訳が可能なので、コード上では、結果を検証するときのターゲット言語を指定する必要があります。 次のコードでは、ドイツ語の翻訳結果を取得しています。

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
