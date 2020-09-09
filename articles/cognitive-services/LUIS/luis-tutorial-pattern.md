---
title: チュートリアル:パターン - LUIS
description: このチュートリアルでは、パターンを使用して意図とエンティティの予測を改善すると共に、与える発話の例を減らします。 このパターンは、エンティティと無視できるテキストを識別するための構文を含むテンプレート発話の例として与えられます。
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 3ca8bb15d19b0fa0dd6b33d35a380c0b1b07abe0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039502"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>チュートリアル:一般的なパターン テンプレート発話フォーマットを追加して予測を改善する

このチュートリアルでは、パターンを使用して意図とエンティティの予測を改善します。これによって、与える発話の例を減らすことができます。 このパターンは、意図に割り当てられるテンプレート発話であり、エンティティおよび無視できるテキストを識別するための構文を含んでいます。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * パターンを作成する
> * パターン予測の改善を検証する
> * テキストを無視できるものとマークし、パターン内で入れ子にする
> * テスト パネルを使用してパターンの成功を検証する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>意図とパターンにおける発話

LUIS アプリに格納される発話には、次の 2 種類があります。

* 意図における発話の例
* パターンにおけるテンプレート発話

テンプレート発話をパターンとして追加することにより、意図に対して与える発話の例を全体的に減らすことができます。

パターンは、テキストの照合と機械学習の組み合わせとして適用されます。  パターン内のテンプレート発話は、意図に含まれる発話の例と共に、どのような発話が意図に適合するかを LUIS がより適切に解釈できるようにします。

## <a name="import-example-app-and-clone-to-new-version"></a>サンプル アプリをインポートして新しいバージョンにクローンする

次の手順に従います。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true)をダウンロードして保存します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. [LUIS ポータル](https://www.luis.ai)で、その JSON を新しいアプリにインポートします。 **[マイ アプリ]** ページで、 **[+ New app for conversation]\(+ 会話用の新しいアプリ\)** を選択し、 **[Import as JSON]\(JSON としてインポート\)** を選択します。 前の手順でダウンロードしたファイルを選択し、アプリに `Patterns tutorial` という名前を付けます。

## <a name="create-new-intents-and-their-utterances"></a>新しい意図とその発話を作成する

2 つの意図は、マネージャーまたはマネージャーの直属の部下を発話テキストに基づいて探すものです。 難しいのは、2 つの意図が異なる事柄を "_意味_" するのに、大半の単語が同じである点です。 異なるのは単語の順序だけです。 意図を正しく予測するためには、多数の例が必要となるでしょう。

1. ナビゲーション バーから **[Build]\(ビルド\)** を選択します。

1. **[Intents]\(意図\)** ページの **[+ Create]\(+ 作成\)** を選択して新しい意図を作成します。

1. ポップアップ ダイアログ ボックスに「`OrgChart-Manager`」と入力して、 **[完了]** を選択します。

    ![新しいメッセージ ポップアップ ウィンドウを作成する](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. 発話の例を意図に追加します。 これらの発話は "_厳密_" には異なりますが、抽出可能なパターンは確かに存在します。

    |発話の例|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. 左側のナビゲーションで、 **[Intents]\(意図\)** を選択します。

1. **[+ Create]\(+ 作成\)** を選択して新しい意図を作成します。 ポップアップ ダイアログ ボックスに「`OrgChart-Reports`」と入力して、 **[完了]** を選択します。

1. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>発話の例の量に関する注意

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>テストまたは発行する前に、アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>アプリを発行してエンドポイントからクエリを実行する

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. アドレス バーの URL の末尾に移動して、_YOUR_QUERY_HERE_ を `Who is the boss of Jill Jones?` に置き換えます。

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

正しい最上位の意図 `OrgChart-Manager` が予測されましたが、スコアが 70% 以下であり、次に高い意図よりも十分高いとは言えません。 正しい意図のスコアを割合で大幅に高く、また次に高いスコアからさらに離れたものにするパターンを使用してください。

この 2 つ目のブラウザー ウィンドウは開いたままにします。 このチュートリアルで、後でまた使います。

## <a name="template-utterances"></a>テンプレート発話
サブジェクト ドメインである人事の性質上、組織内の従業員の関係について一般的なたずね方がいくつかあります。 次に例を示します。

|発話|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

発話の_多数_の例を提供せずに、それぞれの文脈の一意性を判断するには、これらの発話は類似しすぎています。 意図のパターンを追加することにより、発話の多数の例をさらに提供しなくても、LUIS は意図の一般的な発話パターンを学習します。

この意図のテンプレート発話例には、次のものが含まれます:

|テンプレート発話例|構文の意味|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|交換可能な `{EmployeeListEntity}`<br>無視 `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|交換可能な `{EmployeeListEntity}`<br>無視 `[?]`|

`{EmployeeListEntity}` 構文では、テンプレート発話内でのエンティティの位置とそれがどのエンティティであるかがマークされます。 オプションの構文 `[?]` は、オプションである単語または[句読点](luis-reference-application-settings.md#punctuation-normalization)をマークします。 LUIS は、かっこ内のオプションのテキストを無視し、発話を照合します。

構文は正規表現のように見えますが、正規表現ではありません。 中かっこ `{}` と角かっこ `[]` の構文のみがサポートされています。 これらは 2 階層まで入れ子にすることができます。

パターンが発話に一致するには、"_まず_"、発話内のエンティティがテンプレート発話内のエンティティに一致している必要があります。 つまり、エンティティを含んだパターンがうまく機能するためには、発話の例に、予測精度の高いエンティティの例が十分に存在していなければなりません。 ただし、テンプレートは意図の予測にのみ役立ち、エンティティの予測には役立ちません。

**パターンを使用すると提供される発話の例を少なくすることができますが、エンティティが検出されない場合、パターンは一致しません。**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>OrgChart-Manager 意図のパターンを追加する

1. 上部メニューの **[ビルド]** を選択します。

1. 左側のナビゲーションの **[Improve app performance]\(アプリのパフォーマンスの改善\)** で、 **[Patterns]\(パターン\)** を選択します。

1. **OrgChart-Manager** 意図を選択し、次のテンプレート発話を入力します。

    |テンプレート発話|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    これらのテンプレート発話には、中かっこ表記の **EmployeeListEntity** エンティティが含まれています。

1. [パターン] ページで **OrgChart-Reports** 意図を選択し、次のテンプレート発話を入力します。

    |テンプレート発話|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>パターンが使用されるときにエンドポイントにクエリを実行する

パターンがアプリに追加されたので、予測ランタイム エンドポイントでアプリのトレーニング、公開、およびクエリを実行します。

1. **[Train]\(トレーニング\)** を選択します。 トレーニングが完了したら、 **[Publish]\(発行\)** を選択し、 **[Production]\(運用\)** スロットを選択して、 **[Done]\(完了\)** を選択します。

1. 発行が完了したら、ブラウザーのタブをエンドポイント URL のタブに再び切り替えます。

1. アドレス バーで URL の末尾に移動し、クエリがまだ `Who is the boss of Jill Jones?` であることを確認してから、新しい予測の URL を送信します。

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

これで、意図の予測の確度は大幅に高くなり、次に高い意図のスコアは非常に小さくなりました。 トレーニング時に、この 2 つの意図が入れ替わることはありません。

### <a name="working-with-optional-text-and-prebuilt-entities"></a>省略可能なテキストと事前構築済みエンティティの使用

このチュートリアルの前掲のパターン テンプレートの発話には、文字 s の所有格での使用 `'s` や疑問符の使用 `?` など、省略可能なテキストの例がいくつか含まれました。 発話テキストで現在および将来の日付を考慮する必要があるとします。

発話の例は次のようになります。

|Intent|省略可能なテキストと事前構築済みエンティティを含む発話の例|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

これらの各例では、LUIS が正しく予測するために必要な動詞の時制 `was`、`is`、`will be`、および日付 `March 3`、`now`、`in a month` が使用されています。 この表の最後の 2 つの例では、`in` と `on` 除きほとんど同じテキストが使用されていることに注意してください。

この省略可能な情報を考慮したテンプレート発話の例:

|Intent|省略可能なテキストと事前構築済みエンティティを含む発話の例|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


省略可能な構文の角かっこ `[]` を使用すると、この省略可能なテキストをテンプレート発話に簡単に追加でき、最大 2 レベルまで入れ子にでき `[[]]`、エンティティやテキストを含めることができます。


**質問: 各テンプレート発話の最初の文字 `w` がすべて小文字になっているのはなぜですか。必要に応じて大文字または小文字にする必要はありませんか。** クライアント アプリケーションによってクエリ エンドポイントに送信される発話は、小文字に変換されます。 テンプレート発話は大文字でも小文字でもよく、エンドポイント発話もどちらでもかまいません。 比較は常に小文字への変換後に行われます。

**質問: 3 月 3 日が数値 `3` および日付 `March 3` の両方として予測される場合、事前構築済みの数がテンプレート発話の一部になっていないのはなぜですか。** コンテキスト上、テンプレート発話では日付を `March 3` のようにリテラルで、または `in a month` のように抽象化して使用しています。 日付は数値を含むことができますが、数値は必ずしも日付として見えなくてもかまいません。 常に、予測 JSON の結果で返したい種類をもっともよく表すエンティティを使用します。

**質問: `Who will {EmployeeListEntity}['s] manager be on March 3?` のような不適切な語句の発話はどうなりますか。** `will` と `be` が分けられるこのような文法的に異なる動詞の時制は、新しいテンプレート発話にする必要があります。 既存のテンプレート発話は一致しません。 発話の意図は変わっていませんが、発話での単語の配置が変わっています。 この変更は、LUIS での予測に影響します。 動詞の時制の[グループ化や OR 演算子](#use-the-or-operator-and-groups)を使用して、このような発話を組み合わせることができます。

> [!CAUTION]
> **注意: エンティティが最初に見つけられた後、パターンが照合されます。**

### <a name="add-new-pattern-template-utterances"></a>新しいパターン テンプレート発話を追加する

1. **[Build]\(ビルド\)** の **[Patterns]\(パターン\)** セクションで、複数の新しいパターン テンプレート発話を追加します。 [Intent]\(意図\) ドロップダウン メニューから **OrgChart-Manager** を選択し、次のテンプレート発話を入力します。

    |Intent|省略可能なテキストと事前構築済みエンティティを含む発話の例|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. ナビゲーション バーの **[Train]\(トレーニング\)** を選択して、アプリをトレーニングします。

3. トレーニングが完了したら、パネル上部の **[Test]\(テスト\)** を選択して、テスト パネルを開きます。

4. テスト発話をいくつか入力し、パターンが一致して意図スコアが十分に高いことを確認します。

    最初の発話を入力した後、結果の下の **[Inspect]\(検査\)** を選択してすべての予測結果を見ることができます。 各発話には **OrgChart-Manager** 意図があるため、`EmployeeListEntity` および `datetimeV2` エンティティの値が抽出されるはずです。

    |発話|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

これらの発話はすべてエンティティ内で検出され、したがって同じパターンに一致し、高い予測スコアになります。 さまざまなバリエーションの発話と一致するパターンをいくつか追加しました。 パターンとしてテンプレート発話を機能させるために、発話の例を意図に対して追加する必要はありません。

このようにパターンを使用することで、次の結果が得られました。
* 予測スコアが向上した
* 意図における発話の例は同じまま
* 適切に作成されたテンプレート発話をいくつかパターンに含めただけ

### <a name="use-the-or-operator-and-groups"></a>OR 演算子とグループを使用する

前述のテンプレート発話の一部はとても似ています。 テンプレート発話を減らすには、**グループ** `()` と **OR** `|` 構文を使用します。

次の 2 つのパターンは、グループ `()` と OR `|` の構文を使用して 1 つのパターンに結合できます。

|Intent|省略可能なテキストと事前構築済みエンティティを含む発話の例|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

新しいテンプレート発話は次のようになります。

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

ここでは、必須の動詞の時制を囲む**グループ**と、**or** パイプでつなげた省略可能な `in` および `on` を使用しています。

> [!NOTE]
> 例のテンプレートで _OR_ 記号の `|` (パイプ) を使用する場合は、パイプ記号の前後にスペースを挿入して区切ってください。

1. **[パターン]** ページで **[OrgChart-Manager]** フィルターを選択します。 `manager` を検索して一覧を絞り込みます。

1. いずれかのバージョンのテンプレート発話を保存し (次の手順で編集します)、他のバリエーションを削除します。

1. テンプレートの発話を次のように変更します。

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. ナビゲーション バーの **[Train]\(トレーニング\)** を選択して、アプリをトレーニングします。

3. トレーニングが完了したら、パネル上部の **[Test]\(テスト\)** を選択して、テスト パネルを開きます。

    テスト ウィンドウを使用して発話のバージョンをテストします。

    |テスト ウィンドウに入力する発話|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

より多くのパターン構文を使用することで、高い予測スコアを保ったまま、アプリ内で管理すべきテンプレート発話の数を減らします。

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>発話の開始と終了のアンカーを使用する

パターン構文には、キャレット `^` の最初と最後の発話アンカー構文が用意されています。 最初と最後の発話アンカーは、非常に具体的でリテラルな可能性がある発話をターゲットにするために一緒に使用するか、意図をターゲットにするために個別に使用することができます。

## <a name="using-patternany-entity"></a>Pattern.any エンティティを使用する

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Pattern.any を使用して発話の例を追加する

1. 上部のナビゲーションから **[ビルド]** を選択し、左側のナビゲーションから **[Intents]\(意図\)** を選択します。

1. 意図の一覧から **FindForm** を選択します。

1. 発話の例をいくつか追加します。 Pattern.any として予測されるべきテキストは**太字**で示しています。 発話に含まれるフォーム名は、周囲にある他の単語から判断するのは困難です。 Pattern.any でエンティティの境界をマークすると効果的です。

    |発話の例|フォーム名|
    |--|--|
    |フォーム **ラボで火災が発生したときにするべきこと** はどこにあり、私がそれを読んだ後に誰が署名する必要がありますか?|ラボで火災が発生したときにするべきこと
    |**会社の新しい従業員からの配置換えリクエスト** はサーバー上のどこにありますか?|会社の新しい従業員からの配置換えリクエスト|
    |"**メイン キャンパスでの健康とウェルネスのリクエスト**" を作成したのは誰で、最新バージョンはいくつですか?|メイン キャンパスでの健康とウェルネスのリクエスト|
    |私は "**物理資産を含むオフィス移転リクエスト**" という名前のフォームを探しています。 |物理資産を含むオフィス移転リクエスト|

    Pattern.any エンティティがない場合、フォーム名の多くのバリエーションのため、フォームのタイトルの末尾はどこかを LUIS が理解することは難しくなります。

### <a name="create-a-patternany-entity"></a>Pattern.any エンティティの作成
Pattern.any エンティティは、さまざまな長さのエンティティを抽出します。 パターン構文によってエンティティの先頭と末尾がマークされるため、これはそのパターン内でのみ機能します。

1. 左側のナビゲーションの **[Entities]\(エンティティ\)** を選択します。

1. **[+ Create]\(+ 作成\)** を選択し、名前「`FormName`」を入力し、タイプとして **[Pattern.any]** を選択します。 **［作成］** を選択します

### <a name="add-a-pattern-that-uses-the-patternany"></a>Pattern.any を使用するパターンを追加する

1. 左側のナビゲーションの **[Patterns]\(パターン\)** を選択します。

1. 意図 **[FindForm]** を選択します。

1. 新しいエンティティを使用する次のテンプレート発話を入力します。

    |テンプレート発話|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. アプリをトレーニングします。

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>新しいパターンで自由形式のデータが抽出されるかどうかをテストする
1. 上部のバーの **[Test]\(テスト\)** を選択して、テスト パネルを開きます。

1. 次の発話を入力します。

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. 結果の下の **[検査]** を選択して、エンティティと意図のテスト結果を確認します。

    まずエンティティ `FormName` が検出され、次にパターンが検出されて意図が決定されます。 テスト結果でエンティティが検出されず、その結果としてパターンが検出されない場合は、(パターンではなく) その意図の発話の例をさらに追加する必要があります。

1. 上部のナビゲーションの **[Test]\(テスト\)** を選択してテスト パネルを閉じます。

### <a name="using-an-explicit-list"></a>明示的なリストの使用

Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](reference-pattern-syntax.md#explicit-lists)を使用してこの問題を修正します。

## <a name="what-did-this-tutorial-accomplish"></a>このチュートリアルで達成されたこと

このチュートリアルでは、LUIS で意図を予測しやすくすると共に、発話の例を追加しなくてもスコアを大幅に引き上げることができるようパターンを追加しました。 エンティティと無視可能テキストのマーク付けによって、LUIS でより多様な発話にパターンを適用できるようになりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ


> [!div class="nextstepaction"]
> [パターンと共にロールを使用する方法について](luis-tutorial-pattern.md)
