---
title: クイック スタート:Azure Time Series Insights プレビューのデモ環境を探索する | Microsoft Docs
description: Azure Time Series Insights プレビューのデモ環境を理解します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276834"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>クイック スタート:Azure Time Series Insights プレビューのデモ環境を探索する

このクイック スタートでは、無料のデモンストレーション環境で Azure Time Series Insights プレビュー エクスプローラーを使用する方法を示します。 Web ブラウザーを使用して大量の IoT 産業履歴データを視覚化する方法について説明した後、Time Series Insights プレビュー エクスプローラーの主な機能について説明します。

Time Series Insights では、エンド ツー エンドのサービスとしてのプラットフォーム (PaaS) オファリングが提供されます。 簡単にデータを探索できるように、豊富なコンテキスト情報を付加され、時系列に合わせて最適化された IoT スケールのデータの、取り込み、格納、クエリを実行できます。 また、運用分析も提供されます。 Time Series Insights は、産業用 IoT デプロイ特有のニーズに合わせて調整された差別化された製品です。

デモ環境では、電力会社である Contoso が示されています。 その環境で、Time Series Insights を使って Contoso 社のデータからアクションにつながる分析情報を見つけ出し、簡単な根本原因分析を実行します。 Contoso は 2 箇所の風力タービン ファームを運営しており、それぞれに 10 基のタービンがあります。 各タービンには 20 個のセンサーが付いていて、1 分ごとに Azure IoT Hub にデータを報告します。 センサーは、気象条件、ブレードの回転速度とヨーの位置、発電機の実績、ギアボックスの動作、安全モニターに関する情報を収集します。

Time Series Insights プレビューを使用して、増加し続ける Contoso 社の 2 年前からのデータセットを分析します。現在のデータ サイズは 40 GB です。 重大な障害となかなか進まないメンテナンスの問題の両方を理解して予測するのに役立ちます。

Azure サブスクリプションをお持ちでない場合は、始める前に [無料 Azure アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>デモ環境で Time Series Insights エクスプローラーを調べる

1. ブラウザーで、 [Contoso Wind Farm 環境](https://insights.timeseries.azure.com/preview/samples)に移動します。  

1. 要求されたら、Azure アカウントの資格情報を使用して Time Series Insights エクスプローラーにサインインします。

### <a name="demo-step-1"></a>デモ手順 1

1. **Contoso Plant 1** で風力タービン **W7** を見てみましょう。  

    * **アクション**:表示範囲を **1/1/17 20:00 から 3/10/17 20:00 (UTC)** に更新し、**Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed** センサーを追加してから、結果を表示します。

       ![クイック スタート 1][1]

1. 最近、Contoso は風力タービン **W7** で火災を発見しました。 それについてドリルダウンしてみましょう。 火災の間に火災アラート センサーが起動されたことがわかります。

    * **アクション**:表示範囲を **3/9/17 20:00 から 3/10/17 20:00 (UTC)** に更新し、**Safety System** > **FireAlert** センサーを追加します。

      ![クイック スタート 2][2]

1. 出火の前後に他に何が起きたかを見てみましょう。 出火の直前に油圧とアクティブな警告の両方が急上昇していますが、その時点では問題を防ぐには遅すぎました。

    * **アクション**:**Pitch System** > **HydraulicOilPressure** センサーと **Pitch System** > **ActiveWarning** センサーを追加します。

      ![クイック スタート 3][3]

1. ズーム アウトすると、出火に至る兆候があったことがわかります。 両方のセンサーが変動しています。 ところで、この問題は以前にも発生したのでしょうか。

    * **アクション**:表示範囲を **2/24/17 20:00 から 3/10/17 20:00 (UTC)** に更新します。

      ![クイック スタート 4][4]

1. 2 年間すべてのデータを調べると、同じ兆候を伴っている以前の火災を見つけることができます。 このデータを使用すると、このような問題を早期に把握するシステムを構築できます。

    * **アクション**:表示範囲を **1/1/16 から 12/31/17** (全データ) に更新します。

       ![クイック スタート 5][5]

### <a name="demo-step-2"></a>デモ手順 2

1. 他の問題は、さらに微妙であり、診断が困難になります。 Time Series Insights では、困難な問題を追跡するために役立つ幅広い手段が提供されています。 ここでは、**6/25**に **W6** の警告センサーが停止していることを確認できます。 実際には何が起きているのでしょうか。

    * **アクション**:現在のセンサーを削除し、表示範囲を **6/1/17 20:00 から 7/1/17 20:00 (UTC)** に更新してから、**Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning** センサーを追加します。

       ![クイック スタート 6][6]

1. 警告は、発電機によって出力されている電圧の問題を示しています。 原因は何でしょうか。 発電機の全体的な出力電力は、狭い間隔では問題がないように見えます。 ただし、データを集約することで、明確に低下していることがわかります。

    * **アクション**:**VoltageActuatorSwitchWarning** センサーを削除し、**Generator System** > **ActivePower** センサーを追加してから、間隔を **3d** に更新します。

       ![クイック スタート 7][7]

1. データセットを前方に進むと、これが一時的な問題ではないことがわかります。 継続しています。

    * **アクション**:期間を右側に延ばします。

       ![クイック スタート 8][8]

1. さらに詳しく見てみましょう。 センサーの他のデータ ポイントを追加し、フェーズごとに電圧を確認できます。 しかし、データ ポイントはすべて同じように見えます。 実際の値を示すマーカーを置いてみましょう。 フェーズ 3 の出力に問題があるように見えます。

    * **アクション**:**Generator System** > **GridVoltagePhase1**、**GridVoltagePhase2**、**GridVoltagePhase3** の各センサーを追加します。 表示領域の最後のデータ ポイントにマーカーを置きます。

       ![クイック スタート 8][8]

1. 同じスケールで 3 つのデータ ポイントすべてを表示すると、フェーズ 3 の低下がさらにはっきりします。 この時点で、警告の原因に関する手がかりが得られ、問題をメンテナンス チームに任せる準備ができます。  

    * **アクション**:同じグラフ スケール上にすべてのセンサーをオーバーレイ表示するように更新します。

       ![クイック スタート 9][9]

## <a name="next-steps"></a>次の手順

独自の Time Series Insights プレビュー環境を作成する準備ができました。

> [!div class="nextstepaction"]
> [Time Series Insights プレビューの環境を計画する](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
