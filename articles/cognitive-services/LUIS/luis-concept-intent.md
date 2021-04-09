---
title: 意図とエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: 1 つの意図は、ユーザーが実行しようとしているタスクまたはアクションを表します。 ユーザーの発話で表明される目的や目標です。 ユーザーがアプリケーションで実行しようとしているアクションに対応する、意図のセットを定義します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025910"
---
# <a name="intents-in-your-luis-app"></a>LUIS アプリにおける意図

意図は、ユーザーが実行しようとしているタスクまたはアクションを表します。 ユーザーの[発話](luis-concept-utterance.md)で表明される目的や目標です。

ユーザーがアプリケーションで実行しようとしているアクションに対応する、意図のセットを定義します。 たとえば、旅行アプリでいくつかの意図を定義します。

旅行アプリの意図   |   発話の例   |
------|------|
 BookFlight     |   "来週のリオへの便を予約して" <br/> "24 日に飛行機でリオに行きたい" <br/> "次の日曜日、リオデジャネイロへの航空券が必要だ"    |
 Greeting     |   "やあ" <br/>"Hello" <br/>"おはようございます"  |
 CheckWeather | "ボストンの天気はどうですか" <br/> "今週末の天気予報を教えてください" |
 なし         | "クッキーのレシピが知りたい"<br>"レイカーズは勝った?" |

すべてのアプリケーションに事前定義済みの意図 "[None](#none-intent)" が付属します。これは、代替として使用できる意図です。

## <a name="prebuilt-domains-provide-intents"></a>事前構築済みドメインによる意図の提供
ユーザーが定義する意図だけでなく、いずれかの[事前構築済みドメイン](./howto-add-prebuilt-models.md)から事前構築済みの意図を使用できます。

## <a name="return-all-intents-scores"></a>すべての意図のスコアを返す
発話は 1 つの意図に割り当てます。 LUIS では、エンドポイントで発話が受信されると、既定ではそのエンドポイントから発話の最上位の意図が返されます。

その発話のすべての意図にスコアを付ける場合、予測 API のクエリ文字列にフラグを指定できます。

|予測 API のバージョン|フラグ|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>意図とエンティティの比較
意図は、アプリケーションがユーザーに対して実行する必要のあるアクションを表し、発話全体に基づきます。 発話は最上位スコアの意図を 1 つのみ持つことができますが、エンティティは多数持つことができます。

<a name="how-do-intents-relate-to-entities"></a>

ユーザーの "_意図_" によって、checkweather() 関数の呼び出しなど、クライアント アプリケーションでアクションをトリガーする場合に、意図を作成します。 次に、アクションの実行に必要なパラメーターを表すエンティティを作成します。

|Intent   | Entity | 発話の例   |
|------------------|------------------------------|------------------------------|
| CheckWeather | { "型": "場所", "エンティティ": "シアトル" }<br>{ "型": "builtin.datetimeV2.date","エンティティ": "明日","解像度":"2018-05-23" } | `Seattle` の `tomorrow` の天気はどうですか |
| CheckWeather | { "型": "日付の範囲", "エンティティ": "今週末" } | `this weekend` の天気予報を教えてください |
||||

## <a name="prebuilt-domain-intents"></a>事前構築済みドメインの意図

[事前構築済みのドメイン](./howto-add-prebuilt-models.md)は、発話を伴う意図を提供します。

## <a name="none-intent"></a>None 意図

**None** 意図は作成されますが、故意に空のままにされます。 **None** 意図は、必須の意図であり、削除したり名前を変更したりすることはできません。 ご自分のドメイン外の発話をこれに追加してください。

**None** 意図はフォールバック インテントであり、すべてのアプリにとって重要で、発話全体の 10% を占める必要があります。 LUIS にアプリ ドメイン (サブジェクト領域) 内で重要でない発話を学習させるために使用します。 **None** 意図に対して発話を追加しない場合は、LUIS によってドメイン外の発話が、強制的にドメインの意図の 1 つに追加されます。 これによって、発話に対して間違った意図を LUIS に教えることになるため、予測スコアがゆがめられることになります。

発話が None 意図として予測されると、クライアント アプリケーションはさらに質問を重ねたり、有意義な選択ができるようにユーザーを方向付けるメニューを提供したりすることができます。

## <a name="negative-intentions"></a>負の意図
"自動車が **ほしい**" と "自動車が **ほしくない**" など、負と正の意図を判断する必要がある場合、2 つの意図 (正と負を 1 つずつ) を作成し、それぞれに適した発話を追加できます。 または、1 つの意図を作成して、正と負の 2 つの異なる用語をエンティティとしてマークすることもできます。

## <a name="intents-and-patterns"></a>意図とパターン

発話の例があり、その一部または全体を正規表現として定義できる場合は、[正規表現エンティティ](luis-concept-entity-types.md#regular-expression-entity)を[パターン](luis-concept-patterns.md)と組み合わせて使用することを検討してください。

正規表現エンティティを使用すると、パターンと一致するデータ抽出が保証されます。 パターン マッチングでは、正確な意図が返されることが保証されます。

## <a name="intent-balance"></a>意図のバランス
アプリ ドメインの意図は、各意図の間で発話のバランスが取れている必要があります。 ある意図では 10 の発話、別の意図では 500 の発話を用意するようなことはしないでください。 これではバランスが崩れています。 このような状況では、500 の発話が用意されている意図を見直して、意図の多くを[パターン](luis-concept-patterns.md)に再構成できるかどうかを確認します。

**None** 意図は、そのバランスには含まれません。 この意図には、アプリの発話合計の 10% が含まれる必要があります。

## <a name="intent-limits"></a>意図の制限
[制限](luis-limits.md#model-boundaries)を確認して、モデルに追加できる意図の数を理解してください。

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>意図の最大数を超える必要がある場合
まず、システムで使用する意図が多すぎないかどうかを検討してください。

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>複数の意図を複数のエンティティを含む単一の意図に結合可能か
意図が類似していると、LUIS による区別が困難になる可能性があります。 各意図はユーザーが求めている主となるタスクを把握できるくらい十分に異なっている必要がありますが、コードで取得するすべてのパスまで把握する必要はありません。 たとえば、BookFlight と FlightCustomerService は旅行アプリ内で別の意図である可能性がありますが、BookInternationalFlight と BookDomesticFlight は非常に似ています。 システムでこれらを区別する必要がある場合は、意図ではなく、エンティティまたはその他のロジックを使用します。

### <a name="dispatcher-model"></a>ディスパッチャー モデル
[ディスパッチ モデル](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)を使用した LUIS アプリと QnA Maker アプリの結合に関する詳細を参照してください。

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>非常に多くの意図を持つアプリに関するヘルプの要求
意図の数を減らしたり、意図を複数のアプリに分割できない場合は、サポートにお問い合わせください。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

* 意図に関連する重要な単語である、[エンティティ](luis-concept-entity-types.md)について詳細を確認します。
* LUIS アプリで[意図を追加および管理](luis-how-to-add-intents.md)する方法を確認します。
* 意図に関する[ベスト プラクティス](luis-concept-best-practices.md)を確認します。