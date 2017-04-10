---
title: "(非推奨) Azure Machine Learning 異常検出サービス | Microsoft Docs"
description: "(非推奨) 異常検出サービスは、Microsoft Azure Machine Learning を使用して作成される例の&1; つで、時系列に従った一定の間隔での数値を含む時系列データの異常を検出します。"
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.devlang: na
ms.topic: deprecated
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 01/18/2017
ms.author: alok
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: eff129aceac18342a79f06376023301afc676763
ms.openlocfilehash: 847c24f8baf5f5db93474b469eb402d3ab7d4880


---
# <a name="machine-learning-anomaly-detection-service-deprecated"></a>Machine Learning 異常検出サービス (非推奨)
> [!NOTE]
> [Cortana Intelligence ギャラリー](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)の新しい **signup** オプションを使用して、この API を Azure サブスクリプションにデプロイできるようになりました。 詳細については、「[Machine Learning Anomaly Detection API](machine-learning-apps-anomaly-detection-api.md)」(Machine Learning 異常検出 API) をご覧ください。
> 
> この記事で説明する、Azure Datamarket から API にアクセスする方法は推奨されていません。

## <a name="overview"></a>概要
[異常検出 API (非推奨)](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) は、Azure Machine Learning を使用して作成される例の&1; つで、時系列に従った一定の間隔での数値を含む時系列データの異常を検出します。 

この API で時系列データから検出できる異常パターンのタイプは次のとおりです。

* **正と負の値の傾向**: たとえば、コンピューティングのメモリ使用量を監視する場合、上昇傾向が関心の対象となります。メモリ使用量の上昇はメモリ リークの兆候であるためです。
* **値のダイナミック レンジの変化**: たとえば、サービスによってスローされる例外を監視する場合、値のダイナミック レンジの変化は、クラウド サービスの正常性が不安定になっていることを示す可能性があります。
* **スパイクと DIP**: たとえば、サービスへのログインの失敗の数や、電子商取引サイトのチェックアウトの数を監視している場合、スパイクや DIP は異常な動作を示している可能性があります。

こうした Machine Learning を使用した検出は、時間の経過に伴う値の変化を追跡し、異常が記録されたときの値の継続的な変化を報告します。 これらはアドホックなしきい値の調整を必要とせず、スコアを使用して誤検知率を制御できます。 異常検出 API は、一定時間 KPI を追跡することによるサービスの監視、各種メトリック (検索回数、クリック数など) に基づく使用状況の監視、各種カウンター (メモリ、CPU、ファイル読み取りなど) を一定時間追跡することによるパフォーマンスの監視など、さまざまなシナリオで役に立ちます。

異常検出に関して、すぐに使い始めることのできる便利なツールが付属しています。 

* たとえば目的のデータに関して異常検出 API から得られた結果は、 [Web アプリケーション](http://anomalydetection-aml.azurewebsites.net/) を使用して評価し、視覚化することができます。 
* また [サンプル コード](http://adresultparser.codeplex.com/) では、C# プログラムから API にアクセスして結果を解析する方法が紹介されています。

> [!NOTE]
> [この API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) を利用した **IT Anomaly Insights ソリューション**をお試しください
> 
> このエンド ツー エンド ソリューションを Azure サブスクリプションに展開するには、<a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**ここから始めてください >**</a>
> 
> 

## <a name="api-definition"></a>API の定義
サービスは、REST ベースの API を HTTPS 経由で提供しますが、これは Web アプリケーションやモバイル アプリケーション、R、Python、Excel などを含むさまざまな方法で使用できます。このサービスに対する時系列データを REST API 呼び出しによって送信することができ、前述の&3; つの異常の種類の組み合わせを実行します。 サービスは Azure Machine Learning プラットフォーム上で動作し、ビジネス ニーズに合わせてサイズをシームレスに調整し、SLA を提供しています。

### <a name="headers"></a>ヘッダー
要求には次のような正しいヘッダーを必ず追加します。

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

アカウントのアカウント キーは [Azure データ マーケット](https://datamarket.azure.com/account/keys)で確認できます。 

### <a name="score-api"></a>スコア API
スコア API は、季節に依存しない時系列データに対する異常検出に使用します。 この API は、データに対してさまざまな異常検出機能を実行し、その異常スコアを返します。 以下の図は、スコア API で検出できる異常の例です。 この時系列には、2 つの明確なレベルの変化と 3 つのスパイクがあります。 赤い点はレベルの変化が検出された時を示し、黒い点は検出されたスパイクを示しています。

![スコア API][1]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**要求本文の例**

以下の要求では、2016 年 3 月 1 日から 2016 年 3 月 10 日のデータ ポイントとスパイク検出パラメーターを含む時系列 (一部省略) を API に送信して、これらのデータ ポイントに異常が存在するかどうかを検出しています。 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

これに対する応答は、次のようになります。 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

### <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality API は、季節的なパターンを含んだ時系列データの異常検出に使用します。 この API は、季節的なパターンからの逸脱を検出する目的で利用できます。  

次の図は、季節的な時系列データから検出された異常の例です。 この時系列データには、1 つのスパイク (1 つ目の黒い点) と 2 つのディップ (2 つ目の黒い点と一番端にある黒い点)、1 つのレベルの変化 (赤い点) があります。 時系列の中央にあるディップとレベルの変化はどちらも、時系列から季節的な要因を取り除いた後でしか識別できないことに注意してください。

![Seasonality API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**要求本文の例**

以下の要求では、2016 年 3 月 1 日から 2016 年 3 月 10 日のデータ ポイントとパラメーターを含む時系列 (一部省略) を API に送信して、これらのデータ ポイントに異常が存在するかどうかを検出しています。 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

これに対する応答は、次のようになります。 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
              "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
              "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
              ]
        }"
    }

### <a name="detectors"></a>検出機能
異常検出 API がサポートしている検出機能 (ディテクター) は大きく 3 つのカテゴリに分けられます。 検出機能ごとの具体的な入力パラメーターと出力について詳しくは、次の表を参照してください。

| 検出機能のカテゴリ | 検出機能 | Description | 入力パラメーター | 出力 |
| --- | --- | --- | --- | --- |
| スパイク検出機能 |T スパイク検出機能 |第&1; 四分位数および第&3; 四分位数から値までの距離に基づいて、スパイクとディップを検出します。 |*tspikedetector.sensitivity:* 1 ～ 10 の範囲の整数値を受け取ります (既定値は 3)。値が高いほど極端な値を取得するため、感度が低くなります。 |TSpike:&2; 進値 – スパイク/ディップが検出された場合は ‘1’、それ以外の場合は ‘0’ |
| Z スパイク検出機能 |平均値からデータポイントまでの距離に基づいて、スパイクとディップを検出します。 |*tspikedetector.sensitivity:* 1 ～ 10 の範囲の整数値を受け取ります (既定値は 3)。値が高いほど極端な値を取得するため、感度が低くなります |ZSpike:&2; 進値 – スパイク/ディップが検出された場合は ‘1’、それ以外の場合は ‘0’ | |
| スロー傾向検出機能 |スロー傾向検出機能 |設定されている感度に従って、ゆっくりとした正方向の傾向を検出します。 |*trenddetector.sensitivity:* 検出機能スコアのしきい値 (既定値: 3.25、妥当な範囲は 3.25 ～ 5、値が大きいほど感度が低下) |TScore: 傾向に関する異常スコアを表す浮動小数点数 |
| レベル変化検出機能 |一方向レベル変化検出機能 |設定されている感度に従って、上向きのレベルの変化を検出します。 |*upleveldetector.sensitivity:* 検出機能スコアのしきい値 (既定値: 3.25、妥当な範囲は 3.25 ～ 5、値が大きいほど感度が低下) |PScore: 上向きのレベルの変化に関する異常スコアを表す浮動小数点数 |
| 双方向レベル変化検出機能 |設定されている感度に従って、上向きと下向きの両方のレベルの変化を検出します。 |*bileveldetector.sensitivity:* 検出機能スコアのしきい値 (既定値: 3.25、妥当な範囲は 3.25 ～ 5、値が大きいほど感度が低下) |RPScore: 上向きと下向きのレベルの変化に関する異常スコアを表す浮動小数点数 | |

### <a name="parameters"></a>パラメーター
以下の表は、前述の入力パラメーターに関する詳しい情報の一覧です。

| 入力パラメーター | Description | 既定の設定 | 型 | 有効範囲 | 推奨範囲 |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |入力時系列の集計間隔 (秒単位) |0 (集計は実行されません) |integer |集計をスキップする場合は 0、それ以外の場合は 0 より大きい値 |5 分 ～ 1 日 (時系列に依存) |
| preprocess.aggregationFunc |指定の AggregationInterval でデータを集計するための関数 |mean |enumerated |mean、sum、length |該当なし |
| preprocess.replaceMissing |欠損データの補完に使用する値 |lkv (last known value) |enumerated |zero、lkv、mean |該当なし |
| detectors.historyWindow |異常スコアの計算に使用された履歴 (データ ポイントの数) |500 |integer |10 ～&2000; |時系列に依存 |
| upleveldetector.sensitivity |上方レベル変化検出機能の感度 |3.25 |double |なし |3.25 ～&5; (値が小さいほど感度が高い) |
| bileveldetector.sensitivity |双方向のレベル変化検出機能の感度。 |3.25 |double |なし |3.25 ～&5; (値が小さいほど感度が高い) |
| trenddetector.sensitivity |正傾向検出機能に使用する感度 |3.25 |double |なし |3.25 ～&5; (値が小さいほど感度が高い) |
| tspikedetector.sensitivity |T スパイク検出機能の感度 |3 |integer |1 ～&10; |3 ～&5; (値が小さいほど感度が高い) |
| zspikedetector.sensitivity |Z スパイク検出機能の感度 |3 |integer |1 ～&10; |3 ～&5; (値が小さいほど感度が高い) |
| seasonality.enable |季節性分析を実行するかどうか |true |boolean |true、false |時系列に依存 |
| seasonality.numSeasonality |検出する周期的サイクルの最大数 |1 |integer |1、2 |1 ～&2; |
| seasonality.transform |異常検出を適用する前に季節的傾向要因を取り除くかどうか |deseason |enumerated |none、deseason、deseasontrend |該当なし |
| postprocess.tailRows |出力結果に維持する最新のデータ ポイントの数 |0 |integer |0 (すべてのデータ ポイントを維持する場合) または結果として維持するデータ ポイントの数を指定 |該当なし |

### <a name="output"></a>出力
この API は、与えられた時系列データに対してすべての検出機能を実行し、時間ポイントごとの&2; 進値のスパイク インジケーターと異常スコアを返します。 以下の表は、API からの出力の一覧です。 

| 出力 | Description |
| --- | --- |
| Time |生データのタイムスタンプ。または、集計/欠損データ補完が適用された場合は集計/補完データのタイムスタンプ。 |
| OriginalData |生データの値。または、集計/欠損データ補完が適用された場合は集計/補完データの値。 |
| ProcessedData |次のいずれかになります。 <ul><li>有意な季節性が検出され、なおかつ deseason オプションが選択された場合は、季節に基づいて調整された時系列</li><li>有意な季節性が検出され、なおかつ deseasontrend オプションが選択された場合は、季節に基づいて調整され、トレンド除去された時系列</li><li>それ以外の場合は、OriginalData と等価</li> |
| TSpike |T スパイク検出機能によってスパイクが検出されたかどうかを示す&2; 進値のインジケーター |
| ZSpike |Z スパイク検出機能によってスパイクが検出されたかどうかを示す&2; 進値のインジケーター |
| Pscore |上向きのレベルの変化に関する異常スコアを表す浮動小数点数 |
| Palert |上向きのレベルの変化に異常が存在するかどうかを、入力された感度に基づいて示す&1;/0 値 |
| RPScore |双方向のレベルの変化に関する異常スコアを表す浮動小数点数 |
| RPAlert |双方向のレベルの変化に異常が存在するかどうかを、入力された感度に基づいて示す&1;/0 値 |
| TScore |正傾向に関する異常スコアを表す浮動小数点数 |
| TAlert |正傾向に異常が存在するかどうかを、入力された感度に基づいて示す&1;/0 値 |

この出力は、[単純なパーサー](https://adresultparser.codeplex.com/)を使って解析できます。このパーサーには、API に接続して出力結果を解析する方法を示したサンプル コードが含まれています。 検出された異常は、ダッシュボードでの視覚化や専門家への提出によって是正措置を講じることができるほか、チケット システムに統合することができます。

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png







<!--HONumber=Feb17_HO3-->


