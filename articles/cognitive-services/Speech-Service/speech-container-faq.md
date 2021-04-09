---
title: 音声サービスコンテナーの良くある質問(FAQ)
titleSuffix: Azure Cognitive Services
description: Speech コンテナーをインストールして実行します。 テキスト読み上げでは、オーディオストリームをテキストにリアルタイムで書き起こし、アプリケーション、ツール、デバイスで使用したり写すことが可能です。 テキスト読み上げでは、入力テキストが人間のような合成音声に変換されます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 16158b4ecfb46ea9092fe9eeb31cc4dee259b1ab
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573746"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>音声サービスコンテナーの良くある質問(FAQ)

音声サービスコンテナーを利用するにあたって、サポートへのお問い合わせ前に、こちらの良くある質問をご覧ください。 この記事は、一般的なものから専門的な質問を載せております。 回答の詳細を見るには、質問をクリックして下さい。

## <a name="general-questions"></a>一般的な質問

<details>
<summary>
<b>音声コンテナーはどのように作動し、どのようにセットアップすればよいでしょうか</b>？
</summary>

**回答:** 運用クラスターを設定する際は、考慮すべき点があります。 まず、ひとつまたは複数のコンテナーを同じ子mピュータにセットアップすることは、大きな問題ではありません。 問題が発生している場合は、ハードウェア関連も問題である可能性があります;まず、リソースを確認してください。（CPUおよびメモリの仕様）

現時点では、 `ja-JP`コンテナーと最新のモデルについて検討してください。 音響モデルはCPUで最も必要とされるピースであり、一方で言語モデルは最もメモリを必要とします。 使用の基準として、オーディオがリアルタイムで流れる時（マイクからなど）、約0.6 CPUコアが必要となります。 （ファイルからなど）オーディオをリアルタイムよりも早く供給する場合、その使用料は2倍（1.2x コア）になります。 一方で、下記のメモリはデコード用に運用されています。 ファイルキャッシュに格納される言語モデルの実際のフルサイズを *考慮する必要は* ありません。 追加の2GBの`ja-JP``en-US`場合は、(6-7)を超える可能性があります。

メモリが不足しており、複数の言語を配置しようとしているコンピュータがある場合は、ファイルキャッシュがフルになり、OSが強制的にモデルを操作します。文字起こしの転写がある場合、パフォーマンスの低下やその他パフォーマンス実行の妨げになる恐れがあります。

さらに、 [advanced vector extension(AVX2)](speech-container-howto.md#advanced-vector-extension-support)命令セットをもつコンピュータ用の、実行可能ファイルを事前にパッケージ化します。 AVX512命令セット付きのコンピュータは、そのターゲットに対してコード生成が必要となり、10の言語用の10個のコンテナーを起動する際は、CPUが一時的に大幅に消費される可能性があります。 次のようなメッセージがdockerログに記録されます:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

`DECODER MAX_COUNT` 変数を使用して、"*単一*" コンテナー内のデコーダの数を設定できます。 なので、基本的には、SKU (CPU/メモリ) から開始し、最大限に活用する方法を提案可能です。 最適な出発点は、推奨されるホストコンピュータのリソース仕様を参照することです。

<br>
</details>

<details>
<summary>
<b>オンプレミスの音声テキスト変換コンテナーの容量計画とコストの見積もりはどうすればいいでしょうか。</b>
</summary>

**回答:** バッチ処理モードのコンテナーの容量については、各デコーダーが２つのCPUコアを使用して、単一の認識をリアルタイムの 2-3x の処理が可能です。 各コンテナーインスタンスにつき２つ以上の認識を保持することは推奨しませんが、ロードバランサーの背景にある信頼性や可能性を考慮し、コンテナーのインスタンスをさらに多く実行することをお勧めします。

ただし、より多くのデコーダーを用いて各コンテナーインスタンスを実行することは可能です。 例えば、８つのコンピューター（それぞれ２x 以上）の各コンテナーインスタンスにつき、７つのデコーダーをセットアップをし、15xスループットを実行できる場合があります。 Param`DECODER_MAX_COUNT`に注意する必要があります。 極端なケースでは、信頼性と待機時間の問題が発生し、スループットが大幅に増加します。 マイクの場合は、リアルタイムの 1xで表示されます。 単一の場合、全体の使用量は１コアにする必要があります。

バッチ処理モード１K時間/日を処理するシナリオでは、極端な場合、３つのVmが24時間以内に処理することが可能ですが、保証する限りではありません。 スパイク日、フェールオーバー、更新、および最小限のバックアップ/BCPを提供するには、各クラスターに３台、２つ以上のクラスターの代わりに４～５台のコンピュータを推奨します。

ハードフェアについては、標準のAzure VMを `DS13_v2`参照として使用します（書くコアは　２.６　GHz以上であり、AVX２ 命令セットが有効になっている必要があります)。

| インスタンス  | vCPU(s) | RAM    | 一時ストレージ | 従量課金制プラン | Azure ハイブリッド特典利用の1-year の予約（％割引） | Azure ハイブリッド特典利用の3-year の予約（％割引） |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56GiB | 112 GiB      | $0.598/時            | $.0.3528/時 (～41％)                 | $0.2333/hour（～61％）                  |

デザインリファレンス (5 つの Vm の2つのクラスターで 1 K 時間/日のオーディオバッチ処理を処理するため) に基づいて、1-year のハードウェアコストは次のようになります:

> 2 (クラスター) * 5 (クラスターあたりの Vm) * $ 0.3528/時間 * 365 (日数) * 24 (時間) = $ 31K/年

物理マシンにマッピングする場合の一般的な推定は、1 vCPU = 1 物理 CPU コアです。 実際には、1vCPU は1つのコアより強力です。

オンプレミスの場合、これらの追加の要因はすべて次のようになります：

- 物理 CPU の種類とそれに対するコアの数
- 同じボックス/コンピューターで同時に実行されている Cpu の数
- Vm のセットアップ方法
- ハイパースレッディング/マルチスレッドの使用方法
- メモリの共有方法
- OS など

通常は、環境の Azure と同じようにチューニングされていません。 他のオーバーヘッドを考慮して、安全な見積もりは10個の物理 CPU コア = 8 Azure vCPU です。 一般的なCPUには8個のコアしかありません。 オンプレミスのデプロイでは、Azure Vm を使用する場合よりもコストが高くなります。 また、減価償却率も考慮してください。

サービスコストはオンラインサービスと同じです。音声からテキストへの変換では、$ 1/時間です。 音声サービスのコスト：

> $1 * 1000 * 365 = $365K

Microsoft に支払われるメンテナンスコストは、サービスのサービスレベルとコンテンツによって異なります。 これは、ベーシック レベルでは $ 29.99/月から、オンサイトサービスには何百もかかります。 大まかな数値は、サービス/管理の場合は、$ 300/時間です。 スタッフのコストは含まれていません。 その他のインフラストラクチャコスト (ストレージ、ネットワーク、ロードバランサーなど) は含まれていません。

<br>
</details>

<details>
<summary>
<b>句読点から文字起こしが欠落しているのはなぜですか？</b>
</summary>

**回答:** `speech_recognition_language=<YOUR_LANGUAGE>`は、カーボンクライアントを使用している場合は、要求で明示的に構成する必要があります。

次に例を示します。

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
出力内容は次のとおりです。

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>音声コンテナーでカスタム音響モデルと言語モデルを使用できますか？</b>
</summary>

現在、カスタム言語モデルまたはカスタム音響モデルのいずれか１つのモデルIDのみをお渡しすることができます。

**回答:** 音響モデルと言語モデルの両方を同時に *サポートしないように* するか決定します。 これは、APIの中断を減らすために統合識別子が作成されるまで有効となります。 申し訳ありませんが、現時点ではサポートされておりません。

<br>
</details>

<details>
<summary>
<b>カスタムのテキスト読み上げコンテナーから発生する、これらのエラーを説明していただけますか？</b>
</summary>

**エラー1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**回答 1:** 最新のカスタムモデルを使用してトレーニングを行っている場合、現在はサポートされていません。 以前のバージョンでトレーニングする場合は、現在も使用可能です。 現在、最新バージョンのサポートに取り組んでいます。

基本的に、カスタムコンテナーは、Halide または ONNX ベースの音響モデル (カスタムトレーニングポータルでは既定) をサポートしていません。 これは、カスタムモデルが暗号化されておらず、ONNX モデルを公開する必要がないためです。言語モデルに問題はありません。 顧客は、カスタムトレーニングのために古い非 ONNX モデルを明示的に選択する必要があります。 精度に変化はありません。 モデルのサイズは、(100 MB) より大きくなることがあります。

> サポートモデル > 20190220 (v4.5 統合)

**エラー 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**回答 2:** 要求で正しい音声名を指定する必要があります。これは大文字と小文字が区別されます。 完全なサービスマッピング名を参照してください。

**エラー 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**回答 3:** Cognitive Services リソースではなく、音声リソースを作成する必要があります。


<br>
</details>

<details>
<summary>
<b>何のAPIプロトコルがサポートされていますか（RESTまたはWS）</b>
</summary>

**回答:** 読み上げおよびまたはカスタム読み上げコンテナーの場合、現在websocketベースのプロトコルのみがサポートされています。 SDK では、WS での呼び出しのみがサポートされますが、REST はサポートしません。 RESTサポートを追加するように予定がありますが、現在到着予定時はありません。 公式のドキュメントを参照してください。[ クエリ予測エンドポイント を参照してください](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>音声 コンテナーでは CentOS はサポートされていますか。</b>
</summary>

**回答:** CentOS 7は現在　Python SDKのサポートをされておれず、Ubuntu 19.04も同様です。

以下のオペレーティング システム用の Python Speech SDK パッケージを入手できます。
- **Windows** - x64 および x86
- **Mac** - macOS X version 10.12　以降
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 x64

環境設定の詳細については、 [「 Python platform setup 」を参照してください。](quickstarts/setup-platform.md?pivots=programming-language-python) 現時点では、Ubuntu 18.04 が推奨されるバージョンです。

<br>
</details>

<details>
<summary>
<b>LUIS 予測エンドポイントを呼び出そうとするとエラーが発生するのはなぜですか？</b>
</summary>

IoT Edge のデプロイで LUIS コンテナーを使用しており、別のコンテナーから LUIS 予測エンドポイントを呼び出そうとしています。 LUIS コンテナーはポート5001でリッスンしていて、使用している URL は次のようになっています：

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

取得しているエラーは次のとおりです：

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

LUIS コンテナーのログに要求が表示され、次のメッセージが表示されます:

```cmd
The request path /luis//predict" does not match a supported file type.
```

これはどういう意味でしょうか。 何が不足していますか? ここでは、音声SDK の例に従っています。 こちら [を参照してください](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。 このシナリオでは、トレーニングした LUIS アプリに基づいて、オーディオを PC マイクから直接検出し、意図を判断しようとしています。 リンクした例では、まさにこれが行われています。 また、LUIS クラウドベースのサービスでも正常に動作します。 音声SDK を使用すると、音声からテキストへの API を個別に明示的に呼び出す必要がなくなりました。その後、LUIS への2回目の呼び出しを行う必要があります。

そのため、クラウドで LUIS を使用するシナリオから LUIS コンテナーを使用するように切り替えます。 音声SDK が機能しているかどうかは想像できませんが、もう一方では機能しません。

**回答:** LUIS コンテナーに対して音声SDK を使用することはできません。 LUIS コンテナーを使用するには、LUIS SDK または LUIS REST API を使用する必要があります。 音声SDK は、音声コンテナーに対して使用する必要があります。

クラウドは、コンテナーとは異なります。 クラウドは、複数の集約されたコンテナー (マイクロサービスとも呼ばれます) で構成できます。 そのため、LUIS コンテナーと音声コンテナーの2つがあります。 音声コンテナーは音声のみ対応します。 LUIS コンテナーは、LUIS のみ対応します。 クラウドでは、両方のコンテナーが展開されていることがわかっているため、リモートクライアントがクラウドにアクセスし、音声を出して、もう一度クラウドにアクセスして、LUIS を実行すると、クライアントが音声にアクセスしてクラウドにアクセスできるようにする機能を提供し、クライアントに戻ることができます。 したがって、このシナリオでも、音声SDKは音声付きの音声クラウドコンテナーに移動し、音声クラウドコンテナーはテキスト付きのLUISクラウドコンテナーと通信します。 LUIS コンテナーにはオーディオを受け入れるという概念がありません (LUIS コンテナーがストリーミングオーディオを受け入れることは意味がありません-LUIS はテキストベースのサービスです)。 オンプレミスでは、お客様が両方のコンテナーをデプロイしたという確実性はありません。お客様のオンプレミスのコンテナー間で調整する必要はありません。両方のコンテナーがオンプレミスにデプロイされている場合は、最初に SR を使用してクライアントに戻し、そのテキストを取得して LUIS に移動します。

<br>
</details>

<details>
<summary>
<b>MacOS、音声コンテナー、Python SDK でエラーが発生するのはなぜですか。</b>
</summary>

*.wav* ファイルを送信すると、結果は次のように返されます：

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Websocket が正しくセットアップされていることがわかります。

**回答:** その場合は、 [この GitHub の問題 を参照してください](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)。 ここでは、回避策として、[に提案 ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)します。

カーボンはバージョン1.8 で修正しています。


<br>
</details>

<details>
<summary>
<b>音声 コンテナーエンドポイントにはどのような違いがありますか？</b>
</summary>

テストする関数や、SDK と REST APIs をテストする方法など、次のテストメトリックについての情報を入力してください？ 特に、interactiveと メッセージ交換 の違いは、既存のドキュメント/サンプルからは見られませんでした。

| エンドポイント                                                | 機能テスト                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | テキストの合成（テキストから音声）                                  |     | はい      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | オンプレミスのディクテーション v1 の websocket エンドポイントの Cognitive Services        | はい | いいえ       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | オンプレミスの対話型 v1 websocket エンドポイント Cognitive Services  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 認知サービスのオンプレミスの通信 v1 の websocket エンドポイント |     |          |

**回答:** これは、次の fusion です：
- コンテナーのディクテーションエンドポイントを試しているユーザー (その URL がどのようになったかわからない)
- 1<sup> st </sup>パーティエンドポイントがコンテナー内にあります。
- 1<sup>st</sup>パーティエンドポイントは、 `speech.hypothesis`メッセージではなく、speech メッセージを返します。 3<sup> rd</sup> パートエンドポイントは、ディクテーションエンドポイントに対して返します。
- カーボンクイックスタートでは、すべて`RecognizeOnce` (対話モード) が使用されます
- `speech.fragment`メッセージを要求するために、対話モードで返されないメッセージに対するアサートが発生しました。
- リリースビルドでアサートが発生します (プロセスを終了します)。

回避策として、コード内での連続認識の使用に切り替えるか、またはコンテナー内の対話型エンドポイントまたは連続エンドポイントに接続することができます。
コードでは、エンドポイントを `host:port`/speech/recognition/interactive/cognitiveservices/v1 に設定します

さまざまなモードについては、以下の音声モード参照してください：

## <a name="speech-modes---interactive-conversation-dictation"></a>音声モード - 対話型、会話、ディクテーション

[!INCLUDE [speech-modes](includes/speech-modes.md)]

適切な修正は、オンプレミスのサポートがある SDK 1.8 で行われます (適切なエンドポイントが選択されるため、オンラインサービスよりも悪いことはありません)。 それまでの間は、継続的な認識のためのサンプルがあります。それを指していないのはなぜですか。

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>さまざまなオーディオファイルに、どのモードを使用すればよいですか。</b>
</summary>

**回答:** [Python を使用した](./get-started-speech-to-text.md?pivots=programming-language-python)クイックスタートを次に示します。 Docs サイトにリンクされている他の言語を見つけることができます。

対話型、会話、ディクテーションを明確にします。これは、サービスが音声要求を処理する特定の方法を指定するための高度な方法です。 申し訳ありませんが、オンプレミスのコンテナーについては、(ローカルコンピューターを含む) 完全な URI を指定する必要があるため、この情報は抽象化から漏洩します。 SDK チームと協力して、今後も使用できるようにしています。

<br>
</details>

<details>
<summary>
<b>トランザクション/秒/コアの大まかな尺度をベンチマークするにはどうすればよいでしょうか。</b>
</summary>

**回答:** 次に、既存のモデルから予想されるいくつかの大まかな数値を示します (GA で提供されるものよりも適切なものに変更されます)：

- ファイルの場合、調整は speech SDK の2x になります。 最初の5秒間のオーディオは調整されません。 デコーダーはリアルタイム約3x の速さで実行できます。 この場合、単一の認識のために全体的な CPU 使用率が2コアに近くなります。
- mic の場合、これは1x のリアルタイムで実行されます。 単一の認識の場合、全体的な使用量は約1コアである必要があります。

これはすべて docker ログから検証できます。 実際には、セッションと語句/(発話) 統計を使用して行をダンプします。これには、RTF 番号が含まれています。

<br>
</details>

<details>
<summary>
<b>同じホスト上で、どのように複数のコンテナーを実行すればよいでしょうか。</b>
</summary>

このドキュメントでは、別のポートを公開するように指定していますが、LUIS コンテナーはポート5000でリッスンしていますか。

**回答:** `-p <outside_unique_port>:5000`をお試しください。 たとえば、「 `-p 5001:5000` 」のように入力します。


<br>
</details>

## <a name="technical-questions"></a>技術的なご質問

<details>
<summary>
<b>Batch 以外の APIs を使用してオーディオ&lt;15 秒間処理する方法はありますか。</b>
</summary>

**回答:** 対話モードの `RecognizeOnce()` は、最大 15 秒のオーディオのみを処理します。このモードは発話が短いことが予想される音声のコマンド実行を対象としているためです。 ディクテーションまたは会話に `StartContinuousRecognition()` を使用する場合、15 秒の制限はありません。


<br>
</details>

<details>
<summary>
<b>50同時要求の場合、推奨リソース、CPU、および RAM とは何ですか。</b>
</summary>

4コアの 4 GB RAM では、同時要求の数はどれくらいですか。 たとえば、50の同時要求を処理する必要がある場合、コアと RAM の数を推奨しますか。

**回答:** 最新の`en-US`を使用すると、リアルタイムで8個の同時要求を超えて、より多くの docker コンテナー6を使用することをお勧めします。 16コアを超える crazier を取得し、non-uniform memory access (NUMA) ノードに依存するようになります。 次の表に、各 Speech コンテナーに割り当てるリソースの最小値と推奨値を示します。

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

| コンテナー      | 最小値             | 推奨         |
|----------------|---------------------|---------------------|
| 音声テキスト変換 | 2 コア、2 GB のメモリ | 4 コア、4 GB メモリ |

# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

| コンテナー             | 最小値             | 推奨         |
|-----------------------|---------------------|---------------------|
| カスタム音声変換 | 2 コア、2 GB のメモリ | 4 コア、4 GB メモリ |

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

| コンテナー      | 最小値             | 推奨         |
|----------------|---------------------|---------------------|
| テキスト読み上げ | 1 コア、2 GB メモリ | 2 コア、3 GB のメモリ |

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

| コンテナー             | 最小値             | 推奨         |
|-----------------------|---------------------|---------------------|
| カスタム テキスト読み上げ | 1 コア、2 GB メモリ | 2 コア、3 GB のメモリ |

***

- 各コアは 2.6 GHz 以上にする必要があります。
- ファイルの場合、スロットルは音声SDK の2x になります (最初の5秒間のオーディオは調整されません)。
- デコーダーは、リアルタイム訳 2-3x の速さで実行できます。 このため、単一の認識のために、全体的な CPU 使用率が2コアに近くなります。 そのため、コンテナーインスタンスごとに2つ以上のアクティブな接続を保持しないことをお勧めします。 極端な場合は、`DS13_V2`のように、8つのコアコンピューターに約10個のデコーダーを2x のリアルタイムで配置することです。 コンテナーバージョン1.3 以降には、 `DECODER_MAX_COUNT=20`設定を試すことができるパラメーターがあります。
- マイクの場合は、1X リアルタイムの速さになります。 単一の場合、全体の使用量は１コアにする必要があります。

オーディオの合計時間数を考慮してください。 この数が大きい場合は、信頼性と可用性を向上させるために、1つのボックスまたは複数のボックスで、ロードバランサーの背後にあるコンテナーのインスタンスをさらに実行することをお勧めします。 オーケストレーションは、Kubernetes (K8S) とHelmを使用して、または Docker の作成によって行うことができます。

例えば、1000時間/24 時間に対応するために、VM あたり10個のインスタンス/デコーダーを使用して 3-4 Vm を設定しようとしました。

<br>
</details>

<details>
<summary>
<b>音声コンテナーは句読点をサポートしていますか？</b>
</summary>

**回答:** オンプレミスのコンテナーで使用可能な大文字 (ITN) があります。 句読点は言語に依存しており、一部の言語 (中国語、日本語など) ではサポートされていません。

既存のコンテナーに対しては、暗黙的な句読点のサポートがあり *ます* が、これは`off`既定でのものです。 つまり、こ `.`の場合では文字を取得できますが、`。`文字は取得できません。 この暗黙的なロジックを有効にするには、音声SDK を使用して Python でこれを行う方法の例を次に示します (他の言語でも同様です)：

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>音声からテキストへのコンテナーにデータを投稿しようとすると、404エラーが発生するのはなぜですか？</b>
</summary>

こちらがHTTO POSTの例になります：

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**回答:** 音声to text コンテナーの REST API はサポートされていません。 音声 SDK を通じてサポートされているのは Websocket だけです。 公式のドキュメントを参照してください。[ クエリ予測エンドポイント を参照してください](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>


<details>
<summary>
<b> コンテナーが非ルート ユーザーとして実行されているのはなぜですか? これにより、どのような問題が発生しますか?</b>
</summary>

**回答:** コンテナー内の既定のユーザーは、非ルート ユーザーであることにご注意ください。 これにより、コンテナーをエスケープするプロセスや、ホスト ノードにエスカレートされたアクセス許可を取得するプロセスが保護されます。 既定では、OpenShift Container Platform などの一部のプラットフォームで、任意に割り当てられたユーザー ID を使用してコンテナーを実行することで、この処理が既に実行されています。 これらのプラットフォームでは、非ルート ユーザーは、書き込みが必要な外部にマップされたボリュームに書き込むためのアクセス許可を持っている必要があります。 たとえば、ログ フォルダーやカスタムのモデル ダウンロード フォルダーなどです。
<br>
</details>

<details>
<summary>
<b>音声入力サービスを使用する場合、このエラーが発生するのはなぜですか？</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**回答:** これは通常、音声認識コンテナーが音声認識コンテナーを使用するよりも高速にオーディオをフィードする場合に発生します。 クライアントバッファーがいっぱいになり、取り消しがトリガーされます。 オーディオを送信する際に、同時実行と RTF を制御する必要があります。

<br>
</details>

<details>
<summary>
<b>これらのテキストから読み上げコンテナーのエラーについてC++はの例を使用で説明していただけますか？</b>
</summary>

**回答:** コンテナーのバージョンが1.3 より前の場合は、次のコードを使用する必要があります：

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

以前のコンテナーには、`FromHost` API で使用するために必要なカーボンのエンドポイントがありません。 コンテナーがバージョン1.3 で使用されている場合は、次のコードを使用する必要があります：

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

以下が`FromEndpoint`APIの使用例です：

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 `SetSpeechSynthesisVoiceName`関数が呼び出されます。更新されたテキスト変換エンジンを持つコンテナーには、音声名が必要であるためです。

<br>
</details>

<details>
<summary>
<b>音声 SDK の v1.7  を Speech コンテナーと共に使用するにはどうすればよいですか？</b>
</summary>

**回答:** 音声コンテナーには、さまざまな用途のエンドポイントが3つあります。これらは音声モードとして定義されています。以下を参照してください：

## <a name="speech-modes"></a>音声モード

[!INCLUDE [speech-modes](includes/speech-modes.md)]

さまざまな目的で使用され、異なる方法で使用されます。

Python [サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)：
- カスタムエンドポイント（エンドポイントパラメターを用いた`SpeechConfig` ）を使用した単一の認識 (対話モード) の場合 は、`speech_recognize_once_from_file_with_custom_endpoint_parameters()`を参照してください。
- 継続的な認識 (会話モード) の場合は、上記のカスタムエンドポイントを使用するように変更するだけで、`speech_recognize_continuous_from_file()`を参照してください。
- (本当に必要な場合にのみ) 上記のようなサンプルでディクテーションを有効にするには、 を作成した直後に`speech_config`、コード を追加します`speech_config.enable_dictation()`。

ディクテーションC#を有効にするには、 関数`SpeechConfig.EnableDictation()`を呼び出します。

### <a name="fromendpoint-apis"></a>`FromEndpoint` APIs
| Language | APIの詳細 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 現在サポート、または計画されていません。 |

<br>
</details>

<details>
<summary>
<b>音声SDK の v1.8 を Speech コンテナーと共に使用するにはどうすればよいですか？</b>
</summary>

**回答:** 新しい `FromHost`API があります。 これは現存しているAPIの取り換え、または修正はできません。 カスタムホストを使用して音声構成を作成するための別の方法を追加するだけです。

### <a name="fromhost-apis"></a>`FromHost` APIs

| Language | APIの詳細 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 現在、サポートされていません |

> パラメーター: host (必須)、サブスクリプションキー (省略可能。サービスを使用できない場合)。

ホストの形式は`protocol://hostname:port``:port`は省略可能です (下記参照)：
- コンテナーがローカルで実行されている場合、ホスト名は`localhost`です。
- コンテナーがリモートサーバー上で実行されている場合は、そのサーバーのホスト名または IPv4 アドレスを使用します。

読み上げのホストパラメーターの例:
- ポート5000を使用したローカルコンテナーへの`ws://localhost:5000`セキュリティで保護されていない接続
- リモートサーバーで実行されているコンテナーへの`ws://some.host.com:5000`セキュリティで保護されていない接続

上記の Python サンプルは`endpoint`ではなく`host`パラメーターを使用しています。

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Cognitive Services コンテナー](speech-container-howto.md)