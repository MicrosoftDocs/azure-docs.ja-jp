---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 931fcd113f883f126c2f94e6f3d06684b9a0e772
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407636"
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


## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

この例では、サブスクリプション キーとリージョンを使用して [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) を作成します。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。 また、この記事の残りの部分で使用する、基本的な定型コードをいくつか作成します。これを変更して、さまざまなカスタマイズを行います。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次に、[`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) オブジェクトを作成します。これにより、テキストから音声への変換と、スピーカー、ファイル、またはその他の出力ストリームへの出力が実行されます。 [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) は、前の手順で作成された [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) オブジェクト、および出力結果の処理方法を指定する [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) オブジェクトをパラメーターとして受け取ります。

まず、`fromAudioFileOutput()` 静的関数を使用して `.wav` ファイルに出力を自動的に書き込む `AudioConfig` を作成します。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

次に、`speechConfig` オブジェクトと `audioConfig` オブジェクトをパラメーターとして渡す `SpeechSynthesizer` をインスタンス化します。 こうすることで、音声合成を実行してファイルに書き込むことが、テキスト文字列を使用して `speakTextAsync()` を実行するのと同じぐらい簡単になります。 結果のコールバックは `synthesizer.close()` を呼び出すのに最適な場所です。実際、合成が正しく機能するためにはこの呼び出しが必要です。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

このプログラムを実行すると、合成された `.wav` ファイルが、指定した場所に書き込まれます。 以上は最も基本的な使用方法の好例ですが、この次は、カスタム シナリオに対応できるよう、出力をカスタマイズし、出力応答をインメモリ ストリームとして処理する方法について説明します。

## <a name="synthesize-to-speaker-output"></a>スピーカー出力に合成する

場合によっては、合成された音声をスピーカーに直接出力することが必要になる場合があります。 これを行うには、`fromDefaultSpeakerOutput()` 静的関数を使用して `AudioConfig` をインスタンス化します。 これにより、現在のアクティブな出力デバイスに対して出力が行われます。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>結果をインメモリ ストリームとして取得する

音声アプリケーション開発の多くのシナリオでは、結果として得られたオーディオ データは、ファイルに直接書き込むのではなく、インメモリ ストリームとして必要となるケースがよくあります。 その場合、次のようなカスタム動作を構築できます。

* 結果として得られたバイト配列を、カスタム ダウンストリーム サービス向けのシーク可能なストリームとして抽象化する。
* 結果を他の API またはサービスと統合する。
* オーディオ データの変更やカスタム `.wav` ヘッダーの記述などを行う。

この変更は、前の例から簡単に行うことができます。 まず、`AudioConfig` ブロックを削除します。これは、この時点から出力動作を手動で管理して制御を強化するためです。 次に、`SpeechSynthesizer` コンストラクターの `AudioConfig` に `undefined` を渡します。 

> [!NOTE]
> 前述のスピーカー出力の例のように省略するのではなく、`AudioConfig` に `undefined` を渡した場合、既定ではオーディオは現在のアクティブな出力デバイスで再生されません。

今回は、結果を [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) 変数に保存します。 `SpeechSynthesisResult.audioData` プロパティは、出力データの `ArrayBuffer` を返します。 この `ArrayBuffer` は手動で操作できます。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

ここから、結果として得られた `ArrayBuffer` オブジェクトを使用して、任意のカスタム動作を実装できます。

## <a name="customize-audio-format"></a>オーディオ形式をカスタマイズする

次のセクションでは、次のようなオーディオ出力属性をカスタマイズする方法について説明します。

* オーディオ ファイルの種類
* サンプルレート
* ビット深度

オーディオ形式を変更するには、`SpeechConfig` オブジェクトで `speechSynthesisOutputFormat` プロパティを使用します。 このプロパティには、[`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) 型の `enum` が必要です。これは、出力形式を選択するために使用します。 使用できる[オーディオ形式の一覧](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)については、リファレンス ドキュメントを参照してください。

要件に応じて、ファイルの種類ごとにさまざまなオプションがあります。 定義上、`Raw24Khz16BitMonoPcm` のような未加工の形式にはオーディオ ヘッダーが含まれないことに注意してください。 未加工の形式は、ダウンストリームの実装で未加工のビットストリームをデコードできることがわかっている場合か、ビット深度、サンプル レート、チャネル数などに基づいてヘッダーを手動で作成する場合にのみ使用してください。

この例では、`SpeechConfig` オブジェクトに `speechSynthesisOutputFormat` を設定することにより、高忠実度の RIFF 形式 `Riff24Khz16BitMonoPcm` を指定します。 前のセクションの例と同様に、オーディオ `ArrayBuffer` データを取得して操作します。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

プログラムをもう一度実行すると、指定したパスに `.wav` ファイルが書き込まれます。

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML を使用して音声の特徴をカスタマイズする

音声合成マークアップ言語 (SSML) を使用すると、XML スキーマから要求を送信して、テキスト読み上げ出力のピッチ、発音、読み上げ速度、ボリュームなどを微調整することができます。 このセクションでは実用的な使用例をいくつか紹介しますが、詳細なガイドについては、[SSML の操作方法に関する記事](../../../speech-synthesis-markup.md)を参照してください。

SSML を使用したカスタマイズを開始するには、音声を切り替える単純な変更を加えます。
まず、ルート プロジェクト ディレクトリに SSML 構成用の新しい XML ファイルを作成します (この例では `ssml.xml`)。 ルート要素は常に `<speak>` であり、テキストを `<voice>` 要素でラップすることで、`name` パラメーターを使用して音声を変更できます。 この例では、音声を男性の英語 (英国) の音声に変更します。 この音声が**標準**音声であることに注意してください。これは、**ニューラル**音声とは価格や可用性が異なります。 サポートされている**標準**音声の[全一覧](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices)を参照してください。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

次に、XML ファイルを参照するように音声合成要求を変更する必要があります。 要求はほとんど同じですが、`speakTextAsync()` 関数を使用する代わりに、`speakSsmlAsync()` を使用します。 この関数には XML 文字列が必要なので、最初に、XML ファイルを読み込んでそれを文字列として返す関数を作成します。

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

`readFileSync` の詳細については、「<a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js ファイル システム<span class="docon docon-navigate-external x-hidden-focus"></span></a>」を参照してください。 ここからは、結果のオブジェクトは前の例とまったく同じです。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

出力は機能しますが、さらに自然に聞こえるように、いくつかの簡単な変更を加えることができます。 話す速度が全体的に少し速すぎるため、`<prosody>` タグを追加して、速度を既定速度の **90%** に下げます。 また、文中のコンマの後の一時停止が少し短すぎて、不自然に聞こえます。 この問題を解決するには、`<break>` タグを追加して音声を遅らせ、time パラメーターを **200ms** に設定します。 合成を再実行して、これらのカスタマイズが出力にどのように影響したかを確認します。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>ニューラル音声

ニューラル音声とは、ディープ ニューラル ネットワークを使用した音声合成アルゴリズムです。 ニューラル音声を使用した場合、合成音声は人間の録音とほとんど区別がつきません。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムと対話する際のリスニング疲労が大幅に軽減されます。

ニューラル音声に切り替えるには、`name` を[ニューラル音声オプション](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)のいずれかに変更します。 次に、`mstts` の XML 名前空間を追加し、テキストを `<mstts:express-as>` タグ内にラップします。 読み上げスタイルをカスタマイズするには、`style` パラメーターを使用します。 この例では `cheerful` を使用していますが、`customerservice` または `chat` に設定して、読み上げスタイルの違いを確認してみてください。

> [!IMPORTANT]
> ニューラル音声は、"*米国東部*"、"*東南アジア*"、"*西ヨーロッパ*" のリージョンで作成された音声リソースで**のみ**サポートされています。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
