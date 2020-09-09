---
title: 発話の良い例 - LUIS
description: 発話は、アプリが解釈する必要のあるユーザーからの入力です。 ユーザーが入力すると思われる語句を収集します。 同じことを意味しますが、異なる単語の長さと単語の配置で構成されている発話を含めるようにします。
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 6a17416183762893432841c27124ec6a0f8a289d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685272"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>LUIS アプリに対して良い発話を理解する

**発話**は、アプリが解釈する必要のあるユーザーからの入力です。 そこから意図とエンティティを抽出するように LUIS をトレーニングするには、各意図に対してさまざまな異なる発話の例をキャプチャすることが重要です。 新しい発話についてのアクティブな学習、または継続的なトレーニング プロセスは、LUIS が提供する機械学習インテリジェンスに不可欠です。

ユーザーが入力すると思われる発話を収集します。 同じことを意味するが、さまざまな異なる方法で構成されている発話を含めます。

* 発話の長さ - クライアント アプリケーションに対して短い、中程度、長い
* 単語や語句の長さ
* 単語の配置 - 発話の先頭、中央、末尾にあるエンティティ
* 文法
* 複数形化
* 語幹検索
* 名詞と動詞の選択
* [句読点](luis-reference-application-settings.md#punctuation-normalization) - 正しい文法、正しくない文法、文法なしを使用した多様性

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

ここでのコア用語 "コンピューター" は全部同じです。 デスクトップ コンピューター、ラップトップ、ワークステーション、マシンなどの代替を使用します。 LUIS はコンテキストからシノニムをインテリジェントに推論できますが、それでもトレーニング用の発話を作成するときは、常に変更することをお勧めします。

## <a name="example-utterances-in-each-intent"></a>各意図での発話の例

意図ごとに少なくとも 15 個の発話の例が必要です。 発話の例がない意図がある場合、LUIS をトレーニングできません。 意図の発話の例が 1 つまたは非常に少ない場合、LUIS は意図を正確に予測できないことがあります。

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>作成イテレーションごとに 15 個の発話からなる小さいグループを追加する

モデルの各イテレーションでは、大量の発話を追加しないようにします。 15 個の発話を追加します。 もう一度[トレーニング](luis-how-to-train.md)し、[発行](luis-how-to-publish-app.md)し、[テスト](luis-interactive-test.md)します。

LUIS は、LUIS モデルの作成者によって慎重に選択された発話で有効なモデルを構築します。 追加する発話が多すぎると、混乱が発生するので役に立ちません。

少数の発話で開始し、[エンドポイント発話を確認](luis-how-to-review-endpoint-utterances.md)して意図の予測とエンティティの抽出が正しいことを確認することをお勧めします。

## <a name="utterance-normalization"></a>発話の正規化

発話の正規化とは、トレーニングおよび予測中に、句読点や分音記号など、テキストの種類の影響を無視する処理のことです。

既定では、発話の正規化の設定は、オフになっています。 これらの設定には、以下が含まれます。

* 単語の形式
* 分音記号
* 句読点

正規化設定を有効にすると、 **[テスト]** ペイン、バッチ テスト、およびエンドポイント クエリのスコアは、その正規化設定のすべての発話に対して変更されます。

LUIS ポータルでバージョンを複製すると、バージョン設定は新しい複製されたクローン バージョンに継続されます。

バージョン設定は、LUIS ポータルの **[アプリケーション設定]** ページの **[管理]** セクション、または [Update Version Settings API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) で設定します。 これらの正規化の変更の詳細については、[リファレンス](luis-reference-application-settings.md)に関するページを参照してください。

### <a name="word-forms"></a>単語の形式

**単語の形式**を正規化すると、語根以外に拡張された単語の違いは無視されます。

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>分音記号

分音記号とは、テキスト内のマークや記号のことです。例を次に示します。

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>句読点
**句読点**を正規化すると、モデルがトレーニングされる前、およびエンドポイント クエリが予測される前に、発話から句読点が削除されます。

句読点は、LUIS 内の個別のトークンです。 末尾にピリオドを含む発話と末尾にピリオドを含まない発話は 2 つの個別の発話であり、2 つの異なる予測が得られる可能性があります。

既定では、句読点を正規化しない場合、LUIS で句読点は無視されません。これは、クライアント アプリケーションによっては、句読点が重要視されるものがあるためです。 どちらの形式でも同じ相対スコアが返されるように、発話の例では必ず、句読点があるケースとないケースの両方を使用してください。

モデルが発話の例 (句読点がある場合とない場合) か、または[パターン](luis-concept-patterns.md) (特殊な構文 `I am applying for the {Job} position[.]` を使用して句読点を簡単に無視できる場合) のどちらかで句読点を処理することを確認してください。

クライアント アプリケーションで句読点が特別な意味を持たない場合は、句読点を正規化して[句読点を無視](#utterance-normalization)することを検討してください。

### <a name="ignoring-words-and-punctuation"></a>単語と句読点を無視する

パターン内の特定の語や句読点を無視する場合は、[パターン](luis-concept-patterns.md#pattern-syntax)使用時に大かっこ `[]` の _ignore_ 構文を指定します。

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>すべての発話を使用したトレーニング

一般にトレーニングは非決定論的です。発話の予測は、バージョンまたはアプリによって若干変わる可能性があります。
トレーニング データをすべて使用するために、[バージョン設定](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API を `UseAllTrainingData` の名前/値のペアで更新して非決定論的トレーニングを削除することができます。

## <a name="testing-utterances"></a>発話のテスト

開発者は、発話を[予測エンドポイント](luis-how-to-azure-subscription.md) URL に送信することにより、実際のトラフィックで LUIS アプリケーションのテストを開始する必要があります。 これらの発話は、[発話の確認](luis-how-to-review-endpoint-utterances.md)での意図とエンティティのパフォーマンスを向上させるために使われます。 LUIS Web サイトのテスト ウィンドウで送信されるテストは、エンドポイント経由では送信されず、したがってアクティブな学習には影響しません。

## <a name="review-utterances"></a>発話を確認する

モデルがトレーニングされて発行され、[エンドポイント](luis-glossary.md#endpoint) クエリを受け取った後、LUIS によって提案された[発話を確認](luis-how-to-review-endpoint-utterances.md)します。 LUIS は、意図またはエンティティに対してスコアの低いエンドポイント発話を選択します。

## <a name="best-practices"></a>ベスト プラクティス

[ベスト プラクティス](luis-concept-best-practices.md)を確認し、それらを通常の作成サイクルの一環として適用します。

## <a name="label-for-word-meaning"></a>単語の意味のラベル

単語の選択や配置は同じなのに同じものを意味しない場合は、そのエンティティでラベル付けしません。

次の発話で、単語 `fair` は、同形異義語です。 スペルは同じですが、意味が異なります。

|発話|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

イベント エンティティですべてのイベント データを検索する場合は、2 つ目ではなく最初の発話の単語 `fair` にラベルを付けます。


## <a name="next-steps"></a>次のステップ
ユーザーの発話を理解するための LUIS アプリのトレーニングについては、「[発話の例を追加する](luis-how-to-add-example-utterances.md)」を参照してください。

