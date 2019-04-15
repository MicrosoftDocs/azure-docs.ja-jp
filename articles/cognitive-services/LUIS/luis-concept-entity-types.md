---
title: エンティティの種類
titleSuffix: Language Understanding - Azure Cognitive Services
description: エンティティは、発話からデータを抽出します。 エンティティの種類では、データの予測可能な抽出が提供されます。 エンティティには、機械学習と非機械学習の 2 種類があります。 発話で使用しているエンティティの種類を把握していることが重要です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: 6e37466145af58a52a86a08a2a873e406c99b9e5
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895547"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>LUIS におけるエンティティの種類とその目的

エンティティは、発話からデータを抽出します。 エンティティの種類では、データの予測可能な抽出が提供されます。 エンティティには、機械学習と非機械学習の 2 種類があります。 発話で使用しているエンティティの種類を把握していることが重要です。 

## <a name="entity-compared-to-intent"></a>エンティティと意図の比較

エンティティは、抽出しようとしている発話内の単語またはフレーズを表します。 発話には、多数のエンティティを含めることも、まったく含めないこともできます。 エンティティは、類似したオブジェクトのコレクション (場所、もの、人、イベント、または概念) を含むクラスを表します。 エンティティは、意図に関連する情報を説明し、アプリがタスクを実行するうえで不可欠なこともあります。 たとえば、ニュース検索アプリには、"トピック"、"ソース"、"キーワード"、"公開日" などのエンティティが含まれることがありますが、これらはニュースを検索する際のキー データです。 旅行予約アプリでは、"場所"、"日付"、"航空会社"、"旅行クラス"、および "チケット" が、航空券の予約のキー情報です ("航空券予約" という意図に関連して)。

一方、意図は、発話全体の予測を表します。 

## <a name="entities-help-with-data-extraction-only"></a>エンティティはデータの抽出だけに役立つ

エンティティのラベル付けやマーキングは、エンティティ抽出のみを目的として行います。意図の予測には役立ちません。

## <a name="entities-represent-data"></a>エンティティはデータを表す

エンティティは、発話から引き出す必要があるデータです。 名前、日付、製品名、任意の単語のグループを指定できます。 

|発話|エンティティ|データ|
|--|--|--|
|ニューヨーク行きのチケットを 3 枚購入する|事前構築済みの数値<br>場所.目的地|3<br>ニューヨーク|
|3 月 5 日のニューヨーク発ロンドン行きのチケットを購入する|場所.出発地<br>場所.目的地<br>事前構築済みの datetimeV2|ニューヨーク<br>ロンドン<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>エンティティは省略可能だが強く推奨される

意図は必須ですが、エンティティは省略可能です。 アプリ内のすべての概念を表すエンティティを作成する必要はありません。クライアント アプリケーションがアクションを実行するために必須のエンティティのみ作成します。 

ボットが継続するために必要な詳細が発話に含まれない場合は、エンティティを追加する必要はありません。 アプリが成熟するに従い、後で追加することもできます。 

情報の使用方法が不明の場合は、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序数](luis-reference-prebuilt-ordinal.md)、[電子メール アドレス](luis-reference-prebuilt-email.md)、[電話番号](luis-reference-prebuilt-phonenumber.md)などの一般的な事前構築済みのエンティティを追加します。

## <a name="label-for-word-meaning"></a>単語の意味のラベル

単語の選択や配置は同じなのに同じものを意味しない場合は、そのエンティティでラベル付けしません。 

次の発話で、単語 `fair` は、同形異義語です。 スペルは同じですが、意味が異なります。

|発話|
|--|
|今年の夏、シアトル地区でどのような種類のカウンティー フェアが開催されますか?|
|シアトル レビューの現在の評価は公正ですか?|

イベント エンティティですべてのイベント データを検索する場合は、2 つ目ではなく最初の発話の単語 `fair` にラベルを付けます。

## <a name="entities-are-shared-across-intents"></a>エンティティは意図全体で共有される

エンティティは、意図全体で共有されます。 これらは 1 つの意図に属しません。 意図とエンティティは、意味的に関連付けることができますが、排他的な関係ではありません。

発話 "パリへのチケットを予約する" の "パリ" は、場所を表すエンティティです。 LUIS では、ユーザーの発話で言及されるエンティティを認識することで、ユーザーの要求を実現するために実行する特定のアクションをクライアント アプリケーションで選択できるようにします。

## <a name="mark-entities-in-none-intent"></a>None 意図のエンティティをマークする

**None** 意図を含むすべての意図で、可能な場合にエンティティをマークする必要があります。 これによって、LUIS では、エンティティが発話内のどこにあるかと、どの単語がエンティティの周囲にあるかについて学習できます。 

## <a name="entity-status-for-predictions"></a>予測に対するエンティティの状態

LUIS ポータルでは、発話の例内のエンティティが、マークされたエンティティと異なるか、別のエンティティに近すぎて明確でない場合に、それが通知されます。 これは、発話の例内で赤色の下線で示されます。 

詳しくは、「[エンティティの状態の予測](luis-how-to-add-example-utterances.md#entity-status-predictions)」をご覧ください。 

## <a name="types-of-entities"></a>エンティティの種類

LUIS では、さまざまな種類のエンティティが提供されます。 データの抽出方法と抽出後にそれを表現する方法に基づいてエンティティを選択します。

エンティティは機械学習を使用して抽出できるので、LUIS では発話にエンティティがどのように出現するかについて継続的に学習できます。 機械学習なしでエンティティを抽出し、正確なテキストまたは正規表現とマッチングできます。 パターン内のエンティティは、混合実装を使用して抽出できます。 

エンティティが抽出されたら、エンティティ データを 1 単位の情報として表現するか、他のエンティティと組み合わせて、クライアント アプリケーションで使用できる情報の単位を形成できます。

|機械学習|マーク可能|チュートリアル|例<br>Response|エンティティの種類|目的|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**複合**](#composite-entity)|エンティティ型に関係なく、エンティティのグループ。|
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**階層構造**](#hierarchical-entity)|シンプルなエンティティのグループ。|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**List**](#list-entity)|項目の一覧と、完全なテキスト一致を使用して抽出されたそれらのシノニム。|
|混合||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|エンティティの末尾の特定が困難なエンティティ。|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**事前構築済み**](#prebuilt-entity)|さまざまな種類のデータを抽出するために既にトレーニングされています。|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**正規表現**](#regular-expression-entity)|正規表現を使用してテキストを照合します。|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**シンプル**](#simple-entity)|単語または語句に 1 つの概念が含まれています。|

すべての意図の発話の例では、機械学習エンティティのみをマークする必要があります。 機械学習エンティティは、[エンドポイント クエリ](luis-concept-test.md#endpoint-testing)を使用してテストし、[エンドポイントの発話の確認](luis-how-to-review-endpoint-utterances.md)を行うと、最適に動作します。 

Pattern.any エンティティは、意図のユーザー例内ではなく、[パターン](luis-how-to-model-intent-pattern.md) テンプレート例内でマークする必要があります。 

混合エンティティでは、エンティティの検出方法の組み合わせを使用します。

## <a name="composite-entity"></a>複合エンティティ

複合エンティティは、事前構築済みエンティティ、シンプル エンティティ、正規表現エンティティ、リスト エンティティ、階層構造エンティティなどの他のエンティティで構成されます。 個別のエンティティが、エンティティ全体を形成します。 

このエンティティは、次のようなデータに最適です。

* 相互に関連している。 
* 発話のコンテキストで相互に関連する。
* さまざまなエンティティ型を使用する。
* クライアント アプリケーションによって情報の単位としてグループ化され、処理される必要がある。
* 機械学習を必要とするさまざまなユーザー発話がある。

![複合エンティティ](./media/luis-concept-entities/composite-entity.png)

[チュートリアル](luis-tutorial-composite-entity.md)<br>
[エンティティの JSON 応答例](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>階層構造エンティティ

階層構造エンティティは、コンテキストから学習された子と呼ばれるシンプル エンティティのカテゴリです。

このエンティティは、次のようなデータに最適です。

* シンプル エンティティである。
* 発話のコンテキストで相互に関連する。
* 特定の単語の選択を使用して、それぞれの子エンティティを表す  ("から/への"、"を出て/に向かう"、"から/に" などの単語)。
* 子が同じ発話内に頻出する。 
* クライアント アプリによって情報の単位としてグループ化され、処理される必要がある。

次の場合は使用しないでください。

* コンテキストに関係なく、子にテキストが完全に一致するエンティティが必要。 代わりに[リスト エンティティ](#list-entity)を使用してください。 
* 他のエンティティ型と親子関係があるエンティティが必要。 [複合エンティティ](#composite-entity)を使用してください。

![階層構造エンティティ](./media/luis-concept-entities/hierarchical-entity.png)

[チュートリアル](luis-quickstart-intent-and-hier-entity.md)<br>
[エンティティの JSON 応答例](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>ロールと階層エンティティ

パターンの[ロール](luis-concept-roles.md#roles-versus-hierarchical-entities)は、階層型エンティティと同じ問題を解決しますが、すべてのエンティティ型に適用されます。 ロールは、現在パターンでのみ使用できます。 ロールは、意図の発話の例で使用できません。  

## <a name="list-entity"></a>リスト エンティティ

リスト エンティティは、固定かつ限定された関連単語セットとそのシノニムを表します。 LUIS では、リスト エンティティの追加の値は検出されません。 現在のリストに基づいて新しい単語の候補を表示するには、**[Recommend] (推奨)** 機能を使用します。 同じ値を持つリスト エンティティが複数存在する場合は、エンドポイント クエリに各エンティティが返されます。 

エンティティは、次のようなテキスト データに最適です。

* 既知のセットである。
* セットがこのエンティティ型の最大 LUIS [境界](luis-boundaries.md)を超えていない。
* 発話内のテキストがシノニムまたは正規名に完全に一致している。 LUIS では、完全なテキストの一致以外にリストは使用されません。 語幹抽出、複数形、その他のバリエーションは、リスト エンティティでは解決されません。 バリエーションを管理するには、オプションのテキスト構文で[パターン](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)を使用することを検討します。

![リスト エンティティ](./media/luis-concept-entities/list-entity.png)

[チュートリアル](luis-quickstart-intent-and-list-entity.md)<br>
[エンティティの JSON 応答例](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Pattern.any エンティティ

Pattern.any は、エンティティの開始位置と終了位置を示すためにパターンのテンプレート発話でのみ使用される、可変長プレースホルダーです。  

エンティティは、次のような場合に最適です。

* エンティティの末尾を発話の残りのテキストと混同する可能性がある。 
[チュートリアル](luis-tutorial-pattern.md)<br>
[エンティティの JSON 応答例](luis-concept-data-extraction.md#patternany-entity-data)

**例**  
タイトルに基づいて書籍を検索するクライアント アプリケーションでは、pattern.any によって完全なタイトルが抽出されます。 この書籍検索に対して pattern.any を使用するテンプレート発話は `Was {BookTitle} written by an American this year[?]` です。 

次の表では、各行に 2 つのバージョンの発話があります。 上の発話は、LUIS によって発話が最初にどのように認識されるかを示したもので、書籍のタイトルの始まりと終わりが明確ではありません。 下の発話は、抽出のためのパターンが配置された場合に LUIS によって書籍のタイトルがどのように認識されるかを示しています。 

|発話|
|--|
|The Man Who Mistook His Wife for a Hat and Other Clinical Tales (自分の妻を帽子と間違える男やその他の臨床例) は、アメリカ人によって今年執筆されましたか?<br>「**The Man Who Mistook His Wife for a Hat and Other Clinical Tales (自分の妻を帽子と間違える男やその他の臨床例)**」は、アメリカ人によって今年執筆されましたか?|
|Half Asleep in Frog Pajamas (カエルのパジャマで夢うつつ) は、アメリカ人によって今年執筆されましたか?<br>「**Half Asleep in Frog Pajamas (カエルのパジャマで夢うつつ)**」は、アメリカ人によって今年執筆されましたか?|
|The Particular Sadness of Lemon Cake:A Novel (悲しきレモン ケーキ: 小説) は、アメリカ人によって今年執筆されましたか?<br>「**The Particular Sadness of Lemon Cake:A Novel (悲しきレモン ケーキ: 小説)**」は、アメリカ人によって今年執筆されましたか?|
|There's A Wocket In My Pocket! (ポケットにウォケットが!) は、アメリカ人によって今年執筆されましたか?<br>「**There's A Wocket In My Pocket! (ポケットにウォケットが!)**」 は、アメリカ人によって今年執筆されましたか?|

## <a name="prebuilt-entity"></a>事前構築済みのエンティティ

事前構築済みのエンティティは、電子メール、URL、電話番号などの一般的な概念を表す組み込みの型です。 事前構築済みのエンティティ名は予約されています。 発話に検出された場合、アプリケーションに追加される[すべての事前構築済みエンティティ](luis-prebuilt-entities.md)がエンドポイント予測クエリに返されます。 

エンティティは、次のような場合に最適です。

* データが、言語カルチャに対する事前構築済みのエンティティでサポートされている一般的なユース ケースと一致する。 

事前構築済みのエンティティは、いつでも追加および削除できます。

![事前構築済みのエンティティ Number](./media/luis-concept-entities/number-entity.png)

[チュートリアル](luis-tutorial-prebuilt-intents-entities.md)<br>
[エンティティの JSON 応答例](luis-concept-data-extraction.md#prebuilt-entity-data)

これらの事前構築済みエンティティの一部は、オープンソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されます。 ご自分の特定のカルチャまたはエンティティが現在サポートされていない場合は、プロジェクトにご協力ください。 

### <a name="troubleshooting-prebuilt-entities"></a>事前構築済みエンティティのトラブルシューティング

LUIS ポータルでは、事前構築済みエンティティがご自分のカスタム エンティティ以外でタグ付けされている場合、これを解決するにはいくつかの選択肢があります。

発話で同じテキストのカスタム エンティティを抽出する必要がある場合であっても、アプリに追加された事前構築済みエンティティが "_常に_" 返されます。 

#### <a name="change-tagged-entity-in-example-utterance"></a>発話の例でタグが付けられたエンティティを変更する

事前構築済みエンティティがカスタム エンティティと同じテキストまたはトークンである場合は、発話の例でテキストを選択し、タグが付けられた発話を変更します。 

事前構築済みエンティティが、カスタム エンティティより多くのテキストまたはトークンでタグを付けられている場合は、この問題を解決するのに 2 つの選択肢があります。

* [発話の例を削除する](#remove-example-utterance-to-fix-tagging)方法
* [事前構築済みエンティティを削除する](#remove-prebuilt-entity-to-fix-tagging)方法

#### <a name="remove-example-utterance-to-fix-tagging"></a>発話の例を削除してタグ付けを修正する 

最初の選択肢は、発話の例を削除することです。 

1. 発話の例を削除します。
1. アプリを再トレーニングします。 
1. より、完全な発話の例として、エンティティである語句だけを追加して戻すと、事前構築済みのエンティティとしてマークされます。 語句はまだ、事前構築済みエンティティとしてマークされています。 
1. **[意図]** ページの発話の例でエンティティを選択し、カスタム エンティティに変更して、再トレーニングします。 これにより、LUIS では、そのテキストを使用する発話の例に含まれる事前構築済みエンティティとして、この正確なテキストはマークされなくなります。 
1. 元の発話の例全体を意図に追加して戻します。 事前構築済みエンティティではなく、カスタム エンティティが引き続きマークされます。 カスタム エンティティがマークされない場合は、発話でのそのテキストの例をさらに追加する必要があります。

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>事前構築済みエンティティを削除してタグ付けを修正する

1. アプリから事前構築済みエンティティを削除します。 
1. **[意図]** ページで、発話の例に含まれるカスタム エンティティをマークします。
1. アプリをトレーニングします。
1. 事前構築済みエンティティをアプリに追加して戻し、アプリをトレーニングします。 この修正では、事前構築済みエンティティが複合エンティティの一部ではないものと想定しています。

## <a name="regular-expression-entity"></a>正規表現エンティティ 

生の発話テキストには正規表現をお勧めします。 大文字小文字とカルチャのバリアントは無視されます。  正規表現の照合は、スペルチェックによる変更後に、トークン レベルではなく文字レベルで適用されます。 角かっこが多用されているなど、正規表現が複雑すぎる場合は、このモデルに式を追加できません。 [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) ライブラリの全体ではなく一部が使用されます。 

エンティティは、次のような場合に最適です。

* データが一貫して書式設定され、バリエーションにも一貫性がある。
* 正規表現では、2 レベルを超える入れ子は必要ありません。 

![正規表現エンティティ](./media/luis-concept-entities/regex-entity.png)

[チュートリアル](luis-quickstart-intents-regex-entity.md)<br>
[エンティティの JSON 応答例](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>シンプル エンティティ 

シンプル エンティティとは、1 つの概念について説明し、機械学習コンテキストから学習した汎用的エンティティです。 シンプル エンティティは、一般に会社名、製品名、その他のカテゴリの名前などの名前なので、使用する名前のシグナルの強化にシンプル エンティティを使用する場合は、[フレーズ リスト](luis-concept-feature.md)を追加します。 

エンティティは、次のような場合に最適です。

* データが一貫して書式設定されていないが、同じことを示している。 

![シンプル エンティティ](./media/luis-concept-entities/simple-entity.png)

[チュートリアル](luis-quickstart-primary-and-secondary-data.md)<br/>
[エンティティの応答例](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>エンティティの制限

[制限](luis-boundaries.md#model-boundaries)を確認して、モデルに追加できるエンティティの種類ごとの数を把握してください。

## <a name="composite-vs-hierarchical-entities"></a>複合エンティティと階層構造エンティティの比較

複合エンティティと階層構造エンティティは、どちらも親子関係を持ち、機械学習されます。 機械学習により、LUIS では、異なるコンテキスト (単語の配置) に基づいてエンティティを理解できます。 複合エンティティでは、子としてさまざまな種類のエンティティを使用できるため、より柔軟です。 階層構造エンティティの子は、シンプルなエンティティのみです。 

|Type|目的|例|
|--|--|--|
|階層構造|シンプルなエンティティの親子|Location.Origin=New York<br>Location.Destination=London|
|複合|親子エンティティ: 事前構築済み、リスト、シンプル、階層構造| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>最大数を超えるエンティティが必要な場合 

階層構造エンティティと複合エンティティを使用することが必要になる場合があります。 階層構造エンティティには、特性を共有しているエンティティ間や、カテゴリのメンバーであるエンティティ間の関係が反映されます。 子エンティティは、その親のカテゴリのすべてのメンバーです。 たとえば、PlaneTicketClass という名前の階層構造エンティティは、子エンティティ EconomyClass および FirstClass を持つことができます。 階層の範囲は、1 つのレベルの深さのみです。  

複合エンティティは、全体の一部を表します。 たとえば、PlaneTicketOrder をという名前の複合エンティティは、子エンティティ Airline、Destination、DepartureCity、DepartureDate、および PlaneTicketClass を持つことができます。 複合エンティティは、既存のシンプルなエンティティ、階層構造エンティティの子、または事前構築済みのエンティティから作成します。  

LUIS には、機械学習は行われないが、LUIS アプリに固定の値リストを指定できるリスト エンティティ型も用意されています。 「[LUIS Boundaries (LUIS の境界)](luis-boundaries.md)」を参照し、リスト エンティティの種類の制限を確認してください。 

階層構造エンティティ、複合エンティティ、リスト エンティティを検討した結果、制限を超えるエンティティが必要な場合は、サポートにお問い合わせください。 それを行うには、システムに関する詳細情報を収集し、[LUIS](luis-reference-regions.md#luis-website) Web サイトにアクセスして **[サポート]** を選択します。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 

## <a name="next-steps"></a>次の手順

正しい[発話](luis-concept-utterance.md)の概念について学習します。 

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
