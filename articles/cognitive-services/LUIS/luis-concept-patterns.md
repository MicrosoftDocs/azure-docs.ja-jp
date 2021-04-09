---
title: 予測に役立つパターン - LUIS
titleSuffix: Azure Cognitive Services
description: パターンを使用すると、さらに多くの発話を提供しなくても意図の精度を高めることができます。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025961"
---
# <a name="patterns-improve-prediction-accuracy"></a>パターンは予測精度を改善する
パターンは、複数の発話が非常に似ているときに、精度を改善するように設計されています。  パターンを使用すると、さらに多くの発話を提供しなくても意図の精度を高めることができます。

## <a name="patterns-solve-low-intent-confidence"></a>パターンは信頼度の低い意図を解決する
従業員に関連する組織図を報告する人事管理アプリがあるとします。 ある従業員の名前と関係を指定すると、LUIS はその従業員と関わりのある従業員を返します。 Tom という従業員の上司は Alice で、部下は Michael、Rebbeca、および Carl です。

![組織図のイメージ](./media/luis-concept-patterns/org-chart.png)

|発話|予測される意図|意図スコア|
|--|--|--|
|Who is Tom's subordinate? (Tom の部下は誰。)|GetOrgChart|.30|
|Who is the subordinate of Tom? (Tom の部下は誰ですか。)|GetOrgChart|.30|

アプリに文の長さが異なる、語順が異なる、および違う言葉 ("subordinate (部下)"、"manage (管理)"、"report (所属)" の同義語) が使用されている発話が 10 から 20 ある場合、LUIS が返す信頼度スコアは低くなることがあります。 LUIS が語順の重要度を理解できるように、パターンを作成します。

パターンは次のような状況を解決します。

* 意図スコアが低い
* 正しい意図が最上位のスコアではないが、最上位のスコアに近すぎる。

## <a name="patterns-are-not-a-guarantee-of-intent"></a>パターンは意図を保証するものではない
パターンは、さまざまな予測テクノロジを使用します。 パターンにテンプレートの発話の意図を設定することは、意図の予測を保証するものではありませんが、強力なシグナルになります。

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>パターンでは機械学習エンティティの検出は改善されない

パターンは主として、意図とロールの予測を支援するためのものです。 _pattern.any_ エンティティは、自由形式のエンティティを抽出するために使用されます。 パターンではエンティティが使用されますが、パターンは機械学習エンティティの検出には役に立ちません。

複数の発話を 1 つのパターンにまとめた場合は、エンティティ予測の向上を期待しないでください。 単純エンティティを動作させるには、発話を追加するか、リスト エンティティを使用する必要があります。そうでないと、パターンは動作しません。

## <a name="patterns-use-entity-roles"></a>パターンはエンティティのロールを使用する
パターン内の 2 つ以上のエンティティがコンテキスト上関連性がある場合、パターンはエンティティの[ロール](./luis-concept-entity-types.md)を使用して、エンティティに関するコンテキスト情報を抽出します。

## <a name="prediction-scores-with-and-without-patterns"></a>パターンありとなしの場合の予測スコア
発話例が十分に指定されている場合、LUIS はパターンなしで予測の信頼度を改善できます。 パターンがあれば、それほど多くの発話を指定しなくても信頼度スコアを改善できます。

## <a name="pattern-matching"></a>パターン マッチング
パターンは、まずパターン内のエンティティの検出に基づいて、次に残りのワードとパターンの語順を検証することでマッチングを行います。 パターンが一致するには、パターン内にエンティティが必要です。 パターンは、文字レベルではなく、トークン レベルで適用されます。

## <a name="pattern-only-apps"></a>パターンのみのアプリ
意図ごとにパターンがある限り、発話の例を持たない意図を使用してアプリを構築できます。 パターンのみのアプリの場合、そのパターンには機械学習エンティティが含まれていてはなりません。これらのエンティティは発話例を必要とするためです。

## <a name="patternany-entity"></a>Pattern.any エンティティ

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>ベスト プラクティス
[ベスト プラクティス](luis-concept-best-practices.md)を確認します。

## <a name="pattern-syntax"></a>パターンの構文

パターンの構文については、[パターン構文のリファレンス](reference-pattern-syntax.md)を参照してください。

## <a name="next-steps"></a>次のステップ

パターンについて学習する:

* [パターンの追加方法](luis-how-to-model-intent-pattern.md)
* [pattern.any エンティティの追加方法](luis-how-to-add-entities.md#add-a-patternany-entity)
* [パターンの構文](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [このチュートリアルでパターンを実装する方法を学習する](luis-tutorial-pattern.md)
