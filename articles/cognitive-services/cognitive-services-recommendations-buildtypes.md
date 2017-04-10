---
title: "ビルドの種類とモデルの品質: Recommendations API |Microsoft Docs"
description: "Azure Machine Learning Recommendations - クイック スタート ガイド"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 7e0beaa7-2646-4504-bce3-0d7bf767136a
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f2cabdd547599639ed5b7d9a145c0ce9592d9d46


---
# <a name="build-types-and-model-quality"></a>ビルドの種類とモデルの品質
<a name="TypeofBuilds"></a>

## <a name="supported-build-types"></a>サポートされているビルドの種類
Recommendations API は、現在 *recommendation* と *FBT* の 2 種類のビルドをサポートしています。 これらはそれぞれ異なるアルゴリズムを使用してビルドされます。また、互いに異なる長所を備えています。 このドキュメントでは、こうしたビルドと、生成されるモデルの品質を比較するための手法について説明します。

[クイック スタート ガイド](cognitive-services-recommendations-quick-start.md)をまだ完了していない場合は、完了しておくことをお勧めします。

<a name="RecommendationBuild"></a>

### <a name="recommendation-build-type"></a>Recommendation ビルド タイプ
recommendation ビルド タイプでは、行列因子分解を使用して推奨を提供します。 各項目を説明するトランザクションに基づいて[潜在的な特徴](https://en.wikipedia.org/wiki/Latent_variable)ベクトルを生成します。これらの潜在的なベクトルを使って類似した項目を比較します。

ある電子機器ストアでの購入に基づいてモデルをトレーニングし、Lumia 650 フォンを入力としてモデルに付与すると、モデルは Lumia 650 フォンを購入する可能性が高い人によって購入される傾向のある一連の項目を返します。 これらの項目は相補的でないことがあります。 この例では、Lumia 650 を好きな人が他のスマートフォンを好きな可能性もあるため、モデルが他のスマートフォンを返すことがあります。

Recommendation ビルドには魅力的な機能が 2 つあります。

**recommendation ビルドでは "*コールド項目*" の配置がサポートされています。**

使用頻度の高くない項目は、コールド項目と呼ばれます。 たとえば、これまで販売実績がないスマートフォンが入荷された場合は、システムは、トランザクションのみに基づいてこの製品の推奨を提示することができません。 これは、システムが製品自体の情報から学習する必要があることを意味します。

コールド項目の配置を使用する場合は、カタログの各項目の特徴情報を提供する必要があります。 カタログの最初の数行は次のようになります (特徴の key=value の形式に注目してくだい)。

> 6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128 GB, Memory=4G, Manufacturer=Microsoft
> 
> 73H-00013,Wake Xbox 360,Gaming,, Type=Software, Language=English, Rating=Mature
> 
> WAH-0F05,Minecraft Xbox 360,Gaming,, * Type=Software, Language=Spanish, Rating=Youth
> 
> 

また、次のビルド パラメーターも設定する必要があります。

| ビルド パラメーター | メモ |
| --- | --- |
| *useFeaturesInModel* |**true**に設定します。  推奨モデルを強化するために特徴を使用するかどうかを示します。 |
| *allowColdItemPlacement* |**true**に設定します。 推奨事項が特徴の類似性を使用してコールド項目もプッシュするかどうかを示します。 |
| *modelingFeatureList* |推奨事項を強化するために recommendation ビルドに使用される、特徴名のコンマ区切りの一覧。 たとえば上記の例では "Language,Storage" です。 |

**recommendation ビルドはユーザー推奨をサポート**

ｒecommendation ビルドでは、 [ユーザー推奨](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)がサポートされています。 つまり、ユーザーのトランザクション履歴に基づいて、そのユーザー個人用に設定した推奨を提供できます。 ユーザー推奨を行うために、ユーザー ID やそのユーザーの最近のトランザクション履歴を指定できます。

ユーザー推奨の適用が望ましい典型的な例の 1 つは、[ようこそ] ページへのサインイン時です。 ここで、特定のユーザーを対象とするコンテンツを宣伝できます。

また、ユーザーによる支払いの際に recommendation ビルド タイプを利用したい場合もあります。 この時点では、ユーザーが購入しようとしている項目の一覧があるため、その時点での買い物かごに基づいた推奨を提供することができます。

#### <a name="recommendations-build-parameters"></a>推奨ビルドのパラメーター
| 名前 | Description | 型、 <br>  有効な値 <br>  (既定値) |
| --- | --- | --- |
| *NumberOfModelIterations* |モデルが実行するイテレーションの数は、全体的なコンピューティング時間とモデルの精度に反映されます。 大きな値にするとモデルがより正確になりますが、コンピューティング時間がかかります。 |整数、 <br>     10 ～ 50 <br>(40) |
| *NumberOfModelDimensions* |ディメンションの数は、データ内でモデルが検索しようとする特徴の数に関連しています。 ディメンションの数を増やすと、結果をより詳細に微調整して、小さいクラスターにすることができます。 ただし、ディメンションが多すぎると、モデルが項目間の相関関係を検出できなくなります。 |整数、 <br> 10 ～ 40 <br>(20) |
| *ItemCutOffLowerBound* |特定の項目がモデルで考慮されるために存在する必要のある使用状況ポイントの最小数を定義します。 |整数、 <br> 2 以上 <br> (2) |
| *ItemCutOffUpperBound* |特定の項目がモデルで考慮されるために存在する必要のある使用状況ポイントの最大数を定義します。 |整数、 <br>2 以上<br> (2147483647) |
| *UserCutOffLowerBound* |特定のユーザーがモデルで考慮されるために実行すべきトランザクションの最小数を定義します。 |整数、 <br> 2 以上 <br> (2) |
| *UserCutOffUpperBound* |特定のユーザーがモデルで考慮されるために実行すべきトランザクションの最大数を定義します。 |整数、 <br>2 以上 <br> (2147483647) |
| *UseFeaturesInModel* |推奨モデルを強化するために特徴を使用するかどうかを示します。 |ブール<br>  既定: True |
| *ModelingFeatureList* |推奨事項を強化するために recommendation ビルドに使用される、特徴名のコンマ区切りの一覧。 重要となる特徴に依存します。 |文字列、最大 512 文字 |
| *AllowColdItemPlacement* |推奨事項が特徴の類似性を使用してコールド項目もプッシュするかどうかを示します。 |ブール <br> 既定: False |
| *EnableFeatureCorrelation* |理由で特徴を使用するかどうかを示します。 |ブール <br> 既定: False |
| *ReasoningFeatureList* |理由の文 (推奨事項の説明など) に使用される特徴名のコンマ区切りの一覧。 カスタマーにとって重要となる特徴に依存します。 |文字列、最大 512 文字 |
| *EnableU2I* |ユーザーから項目 (U2I) の推奨事項とも呼ばれる、個人に合わせた推奨を有効にします。 |ブール <br> 既定: True |
| *EnableModelingInsights* |モデル化についての洞察 (精度、多様性のメトリックなど) を収集するためにオフライン評価を実行するかどうかを定義します。 true に設定した場合、データの一部は、モデルのテスト用に予約する必要があるため、トレーニングに使用されません。 詳細については、「 [オフライン評価](#OfflineEvaluation)」を参照してください。 |ブール <br> 既定: False |
| *SplitterStrategy* |EnableModelingInsights を *true*に設定した場合、評価目的でのデータの分割方法を指定します。 |文字列、*RandomSplitter* または *LastEventSplitter* <br>既定値: RandomSplitter |

<a name="FBTBuild"></a>

### <a name="fbt-build-type"></a>FBT ビルド タイプ
よく一緒に購入されている品目 (FBT: Frequently Bought Together) ビルドでは、2 つまたは 3 つの異なる製品が同時に購入された回数をカウントする分析を実行します。 続いて、類似性関数 (**co-occurrences**、**Jaccard**、**lift**) に基づいてこのセットを並べ替えます。

**Jaccard** と **lift** は共起を正規化する方法だと考えてください。  これは、項目はシード項目と一緒に購入された場合にのみ返されることを意味します。

Lumia 650 フォンの例では、スマートフォン X は、それが Lumia 650 フォンと同じセッションで購入された場合にのみ返されます。 このようなケースは可能性が低いため、Lumia 650 用の画面保護シートや電源アダプターなど、Lumia 650 に対し補完的な項目が返されることが予想されます。

現在、ユーザー ID とタイムスタンプが同じであるトランザクションで 2 つの項目が発生した場合に、これらは同じセッションで購入されたと想定されます。

FBT ビルドでは、コールド項目はサポートされていません。同じトランザクション内で 2 つの項目が購入されることを想定しているためです。 FBT ビルドでは項目のセット (トリプレット) を返すことができますが、入力として単一のシード項目を受け取るため、個人に合わせた推奨はサポートされていません。

#### <a name="fbt-build-parameters"></a>FBT ビルド パラメーター
| 名前 | Description | 型、  <br> 有効な値 <br>  (既定値) |
| --- | --- | --- |
| *FbtSupportThreshold* |モデルがどの程度控えめか。 モデル化で考慮すべき項目の同時発生の数。 |整数、 <br> 3 ～ 50 <br> (6) |
| *FbtMaxItemSetSize* |頻度のセット内のアイテム数の限度を定めます。 |Integer    <br> 2 ～ 3 <br> (2) |
| *FbtMinimalScore* |返される結果に含めるために頻度のセットが持つべきスコアの最小値。 大きいほど良好です。 |Double <br> 0 以上 <br> (0) |
| *FbtSimilarityFunction* |ビルドで使用する類似関数を定義します。 **lift** ではセレンディピティ、**co-occurrence** では予測可能性が優先され、**Jaccard** は両者の中間です。 |文字列、  <br>  <i>cooccurrence、lift、jaccard</i><br> 既定値: <i>jaccard</i> |

<a name="SelectBuild"></a>

## <a name="build-evaluation-and-selection"></a>ビルドの評価および選択
上記のガイダンスは、recommendation ビルドと FBT ビルドのどちらを使用すべきかを判断するうえで参考になりますが、どちらのビルドも使用できる場合には、確かな答えは得られません。 また、FBT ビルド タイプを使用する必要があるとわかっている場合でも、類似関数として **Jaccard** か **lift** を選択したい場合があります。

2 つの異なるビルドのどちらかを選択する最善の方法は、現実の世界でテスト (オンライン評価) して、それぞれのビルドのコンバージョン率を追跡することです。 コンバージョン率は、推奨のクリックまたは推奨の提示からの実際の購入数に基づいて測定できます。また、これらのさまざまな推奨が提示された場合の実際の売上額に基づいて測定することもできます。 コンバージョン率のメトリックは、ビジネス目標に応じて選択できます。

場合によっては、運用環境で実行する前にモデルをオフラインで評価する必要があります。 オフライン評価はオンライン評価の代わりにはなりませんが、メトリックとして役に立ちます。

<a name="OfflineEvaluation"></a>

## <a name="offline-evaluation"></a>オフライン評価
オフライン評価の目的は、推奨の精度 (推奨される項目のいずれかを購入するユーザーの数) と推奨の多様性 (推奨される項目の数) を予測することです。
精度と多様性のメトリック評価の一環として、システムはユーザーのサンプルを探し、これらのユーザーのトランザクションをトレーニング データセットとテスト データセットの 2 つのグループに分割します。

> [!NOTE]
> オフライン メトリックを使用するには、使用状況データにタイムスタンプが含まれている必要があります。
> 時刻のデータは、トレーニング データセットとテスト データセットの間で正確に使用状況を分割するために必要です。
> 
> なお、オフライン評価では、使用状況ファイルが小さい場合は結果が生成されないことがあります。 完全な評価を行うためには、テスト データセットに最低でも 1,000 の使用状況ポイントが必要です。
> 
> 

<a name="Precision"></a>

### <a name="precision-at-k"></a>K の精度
次の表は、K の精度によるオフライン評価の出力を表しています。

| K | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- |
| 割合 |13.75 |18.04 |21 |24.31 |26.61 |
| テスト中のユーザー |10,000 |10,000 |10,000 |10,000 |10,000 |
| 考慮されるユーザー |10,000 |10,000 |10,000 |10,000 |10,000 |
| 考慮されないユーザー |0 |0 |0 |0 |0 |

#### <a name="k"></a>K
上記の表では、 *k* は顧客に提示された推奨の数を表しています。 この表から、"テスト期間中に顧客に提示された推奨が 1 つだけの場合、その推奨を購入したのはユーザーの 13.75% のみである" と解釈できます。 この解釈は、モデルが購入データでトレーニングされているという前提に基づいています。 これは、1 の精度 は 13.75 であると言い換えることもできます。

顧客に提示される項目が多いほど、顧客が推奨された項目を購入する確率も高くなっていることがわかります。 上の実験では、この確率は推奨される項目が 5 つの場合に 26.61% であり、ほぼ 2 倍になっています。

#### <a name="percentage"></a>割合
*k* 個の推奨のうち少なくとも 1 つの推奨を操作したユーザーの割合を示します。 この割合は、少なくとも 1 つの推奨を操作したユーザーの数を、考慮されるユーザーの合計で割ることによって計算します。 詳細については「考慮されるユーザー」を参照してください。

#### <a name="users-in-test"></a>テスト中のユーザー
この行のデータは、テスト データセット中のユーザーの合計数を表します。

#### <a name="users-considered"></a>考慮されるユーザー
ユーザーは、トレーニング データセットを使用して生成されたモデルに基づいて、システムが少なくとも *k* 個の項目を推奨した場合にのみ考慮されます。

#### <a name="users-not-considered"></a>考慮されないユーザー
この行のデータは、考慮されないすべてのユーザーを表します。 これは、推奨された項目を *k* 個以上受け取らなかったユーザーです。

考慮されないユーザー = テスト中のユーザー – 考慮されるユーザー

<a name="Diversity"></a>

### <a name="diversity"></a>多様性
多様性のメトリックでは、推奨された項目の種類を測定できます。 次の表は、多様性によるオフライン評価の出力を表しています。

| 百分位のバケット | 0 ～ 90 | 90 ～ 99 | 99 ～ 100 |
| --- | --- | --- | --- |
| 割合 |34.258 |55.127 |10.615 |

推奨された項目の合計: 100,000

推奨された一意の項目: 954

#### <a name="percentile-buckets"></a>百分位のバケット
百分位のバケットはそれぞれ、範囲によって表されます (最小値 0 と最大値 100 の間)。 値が 100 に近い項目は最も人気のある項目であり、値が 0 に近い項目は最も人気のない項目です。 たとえば、百分位のバケットが 99 ～ 100 の場合、割合の値は 10.6 です。これは、推奨の 10.6% が上位 1% の最も人気のある項目を返したことを意味します。 百分位のバケットでは、最小値は範囲に含まれており、100 を除いて最大値は範囲に含まれていません。

#### <a name="unique-items-recommended"></a>推奨された一意の項目
推奨された一意の項目メトリックは、評価の結果として返された別個の項目の数を示します。

#### <a name="total-items-recommended"></a>推奨された項目の合計
推奨された項目の合計メトリックは、推奨された項目の数を示します。 一部が重複していることがあります。

<a name="ImplementingEvaluation"></a>

### <a name="offline-evaluation-metrics"></a>オフライン評価メトリック
精度と多様性のオフライン メトリックは、どちらのビルドを使用するかを選択するときに役に立つ場合があります。 ビルド時に FBT ビルド パラメーターまたは recommendation ビルド パラメーターをそれぞれ設定する際に、

* *enableModelingInsights* ビルド パラメーターを **true**に設定します。
* 必要に応じて *splitterStrategy* (*RandomSplitter* または *LastEventSplitter*) を選択します。
  *RandomSplitter* は、特定の *randomSplitterParameters* テスト パーセントとランダム シード値に基づいて、使用状況データをトレーニング セットとテスト セットに分割します。
  *LastEventSplitter* は、各ユーザーの最後のトランザクションに基づいて、使用状況データをトレーニング セットとテスト セットに分割します。

これにより、トレーニング用のデータのサブセットのみを使用したビルドがトリガーされ、残りのデータは評価メトリックを計算するために使用されます。  ビルドの完了後に評価の出力を取得するには、[Get build metrics API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f) を呼び出し、それぞれの *modelId* と *buildId* を渡す必要があります。

 以下は、サンプル評価の JSON 出力です。

    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }



<!--HONumber=Nov16_HO3-->


