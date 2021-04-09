---
title: モデルを使用した設計 - LUIS
description: 言語を理解するために、いくつかの種類のモデルを使用できます。 複数の方法で使用できるモデルもあります。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316513"
---
# <a name="design-with-intent-and-entity-models"></a>意図およびエンティティ モデルを使用した設計

Language Understanding には、アプリ スキーマを定義するための 2 種類のモデルが用意されています。 アプリ スキーマによって、新しいユーザーの発話の予測から受け取る情報が決まります。

アプリ スキーマは、[機械教示](#authoring-uses-machine-teaching)を使用して作成したモデルから構築されます。
* [意図](#intents-classify-utterances)を使用してユーザーの発話を分類します
* [エンティティ](#entities-extract-data)を使用して発話からデータを抽出します

## <a name="authoring-uses-machine-teaching"></a>作成には機械教示が使用されます

LUIS の機械学習手法を使用すると、簡単に概念をコンピューターに教えることができます。 LUIS を使用するために "_機械学習_" を理解する必要はありません。 代わりに、自分が教師となり、概念の例を提供し、他の関連する概念を使用して概念をモデル化する方法を説明することにより、概念を LUIS に伝えます。 教師となり、予測ミスを特定して修正することで、LUIS のモデルを対話形式で向上させることもできます。

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>意図分類発話

意図は、発話の例を分類して、意図について LUIS を教えます。 意図内の発話の例は、発話の肯定的な例として使用されます。 これらの同じ発話は、他のすべての意図で否定的な例として使用されます。

本を注文するためにユーザーの意図を判断する必要があるアプリ、および顧客の発送先住所が必要なアプリを考えてみましょう。 このアプリには、`OrderBook` と `ShippingLocation`の 2 つの意図があります。

次の発話は、`OrderBook` 意図の **肯定的な例** と、`ShippingLocation` と `None` の意図の **否定的な例** です。

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>データを抽出するエンティティ

エンティティは、発話から抽出するデータの単位を表します。 機械学習エンティティは、機械学習エンティティであるサブエンティティを含む最上位レベルのエンティティです。

機械学習エンティティの例としては、航空券の注文があります。 概念的には、これは、日付、時刻、座席数、ファースト クラスまたはコーチなどの座席の種類、出発地、目的地、食事の選択など、多くの小さなデータ単位を持つ 1 つのトランザクションです。

## <a name="intents-versus-entities"></a>意図とエンティティ

意図は、発話 _全体_ の目的とする結果であり、エンティティは発話から抽出されたデータの一部です。 通常、意図はクライアント アプリケーションで実行する必要があるアクションに関連付けられています。 エンティティは、このアクションを実行するために必要な情報です。 プログラミングの観点からは、意図によってメソッドの呼び出しがトリガーされ、そのエンティティがメソッド呼び出しのパラメーターとして使用されます。

この発話は、意図を持つ _必要があり_、多くのエンティティを持つ _場合があります_。

`Buy an airline ticket from Seattle to Cairo`

この発話には 1 つの意図があります。

* 航空券の購入

この発話にはいくつかのエンティティがある _場合があります_。

* シアトル (出発地) とカイロ (目的先) の場所
* 1 つのチケットの枚数

## <a name="entity-model-decomposition"></a>エンティティ モデルの分解

LUIS はオーサリング API を使用する "_モデルの分解_" をサポートしています。これを使用して、概念を小さなパーツに分解します。 これにより、さまざまな部分がどのように構築および予測されるかについて自信を持ってモデルを構築することができます。

モデルの分解には、次の部分があります。

* [意図](#intents-classify-utterances)
    * [features](#features)
* [機械学習エンティティ](reference-entity-machine-learned-entity.md)
    * サブエンティティ (機械学習エンティティでもあります)
        * [features](#features)
            * [フレーズ リスト](luis-concept-feature.md)
            * [非機械学習エンティティ](luis-concept-feature.md) ([正規表現](reference-entity-regular-expression.md)、[リスト](reference-entity-list.md)、[事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)など)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>特徴

[特徴](luis-concept-feature.md)とは、お使いのシステムによって順守される、データの特徴的な特性または属性です。 機械学習の特徴は、LUIS にとって、概念を区別するものをどこで探すかに関する重要な手がかりとなります。 これらは LUIS に使用できるヒントですが、厳密な規則ではありません。 これらのヒントは、データを検索するためにラベルと組み合わせて使用されます。

## <a name="patterns"></a>パターン

[パターン](luis-concept-patterns.md)は、複数の発話が非常に似ているときに、精度を改善するように設計されています。 パターンを使用すると、さらに多くの発話を提供しなくても意図の精度を高めることができます。

## <a name="extending-the-app-at-runtime"></a>実行時のアプリの拡張

アプリのスキーマ (モデルと機能) がトレーニングされ、予測エンドポイントに公開されます。 予測を強化するために、ユーザーの発話と共に、予測エンドポイントに[新しい情報を渡す](schema-change-prediction-runtime.md)ことができます。

## <a name="next-steps"></a>次のステップ

* [意図](luis-concept-intent.md)と[エンティティ](luis-concept-entity-types.md)を理解する
* [特徴](luis-concept-feature.md)の詳細を確認する