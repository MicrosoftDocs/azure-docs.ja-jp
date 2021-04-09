---
title: Azure Machine Learning 異常検出 API - Team Data Science Process
description: 異常検出 API は、Microsoft Azure Machine Learning を使用して作成される例の 1 つで、時系列に従った一定の間隔での数値を含む時系列データの異常を検出します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93305699"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning 異常検出 API

> [!NOTE]
> この項目はメンテナンス中です。 Azure Cognitive Services の Machine Learning アルゴリズムのギャラリーを利用する [Anomaly Detector API サービス](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/)を使用して、ビジネス、運用、および IoT のメトリックから異常を検出することをお勧めします。

## <a name="overview"></a>概要
[異常検出 API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) は、Azure Machine Learning を使用して作成される例の 1 つで、時系列に従った一定の間隔での数値を含む時系列データの異常を検出します。

この API で時系列データから検出できる異常パターンのタイプは次のとおりです。

* **正と負の値の傾向**: たとえば、コンピューティングのメモリ使用量を監視する場合、上昇傾向が関心の対象となります。メモリ使用量の上昇はメモリ リークの兆候であるためです。
* **値のダイナミック レンジの変化**: たとえば、サービスによってスローされる例外を監視する場合、値のダイナミック レンジの変化は、クラウド サービスの正常性が不安定になっていることを示す可能性があります。
* **スパイクと DIP**: たとえば、サービスへのログインの失敗の数や、電子商取引サイトのチェックアウトの数を監視している場合、スパイクや DIP は異常な動作を示している可能性があります。

こうした Machine Learning を使用した検出は、時間の経過に伴う値の変化を追跡し、異常が記録されたときの値の継続的な変化を報告します。 これらはアドホックなしきい値の調整を必要とせず、スコアを使用して誤検知率を制御できます。 異常検出 API は、一定時間 KPI を追跡することによるサービスの監視、各種メトリック (検索回数、クリック数など) に基づく使用状況の監視、各種カウンター (メモリ、CPU、ファイル読み取りなど) を一定時間追跡することによるパフォーマンスの監視など、さまざまなシナリオで役に立ちます。

異常検出に関して、すぐに使い始めることのできる便利なツールが付属しています。

* たとえば目的のデータに関して異常検出 API から得られた結果は、 [Web アプリケーション](https://anomalydetection-aml.azurewebsites.net/) を使用して評価し、視覚化することができます。

> [!NOTE]
> [この API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) を利用した **IT Anomaly Insights ソリューション** をお試しください
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API のデプロイ
API を使用するには、Azure Machine Learning Web サービスとしてホストされる Azure サブスクリプションに API をデプロイする必要があります。  これは [Azure AI ギャラリー](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)から実行できます。  季節性検出を含む異常検出と季節性検出を含まない異常検出という、2 つの Azure Machine Learning Studio (クラシック) Web サービス (およびその関連リソース) が Azure サブスクリプションにデプロイされます。  デプロイが完了したら、[Azure Machine Learning Studio (クラシック) Web サービス](https://services.azureml.net/webservices/) ページから API を管理できます。  このページから、エンドポイントの場所、API キー、API を呼び出すためのサンプル コードを検索できます。  詳細な手順については、[こちら](../classic/manage-new-webservice.md)を参照してください。

## <a name="scaling-the-api"></a>API のスケーリング
既定では、デプロイは、1,000 件のトランザクション/月と 2 時間のコンピューティング時間/月が含まれる Dev/Test 料金プランで実行されます。  ニーズに応じて別のプランにアップグレードできます。  さまざまなプランの料金の詳細については、[こちらの](https://azure.microsoft.com/pricing/details/machine-learning/)「実稼働 Web API の価格」を参照してください。

## <a name="managing-aml-plans"></a>AML プランの管理
課金プランは、[こちら](https://services.azureml.net/plans/)で管理できます。  プラン名は、API のデプロイ時に選択したリソース グループ名とサブスクリプションに固有の文字列に基づきます。  プランをアップグレードする手順については、[こちらの](../classic/manage-new-webservice.md)「課金プランの管理」セクションを参照してください。

## <a name="api-definition"></a>API の定義
この Web サービスは、REST ベースの API を HTTPS 経由で提供しますが、これは Web アプリケーションやモバイル アプリケーション、R、Python、Excel などを含むさまざまな方法で使用できます。時系列データを REST API 呼び出しによってこのサービスに送信することができ、後述する 3 つの異常の種類の組み合わせを実行します。

## <a name="calling-the-api"></a>API の呼び出し
この API を呼び出すには、エンドポイントの場所と API キーを知っている必要があります。  [Azure Machine Learning Studio (クラシック) Web サービス](https://services.azureml.net/webservices/) ページから、これら 2 つの要件と API 呼び出しのサンプル コードを入手できます。  目的の API に移動し、[使用] タブをクリックして検索します。  API は、`format=swagger` URL パラメーターを付けて Swagger API として呼び出すことも、`format` URL パラメーターを付けずに非 Swagger API として呼び出すこともできます。  サンプル コードでは、Swagger 形式を使用します。  非 Swagger 形式の要求と応答例を次に示します。  これらの例は、季節性エンドポイントに対するものですが、  非季節性エンドポイントも同様です。

### <a name="sample-request-body"></a>要求本文のサンプル
要求には、`Inputs` と `GlobalParameters` という 2 つのオブジェクトが含まれます。  次の要求例では、一部のパラメーターは明示的に送信され、一部は明示的に送信されていません (一覧を下にスクロールして各エンドポイントのパラメーターを確認してください)。  明示的に送信されない要求のパラメーターでは、後述する既定値が使用されます。

```json
{
            "Inputs": {
                    "input1": {
                            "ColumnNames": ["Time", "Data"],
                            "Values": [
                                    ["5/30/2010 18:07:00", "1"],
                                    ["5/30/2010 18:08:00", "1.4"],
                                    ["5/30/2010 18:09:00", "1.1"]
                            ]
                    }
            },
    "GlobalParameters": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "bileveldetector.sensitivity": "3.25",
        "detectors.spikesdips": "Both"
    }
}
```

### <a name="sample-response"></a>応答のサンプル
`ColumnNames` フィールドを表示するには、URL パラメーターとして `details=true` を要求に含める必要があります。  各フィールドの意味については、この後の表を参照してください。

```json
{
    "Results": {
        "output1": {
            "type": "table",
            "value": {
                "Values": [
                    ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                    ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                    ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                ],
                "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
            }
        }
    }
}
```


## <a name="score-api"></a>スコア API
スコア API は、季節に依存しない時系列データに対する異常検出に使用します。 この API は、データに対してさまざまな異常検出機能を実行し、その異常スコアを返します。
以下の図は、スコア API で検出できる異常の例です。 この時系列には、2 つの明確なレベルの変化と 3 つのスパイクがあります。 赤い点はレベルの変化が検出された時を示し、黒い点は検出されたスパイクを示しています。
![スコア API][1]

### <a name="detectors"></a>検出機能
異常検出 API がサポートしている検出機能 (ディテクター) は大きく 3 つのカテゴリに分けられます。 検出機能ごとの具体的な入力パラメーターと出力について詳しくは、次の表を参照してください。

| 検出機能のカテゴリ | 検出機能 | 説明 | 入力パラメーター | 出力 |
| --- | --- | --- | --- | --- |
| スパイク検出機能 |T スパイク検出機能 |第 1 四分位数および第 3 四分位数から値までの距離に基づいて、スパイクとディップを検出します。 |*tspikedetector.sensitivity:* 1 ～ 10 の範囲の整数値を受け取ります (既定値は 3)。値が高いほど極端な値を取得するため、感度が低くなります。 |TSpike: 2 進値 – スパイク/ディップが検出された場合は ‘1’、それ以外の場合は ‘0’ |
| スパイク検出機能 | Z スパイク検出機能 |平均値からデータポイントまでの距離に基づいて、スパイクとディップを検出します。 |*tspikedetector.sensitivity:* 1 ～ 10 の範囲の整数値を受け取ります (既定値は 3)。値が高いほど極端な値を取得するため、感度が低くなります |ZSpike: 2 進値 – スパイク/ディップが検出された場合は ‘1’、それ以外の場合は ‘0’ |
| スロー傾向検出機能 |スロー傾向検出機能 |設定されている感度に従って、ゆっくりとした正方向の傾向を検出します。 |*trenddetector.sensitivity:* 検出機能スコアのしきい値 (既定値: 3.25、妥当な選択範囲は 3.25 ～ 5、値が大きいほど感度が低下) |tscore: 傾向に関する異常スコアを表す浮動小数点数 |
| レベル変化検出機能 | 双方向レベル変化検出機能 |設定されている感度に従って、上向きと下向きの両方のレベルの変化を検出します。 |*bileveldetector.sensitivity:* 検出機能スコアのしきい値 (既定値: 3.25、妥当な選択範囲は 3.25 ～ 5、値が大きいほど感度が低下) |rpscore: 上向きと下向きのレベルの変化に関する異常スコアを表す浮動小数点数 |

### <a name="parameters"></a>パラメーター
以下の表は、前述の入力パラメーターに関する詳しい情報の一覧です。

| 入力パラメーター | 説明 | 既定の設定 | Type | 有効範囲 | 推奨範囲 |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |異常スコアの計算に使用された履歴 (データ ポイントの数) |500 |整数 (integer) |10 ～ 2000 |時系列に依存 |
| detectors.spikesdips | スパイクのみ、ディップのみ、または両方を検出するかどうか |両方 |enumerated |Both、Spikes、Dips |両方 |
| bileveldetector.sensitivity |双方向のレベル変化検出機能の感度。 |3.25 |double |なし |3.25 ～ 5 (値が小さいほど感度が高い) |
| trenddetector.sensitivity |正傾向検出機能に使用する感度 |3.25 |double |なし |3.25 ～ 5 (値が小さいほど感度が高い) |
| tspikedetector.sensitivity |T スパイク検出機能の感度 |3 |整数 (integer) |1 ～ 10 |3 ～ 5 (値が小さいほど感度が高い) |
| zspikedetector.sensitivity |Z スパイク検出機能の感度 |3 |整数 (integer) |1 ～ 10 |3 ～ 5 (値が小さいほど感度が高い) |
| postprocess.tailRows |出力結果に維持する最新のデータ ポイントの数 |0 |整数 (integer) |0 (すべてのデータ ポイントを維持する場合) または結果として維持するデータ ポイントの数を指定 |なし |

### <a name="output"></a>出力
この API は、与えられた時系列データに対してすべての検出機能を実行し、時間ポイントごとの 2 進値のスパイク インジケーターと異常スコアを返します。 以下の表は、API からの出力の一覧です。

| 出力 | 説明 |
| --- | --- |
| Time |生データのタイムスタンプ。または、集計/欠損データ補完が適用された場合は集計/補完データのタイムスタンプ。 |
| Data |生データの値。または、集計/欠損データ補完が適用された場合は集計/補完データの値。 |
| TSpike |T スパイク検出機能によってスパイクが検出されたかどうかを示す 2 進値のインジケーター |
| ZSpike |Z スパイク検出機能によってスパイクが検出されたかどうかを示す 2 進値のインジケーター |
| rpscore |双方向のレベルの変化に関する異常スコアを表す浮動小数点数 |
| rpalert |双方向のレベルの変化に異常が存在するかどうかを、入力された感度に基づいて示す 1/0 値 |
| tscore |正傾向に関する異常スコアを表す浮動小数点数 |
| talert |正傾向に異常が存在するかどうかを、入力された感度に基づいて示す 1/0 値 |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality API は、季節的なパターンを含んだ時系列データの異常検出に使用します。 この API は、季節的なパターンからの逸脱を検出する目的で利用できます。
次の図は、季節的な時系列データから検出された異常の例です。 この時系列データには、1 つのスパイク (1 つ目の黒い点) と 2 つのディップ (2 つ目の黒い点と一番端にある黒い点)、1 つのレベルの変化 (赤い点) があります。 時系列の中央にあるディップとレベルの変化はどちらも、時系列から季節的な要因を取り除いた後でしか識別できません。
![季節性 API][2]

### <a name="detectors"></a>検出機能
季節性エンドポイントの検出機能は、非季節性エンドポイントの検出機能に似ていますが、パラメーター名が少し異なります (下記参照)。

### <a name="parameters"></a>パラメーター

以下の表は、前述の入力パラメーターに関する詳しい情報の一覧です。

| 入力パラメーター | 説明 | 既定の設定 | Type | 有効範囲 | 推奨範囲 |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |入力時系列の集計間隔 (秒単位) |0 (集計は実行されません) |整数 (integer) |集計をスキップする場合は 0、それ以外の場合は 0 より大きい値 |5 分 ～ 1 日 (時系列に依存) |
| preprocess.aggregationFunc |指定の AggregationInterval でデータを集計するための関数 |mean |enumerated |mean、sum、length |なし |
| preprocess.replaceMissing |欠損データの補完に使用する値 |lkv (last known value) |enumerated |zero、lkv、mean |なし |
| detectors.historyWindow |異常スコアの計算に使用された履歴 (データ ポイントの数) |500 |整数 (integer) |10 ～ 2000 |時系列に依存 |
| detectors.spikesdips | スパイクのみ、ディップのみ、または両方を検出するかどうか |両方 |enumerated |Both、Spikes、Dips |両方 |
| bileveldetector.sensitivity |双方向のレベル変化検出機能の感度。 |3.25 |double |なし |3.25 ～ 5 (値が小さいほど感度が高い) |
| postrenddetector.sensitivity |正傾向検出機能に使用する感度 |3.25 |double |なし |3.25 ～ 5 (値が小さいほど感度が高い) |
| negtrenddetector.sensitivity |負傾向検出機能に使用する感度 |3.25 |double |なし |3.25 ～ 5 (値が小さいほど感度が高い) |
| tspikedetector.sensitivity |T スパイク検出機能の感度 |3 |整数 (integer) |1 ～ 10 |3 ～ 5 (値が小さいほど感度が高い) |
| zspikedetector.sensitivity |Z スパイク検出機能の感度 |3 |整数 (integer) |1 ～ 10 |3 ～ 5 (値が小さいほど感度が高い) |
| seasonality.enable |季節性分析を実行するかどうか |true |boolean |true、false |時系列に依存 |
| seasonality.numSeasonality |検出する周期的サイクルの最大数 |1 |整数 (integer) |1、2 |1 ～ 2 |
| seasonality.transform |異常検出を適用する前に季節的傾向要因を取り除くかどうか |deseason |enumerated |none、deseason、deseasontrend |なし |
| postprocess.tailRows |出力結果に維持する最新のデータ ポイントの数 |0 |整数 (integer) |0 (すべてのデータ ポイントを維持する場合) または結果として維持するデータ ポイントの数を指定 |なし |

### <a name="output"></a>出力
この API は、与えられた時系列データに対してすべての検出機能を実行し、時間ポイントごとの 2 進値のスパイク インジケーターと異常スコアを返します。 以下の表は、API からの出力の一覧です。

| 出力 | 説明 |
| --- | --- |
| Time |生データのタイムスタンプ。または、集計/欠損データ補完が適用された場合は集計/補完データのタイムスタンプ。 |
| OriginalData |生データの値。または、集計/欠損データ補完が適用された場合は集計/補完データの値。 |
| ProcessedData |次のいずれかのオプションです。 <ul><li>有意な季節性が検出され、なおかつ deseason オプションが選択された場合は、季節に基づいて調整された時系列</li><li>有意な季節性が検出され、なおかつ deseasontrend オプションが選択された場合は、季節に基づいて調整され、トレンド除去された時系列</li><li>それ以外の場合、このオプションは OriginalData と等価</li> |
| TSpike |T スパイク検出機能によってスパイクが検出されたかどうかを示す 2 進値のインジケーター |
| ZSpike |Z スパイク検出機能によってスパイクが検出されたかどうかを示す 2 進値のインジケーター |
| BiLevelChangeScore |レベルの変化に関する異常スコアを表す浮動小数点数 |
| BiLevelChangeAlert |レベルの変化に異常が存在するかどうかを、入力された感度に基づいて示す 1/0 値 |
| PosTrendScore |正傾向に関する異常スコアを表す浮動小数点数 |
| PosTrendAlert |正傾向に異常が存在するかどうかを、入力された感度に基づいて示す 1/0 値 |
| NegTrendScore |負傾向に関する異常スコアを表す浮動小数点数 |
| NegTrendAlert |負傾向に異常が存在するかどうかを、入力された感度に基づいて示す 1/0 値 |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png