---
title: ベスト プラクティス - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS アプリのモデルから最良の結果を得るための LUIS のベスト プラクティスについて説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 91ff99f674439580d369aad1490ded85d39d377c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382891"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Cognitive Services を使用して Language Understanding アプリを構築するためのベスト プラクティス
アプリの作成プロセスを使用して、ご自身の LUIS アプリをビルドします。 

* 言語モデルを構築する
* 発話のトレーニング例をいくつか追加する (意図あたり 10 ～ 15 個)
* 発行 
* エンドポイントからテストする 
* 機能を追加する

ご自身のアプリが[公開](luis-how-to-publish-app.md)されたら、作成サイクルを使用して、機能を追加し、公開し、エンドポイントからテストします。 発話の例を追加することによって、次の作成サイクルを開始しないでください。 そうすると、実際のユーザーの発話を使用して LUIS にモデルを学習させることができなくなります。 

LUIS による学習効率を高めるには、現在の発話の例とエンドポイント発話の両方が、高い予測スコアを確実に返すまでは、発話を増やさないようにします。 スコアを上げるには、[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md)、[パターン](luis-concept-patterns.md)、および[フレーズ リスト](luis-concept-feature.md)を使用します。 

## <a name="do-and-dont"></a>すべきことと、やってはいけないこと
LUIS アプリのベスト プラクティスを次に示します。

|すべきこと|やってはいけないこと|
|--|--|
|[意図を個別に定義する](#do-define-distinct-intents) |[多数の発話の例を意図に追加する](#dont-add-many-example-utterances-to-intents) |
|[意図の汎用性と専用性のスイート スポットを見つける](#do-find-sweet-spot-for-intents)|[LUIS をトレーニング プラットフォームとして使用する](#dont-use-luis-as-a-training-platform)|
|[ご自身のアプリを反復的にビルドする](#do-build-the-app-iteratively)|[同じ形式の発話の例を多数追加して、他の形式を無視する](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[後のイテレーションでフレーズ リストとパターンを追加する](#do-add-phrase-lists-and-patterns-in-later-iterations)|[意図とエンティティの定義を一緒にする](#dont-mix-the-definition-of-intents-and-entities)|
|None 意図を除く[すべての意図の間で発話のバランスを取る](#balance-your-utterances-across-all-intents)。<br>[発話の例を None 意図に追加する](#do-add-example-utterances-to-none-intent)|[指定できるすべての値でフレーズ リストを作成する](#dont-create-phrase-lists-with-all-the-possible-values)|
|[アクティブ ラーニングの提案機能を活用する](#do-leverage-the-suggest-feature-for-active-learning)|[非常に多くのパターン追加する](#dont-add-many-patterns)|
|[ご自身のアプリのパフォーマンスを監視する](#do-monitor-the-performance-of-your-app)|[追加されたすべての発話の例でトレーニングして公開する](#dont-train-and-publish-with-every-single-example-utterance)|
|[アプリのイテレーションごとにバージョンを使用する](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>すべきこと: 意図を個別に定義する
各意図のボキャブラリがその意図のみを対象としていること、また、別の意図と重複していないことを確認します。 たとえば、飛行機のフライトやホテルなど、旅行の手配を処理するアプリが必要な場合は、これらの主題領域を、それぞれ個別の意図として定義するか、発話内に特定のデータのエンティティを含む 1 つの意図として定義するかを選択できます。

2 つの意図のボキャブラリが同じ場合は、意図を結合し、エンティティを使用します。 

たとえば、次の発話の例について考えます。

|発話の例|
|--|
|Book a flight|
|Book a hotel|

この "Book a flight" と "Book a hotel" で使用されている共通のボキャブラリが "book a " です。 この形式は同じであるため、flight (フライト) および hotel (ホテル) という異なる単語を抽出されたエンティティとして含む同じ意図として定義する必要があります。 

詳細:
* 概念: [LUIS アプリにおける意図の概念](luis-concept-intent.md)
* チュートリアル:[ユーザーの意図を特定する LUIS アプリを構築する](luis-quickstart-intents-only.md)
* 方法:[ユーザーの発話意図を判断する意図を追加する](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>すべきこと: 意図のスイート スポットを見つける
LUIS の予測データを使用して、意図が重複していないかどうかを判断します。 意図が重複していると LUIS が混乱します。 その結果、上位スコアの意図と他の意図が非常に近くなります。 LUIS では、トレーニング用のデータから毎回同じパスを使用するわけではないため、重複している意図は、トレーニングで 1 位または 2 位になる可能性があります。 各意図の発話のスコアは、このフリップフロップが発生しないように、それぞれ差をつける必要があります。 意図が適切に区別されていれば、最上位の意図は毎回最上位にくるはずです。 
 
## <a name="do-build-the-app-iteratively"></a>すべきこと: ご自身のアプリを反復的にビルドする
[発話の例](luis-concept-utterance.md)またはエンドポイント発話として使用されていない発話のセットを個別に保持します。 ご自身のテスト セット用のアプリは改善し続けてください。 実際のユーザーの発話が反映されるようにテスト セットを調整します。 このテスト セットを使用して、アプリの各イテレーションまたはバージョンを評価します。 

開発者には 3 つのデータ セットが必要です。 1 つ目は、モデルを構築するための発話の例です。 2 つ目は、エンドポイントでモデルをテストするためのデータ セットです。 3 つ目は、[バッチ テスト](luis-how-to-batch-test.md)で使われているブラインド テスト データです。 この最後のセットは、エンドポイントでの送信やアプリケーションのトレーニングには使用されません。  

詳細:
* 概念: [LUIS アプリの作成サイクル](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>すべきこと: 後のイテレーションでフレーズ リストとパターンを追加する

アプリのテストが済むまではこれらのプラクティスを適用しないのがベスト プラクティスです。 [フレーズ リスト](luis-concept-feature.md)や[パターン](luis-concept-patterns.md)を追加する前に、アプリがどのように動作するかを理解しておく必要があります。これらの機能は、発話の例よりも重く重み付けされており、信頼性を歪めることになるためです。 

これらがないときにアプリがどのように動作するかを理解したら、これらの機能がアプリに適用されるときに各機能を追加します。 これらの機能を[繰り返し](luis-concept-app-iteration.md)のたびに追加したり、バージョンが変わるたびに変更したりする必要はありません。 

それらをモデル設計の開始時に追加しても害はありませんが、モデルを発話でテストした後、各機能変更がどのような結果になるかを確認することが容易になります。 

ベスト プラクティスは、[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md)の追加されたベネフィットを得られるよう、[エンドポイント](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)を介してテストすることです。 [対話型テスト ウィンドウ](luis-interactive-test.md)も有効なテスト手法になります。 
 

### <a name="phrase-lists"></a>フレーズ リスト

[フレーズ リスト](luis-concept-feature.md)を使用すると、お使いのアプリ ドメインに関連する単語のディクショナリを定義できます。 いくつかの単語を使ってフレーズ リストをシード処理し、提案機能を使用することで、LUIS によって、アプリに固有のボキャブラリの単語がさらに詳しく認識されるようになります。 フレーズ リストを使用すると、アプリにとって重要な語句に関連付けられているシグナルをブーストすることで、意図の検出とエンティティの分類が向上します。 

フレーズ リストは完全一致ではないため、ボキャブラリにはすべての単語は追加しないでください。 

詳細:
* 概念: [LUIS アプリのフレーズ リストのフィーチャー](luis-concept-feature.md)
* 方法: [フレーズ リストを使用して単語リストのシグナルをブーストする](luis-how-to-add-features.md)

### <a name="patterns"></a>パターン

エンドポイントからの実際のユーザーの発話で、非常によく似ているものにより、単語の選択と配置のパターンが明らかになる可能性があります。 [パターン](luis-concept-patterns.md)機能では、この単語の選択と配置および正規表現を使って、ご自身の予測精度を向上させます。 パターン内の正規表現を使用すると、単語や句読点を無視して、パターンを一致させることができます。 

句読点を無視するには、パターンの[省略可能な句読点の構文](luis-concept-patterns.md)を使用します。 [明示的なリスト](luis-concept-patterns.md#explicit-lists)を使用して、pattern.any 構文の問題を補正します。 

詳細:
* 概念: [パターンは予測精度を改善する](luis-concept-patterns.md)
* 方法: [パターンを追加して予測精度を改善する方法](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>すべての意図の間で発話のバランスを取る

LUIS による予測を正確に行うには、各意図 (None 意図を除く) の発話の例の量が同程度である必要があります。 

発話の例が 100 個の意図と発話の例が 20 個の意図がある場合、発話が 100 個の意図の方が予測率が高くなります。  

## <a name="do-add-example-utterances-to-none-intent"></a>すべきこと: 発話の例を None 意図に追加する

この意図は代替用の意図で、お使いのアプリケーション外のものすべてを示していました。 ご自身の残りの LUIS アプリでは、発話の例 10 個それぞれに 1 つの発話の例を None 意図に追加してください。

詳細:
* 概念: [LUIS アプリに対して良い発話を理解する](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>すべきこと: アクティブ ラーニングの提案機能を活用する

発話の例をさらに多く意図に追加するのではなく、[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)の**エンドポイントの発話の確認**を定期的に使用します。 アプリでは常にエンドポイント発話を受信しているため、このリストは拡大および変化し続けています。

詳細:
* 概念: [エンドポイント発話のレビューによるアクティブ ラーニング実現の概念](luis-concept-review-endpoint-utterances.md)
* チュートリアル:[チュートリアル:エンドポイントの発話を確認して不確かな予測を修正する](luis-tutorial-review-endpoint-utterances.md)
* 方法: [エンドポイント発話を LUIS ポータルで確認する方法](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>すべきこと: ご自身のアプリのパフォーマンスを監視する

[バッチ テスト](luis-concept-batch-test.md) セットを使用して予測精度を監視します。 

## <a name="dont-add-many-example-utterances-to-intents"></a>やってはいけないこと: 多数の発話の例を意図に追加する

アプリの公開後は、反復的なプロセスでアクティブ ラーニングから発話を追加するだけにします。 発話が似ている場合は、パターンを追加します。 

## <a name="dont-use-luis-as-a-training-platform"></a>やってはいけないこと: LUIS をトレーニング プラットフォームとして使用する

LUIS は、言語モデルのドメインに固有です。 一般的な自然言語トレーニング プラットフォームとして動作するようには作られていません。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>やってはいけないこと: 同じ形式の発話の例を多数追加して、他の形式を無視する

LUIS では、意図の発話にバリエーションが必要です。 発話は全体的に意味が同じでも、さまざまな形があります。 バリエーションには、発話の長さ、単語の選択、単語の配置などが含まれます。 

|同じ形式を使用しない|さまざまな形式を使用する|
|--|--|
|シアトルまでのチケットを購入する<br>パリまでのチケットを購入する<br>オーランドまでのチケットを購入する|シアトル行きのチケットを 1 枚購入する<br>パリまでの夜行便を、次の月曜日に二席予約する<br>春休みの間にオーランドへのチケットを 3 枚確保したいのですが|

2 番目の列では、それぞれ異なる動詞 (購入、予約、確保)、数量 (1、二、3)、語順が使用されていますが、旅行のために航空券を購入するという意図はすべて同じです。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>やってはいけないこと: 意図とエンティティの定義を一緒にする

お使いのボットで取得されるすべてのアクションに対して意図を作成します。 そのアクションを可能にするためのエンティティをパラメーターとして使用します。 

飛行機のフライトを予約するチャットボットについては、**BookFlight** 意図を作成します。 すべての航空会社、またはすべての目的地に対して、意図を作成しないでください。 これらのデータ部分を[エンティティ](luis-concept-entity-types.md)として使用し、発話の例でマークします。 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>やってはいけないこと: 指定できるすべての値でフレーズ リストを作成する

[フレーズ リスト](luis-concept-feature.md)には数個の例を提供します。すべての単語ではありません。 LUIS で汎用化が行われ、コンテキストが考慮されます。 

## <a name="dont-add-many-patterns"></a>やってはいけないこと: 多数のパターンを追加する

追加する[パターン](luis-concept-patterns.md)は、多くなりすぎないようにします。 LUIS は、少ない例をすばやく学習するように作られています。 不必要にシステムに負荷をかけないでください。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>やってはいけないこと: すべての発話の例でトレーニングして公開する

10 ～ 15 個の発話を追加したら、トレーニングして公開します。 これにより、予測精度への影響を確認することができます。 1 つの発話を追加しても、スコアに目に見える影響は出ない可能性があります。 

## <a name="do-use-versions-for-each-app-iteration"></a>アプリのイテレーションごとにバージョンを使用する

各作成サイクルは、既存のバージョンから複製された新しい[バージョン](luis-concept-version.md)内にある必要があります。 LUIS にはバージョンの制限がありません。 バージョン名は API ルートの一部として使用されるので、バージョンには URL で許可されている文字を選び、10 文字以内にすることが重要です。 バージョンを整理するためのバージョン名戦略を策定してください。 

詳細:
* 概念: [LUIS バージョンを使用する方法とタイミングを理解する](luis-concept-version.md)
* 方法: [バージョンを使用してステージング アプリまたは運用環境アプリに影響を与えることなく編集とテストを行う](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>次の手順

* お使いの LUIS アプリで[ご自身のアプリを計画](luis-how-plan-your-app.md)する方法を確認します。
