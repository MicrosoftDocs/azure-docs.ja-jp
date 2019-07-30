---
title: コール センターの文字起こし - Speech Services
titleSuffix: Azure Cognitive Services
description: 音声変換で共通するシナリオは、インタラクティブ ボイス レスポンス (IVR) など、さまざまなシステムから入ってくる大量の電話データを文字に起こすことです。 音声はステレオかモノラルであり、未加工です。信号の上では後処理がほとんどないか、まったくありません。 企業は Speech Services と Unified 音声モデルを使用すると、さまざまな音声取り込みシステムで高品質の文字起こしが可能になります。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 37d68a4d2b7658542ebcfdb5d22a10676a8e4d52
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603306"
---
# <a name="speech-services-for-telephony-data"></a>電話データのための Speech Services

固定電話、携帯電話、無線電話で生成される電話データは一般的に低品質であり、8 KHz と狭帯域であり、音声をテキストに変換する作業を難しくします。 Azure Speech Services の最新の音声認識モデルはそのような電話データの文字起こしに優れており、人間には理解が難しいデータにも対応しています。 この最新モデルは大量の電話データでトレーニングされており、騒々しい環境でも、市場に出回っているモデルの中で最高精度の認識機能を発揮します。

音声変換で共通するシナリオは、インタラクティブ ボイス レスポンス (IVR) など、さまざまなシステムから入ってくる大量の電話データを文字に起こすことです。 こうしたシステムから入ってくる音声はステレオかモノラルであり、未加工です。信号の上では後処理がほとんどないか、まったくありません。 企業は Speech Services と Unified 音声モデルを使用すると、音声の取り込みに利用されるシステムに関係なく、高品質の文字起こしが可能になります。

電話データは、顧客が求めていることをよりよく理解したり、新しいマーケティング機会を見つけたり、コール センター エージェントの仕事ぶりを評価したりする目的で利用できます。 データが文字に起こされたら、企業はその改善された電話データを利用し、キーフレーズを特定したり、顧客のセンチメントを分析したりできます。

このページで概略的に示す技術は、サポート コールのさまざまな処理サービスのために Microsoft が社内で利用しています。リアルタイムとバッチ モードの両方で利用されています。

それでは Azure Speech Services の技術と関連機能をいくつか確認してみましょう。

> [!IMPORTANT]
> Speech Services Unified はさまざまなデータでトレーニングされており、書き取りから電話の分析まで、さまざまなシナリオにこれ 1 つで対応するモデル ソリューションを提供します。

## <a name="azure-technology-for-call-centers"></a>コール センター向けの Azure テクノロジ

コール センターで利用されるとき、機能的な面を除く Speech Services の主な目的は、顧客体験を改善することにあります。 この点については、3 つの分野に明確に分かれています。

* 通話後の分析。つまり、通話記録のバッチ処理です。
* リアルタイム分析。通話が行われている間に音声信号を処理し、さまざまな分析情報を抽出します。重要なユース ケースにセンチメントがあります。
* 仮想アシスタント (ボット)。顧客とボットの間の対話を進め、エージェントが関与することなく顧客の問題の解決を試みるか、AI プロトコルのアプリケーションとしてエージェントを支援します。

バッチ シナリオを実装するときの一般的なアーキテクチャ図は下の画像のようになります ![コール センターの文字起こしアーキテクチャ](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>音声分析テクノロジのコンポーネント

分野が通話後であってもリアルタイムであっても、Azure は新しくも完成された一連のテクノロジを提供し、顧客体験を改善します。

### <a name="speech-to-text-stt"></a>音声テキスト変換 (STT)

[音声テキスト変換](speech-to-text.md)はあらゆるコール センター ソリューションで最も求められている機能です。 ダウンストリーム分析プロセスの多くは書き起こされたテキストに依存しているため、ワード エラー率 (WER) が最も重要となります。 コール センターで文字起こしを難しくしている要因には、コール センター内の騒音 (たとえば、他のエージェントが後ろで話しています)、多様な言語ロケールと方言、実際の電話信号の低品質があります。 WER は、特定のロケールを対象に音響と言語のモデルがどの程度トレーニングされているかに密接に関係します。そのため、モデルを自分のロケールに合わせてカスタマイズできることが重要になります。 最新の Unified バージョン 4.x モデルは、文字起こしの精度の問題と遅延の問題を両方解決しています。 非常に大量の音響データと語彙情報でトレーニングされた Unified モデルは、市場に出回っているモデルの中で最も高い精度でコール センター データを文字に起こします。

### <a name="sentiment"></a>センチメント
音声分析をコール センターで利用するとき、分析の重要な領域の 1 つに、顧客が心地良い体験をしたかどうかを計測することが含まれます。 Microsoft の[バッチ文字起こし API](batch-transcription.md) では、発話ごとにセンチメントを分析できます。 通話の文字起こしで取得された一連の値を集計し、エージェントと顧客の両方に対して通話のセンチメントを判断できます。

### <a name="silence-non-talk"></a>沈黙 (会話なし)
サポート コールの 35% が会話なしの状態になることは普通です。 顧客に関する履歴をエージェントが調べているとき、顧客のデスクトップにアクセスし、機能を実行するためのツールをエージェントが使用しているとき、転送のために顧客を待たせているときなどに会話がなくなります。 このような場面では顧客のさまざまな重要な感情が表れるため、通話において沈黙が発生する時と場所を計測できることは極めて重要です。

### <a name="translation"></a>翻訳
デリバリー マネージャーが世界中の顧客体験を理解できるよう、外国語のサポート コールの翻訳を試している企業もあります。 Microsoft の[翻訳](translation.md)機能にまさるものはありません。 さまざまなロケールの音声を音声に変換したり、音声をテキストに変換したりできます。

### <a name="text-to-speech"></a>テキストから音声へ
顧客とやりとりするボットを実装するにあたり、もう 1 つの重要な分野が[テキスト読み上げ](text-to-speech.md)です。 通常、顧客が話し、その声がテキストに書き写され、テキストの意図が分析され、認められた意図に基づいて応答が合成され、アセットが顧客に提示されるか、合成された音声による応答が生成されるという過程になります。 当然ですが、以上の動作は一瞬で行われなければなりません。そのため、こうしたシステムを成功させるには待ち時間が重要な要素となります。

[音声認識](speech-to-text.md)、[LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)、[Bot Framework](https://dev.botframework.com/)、[テキスト読み上げ](text-to-speech.md)など、さまざまなテクノロジが利用されていることを考慮すると、Microsoft サービスのエンドツーエンドの待ち時間はとても短いと言えます。

また、新しい合成音声は人間と区別が付かなくなっています。 各種音声を使いこなすことで自分のボットに個性を与えることができます。

### <a name="search"></a>Search
分析のもう 1 つの中心的要素は、特定のイベントまたは体験が発生したやりとりを特定することです。 これは通常、2 つの手法のいずれかで行われます。ユーザーがフレーズを入力し、システムが応答するその場限りの検索か、通話の中でシナリオを特定する一連の論理的発言をアナリストが作成し、そのような一連の問い合わせに対して通話ごとに索引を付けられる、構造化の度合いが高いクエリです。 "この通話は品質向上のために録音されます..." という、あらゆる所で耳にするコンプライアンスに関する表明が検索の典型的な例です。 エージェントがこの免責事項を通話の録音前に顧客に確実に伝えることを多くの企業が望むためです。 ほとんどの分析システムでは、照会/検索アルゴリズムで検出される動作に傾向を見いだすことができます。傾向を報告することは、分析システムの最重要機能に含まれるためです。 [Cognitive サービス ディレクトリ](https://azure.microsoft.com/services/cognitive-services/directory/search/)を利用することで、エンドツーエンド ソリューションを索引作成と検索の機能で大幅に強化できます。

### <a name="key-phrase-extraction"></a>キー フレーズ抽出
分析アプリケーションとして難易度が高く、また、AI と ML の応用から得られるものが多いのがこの領域です。 これは主に、顧客の意図を推測するために利用されます。 顧客が電話してきた理由は何か? 顧客はどのような問題を抱えているのか? 顧客に不快な思いをさせたのはなぜか? Microsoft の[テキスト分析サービス](https://azure.microsoft.com/services/cognitive-services/text-analytics/)では、すぐに一連の分析機能をお使いいただけます。エンドツーエンド ソリューションを簡単にアップグレードし、重要なキーワードやフレーズを抽出できます。

それでは、音声認識のバッチ処理とリアルタイム パイプラインについてもう少し詳しく見てみましょう。

## <a name="batch-transcription-of-call-center-data"></a>コール センター データの一括文字起こし

音声を一括で文字起こしするために、Microsoft は[バッチ文字起こし API](batch-transcription.md) を開発しました。 バッチ文字起こし API は、大量の音声データを非同期で文字に起こす目的で開発されました。 コール センター データを文字に起こすとき、Microsoft のソリューションは次の柱を基盤とします。

* **精度**:第 4 世代の Unified モデルの文字起こしの品質にはまさるものがありません。
* **待ち時間**:一括で文字を起こすとき、高速の文字起こしが求められることを Microsoft は理解しています。 [バッチ文字起こし API](batch-transcription.md) 経由で始動する文字起こしジョブはすぐに待ち行列に入り、ジョブの実行が始まると、リアルタイムの文字起こしよりも高速で実行されます。
* **セキュリティ**: 通話には扱いに慎重を期するデータが含まれる可能性があることを Microsoft は理解しています。 セキュリティは Microsoft の最優先事項に含まれるのでご安心ください。 Microsoft のサービスは ISO、SOC、HIPAA、PCI の認証を受けています。

コール センターでは毎日、大量の音声データが生成されます。 企業が Azure Storage などの中心的な場所に電話データを保存する場合、[バッチ文字起こし API](batch-transcription.md) を使用し、文字起こしを非同期で要求し、受信できます。

一般的なソリューションで使用されるサービス:

* 音声変換には Azure Speech Services が使用されます。 バッチ文字起こし API を使用するには、Speech Services の Standard サブスクリプション (SO) が必要です。 Free サブスクリプション (F0) は機能しません。
* [Azure Storage](https://azure.microsoft.com/services/storage/) は、電話データと、バッチ文字起こし API によって返されたトランスクリプトの保存に使用されます。 このストレージ アカウントでは通知を利用する必要があります。特に、新しいファイルが追加されたときに通知する必要があります。 通知は文字起こしプロセスのトリガーに利用されます。
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) は、録音ごとに Shared Access Signature (SAS) URI を作成し、HTTP POST 要求をトリガーして文字起こしを開始するために使用されます。 また、Azure Functions は、バッチ文字起こし API で文字起こしを回収し、削除するための要求の作成に使用されます。
* [Webhook](webhooks.md) は、文字起こしが完了したとき、通知を取得するために使用されます。

Microsoft 社内では以上のテクノロジを利用し、バッチ モードの Microsoft カスタマー コールを支援しています。
![バッチ アーキテクチャ](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>コール センター データのリアルタイム文字起こし

会話をリアルタイムで文字起こしすることを必要とする企業もあります。 リアルタイムの文字起こしは、センチメント監視のためにキーワードを特定し、会話に関連するコンテンツとリソースの検索をトリガーしたり、アクセシビリティを改善したり、ネイティブ スピーカーではない顧客やエージェントのために翻訳を提供したりする目的で利用できます。

リアルタイムの文字起こしを必要とする場合、[Speech SDK](speech-sdk.md) の利用をお勧めします。 現在のところ、[20 を超える言語](language-support.md)で音声テキスト変換を利用できます。また、この SDK は C++、C#、Java、Python、Node.js、Objective-C、JavaScript で利用できます。 各言語のサンプルが [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) にあります。 最新ニュースや更新情報については、[リリース ノート](releasenotes.md)をご覧ください。

Microsoft 社内では以上のテクノロジを利用し、Microsoft カスタマー コールをリアルタイムで分析しています。

![バッチ アーキテクチャ](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>IVR のワード

Speech Services は [Speech SDK](speech-sdk.md) か [REST API](rest-apis.md) を利用することで、あらゆるソリューションに簡単に統合できます。 しかしながら、コール センターの文字起こしには付加的なテクノロジが必要になる場合があります。 通常、IVR システムと Azure 間の接続が必要です。 Microsoft ではそのようなコンポーネントを提供していませんが、IVR に接続するとはどのようなものか説明します。

(Genesys や AudioCodes など) IVR 製品または電話サービス製品の中には、Azure Service との間で音声を送受信するための統合機能を提供しているものがあります。 基本的に、カスタム Azure サービスでは、通話セッション (通話の開始や終了など) を定義するためのインターフェイスを提供し、Speech Services と共に使用されるストリーム音声を受信する WebSocket API を公開します。 会話の文字起こしや Bot Framework との接続など、送信の応答を Microsoft のテキスト読み上げサービスと合成し、IVR に返して再生できます。

もう 1 つのシナリオには Direct SIP 統合があります。 Azure サービスは SIP Server に接続し、音声をテキストに変換するときとテキストを読み上げるときに使用される受信ストリームと送信ストリームを取得します。 Ozieki SDK や [Teams calling and meetings API](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) など、SIP Server に接続するためのソフトウェアが市販されています。このようなソフトウェアは、音声通話のこの種のシナリオを支援する目的で設計されています。

## <a name="customize-existing-experiences"></a>既存の体験をカスタマイズする

Azure Speech Services は組み込みのモデルで問題なく動作しますが、製品や環境に合わせてエクスペリエンスをさらにカスタマイズおよび調整したいことがあります。 カスタマイズ オプションは、音響モデルのチューニングから、独自ブランドに固有の音声フォントにまで及びます。 カスタム モデルを作成した後は、すべての Azure Speech Services でそれを使用できます。リアルタイムとバッチ モードの両方が対象となります。

| Speech サービス | モデル | 説明 |
|----------------|-------|-------------|
| 音声テキスト変換 | [音響モデル](how-to-customize-acoustic-models.md) | 自動車や工場など、それぞれに固有の録音条件がある特定の環境で使用されるアプリケーション、ツール、デバイス用のカスタム音響モデルを作成します。 たとえば、アクセント記号付きの音声、特定の背景ノイズ、録音に特定のマイクを使用する場合などです。 |
| | [言語モデル](how-to-customize-language-model.md) | 医療用語や IT の専門用語など、業界固有のボキャブラリと文法の文字起こしを向上させるには、カスタム言語モデルを作成します。 |
| | [発音モデル](how-to-customize-pronunciation.md) | カスタムの発音モデルを使用すると、発音形式と単語または用語の表示を定義できます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 始めるにあたって必要なのは、発音ファイル (単純な .txt ファイル) のみです。 |
| テキスト読み上げ | [音声フォント](how-to-customize-voice-font.md) | カスタム音声フォントを使用すると、ブランド用に認識性の高い固有の音声を作成できます。 少量のデータだけで始めることができます。 提供するデータを増やすと、いっそう自然で人間のように聞こえる音声フォントになります。 |

## <a name="sample-code"></a>サンプル コード

各 Azure Speech Services のサンプル コードを、GitHub で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。 SDK と REST のサンプルを見るには、次のリンクを使用してください。

* [音声テキスト変換と音声翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Service のサブスクリプション キーを無料で取得する](get-started.md)
