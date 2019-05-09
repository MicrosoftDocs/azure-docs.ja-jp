---
title: 用語集
titleSuffix: Language Understanding - Azure Cognitive Services
description: 用語集では、LUIS API サービスの使用中に目にする可能性のある用語について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ced83b83c2d64ba5c4816f378c66dae9f4210391
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874483"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>一般的な用語や概念に関する Language Understanding の用語集
Language Understanding (LUIS) 用語集では、LUIS API サービスの使用中に目にする可能性のある用語について説明します。

## <a name="active-version"></a>アクティブなバージョン

アクティブな LUIS バージョンは、モデルへの変更が受け入れられるバージョンです。 [LUIS](luis-reference-regions.md) Web サイトで、変更を加えたいバージョンがアクティブでない場合は、最初にそのバージョンをアクティブとして設定する必要があります。

## <a name="authoring"></a>オーサリング

[LUIS](luis-reference-regions.md) Web サイトまたは[オーサリング API](https://aka.ms/luis-authoring-api) のいずれかを使用して、[LUIS アプリ](#luis-app)を作成、管理、およびデプロイする機能です。

## <a name="authoring-key"></a>オーサリング キー

以前の名前付き "プログラム" キーです。 アプリの作成に使用されます。 運用レベルのエンドポイント クエリでは使用されません。 詳細については、「[キーの制限](luis-boundaries.md#key-limits)」を参照してください。   

## <a name="batch-test-json-file"></a>バッチ テキストの JSON ファイル

バッチ ファイルは JSON 配列です。 配列内の各要素に、`text`、`intent`、`entities` の 3 つのプロパティがあります。 `entities` プロパティは配列です。 配列は空にできます。 `entities` 配列が空でない場合、この配列では、エンティティを正しく特定する必要があります。

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>コラボレーター

コラボレーターはアプリの[所有者](#owner)ではありませんが、意図を追加、編集、および削除するための同様のアクセス許可を持っています。

## <a name="currently-editing"></a>現在編集中

[アクティブなバージョン](#active-version)と同じ

## <a name="domain"></a>ドメイン

LUIS のコンテキストでは、**ドメイン**はナレッジの一領域です。 ドメインは、お使いのアプリのナレッジ領域に固有です。 これは、Travel Agent アプリなど、一般的な領域として使用できます。 また、Travel Agent アプリでは、特定の地理的な場所、言語、サービスなど、ご自身の会社の情報領域のみに固有として指定することもできます。

## <a name="endpoint"></a>エンドポイント

[LUIS エンドポイント](https://aka.ms/luis-endpoint-apis) URL は、[LUIS アプリ](#luis-app)が作成および公開された後に、LUIS クエリを送信する場所です。 エンドポイント URL には、公開されたアプリとアプリ ID のリージョンが含まれています。 エンドポイントは、お使いのアプリの **[[Keys and endpoints]\(キーとエンドポイント\)](luis-how-to-azure-subscription.md)** ページにあります。また、[Get App Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API からエンドポイント URL を取得することもできます。

たとえば、エンドポイントは次のようになります。

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Querystring パラメーター|description|
|--|--|
|region| [公開されたリージョン](luis-reference-regions.md#publishing-regions) |
|appID | LUIS アプリ ID |
|subscriptionID | Azure Portal で作成された LUIS エンドポイント (サブスクリプション) キー |
|q | 発話 |
|timezoneOffset| minutes|

## <a name="entity"></a>エンティティ

[エンティティ](luis-concept-entity-types.md)は、[意図](luis-concept-intent.md)に関連する情報を説明する[発話](luis-concept-utterance.md)の重要な単語で、意図には不可欠なことがあります。 エンティティは、実質的には LUIS におけるデータ型です。

## <a name="f-measure"></a>F メジャー

[バッチ テスト](luis-interactive-test.md#batch-testing)では、テストの精度の測定値です。

## <a name="false-negative"></a>検知漏れ (FN)

[バッチ テスト](luis-interactive-test.md#batch-testing)では、このデータ ポイントは、アプリが誤ってターゲットの意図/エンティティの不在を予測した発話を表します。

## <a name="false-positive"></a>誤検知 (FP)

[バッチ テスト](luis-interactive-test.md#batch-testing)では、このデータ ポイントは、アプリが誤ってターゲットの意図/エンティティの存在を予測した発話を表します。

## <a name="features"></a>機能

機械学習では、"[機能](luis-concept-feature.md)" は、お使いのシステムによって順守される、データの特徴的な特性または属性です。

## <a name="intent"></a>意図

[意図](luis-concept-intent.md)は、ユーザーが実行しようとしているタスクまたはアクションを表します。 これは、航空券の予約、請求書の支払い、ニュース記事の検索など、ユーザーの入力によって伝達される目的や目標です。 LUIS では、意図の予測は、全体的な発話に基づいています。 一方、エンティティは発話の一部です。

## <a name="labeling"></a>ラベル付け

ラベル付けは、意図の[発話](#utterance)の単語またはフレーズを、[エンティティ](#entity) (データ型) に関連付けるプロセスです。

## <a name="luis-app"></a>LUIS アプリ

LUIS アプリは、[意図](#intent)、[エンティティ](#entity)、ラベル付き[発話](#utterance)など、自然言語処理のトレーニング済みデータ モデルです。

## <a name="owner"></a>所有者

各アプリに、そのアプリを作成した所有者が 1 人います。 所有者は、[コラボレーター](#collaborator)を追加できます。

## <a name="pattern"></a>パターン
以前のパターン機能は、こちらの[パターン](luis-concept-patterns.md)に置き換えられています。 パターンを使用して、提供するトレーニングの例を減らし、予測精度を改善します。

## <a name="phrase-list"></a>フレーズ リスト

[フレーズ リスト](luis-concept-feature.md#what-is-a-phrase-list-feature)には、同じように処理する必要がある、同じクラスに属している値 (単語またはフレーズ) のグループが含まれています (都市名、製品名など)。 交換可能な一覧は、シノニムとして扱われます。

## <a name="prebuilt-domains"></a>事前構築済みのドメイン

[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)は、ホーム オートメーション (HomeAutomation)、レストランの予約 (RestaurantReservation) など、特定のドメイン用に構成された LUIS アプリです。 意図、発話、およびエンティティは、このドメインに対して構成されています。

## <a name="prebuilt-entity"></a>事前構成済みのエンティティ

[事前構成済みのエンティティ](luis-prebuilt-entities.md)は、数値、URL、電子メールなど、一般的な情報の種類のエンティティで、LUIS によって提供されます。 事前構築済みのエンティティを、お使いのアプリケーションに追加します。

## <a name="precision"></a>精度
[バッチ テスト](luis-interactive-test.md#batch-testing)では、精度 (陽性予測値とも呼ばれます) は、取得された発話の中にある関連する発話の割合です。

## <a name="programmatic-key"></a>プログラム キー

[オーサリング キー](#authoring-key)に名前が変更されました。

## <a name="publish"></a>公開

公開とは、LUIS の[アクティブ バージョン](#active-version)を、ステージングまたは運用[エンドポイント](#endpoint)のいずれかで使用できるようにすることです。  

## <a name="quota"></a>クォータ

LUIS クォータとは、[Azure サブスクリプション レベル](https://aka.ms/luis-price-tier)の制限です。 LUIS クォータは、1 秒あたりの要求数 (HTTP 状態 429) と 1 か月の要求数合計 (HTTP 状態 403) の両方によって制限できます。

## <a name="recall"></a>再現率
[バッチ テスト](luis-interactive-test.md#batch-testing)では、再現率 (感度とも呼ばれます) は、LUIS が一般化を行う能力です。

## <a name="semantic-dictionary"></a>セマンティック ディクショナリ
セマンティック ディクショナリは、リスト エンティティ ページとフレーズ リスト ページに用意されています。 セマンティック ディクショナリでは、現在のスコープに基づいて単語の提案が提供されます。

## <a name="sentiment-analysis"></a>感情分析
感情分析では、[Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) によって得られる発話の正または負の値が提供されます。

## <a name="speech-priming"></a>音声認識の準備

音声認識の準備により、LUIS モデルで音声認識サービスを準備できます。

## <a name="spelling-correction"></a>スペル修正

予測の前に発話の単語のスペル ミスを修正するには、Bing のスペル チェック機能を有効にします。

## <a name="starter-key"></a>スターター キー

[プログラム キー](#programmatic-key)と同じです。オーサリング キーに名前が変更されました。

## <a name="subscription-key"></a>サブスクリプション キー

サブスクリプション キーは、[ユーザーが Azure で作成した](luis-how-to-azure-subscription.md) LUIS サービスに関連付けられた**エンドポイント** キーです。 このキーは[オーサリング キー](#programmatic-key)ではありません。 エンドポイント キーがある場合は、それをオーサリング キーの代わりに、すべてのエンドポイント要求に対して使用してください。 現在のエンドポイント キーは、[LUIS](luis-reference-regions.md) Web サイトの [**[Keys and endpoints]\(キーとエンドポイント\)** ページ](luis-how-to-azure-subscription.md)の一番下にあるエンドポイント URL の中で確認できます。 これは、**subscription-key** の名前/値ペアの値です。

## <a name="test"></a>テスト

LUIS アプリの[テスト](luis-interactive-test.md#test-your-app)とは、発話を LUIS に渡して、JSON の結果を表示することです。

## <a name="timezoneoffset"></a>タイムゾーン オフセット

エンドポイントには、timezoneOffset が含まれています。 これは、事前構築済みエンティティ datetimeV2 から追加または削除する必要がある数値 (分) です。 たとえば、"今何時ですか?" という発話の場合、返される datetimeV2 は、クライアント要求の現在時刻です。 クライアント要求元が、ボットのユーザーと異なるボットまたは他のアプリケーションの場合、ボットとそのユーザーの間のオフセットを渡す必要があります。

「[事前構築済み datetimeV2 エンティティのタイム ゾーンの変更](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)」を参照してください。

## <a name="token"></a>トークン
トークンは、エンティティでラベルを付けることができる最小単位です。 トークン化は、アプリケーションの[カルチャ](luis-language-support.md#tokenization)に基づいて行われます。

## <a name="train"></a>トレーニング

トレーニングは、前回のトレーニング以降に行われた[アクティブなバージョン](#active-version)に対する変更を LUIS に教えるプロセスです。

## <a name="true-negative"></a>真陰性 (TN)

[バッチ テスト](luis-interactive-test.md#batch-testing)では、このデータ ポイントは、アプリが正しくターゲットの意図/エンティティの不在を予測した発話を表します。

## <a name="true-positive"></a>真陽性 (TP)

[バッチ テスト](luis-interactive-test.md#batch-testing)では、このデータ ポイントは、アプリが正しくターゲットの意図/エンティティの存在を予測した発話を表します。

## <a name="utterance"></a>発話

発話は、"次の火曜日にシアトル行のチケットを 2 枚予約する" などの自然言語フレーズです。 意図には発話の例が追加されます。

## <a name="version"></a>バージョン

LUIS [バージョン](luis-how-to-manage-versions.md)は、LUIS アプリ ID と公開済みエンドポイントに関連付けられている特定のデータ モデルです。 LUIS アプリごとに、少なくとも 1 つのバージョンがあります。
