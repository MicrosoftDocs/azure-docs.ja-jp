---
title: パターンは予測に役立つ
titleSuffix: Language Understanding - Azure Cognitive Services
description: パターンを使用すると、さらに多くの発話を提供しなくても意図の精度を高めることができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 2a160ab7447304dc6eb14f76a723df4e8a4d9f46
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523105"
---
# <a name="patterns-improve-prediction-accuracy"></a>パターンは予測精度を改善する
パターンは、複数の発話が非常に似ているときに、精度を改善するように設計されています。  パターンを使用すると、さらに多くの発話を提供しなくても意図の精度を高めることができます。 

## <a name="patterns-solve-low-intent-confidence"></a>パターンは信頼度の低い意図を解決する
従業員に関連する組織図を報告する人事管理アプリがあるとします。 ある従業員の名前と関係を指定すると、LUIS はその従業員と関わりのある従業員を返します。 Tom という従業員の上司は Alice で、部下は Michael、Rebbeca、および Carl です。

![組織図のイメージ](./media/luis-concept-patterns/org-chart.png)

|発話|予測される意図|意図のスコア|
|--|--|--|
|Who is Tom's subordinate? (Tom の部下は誰。)|GetOrgChart|.30|
|Who is the subordinate of Tom? (Tom の部下は誰ですか。)|GetOrgChart|.30|

アプリに文の長さが異なる、語順が異なる、および違う言葉 ("subordinate (部下)"、"manage (管理)"、"report (所属)" の同義語) が使用されている発話が 10 から 20 ある場合、LUIS が返す信頼度スコアは低くなることがあります。 LUIS が語順の重要度を理解できるように、パターンを作成します。 

パターンは次のような状況を解決します。 

* 意図スコアが低い
* 正しい意図が最上位のスコアではないが、最上位のスコアに近すぎる。 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>パターンは意図を保証するものではない
パターンは、さまざまな予測テクノロジを使用します。 パターンにテンプレートの発話の意図を設定することは、意図の予測を保証するものではありませんが、強力なシグナルになります。 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>パターンでは機械学習エンティティの検出は改善されない

パターンは主として、意図とロールの予測を支援するためのものです。 pattern.any エンティティは、自由形式のエンティティを抽出するために使用されます。 パターンではエンティティが使用されますが、パターンは機械学習エンティティの検出には役に立ちません。  

複数の発話を 1 つのパターンにまとめた場合は、エンティティ予測の向上を期待しないでください。 単純エンティティを動作させるには、発話を追加するか、リスト エンティティを使用する必要があります。そうでないと、パターンは動作しません。

## <a name="patterns-use-entity-roles"></a>パターンはエンティティのロールを使用する
パターン内の 2 つ以上のエンティティがコンテキスト上関連性がある場合、パターンはエンティティの[ロール](luis-concept-roles.md)を使用して、エンティティに関するコンテキスト情報を抽出します。  

## <a name="prediction-scores-with-and-without-patterns"></a>パターンありとなしの場合の予測スコア
発話例が十分に指定されている場合、LUIS はパターンなしで予測の信頼度を改善できます。 パターンがあれば、それほど多くの発話を指定しなくても信頼度スコアを改善できます。  

## <a name="pattern-matching"></a>パターン マッチング
パターンは、まずパターン内のエンティティの検出に基づいて、次に残りのワードとパターンの語順を検証することでマッチングを行います。 パターンが一致するには、パターン内にエンティティが必要です。 パターンは、文字レベルではなく、トークン レベルで適用されます。 

## <a name="pattern-syntax"></a>パターンの構文
パターンの構文は、発話のテンプレートです。 テンプレートには、一致させるワードとエンティティのほか、無視するワードや句読点が含まれている必要があります。 正規表現では**ありません**。 

パターン内のエンティティは中かっこ (`{}`) で囲まれます。 パターンにはエンティティ、およびロール付きのエンティティを含めることができます。 [pattern.any](luis-concept-entity-types.md#patternany-entity) はパターンにおいてのみ使用されるエンティティです。 

パターン構文では、次の構文がサポートされています。

|Function|構文|入れ子レベル|例|
|--|--|--|--|
|エンティティ| {} - 中かっこ|2|フォーム {エンティティ名} はどこですか?|
|省略可能|[] - 大かっこ<BR><BR>省略可能とグループ化の組み合わせの入れ子レベルは、3 に制限されています |2|疑問符は省略可能です [?]|
|グループ化|() - 小かっこ|2|は (a \| b)|
|or| \| - 縦棒 (パイプ)<br><br>1 つのグループで使用できる縦棒 (または) は 2 個に制限されています |-|フォーム ({短いフォーム名} &#x7c; {長いフォーム名} &#x7c; {フォーム番号}) はどこですか| 
|発話の開始または終了|^ - キャレット|-|^発話を開始します<br>発話を終了します^<br>^{number} 個のエンティティとの発話全体の厳密なリテラル一致^|

## <a name="nesting-syntax-in-patterns"></a>パターンでの入れ子構文

角かっこの**省略可能**構文は、2 レベルまで入れ子にできます。 (例: `[[this]is] a new form`)。 この例では、次の発話が許容されます。 

|入れ子になった省略可能な発話の例|説明|
|--|--|
|this is a new form (これは新しいフォームです)|パターン内のすべての単語と一致します|
|is a new form (新しいフォームです)|外部の省略可能な単語およびパターン内の省略可能ではない単語と一致します|
|a new form (新しいフォーム)|必要な単語のみと一致します|

かっこによる**グループ化**構文は、2 レベルまで入れ子にできます。 (例: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`)。 この機能により、3 つのエンティティのどれとでも一致できます。 

Entity1 が出発地 (Seattle) や到着地 (Cairo) などの役割を持つ場所であり、Entity 2 がリスト エンティティの既知のビル名 (RedWest-C) である場合、次の発話はこのパターンにマップされます。

|入れ子になったグループ化の発話の例|説明|
|--|--|
|RedWest-C|外側のグループ化エンティティと一致します|
|シアトル|内側のグループ化エンティティの 1 つと一致します|
|Cairo|内側のグループ化エンティティの 1 つと一致します|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>省略可能構文でのグループに対する入れ子の制限

**グループ化**と**省略可能**の構文の組み合わせでは、入れ子レベルは 3 に制限されます。

|許可|例|
|--|--|
|はい|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|いいえ |( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>"または" 構文を含むグループの入れ子の制限

**グループ化**と "**または**" の構文の組み合わせでは、縦棒は 2 個に制限されます。

|許可|例|
|--|--|
|はい|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|いいえ |( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>パターン テンプレートにエンティティを追加する構文
パターン テンプレートにエンティティを追加するには、エンティティ名を `Who does {Employee} manage?` のように中かっこで囲みます。 

|エンティティのあるパターン|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>パターン テンプレートにエンティティとロールを追加する構文
エンティティのロールは、`{entity:role}` のようにエンティティ名、コロン、ロール名の順に記述します。 パターン テンプレートにロール付きのエンティティを追加するには、エンティティ名とロール名を `Book a ticket from {Location:Origin} to {Location:Destination}` のように中かっこで囲みます。 

|エンティティ ロールのあるパターン|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>パターン テンプレートに pattern.any を追加する構文
Pattern.any エンティティでは、パターンに可変長のエンティティを追加することができます。 パターン テンプレートが後に続く限り、pattern.any は任意の長さで指定できます。 

**Pattern.any** エンティティをパターン テンプレートに追加するには、Pattern.any エンティティを `How much does {Booktitle} cost and what format is it available in?` のように中かっこで囲みます。  

|Pattern.any エンティティのあるパターン|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|パターン内の書籍のタイトル|
|--|
|How much does **steal this book** cost and what format is it available in? (steal this book (この本を盗む) の価格はいくらで、どの形式で購入できますか。)|
|How much does **ask** cost and what format is it available in? (ask (質問) の価格はいくらで、どの形式で購入できますか。)|
|How much does **The Curious Incident of the Dog in the Night-Time** cost and what format is it available in? (The Curious Incident of the Dog in the Night-Time (夜間の犬に関する面白い事件) の価格はいくらで、どの形式で購入できますか。)| 

LUIS は Pattern.any エンティティに基づいて書籍のタイトルの終了を認識するので、書籍のタイトルの単語で LUIS が混乱することはありません。

## <a name="explicit-lists"></a>明示的なリスト

オーサリング API で次のときの例外を許可することで[明示的なリスト](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)を作成します。

* パターンに [Pattern.any](luis-concept-entity-types.md#patternany-entity) が含まれている
* そのパターン構文で、発話に基づいて正しくないエンティティ抽出の可能性が許可されている。 

たとえば、オプションの構文 `[]` とエンティティ構文 `{}` の両方が含まれるパターンがあり、データを正しくない方法で抽出するように結合されているとします。

'[find] email about {subject} [from {person}]' というパターンを考えてみます。

次の発話では **subject** エンティティと **person** が一方では正しく抽出されており、もう一方では正しく抽出されていません。

|発話|エンティティ|正しい抽出|
|--|--|:--:|
|email about dogs from Chris (クリスから犬に関する電子メール)|subject=dogs (犬)<br>person=Chris (クリス)|✔|
|email about the man from La Mancha (ラ・マンチャからその男に関する電子メール)|subject=the man (その男)<br>person=La Mancha (ラ・マンチャ)|X|

前の表では、subject は `the man from La Mancha` (書籍のタイトル) である必要がありますが、subject にオプションのワード `from` が含まれているため、タイトルが間違って予測されています。 

このパターンの例外を修正するには、[明示的なリストのオーサリング API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) を使用して、`the man from la mancha` を {subject} エンティティに対する明示的なリストの一致として追加します。

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>テンプレートの発話でオプションのテキストをマークする構文
正規表現の角かっこの構文 `[]` を使用して、発話内のオプションのテキストをマークします。 オプションのテキストは、最大で 2 つの角かっこのみを入れ子にできます。

|省略可能なテキストのあるパターン|意味|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` と `from {person}` は省略可能です|
|助けていただけますか[?]|句読点は省略可能です|

句読点 (`?`、`!`、`.`) は無視しなければならず、パターンで角かっこ構文を使用してそれらを無視する必要があります。 

## <a name="pattern-only-apps"></a>パターンのみのアプリ
意図ごとにパターンがある限り、発話の例を持たない意図を使用してアプリを構築できます。 パターンのみのアプリの場合、そのパターンには機械学習エンティティが含まれていてはなりません。これらのエンティティは発話例を必要とするためです。 

## <a name="best-practices"></a>ベスト プラクティス
[ベスト プラクティス](luis-concept-best-practices.md)を確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [このチュートリアルでパターンを実装する方法を学習する](luis-tutorial-pattern.md)
