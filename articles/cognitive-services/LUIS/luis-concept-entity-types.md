---
title: エンティティの種類 - LUIS
description: 予測ランタイム時に、エンティティによってユーザーの発話からデータが抽出されます。 "_オプション_" の副次的な目的は、エンティティを特徴量として使用することで、意図またはその他のエンティティの予測を強化することです。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316564"
---
# <a name="extract-data-with-entities"></a>エンティティを使用してデータを抽出する

予測ランタイム時に、エンティティによってユーザーの発話からデータが抽出されます。 "_オプション_" の副次的な目的は、エンティティを特徴量として使用することで、意図またはその他のエンティティの予測を強化することです。

エンティティにはいくつかの種類があります。

* [機械学習エンティティ](reference-entity-machine-learned-entity.md) - これはプライマリ エンティティです。 他のエンティティを使用する前に、このエンティティ型を使用してスキーマを設計する必要があります。
* 必須[特徴量](luis-concept-feature.md)として使用される非機械学習 - 事前構築済みのエンティティによるテキスト完全一致、パターン一致、または検出用
* [Pattern.any](#patternany-entity) - 書籍のタイトルなどの自由形式のテキストを[パターン](reference-entity-pattern-any.md)から抽出します

機械学習エンティティを使用すると、データ抽出の選択肢の幅が最も広くなります。 非機械学習エンティティはテキスト照合によって機能し、機械学習エンティティまたは意図用の[必須特徴量](#design-entities-for-decomposition)として使用されます。

## <a name="entities-represent-data"></a>エンティティはデータを表す

エンティティとは、発話から取得するデータのことです。たとえば、名前、日付、製品名、または重要な単語のグループなどです。 発話には、多数のエンティティを含めることも、まったく含めないこともできます。 クライアント アプリケーションでは、そのタスクを実行するためにデータを必要とする "_可能性_" があります。

エンティティには、モデル内の意図ごとに、すべてのトレーニング発話にわたって一貫性を保持してラベル付けする必要があります。

 独自のエンティティを定義することや、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[ordinal (序数)](luis-reference-prebuilt-ordinal.md)、[email (電子メール)](luis-reference-prebuilt-email.md)、[phone number (電話番号)](luis-reference-prebuilt-phonenumber.md) などの一般的な概念について、事前構築済みのエンティティを使用して時間を節約することができます。

|発話|Entity|Data|
|--|--|--|
|ニューヨーク行きのチケットを 3 枚購入する|事前構築済みの数値<br>到着地|3<br>ニューヨーク|


### <a name="entities-are-optional-but-recommended"></a>エンティティは省略可能だが強く推奨される

[意図](luis-concept-intent.md)は必須ですが、エンティティは省略可能です。 すべての概念用のエンティティをアプリ内に作成する必要はありません。クライアント アプリケーションで別のエンティティまたは意図に対するヒントまたはシグナルとして機能するデータまたはエンティティが必要な場合のみ、それらを作成する必要があります。

アプリケーションの開発に伴いデータの新しいニーズが特定されたら、適切なエンティティを LUIS モデルに後で追加することができます。

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>データ抽出を表すエンティティ

エンティティは、_ユーザーの発話内_ のデータの概念を表します。 意図は、_発話全体_ を分類します。

次の 4 つの発話について検討します。

|発話|予測される意図|抽出されたエンティティ|説明|
|--|--|--|--|
|Help|help|-|何も抽出されません。|
|Send something|sendSomething|-|何も抽出されません。 このモデルには、このコンテキストの `something` を抽出するための必須特徴量がなく、受領者が示されていません。|
|Send Bob a present|sendSomething|`Bob`, `present`|このモデルでは、事前構築済みのエンティティ `personName` の必須特徴量を追加することで、`Bob` が抽出されます。 `present` を抽出するのに機械学習エンティティが使用されています。|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|機械学習エンティティによって、データの 2 つの重要な部分である `Bob` と `box of chocolates` が抽出されています。|

## <a name="label-entities-in-all-intents"></a>すべての意図のエンティティにラベルを付ける

エンティティは、予測される意図に関係なくデータを抽出します。 すべての意図で、"_すべての_" 発話の例にラベルを付けるようにしてください。 エンティティのラベル付けがされていない `None` 意図によって、他の意図のトレーニング発話がはるかに多い場合でも混乱が生じます。

## <a name="design-entities-for-decomposition"></a>分解のためのエンティティの設計

機械学習エンティティを使用して、分解用のアプリ スキーマを設計して、大きな概念をサブエンティティに分割できます。

分解のための設計により、LUIS では、クライアント アプリケーションに対してより深いレベルのエンティティ解決を返すことができます。 これにより、クライアント アプリケーションはビジネス ルールに集中して、データの解決を LUIS に任せることができます。

機械学習エンティティは、発話の例を通して学習したコンテキストに基づいてトリガーされます。

[**機械学習エンティティ**](tutorial-machine-learned-entity.md)は、最上位レベルのエクストラクターです。 サブエンティティは、機械学習エンティティの子エンティティです。

## <a name="effective-machine-learned-entities"></a>効果的な機械学習エンティティ

機械学習エンティティを効果的に構築するには、次のようにします。

* ラベル付けは、意図全体で一貫している必要があります。 これには、このエンティティを含む **None** 意図で提供する発話も含まれます。 そうでないと、モデルでシーケンスを効果的に特定できなくなります。
* サブエンティティを持つ機械学習エンティティがある場合は、必ずエンティティとサブエンティティの異なる順序とバリアントがラベル付けされた発話に表示されるようにしてください。 ラベル付けされた発話の例には、有効なすべてのフォームと、発話内に表示され、存在しない、並べ替えられているエンティティが含まれている必要があります。
* エンティティを非常に固定されたセットにオーバーフィットすることは避けてください。 **オーバーフィット** は、モデルが適切に一般化されていない場合に発生します。機械学習モデルでは、一般的な問題です。 これは、アプリが新しいデータで適切に動作しないであろうことを意味します。 今度は、指定した限られた例を超えてアプリで一般化できるように、ラベル付けされた発話の例を変更する必要があります。 表示されている例だけではなく、より多くの概念が考慮されるよう、モデルに対して十分な変更を行うことでサブエンティティを多様にする必要があります。

## <a name="effective-prebuilt-entities"></a>効果的な事前構築済みのエンティティ

[事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)によって提供されるような、共通データを抽出する効果的なエンティティを構築するには、次のプロセスをお勧めします。

特徴量としてのエンティティに独自のデータを取り込むことによって、データの抽出を改善します。 それにより、独自のデータから追加されるすべてのラベルによって、個人名がアプリケーション内に存在する場合のコンテキストが学習されます。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>エンティティの種類

親に対するサブエンティティは、機械学習エンティティである必要があります。 サブエンティティでは、非機械学習エンティティを[特徴量](luis-concept-feature.md)として使用できます。

データの抽出方法と抽出後にそれを表現する方法に基づいてエンティティを選択します。

|エンティティの種類|目的|
|--|--|
|[**機械学習**](tutorial-machine-learned-entity.md)|ラベル付けされた例から学習された、入れ子になった複雑なデータを抽出します。 |
|[**リスト**](reference-entity-list.md)|項目の一覧と、**テキスト完全一致** を使用して抽出されたそれらのシノニム。|
|[**Pattern.any**](#patternany-entity)|エンティティが自由形式であるため、エンティティの終わりが検出されたことを判断するのが難しいエンティティ。 [パターン](luis-concept-patterns.md)でのみ使用できます。|
|[**事前構築済み**](luis-reference-prebuilt-entities.md)|URL や電子メールなど、特定の種類のデータを抽出するために既にトレーニングされています。 これらの事前構築済みエンティティの一部は、オープンソースの [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されます。 ご自分の特定のカルチャまたはエンティティが現在サポートされていない場合は、プロジェクトにご協力ください。|
|[**正規表現**](reference-entity-regular-expression.md)|**テキスト完全一致** のために正規表現を使用します。|


## <a name="extraction-versus-resolution"></a>抽出と解決

エンティティでは、データが発話に表示されたときにそのデータが抽出されます。 エンティティによってデータが変更または解決されることはありません。 エンティティでは、テキストがエンティティの有効な値であるかどうかについては、解決は提供されません。

抽出を解決する方法はありますが、これによってバリエーションやミスに対して影響を受けないというアプリの機能が制限されることに注意する必要があります。

リスト エンティティと正規表現 (テキスト一致) エンティティは、サブエンティティに対して[必須特徴量](luis-concept-feature.md#required-features)として使用でき、抽出のフィルターとして機能します。 これは、アプリの予測機能の妨げとならないよう、慎重に使用する必要があります。

## <a name="extracting-contextually-related-data"></a>文脈的に関連するデータの抽出

1 回の発話にエンティティが 2 回以上出現し、データの意味が発話内のコンテキストに基づく場合があります。 例として、出発地と到着地という 2 つの地理的な場所があるフライトを予約するための発話が挙げられます。

`Book a flight from Seattle to Cairo`

チケットの購入を完了するには、クライアント アプリケーションで各場所の種類が認識されるように 2 つの場所を抽出する必要があります。

出発地と到着地を抽出するには、チケット注文機械学習エンティティの一部として、2 つのサブエンティティを作成します。 各サブエンティティに対して、geographyV2 を使用する必須特徴量を作成します。

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>必須特徴量を使用したエンティティの制限

[必須特徴量](luis-concept-feature.md)の詳細

## <a name="patternany-entity"></a>Pattern.any エンティティ

Pattern.any は、[パターン](luis-concept-patterns.md)内でのみ使用できます。

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>エンティティに対するアプリの制限の超過

[制限](luis-limits.md#model-limits)を超える必要がある場合は、サポートにお問い合わせください。 それを行うには、システムに関する詳細情報を収集し、[LUIS](luis-reference-regions.md#luis-website) Web サイトにアクセスして **[サポート]** を選択します。 Azure サブスクリプションにサポート サービスが含まれている場合は、[Azure のテクニカル サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="entity-prediction-status-and-errors"></a>エンティティ予測の状態とエラー

発話の例で選択したエンティティと異なるエンティティ予測がエンティティに含まれている場合は、LUIS ポータルが表示されます。 この異なるスコアは、現在のトレーニング済みのモデルに基づいています。 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="発話の例で選択したエンティティと異なるエンティティ予測がエンティティに含まれている場合は、LUIS ポータルが表示されます。":::

発話の例では間違っているテキストが強調表示されています。また、例の発話行の右にエラー インジケーターが付いています。赤い三角で確認できます。 

この情報を使用して、以下の 1 つまたは複数の方法で、エンティティ エラーを解決します。
* 強調表示されているテキストではラベルが間違っています。 修正するには、確認し、修正し、再トレーニングします。 
* エンティティの概念の識別に役立つように、エンティティの[特徴量](luis-concept-feature.md)を作成します。
* [発話の例](luis-concept-utterance.md)を追加し、エンティティにラベルを付けます。
* エンティティの概念を識別するのに役立つ、予測エンドポイントで受信したすべての発話に対する[アクティブ ラーニングの提案](luis-concept-review-endpoint-utterances.md)を確認します。

## <a name="next-steps"></a>次のステップ

正しい[発話](luis-concept-utterance.md)の概念について学習します。

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。

「[チュートリアル:Language Understanding (LUIS) で機械学習エンティティを使用して、ユーザーの発話から構造化データを抽出する](tutorial-machine-learned-entity.md)」を参照して、機械学習エンティティを使用して発話から構造化データを抽出する方法について確認します。

