---
title: 発話の良い例
titleSuffix: Language Understanding - Azure Cognitive Services
description: 発話は、アプリが解釈する必要のあるユーザーからの入力です。 ユーザーが入力すると思われる語句を収集します。 同じことを意味しますが、異なる単語の長さと単語の配置で構成されている発話を含めるようにします。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 2fd3416824189007bfdbe55d30907d9cb56f87ca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792540"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>LUIS アプリに対して良い発話を理解する

**発話**は、アプリが解釈する必要のあるユーザーからの入力です。 そこから意図とエンティティを抽出するように LUIS をトレーニングするには、各意図に対してさまざまな異なる発話の例をキャプチャすることが重要です。 新しい発話についてのアクティブな学習、または継続的なトレーニング プロセスは、LUIS が提供する機械学習されたインテリジェンスに不可欠です。

ユーザーが入力すると思われる発話を収集します。 同じことを意味するが、さまざまな異なる方法で構成されている発話を含めます。

* 発話の長さ - クライアント アプリケーションに対して短い、中程度、長い
* 単語や語句の長さ 
* 単語の配置 - 発話の先頭、中央、末尾にあるエンティティ
* 文法 
* 複数形化
* 語幹検索
* 名詞と動詞の選択
* 句読点 - 正しい文法、正しくない文法、文法なしを使用した多様性

## <a name="how-to-choose-varied-utterances"></a>さまざまな発話を選択する方法

LUIS モデルに[発話の例を追加する](luis-how-to-add-example-utterances.md)ことによって初めて開始する場合、念頭に置いておくべきいくつかの原則を次に示します。

### <a name="utterances-arent-always-well-formed"></a>発話は常に正しい形式になっているわけではない

"パリ行きのチケットを予約して" のような文の場合も、"予約" や "パリ便" のような文の一部の場合もあります。  ユーザーはスペルミスをすることがよくあります。 アプリを計画するときは、LUIS に渡す前に [Bing Spell Check](luis-tutorial-bing-spellcheck.md) を実行してユーザー入力を修正するかどうかを検討します。 

ユーザーの発話のスペルチェックを行わない場合は、タイプミスやミススペルを含む発話で LUIS をトレーニングする必要があります。

### <a name="use-the-representative-language-of-the-user"></a>ユーザーの代表的な言語を使用する

発話を選択するときは、開発者が一般的な用語または語句と考えるものがクライアント アプリケーションの一般的なユーザーにとっては適切でない可能性があることに注意してください。 ユーザーはドメインのエクスペリエンスを持たない可能性があります。 専門家だけが話すような用語や語句を使用するときは注意が必要です。

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>さまざまな用語と表現を選択する

さまざまなセンテンス パターンを作成しようとしても、いくつかのボキャブラリを繰り返していることに気付きます。

次の発話の例を使用します。

|発話の例|
|--|
|コンピューターはどうすれば手に入りますか?|
|コンピューターはどこで手に入りますか?|
|コンピューターを手に入れたいのですが、どうすればよいですか?|
|いつコンピューターを入手できますか?| 

ここでのコア用語 "コンピューター" は全部同じです。 デスクトップ コンピューター、ラップトップ、ワークステーション、マシンなどの代替を使用します。 LUIS はコンテキストからシノニムをインテリジェントに推論しますが、それでもトレーニング用の発話を作成するときは、変更することをお勧めします。

## <a name="example-utterances-in-each-intent"></a>各意図での発話の例

意図ごとに少なくとも 15 個の発話の例が必要です。 発話の例がない意図がある場合、LUIS をトレーニングできません。 意図の発話の例が 1 つまたは非常に少ない場合、LUIS は意図を正確に予測できません。 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>作成イテレーションごとに 15 個の発話からなる小さいグループを追加する

モデルの各イテレーションでは、大量の発話を追加しないようにします。 15 個の発話を追加します。 もう一度[トレーニング](luis-how-to-train.md)し、[発行](luis-how-to-publish-app.md)し、[テスト](luis-interactive-test.md)します。  

LUIS は、LUIS モデルの作成者によって慎重に選択された発話で有効なモデルを構築します。 追加する発話が多すぎると、混乱が発生するので役に立ちません。  

少数の発話で開始し、[エンドポイント発話を確認](luis-how-to-review-endpoint-utterances.md)して意図の予測とエンティティの抽出が正しいことを確認することをお勧めします。

## <a name="punctuation-marks"></a>句読点

LUIS は、既定では句読点を無視しません。句読点が重要視されるクライアント アプリケーションもあるためです。 どちらの形式でも同じ相対スコアが返されるように、発話の例では必ず、句読点があるケースとないケースの両方を使用してください。 ご利用のクライアント アプリケーションで句読点に特別な意味がない場合は、パターンを使用して[句読点を無視](#ignoring-words-and-punctuation)することを検討してください。 

## <a name="ignoring-words-and-punctuation"></a>単語と句読点を無視する

発話の例にある特定の単語や句読点を無視する場合、[パターン](luis-concept-patterns.md#pattern-syntax)と _ignore_ 構文を使用します。 

## <a name="training-utterances"></a>発話のトレーニング

一般にトレーニングは非決定論的です。発話の予測は、バージョンまたはアプリによって若干変わる可能性があります。 トレーニング データをすべて使用するために、[バージョン設定](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API を `UseAllTrainingData` の名前/値のペアで更新して非決定論的トレーニングを削除することができます。

## <a name="testing-utterances"></a>発話のテスト 

開発者は、発話を[予測エンドポイント](luis-how-to-azure-subscription.md) URL に送信することにより、実際のトラフィックで LUIS アプリケーションのテストを開始する必要があります。 これらの発話は、[発話の確認](luis-how-to-review-endpoint-utterances.md)での意図とエンティティのパフォーマンスを向上させるために使われます。 LUIS Web サイトのテスト ウィンドウで送信されるテストは、エンドポイント経由では送信されず、したがってアクティブな学習には影響しません。 

## <a name="review-utterances"></a>発話の確認

モデルがトレーニングされて発行され、[エンドポイント](luis-glossary.md#endpoint) クエリを受け取った後、LUIS によって提案された[発話を確認](luis-how-to-review-endpoint-utterances.md)します。 LUIS は、意図またはエンティティに対してスコアの低いエンドポイント発話を選択します。 

## <a name="best-practices"></a>ベスト プラクティス

[ベスト プラクティス](luis-concept-best-practices.md)を確認し、それらを通常の作成サイクルの一環として適用します。

## <a name="next-steps"></a>次の手順
ユーザーの発話を理解するための LUIS アプリのトレーニングについては、「[発話の例を追加する](luis-how-to-add-example-utterances.md)」を参照してください。

