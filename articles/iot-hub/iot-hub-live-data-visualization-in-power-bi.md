---
title: "Azure IoT Hub から取得したリアルタイム センサー データの視覚化 – Power BI | Microsoft Docs"
description: "Power BI を使用して、センサーから収集されて Azure IoT ハブに送信された気温と湿度のデータを視覚化します。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "リアルタイム データの視覚化, ライブ データの視覚化, センサー データの視覚化"
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: b190fea06ffc2406d781c7edad091f097cca9c2d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

Azure IoT ハブが受信したリアルタイム センサー データを Power BI で視覚化する方法について説明します。 Web Apps を使用して IoT ハブのデータを視覚化したい場合は、[Azure Web Apps を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-web-apps.md)に関するページを参照してください。

## <a name="what-you-do"></a>作業内容

- コンシューマー グループを追加して、データにアクセスできるよう IoT ハブを準備する。
- IoT ハブから Power BI アカウントにデータを転送できるよう Stream Analytics ジョブを作成、構成、実行する。
- Power BI レポートの作成と公開を行い、データを視覚化する。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT ハブにメッセージを送信するクライアント アプリケーション。
- Power BI アカウント  ([Power BI を無料で試す](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics ジョブの作成、構成、実行

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. Azure Portal で、[新規]、[モノのインターネット]、[Stream Analytics ジョブ] の順にクリックします。
1. 次の情報をジョブに入力します。

   **[ジョブ名]**: ジョブの名前。 名前はグローバルに一意である必要があります。

   **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   **[場所]**: リソース グループと同じ場所を使用します。

   **[ダッシュボードにピン留めする]**: ダッシュボードから IoT ハブに簡単にアクセスできるようにするには、このオプションをオンにします。

   ![Azure での Stream Analytics ジョブの作成](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. **[作成]**をクリックします。

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

1. Stream Analytics ジョブを開きます。
1. **[ジョブ トポロジ]** で **[入力]** をクリックします。
1. **[入力]** ウィンドウで **[追加]** をクリックしてから、次の情報を入力します。

   **[入力のエイリアス]**: 入力の一意のエイリアス。

   **[ソース]**: **[IoT ハブ]** を選択します。

   **[コンシューマー グループ]**: 先ほど作成したコンシューマー グループを選択します。
1. **[作成]**をクリックします。

   ![Azure で Stream Analytics ジョブに入力を追加する](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. **[ジョブ トポロジ]** で **[出力]** をクリックします。
1. **[出力]** ウィンドウで **[追加]** をクリックしてから、次の情報を入力します。

   **[出力のエイリアス]**: 出力の一意のエイリアス。

   **[シンク]**: **[Power BI]** を選択します。
1. **[承認]** をクリックして、Power BI アカウントにサインインします。
1. 承認が済んだら、次の情報を入力します。

   **[グループ ワークスペース]**: ターゲットのグループ ワークスペースを選択します。

   **[データセット名]**: データセットの名前を入力します。

   **[テーブル名]**: テーブルの名前を入力します。
1. **[作成]**をクリックします。

   ![Azure で Stream Analytics ジョブに出力を追加する](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics ジョブのクエリの構成

1. **[ジョブ トポロジ]** で **[クエリ]** をクリックします。
1. `[YourInputAlias]` をジョブの入力エイリアスに置き換えます。
1. `[YourOutputAlias]` をジョブの出力エイリアスに置き換えます。
1. **[Save]**をクリックします。

   ![Azure で Stream Analytics ジョブにクエリを追加する](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics ジョブで、**[開始]** > **[現在]** > **[開始]** の順にクリックします。 ジョブが正常に開始されると、ジョブの状態が **[停止済み]** から **[実行中]** に変わります。

![Azure での Stream Analytics ジョブの実行](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>データを視覚化する Power BI レポートの作成と公開

1. デバイスでサンプル アプリケーションが実行されていることを確認します。 実行されていない場合、チュートリアルの「[デバイスのセットアップ](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)」を参照してください。
1. [Power BI](https://powerbi.microsoft.com/en-us/) アカウントにサインインします。
1. Stream Analytics ジョブの出力を作成したときに設定したグループ ワークスペースに移動します。
1. **[ストリーミング データセット]** をクリックします。

   Stream Analytics ジョブの出力を作成したときに指定したデータセットが一覧で表示されます。
1. **[アクション]** の左端のアイコンをクリックし、レポートを作成します。

   ![Microsoft Power BI レポートの作成](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. 時間の経過に伴う温度の変化を示す折れ線グラフを作成します。
   1. レポート作成のページで、折れ線グラフを追加します。
   1. **[フィールド]** ウィンドウで、Stream Analytics ジョブの出力を作成したときに指定したテーブルを展開します。
   1. **EventEnqueuedUtcTime** を、**[視覚化]** ウィンドウの **[軸]** にドラッグします。
   1. **temperature** を **[値]** にドラッグします。

      これで、折れ線グラフが作成されました。 x 軸は日付と時刻 (UTC タイム ゾーン) を示し、 y 軸はセンサーから取得した温度を示します。

      ![Microsoft Power BI レポートに温度の折れ線グラフを追加する](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. 時間の経過に伴う湿度の変化を示す、別の折れ線グラフを作成します。 これを行うには、上記と同じ手順を行ったうえで、**EventEnqueuedUtcTime** を x 軸、**humidity** を y 軸に設定します。

   ![Microsoft Power BI レポートに湿度の折れ線グラフを追加する](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. **[保存]** をクリックしてレポートを保存します。
1. **[ファイル]** > **[Web に公開]** の順にクリックします。
1. **[埋め込みコードの作成]**、**[公開]** の順にクリックします。

他のユーザーと共有できるレポート アクセス用のリンクと、ブログまたは Web サイトにレポートを組み込むためのコード スニペットが表示されます。

![Microsoft Power BI レポートの公開](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft は [Power BI のモバイル アプリ](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)も提供しています。これを使用すると、モバイル デバイスで Power BI のダッシュボードとレポートを表示して操作できます。

## <a name="next-steps"></a>次のステップ

Power BI を使用して、Azure IoT ハブからのリアルタイム センサー データを視覚化することができました。
このほかにも、Azure IoT Hub からのデータを視覚化する方法があります。 [Azure Web Apps を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-web-apps.md)に関するページを参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

