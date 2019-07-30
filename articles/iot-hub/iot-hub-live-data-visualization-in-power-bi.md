---
title: Azure IoT Hub から取得したリアルタイム センサー データの視覚化 – Power BI | Microsoft Docs
description: Power BI を使用して、センサーから収集されて Azure IoT ハブに送信された気温と湿度のデータを視覚化します。
author: robinsh
keywords: リアルタイム データの視覚化, ライブ データの視覚化, センサー データの視覚化
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065897"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する

![エンド ツー エンド ダイアグラム](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

Azure IoT ハブが受信したリアルタイム センサー データを Power BI を使用して視覚化する方法について説明します。 Web アプリを使用して IoT ハブ内のデータを視覚化しようとする場合は、[Web アプリを使用した Azure IoT Hub からのリアルタイム センサー データの視覚化](iot-hub-live-data-visualization-in-web-apps.md)に関するページを参照してください。

## <a name="what-you-do"></a>作業内容

* コンシューマー グループを追加して、データにアクセスできるよう IoT ハブを準備する。

* IoT ハブから Power BI アカウントにデータを転送できるよう Stream Analytics ジョブを作成、構成、実行する。

* Power BI レポートの作成と公開を行い、データを視覚化する。

## <a name="what-you-need"></a>必要なもの

* [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)のチュートリアルまたはいずれかのデバイス チュートリアル ([Node.js での Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md) に関するチュートリアルなど) が完了していること。 これらの記事では、次の要件について取り上げています。
  
  * 有効な Azure サブスクリプション
  * サブスクリプションの Azure IoT Hub。
  * Azure IoT Hub にメッセージを送信するクライアント アプリケーション。

* Power BI アカウント ([Power BI を無料で試す](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics ジョブの作成、構成、実行

まずは、Stream Analytics ジョブを作成しましょう。 ジョブを作成したら、入力、出力、およびデータを取得するためのクエリを定義します。

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Stream Analytics ジョブ]** の順に選択します。

2. 次の情報をジョブに入力します。

   **ジョブ名**:ジョブの名前。 名前はグローバルに一意である必要があります。

   **[リソース グループ]** :IoT ハブと同じリソース グループを使用します。

   **[場所]** :リソース グループと同じ場所を使用します。

   ![Azure での Stream Analytics ジョブの作成](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. **作成** を選択します。

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

1. Stream Analytics ジョブを開きます。

2. **[ジョブ トポロジ]** で、 **[入力]** を選択します。

3. **[入力]** ウィンドウで、 **[Add stream input]\(ストリーム入力の追加\)** を選択し、ドロップダウン リストから **[IoT Hub]** を選択します。 新しい入力ウィンドウで、次の情報を入力します。

   **入力のエイリアス**:入力の一意のエイリアスを入力します。

   **[Provide IoT Hub from your subscription]\(サブスクリプションから IoT Hub を指定する\)** :このラジオ ボタンを選択します。

   **サブスクリプション**:このチュートリアルに使用している Azure サブスクリプションを選択します。

   **IoT Hub**:このチュートリアルで使用している IoT Hub を選択します。

   **エンドポイント**: **[メッセージング]** を選びます。

   **共有アクセス ポリシー名**:Stream Analytics ジョブで IoT ハブに使用する共有アクセス ポリシーの名前を選択します。 このチュートリアルでは、*service* を選択できます。 *service* ポリシーは、新しい IoT ハブ上で既定で作成され、IoT ハブによって公開されるクライアント側エンドポイント上で送受信するためのアクセス許可を付与します。 詳細については、「[アクセス制御とアクセス許可](iot-hub-devguide-security.md#access-control-and-permissions)」を参照してください。

   **[共有アクセス ポリシー キー]** :このフィールドは、共有アクセス ポリシー名の選択内容に基づいて自動的に入力されます。

   **コンシューマー グループ**:以前に作成したコンシューマー グループを選びます。

   他のすべてのフィールドは既定値のままにします。

   ![Azure で Stream Analytics ジョブに入力を追加する](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. **[保存]** を選択します。

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. **[ジョブ トポロジ]** で、 **[出力]** を選択します。

2. **[出力]** ウィンドウで、 **[追加]** と **[Power BI]** を選択します。

3. **[Power BI - New output]\(Power BI - 新規出力\)** ウィンドウで、 **[Authorize]\(承認\)** を選択し、指示に従って Power BI アカウントにサインインします。

4. Power BI にサインインした後、次の情報を入力します。

   **出力のエイリアス**:出力の一意のエイリアス。

   **[グループ ワークスペース]** :ターゲットのグループ ワークスペースを選択します。

   **[データセット名]** :データセットの名前を入力します。

   **[テーブル名]** :テーブルの名前を入力します。

   ![Azure で Stream Analytics ジョブに出力を追加する](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. **[保存]** を選択します。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics ジョブのクエリの構成

1. **[ジョブ トポロジ]** で、 **[クエリ]** を選択します。

2. `[YourInputAlias]` をジョブの入力エイリアスに置き換えます。

3. `[YourOutputAlias]` をジョブの出力エイリアスに置き換えます。

   ![Azure で Stream Analytics ジョブにクエリを追加する](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. **[保存]** を選択します。

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics ジョブで、 **[概要]** を選択してから、 **[開始]**  >  **[Now]\(今すぐ\)**  >  **[開始]** を選択します。 ジョブが正常に開始されると、ジョブの状態が **[停止済み]** から **[実行中]** に変わります。

![Azure での Stream Analytics ジョブの実行](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>データを視覚化する Power BI レポートの作成と公開

1. デバイスでサンプル アプリケーションが実行されていることを確認します。 実行されていない場合、チュートリアルの「[デバイスのセットアップ](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)」を参照してください。

2. [Power BI](https://powerbi.microsoft.com/en-us/) アカウントにサインインします。

3. 使用したワークスペースである **[マイ ワークスペース]** を選択します。

4. **[データセット]** を選択します。

   Stream Analytics ジョブの出力を作成したときに指定したデータセットが表示されます。

5. 作成したデータセットで、 **[レポートの追加]** (データセット名の右側にある最初のアイコン) を選択します。

   ![Microsoft Power BI レポートの作成](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. 時間の経過に伴う温度の変化を示す折れ線グラフを作成します。

   1. レポート作成ページの **[視覚化]** ウィンドウで、折れ線グラフのアイコンを選択して折れ線グラフを追加します。

   2. **[フィールド]** ウィンドウで、Stream Analytics ジョブの出力を作成したときに指定したテーブルを展開します。

   3. **EventEnqueuedUtcTime** を、 **[視覚化]** ウィンドウの **[軸]** にドラッグします。

   4. **temperature** を **[値]** にドラッグします。

      折れ線グラフが作成されます。 x 軸は日付と時刻 (UTC タイム ゾーン) を示し、 y 軸はセンサーから取得した温度を示します。

      ![Microsoft Power BI レポートに温度の折れ線グラフを追加する](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. 時間の経過に伴う湿度の変化を示す、別の折れ線グラフを作成します。 これを行うには、上記と同じ手順を行ったうえで、**EventEnqueuedUtcTime** を x 軸、**humidity** を y 軸に設定します。

   ![Microsoft Power BI レポートに湿度の折れ線グラフを追加する](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. **[保存]** を選択してレポートを保存します。

9. 左側のウィンドウで **[レポート]** を選択した後、先ほど作成したレポートを選択します。

10. **[ファイル]**  >  **[Web に公開]** を選択します。

11. **[埋め込みコードの作成]** 、 **[公開]** の順に選択します。

他のユーザーと共有できるレポート アクセス用のリンクと、ブログまたは Web サイトにレポートを組み込むために使用できるコード スニペットが表示されます。

![Microsoft Power BI レポートの公開](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft は [Power BI のモバイル アプリ](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)も提供しています。これを使用すると、モバイル デバイスで Power BI のダッシュボードとレポートを表示して操作できます。

## <a name="next-steps"></a>次の手順

Power BI を使用して、Azure IoT ハブからのリアルタイム センサー データを視覚化することができました。

Azure IoT Hub からのデータを視覚化するための別の方法については、[Web アプリを使用した Azure IoT Hub からのリアルタイム センサー データの視覚化](iot-hub-live-data-visualization-in-web-apps.md)に関するページを参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
