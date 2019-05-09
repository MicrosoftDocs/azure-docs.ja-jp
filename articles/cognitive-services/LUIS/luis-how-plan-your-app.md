---
title: アプリの計画
titleSuffix: Language Understanding - Azure Cognitive Services
description: 関連するアプリの意図とエンティティについて概説し、Language Understanding Intelligent Service (LUIS) でアプリケーション プランを作成します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794993"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>主題の領域、意図、エンティティによって LUIS アプリを計画します

アプリを計画することが重要です。 アプリケーションに関連する可能性のある意図やエンティティなど、ドメインを特定します。  

## <a name="identify-your-domain"></a>ドメインの特定

LUIS アプリは、ドメイン固有のトピックを中心としています。  たとえば、チケット、フライト、ホテル、およびレンタカーを予約する旅行アプリがあります。 また、エクササイズ、フィットネスの努力の追跡、目標設定に関連するコンテンツを提供するアプリなどもあります。 ドメインを特定することで、ドメインにとって重要な単語やフレーズを見つけやすくなります。

> [!TIP]
> LUIS には、多くの一般的なシナリオに対応する[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)が用意されています。
> 事前構築されたドメインをベースに、ご自身のアプリを作成できるかどうかを検討してください。

## <a name="identify-your-intents"></a>意図の特定

ご自身のアプリケーションのタスクにとって重要な[意図](luis-concept-intent.md)について考えます。 たとえば、フライトを予約し、目的地の天気を確認する機能を備えた旅行アプリの例で見てみましょう。 これらのアクションに対しては "BookFlight" 意図と "GetWeather" 意図を定義できます。 さらに多くの機能を備えたより複雑なアプリでは、意図がさらに多くなるため、意図を定義するときは、細かくなりすぎないように気を付ける必要があります。 たとえば、"BookFlight" と "BookHotel" はそれぞれ別の意図にする必要がありますが、"BookInternationalFlight" と "BookDomesticFlight" は非常に似ています。

> [!NOTE]
> 使用する意図の数は、ご自身のアプリで実行する必要がある機能の数に合わせることをお勧めします。 定義する意図が多すぎると、LUIS で発話を正しく分類するのが難しくなります。 少なすぎると、一般的になりすぎて、重複が発生する可能性があります。

## <a name="create-example-utterances-for-each-intent"></a>各意図の発話の例の作成

意図を特定したら、意図ごとに 10 から 15 個の発話の例を作成します。 まず、発話をこの数より少なくしたり、各意図に対して多くの発話を作成したりしないでください。 各発話は、前の発話とは異なるものにする必要があります。 全体の単語数、単語の選択、動詞の時制、句読点など、発話に多様性を持たせることをお勧めします。 

## <a name="identify-your-entities"></a>エンティティの特定

発話の例では、抽出するエンティティを特定します。 フライトを予約するには、目的地、日付、航空会社、チケットの種類、搭乗クラスなどの情報が必要です。 これらは意図を実現するうえで重要であるため、これらのデータの種類のエンティティを作成し、発話の例で[エンティティ](luis-concept-entity-types.md)をマークします。 

どのエンティティをアプリで使用するかを判断するときは、オブジェクト間の関係を把握するための多種多様なエンティティがあることに注意してください。 「[LUIS のエンティティ](luis-concept-entity-types.md)」では、各種エンティティについて詳しく説明しています。

## <a name="next-steps"></a>次の手順

アプリをトレーニングして発行し、アプリによってエンドポイント発話が取得されたら、[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)、[フレーズ リスト](luis-concept-feature.md)、[パターン](luis-concept-patterns.md)を使用した予測機能強化の実装を計画します。 


* LUIS アプリを作成するための簡単なチュートリアルについては、[最初の Language Understanding Intelligent Service (LUIS) アプリの作成](luis-get-started-create-app.md)に関するページをご覧ください。
