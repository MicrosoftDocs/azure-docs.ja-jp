---
title: '分類: フライトの遅延を予測する'
titleSuffix: Azure Machine Learning service
description: この記事では、ドラッグアンドドロップのビジュアル インターフェイスとカスタム R コードを利用してフライトの遅延を予測する機会学習モデルを構築する方法を紹介します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607383"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>サンプル 6 - 分類:R を利用してフライトの遅延を予測する

この実験では、過去のフライトと天候のデータを利用し、予定されている旅客便が 15 分以上遅れるかどうかを予測します。

この問題には、遅延と定刻という 2 つのクラスを予測する分類の問題として取り組むことができます。 分類子を構築する目的で、このモデルは、過去のフライト データから大量の例を使用します。

最終的な実験のグラフは次のようになります。

[![実験のグラフ](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. サンプル 6 実験の **[開く]** ボタンを選択します。

    ![実験を開く](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>データを取得する

この実験では、**Flight Delays Data** データセットが使用されます。 これは米国運輸省の TranStats データ コレクションの一部です。 このデータセットには、2013 年 4 月から 10 月までのフライト遅延情報が含まれます。 データはビジュアル インターフェイスにアップロードする前に次のように前処理されています。

* 米国本土で最も利用者が多い空港上位 70 か所を含めるようにフィルターを適用しました。
* キャンセルされたフライトについては、15 分超の遅延として再分類しました。
* 進路を変更したフライトは除外しました。
* 14 列を選択しました。

フライト データを補足する目的で、**気象データセット**が使用されます。 気象データには、NOAA で観測された土地別/時間別の天候が含まれます。また、気象データは、同じ期間 (2013 年 4 月から 10 月まで) に空港の測候所で観測されたものです。 Azure ML ビジュアル インターフェイスにアップロードする前に次のように前処理されています。

* 気象観測所 ID を対応する空港 ID にマッピングしました。
* 混雑度が上位 70 に入る空港に関連付けられていない気象観測所を除外しました。
* [Date] 列を [Year]、[Month]、[Day] の個別列に分割しました。
* 26 列を選択しました。

## <a name="pre-process-the-data"></a>データを前処理する

通常、データセットには、分析前にある程度の前処理が必要です。

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>フライト データ

**[Carrier]** 、 **[OriginAirportID]** 、 **[DestAirportID]** 列は整数として保存されます。 ただし、分類属性であり、そのために **Edit Metadata** モジュールを使用します。

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

次に、Dataset モジュールの **[列を選択]** を使用し、 **[DepDelay]** 、 **[DepDel15]** 、 **[ArrDelay]** 、 **[Canceled]** 、 **[Year]** というターゲットを漏らす可能性のあるデータセット列から除外します。 

フライト レコードと時間別の気象レコードを結合するには、結合キーの 1 つとして、離陸予定時刻を使用します。 結合するには、[CSRDepTime] 列を最も近い時間に切り捨てます。これは **Execute R Script** モジュールで行われます。 

### <a name="weather-data"></a>気象データ

足りない値が大部分を占める列は **Project Columns** モジュールを利用して除外されます。 このような列には、 **[ValueForWindCharacter]** 、 **[WetBulbFarenheit]** 、 **[WetBulbCelsius]** 、 **[PressureTendency]** 、 **[PressureChange]** 、 **[SeaLevelPressure]** 、 **[StationPressure]** という文字列値の列がすべて含まれます。

次に、**Clean Missing Data** モジュールが残りの列に適用され、データの足りない行が削除されます。

気象観測の時間が最も近い正時に切り上げられます。 フライト時刻前の天候のみがモデルで使用されるように、フライト予定時刻と天候観測時刻は反対の方向に丸められます。 

気象データは現地時間で報告されるため、離陸予定時刻と天候観測時刻からタイム ゾーン列を差し引くことで、タイム ゾーンの差異が計算されます。 このような操作は **Execute R Script** モジュールによって行われます。

### <a name="joining-datasets"></a>データセットの結合

フライト レコードは、**Join Data** モジュールを利用し、フライトの出発地 (**OriginAirportID**) の気象データと結合されます。

 ![フライトの出発地でフライトと天候を結合する](media/ui-sample-classification-predict-flight-delay/join-origin.png)


フライト レコードは、フライトの目的地 (**DestAirportID**) を利用し、気象データと結合されます。

 ![フライトの目的地でフライトと天候を結合する](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>トレーニングおよびテスト サンプルの準備

**Split Data** モジュールにより、データが 4 月から 9 月までのレコード (トレーニング用) と 10 月のレコード (テスト用) に分割されます。

 ![トレーニングおよびテスト データを分割する](media/ui-sample-classification-predict-flight-delay/split.png)

Select Columns モジュールを利用することで、年度、月、時間帯の列がトレーニング データセットから削除されます。

## <a name="define-features"></a>特徴を定義する

機械学習において特徴とは、関心のある項目を個別に測定できるプロパティです。 一連の強力な特徴を見つけるには、実験とドメインの知識が必要です。 対象を予測するにあたり、一部の特徴は他のものよりも有益です。 また、一部の特徴には他の特徴との間に強力な相関関係があり、新しい情報をモデルに追加しません。 そのような特徴は削除できます。

モデルを構築する目的で、利用できるあらゆる特徴を使用したり、特徴のサブセットを選択したりできます。

## <a name="choose-and-apply-a-learning-algorithm"></a>学習アルゴリズムを選択して、適用する

**Two-Class Logistic Regression** モジュールを利用してモデルを作成し、トレーニング データセットでそれをトレーニングします。 

**Train Model** モジュールの結果、分類モデルがトレーニングされ、新しいサンプルを評価し、予測するために利用できます。 テスト セットを使用し、トレーニングしたモデルからスコアを生成します。 次に、**Evaluate Model** モジュールを使用し、モデルを分析し、その品質を比較します。

実験を実行したら、出力ポートをクリックし、 **[視覚化]** を選択すると、**Score Model** モジュールの出力を表示できます。 出力には、点数の付いたラベルとラベルの確率が含まれます。

最後になりますが、結果の品質をテストする目的で、**Evaluate Model** モジュールを実験キャンバスに追加し、左の入力ポートを Score Model モジュールの出力に接続します。 出力ポートをクリックし、 **[視覚化]** を選択することで、実験を実行し、**Evaluate Model** モジュールの出力を表示します。

## <a name="evaluate"></a>Evaluate
このロジスティック回帰モデルでは、テスト セットで AUC が 0.631 になります。

 ![evaluate](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>次の手順

ビジュアル インターフェイスで利用できるその他のサンプルを確認します。

- [サンプル 1 - 回帰: 自動車の価格を予測する](ui-sample-regression-predict-automobile-price-basic.md)
- [サンプル 2 - 回帰: 自動車の価格予測のためのアルゴリズムを比較する](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [サンプル 3 - 分類: 信用リスクを予測する](ui-sample-classification-predict-credit-risk-basic.md)
- [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [サンプル 5 - 分類:顧客離れを予測する](ui-sample-classification-predict-churn.md)
