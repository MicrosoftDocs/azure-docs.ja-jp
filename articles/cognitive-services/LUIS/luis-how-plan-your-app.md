---
title: アプリの計画 - LUIS
description: 関連するアプリの意図とエンティティについて概説し、Language Understanding Intelligent Service (LUIS) でアプリケーション プランを作成します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018958"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>サブジェクト ドメインとデータ抽出を使用して LUIS アプリ スキーマを計画する

LUIS アプリ スキーマには、サブジェクト [ドメイン](luis-glossary.md#domain)に関連する[意図](luis-glossary.md#intent)と[エンティティ](luis-glossary.md#entity)が含まれています。 [意図](luis-glossary.md#utterance)はユーザーの発話を分類し、エンティティはユーザー発話からデータを抽出します。

## <a name="identify-your-domain"></a>ドメインの特定

LUIS アプリはサブジェクト ドメインが中心です。 たとえば、チケット、フライト、ホテル、およびレンタカーの予約を処理する旅行アプリがあります。 また、エクササイズ、フィットネスの努力の追跡、目標設定に関連するコンテンツを提供するアプリなどもあります。 ドメインを特定することで、ドメインに関連する単語やフレーズを見つけやすくなります。

> [!TIP]
> LUIS には、多くの一般的なシナリオに対応する[事前構築済みのドメイン](./howto-add-prebuilt-models.md)が用意されています。 事前構築されたドメインをベースに、ご自身のアプリを作成できるかどうかを検討してください。

## <a name="identify-your-intents"></a>意図の特定

ご自身のアプリケーションのタスクにとって重要な[意図](luis-concept-intent.md)について考えます。

たとえば、フライトを予約し、目的地の天気を確認する機能を備えた旅行アプリの例で見てみましょう。 これらのアクションの `BookFlight` と `GetWeather` 意図を定義できます。

さらに多くの機能を備えたより複雑なアプリでは、意図がさらに多くなるため、意図を定義するときは、意図が細かくなりすぎないように気を付ける必要があります。 たとえば、`BookFlight` と `BookHotel` は別の意図である必要がありますが、`BookInternationalFlight` と `BookDomesticFlight` は似すぎているかもしれません。

> [!NOTE]
> 使用する意図の数は、ご自身のアプリで実行する必要がある機能の数に合わせることをお勧めします。 定義する意図が多すぎると、LUIS で発話を正しく分類するのが難しくなります。 少なすぎると、一般的になりすぎて、重複する可能性があります。

全体的なユーザーの意図を特定する必要がない場合は、すべてのユーザーの発話例を `None` の意図に追加します。 アプリで必要な意図が増えた場合は、後で作成できます。

## <a name="create-example-utterances-for-each-intent"></a>各意図の発話の例の作成

最初は、各意図に多くの発話を作成しないようにします。 意図を特定したら、意図ごとに 15 個から 30 個の発話の例を作成します。 各発話は、以前に提供された発話とは異なる必要があります。 全体の単語数、単語の選択、動詞の時制、[句読点](luis-reference-application-settings.md#punctuation-normalization)など、発話に多様性を持たせることをお勧めします。

詳細については、[LUIS アプリに適した発話の概要](luis-concept-utterance.md)に関する記事を参照してください。

## <a name="identify-your-entities"></a>エンティティの特定

発話の例では、抽出するエンティティを特定します。 フライトを予約するには、目的地、日付、航空会社、チケットの種類、搭乗クラスなどの情報が必要です。 これらのデータ型のエンティティを作成し、発話例の[エンティティ](luis-concept-entity-types.md)をマークします。 意図を達成するにはエンティティが重要です。

どのエンティティをアプリで使用するかを判断するときは、オブジェクトの種類間の関係を把握するための多種多様なエンティティがあることに注意してください。 「[LUIS のエンティティ](luis-concept-entity-types.md)」では、各種エンティティについて詳しく説明しています。

> [!TIP]
> LUIS には、一般的な会話ユーザーのシナリオに合わせて[構築済みのエンティティ](./howto-add-prebuilt-models.md)が用意されています。 アプリケーション開発の出発点として、構築済みのエンティティを使用することを検討してください。

## <a name="resolution-with-intent-or-entity"></a>意図またはエンティティを使用する解決策

多くの場合、特に自然な会話を操作するときには、ユーザーは複数の機能 (つまり、意図) を含む可能性がある発話を提供します。 これに対応するためには、一般的な経験則として、出力の表現は意図とエンティティの両方で行われる可能性があると理解することです。 この表現は、クライアント アプリケーションのアクションにマッピングできる必要があり、その意図に限定される必要はありません。

**Int-ent-ties** は、アクション (通常は意図として理解される) がエンティティとしてもキャプチャされる概念であり、特定のアクションにマップできる出力 JSON の中で、この形式で利用されています。 _Negation_ は、完全抽出において、意図およびエンティティの両方でこの利用の効果を上げるための一般的な使用方法です。

次の 2 つの発話について考えてみます。非常によく似た単語の選択を考慮していますが、結果は異なります。

|発話|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

2 つの独立した意図を用意する代わりに、`FlightAction` 機械学習エンティティによって 1 つの意図を作成します。 機械学習エンティティでは、スケジュールおよびキャンセルの要求の両方に対するアクションの詳細と、出発地または到着地のどちらかを抽出する必要があります。

`FlightAction` エンティティは、機械学習エンティティとサブエンティティの次の psuedo スキーマで構成されます。

* FlightAction
    * アクション
    * 出発地
    * 到着地

抽出しやすくするために、サブエンティティに特徴を追加します。 ユーザーの発話の中で表示したいボキャブラリと、予測応答で返されてほしい値に基づいて、特徴を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [LUIS 開発ライフサイクルの学習](luis-concept-app-iteration.md)