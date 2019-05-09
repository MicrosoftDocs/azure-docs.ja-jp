---
title: Azure Stream Analytics ジョブをデバイスにデプロイするチュートリアル - Azure IoT Edge | Microsoft Docs
description: このチュートリアルでは、Azure Stream Analytics をモジュールとして IoT Edge デバイスに展開します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 31330c3d2b10e6245db775da4039fd1948539df4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106017"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>チュートリアル: Azure Stream Analytics を IoT Edge モジュールとしてデプロイする

IoT ソリューションの多くが、分析サービスを使用して、IoT デバイスからクラウドに送信されたデータの分析情報を得ます。 Azure IoT Edge を使用すると、[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) ロジックを取得し、そのロジックをデバイス自体に移動できます。 エッジで利用統計情報のストリームを処理することで、アップロードされるデータの量を削減し、アクションにつながる分析情報への対応にかかる時間を短縮できます。

Azure IoT Edge と Azure Stream Analytics の統合により、Azure portal で Azure Stream Analytics ジョブを作成し、そのジョブを、追加のコードなしに IoT Edge モジュールとして展開できます。  

Azure Stream Analytics には、クラウド上と IoT Edge デバイス上の両方でのデータ分析のために十分に構造化されたクエリ構文が用意されています。 IoT Edge での Azure Stream Analytics について詳しくは、[Azure Stream Analytics のドキュメント](../stream-analytics/stream-analytics-edge.md)をご覧ください。

このチュートリアルの Stream Analytics モジュールでは、30 秒にわたって平均気温が繰り返し計算され、 その平均値が 70 に達すると、モジュールからアラートが送信され、デバイスでアクションが実行されます。 ここでは、シミュレートされた温度センサーをリセットするというアクションです。 この機能を運用環境で使って、温度が危険なレベルに達したときにマシンを停止させたり予防策を講じたりできます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * エッジでデータを処理するための Azure Stream Analytics ジョブを作成します。
> * 新しい Azure Stream Analytics ジョブを他の IoT Edge モジュールと接続します。
> * Azure Stream Analytics ジョブを Azure portal から IoT Edge デバイスに展開します。

<center>

![図 - チュートリアルのアーキテクチャ、ASA ジョブのステージングとデプロイ](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 


## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブの作成

このセッションでは、Azure Stream Analytics ジョブを作成して IoT ハブからデータを取得し、デバイスから送信されたテレメトリ データに対してクエリを実行した後、その結果を Azure Blob Storage コンテナーに転送します。 

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure Stream Analytics ジョブを作成して IoT Edge デバイスで実行する場合は、デバイスから呼び出すことができるようにジョブを格納する必要があります。 既存の Azure ストレージ アカウントを使用するか、ここで新しいストレージ アカウントを作成することができます。 

1. Azure portal で、**[リソースの作成]** > **[Storage]** > **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。 

1. ストレージ アカウントを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | ----- | ----- |
   | Name | ストレージ アカウント用に一意の名前を指定します。 | 
   | Location | 近くの場所を選択します。 |
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。 |
   | リソース グループ | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |

1. 他のフィールドは既定値のままにして、**[作成]** を選択します。 

### <a name="create-a-new-job"></a>新しいジョブの作成

1. Azure portal で、**[リソースの作成]** > **[モノのインターネット (IoT)]** > **[Stream Analytics ジョブ]** の順に移動します。

1. ジョブを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | ----- | ----- |
   | ジョブ名 | ジョブの名前を指定します。 たとえば、「**IoTEdgeJob**」と指定します。 | 
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。 |
   | リソース グループ | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | Location | 近くの場所を選択します。 | 
   | ホスティング環境 | **[Edge]** を選択します。 |
 
1. **作成**を選択します。

### <a name="configure-your-job"></a>ジョブを構成する

Azure portal で Stream Analytics ジョブが作成されたら、ジョブの入力、出力、および通過するデータに対して実行するクエリを構成することができます。 

このセクションでは、入力、出力、クエリという 3 つの要素を使用して、IoT Edge デバイスから温度データを受け取るジョブを作成します。 そのデータは、反復的に 30 秒間隔で分析されます。 その間隔内で、平均温度が 70 度を超えると、アラートが IoT Edge デバイスに送信されます。 次のセクションで、ジョブをデプロイするときに、データがどこから来てどこに行くかを正確に指定します。  

1. Azure portal で Stream Analytics ジョブに移動します。 

1. **[ジョブ トポロジ]** で、**[入力]**、**[ストリーム入力の追加]** の順に選択します。

   ![Azure Stream Analytics での入力の追加](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. ドロップダウン リストで、**[Edge Hub]** を選択します。

1. **[新しい入力]** ウィンドウで、入力のエイリアスとして「**温度**」と入力します。 

1. 他のフィールドは既定値のままにして、**[保存]** を選択します。

1. **[ジョブ トポロジ]** で **[出力]** を開き、**[追加]** を選択します。

   ![Azure Stream Analytics での出力の追加](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. ドロップダウン リストで、**[Edge Hub]** を選択します。

1. **[新しい出力]** ウィンドウで、出力のエイリアスとして「**アラート**」と入力します。 

1. 他のフィールドは既定値のままにして、**[保存]** を選択します。

1. **[ジョブ トポロジ]** で、**[クエリ]** を選択します。

1. 既定のテキストを次のクエリで置き換えます。 SQL コードは、30 秒間隔での平均マシン温度が 70 度に達した場合、リセット コマンドをアラート出力に送信します。 リセット コマンドは、実行できるアクションとして、センサーに事前にプログラミングされています。 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. **[保存]** を選択します。

### <a name="configure-iot-edge-settings"></a>IoT Edge の設定を構成する

IoT Edge デバイスにデプロイされるように Stream Analytics ジョブを準備するには、ジョブをストレージ アカウント内のコンテナーに関連付ける必要があります。 ジョブをデプロイしようとすると、ジョブ定義がストレージ コンテナーにエクスポートされます。 

1. **[構成]** で、**[Storage account settings]\(ストレージ アカウントの設定)** を選択します。

1. **[Add storage account]\(ストレージ アカウントの追加)** を選択します。 

1. ドロップダウン メニューから **[ストレージ アカウント]** を選択します。

1. **[コンテナー]** フィールドで、**[新規作成]** を選択し、ストレージ コンテナーの名前を指定します。 

1. **[保存]** を選択します。 

## <a name="deploy-the-job"></a>ジョブのデプロイ

IoT Edge デバイスに Azure Stream Analytics ジョブをデプロイする準備が整いました。 

このセクションでは、Azure portal の**モジュールの設定**ウィザードを使用して、*配置マニフェスト*を作成します。 配置マニフェストは、デバイスにデプロイされるすべてのモジュール、モジュール イメージを格納するコンテナー レジストリ、モジュールの管理方法、およびモジュール間の通信方法を記述した JSON ファイルです。 IoT Edge デバイスは、IoT Hub からその配置マニフェストを取得し、その中の情報を使用して、割り当てられているすべてのモジュールをデプロイおよび構成します。 

このチュートリアルでは、2 つのモジュールをデプロイします。 1 つ目は、**tempSensor** です。これは、温度と湿度のセンサーをシミュレートするモジュールです。 2 つ目は、Stream Analytics ジョブです。 センサー モジュールは、ジョブ クエリが分析するデータのストリームを提供します。 

1. Azure portal のご自身の IoT ハブで、**[IoT Edge]** に移動し、ご自身の IoT Edge デバイスの詳細ページを開きます。

1. **[Set modules]\(モジュールの設定\)** を選びます。  

1. 以前、このデバイスに tempSensor モジュールをデプロイしたことがある場合は、自動入力されている可能性があります。 そうでない場合は、次の手順でモジュールを追加します。

   1. **[追加]** をクリックし、**[IoT Edge モジュール]** を選択します。
   1. 名前に「**tempSensor**」と入力します。
   1. イメージ URI として「**mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**」と入力します。 
   1. 他の設定はそのままにして、**[保存]** を選択します。

1. 次の手順に従って、ご自身の Azure Stream Analytics Edge ジョブを追加します。

   1. **[追加]** をクリックし、**[Azure Stream Analytics モジュール]** を選択します。
   1. 自分のサブスクリプションと、作成した Azure Stream Analytics Edge ジョブを選びます。 
   1. **[保存]** を選択します。

1. 作成したストレージ コンテナーに Stream Analytics ジョブが発行されたら、モジュール名をクリックして、Stream Analytics モジュールの構造を確認します。 

   イメージの URI は、標準の Azure Stream Analytics イメージを指しています。 これは、IoT Edge デバイスにデプロイされるすべてのジョブに使用される、同じイメージです。 

   モジュール ツインは、**ASAJobInfo** と呼ばれる望ましいプロパティで構成されています。 そのプロパティの値は、ストレージ コンテナー内のジョブ定義を指しています。 このプロパティによって、特定のジョブ情報で Stream Analytics イメージが構成されます。 

1. モジュール ページを閉じます。

1. 後の手順で必要になるため、Stream Analytics モジュールの名前を書き留めてから、**[次へ]** を選択して続行します。

1. **[ルート]** の既定値を以下のコードで置き換えます。 3 か所の _{moduleName}_ のすべてを、Azure Stream Analytics モジュールの名前に置き換えます。 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   ここで宣言するルートは、IoT Edge デバイスを通過するデータのフローを定義します。 tempSensor のテレメトリ データは、IoT Hub に送信されます。また、Stream Analytics ジョブで構成された**温度**入力にも送信されます。 **アラート**出力メッセージは、IoT Hub に送信され、リセット コマンドをトリガーするように tempSensor モジュールにも送信されます。 

1. **[次へ]** を選択します。

1. **[Review Deployment]\(展開のレビュー\)** ステップで、**[送信]** を選びます。

1. デバイスの詳細ページに戻り、**[更新]** を選びます。  

    新しい Stream Analytics モジュールが、IoT Edge エージェント モジュールおよび IoT Edge ハブと共に実行されていることがわかります。

    ![デバイスごとに報告される tempSensor および ASA モジュール](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>データの表示

ここで、IoT Edge デバイスに移動し、Azure Stream Analytics モジュールと tempSensor モジュールの間のやり取りを確認できます。

1. すべてのモジュールが Docker で実行されていることを確認します。

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. すべてのシステム ログとメトリック データを表示します。 Stream Analytics モジュールの名前を使用してください。

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

30 秒間の温度が 70 度に達するまで、マシンの温度が徐々に上昇するのを観察できます。 次に、Stream Analytics モジュールによってリセットがトリガーされ、マシンの温度が 21 度まで下降します。 

   ![モジュール ログへのコマンド出力をリセットする](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスのデータを分析するように、Azure Streaming Analytics ジョブを構成しました。 その後、温度上昇にローカルで対処するために、IoT Edge デバイスでこの Azure Stream Analytics モジュールを読み込みました。また、集計されたデータ ストリームをクラウドに送信しました。 Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

> [!div class="nextstepaction"] 
> [モジュールとして Azure Machine Learning モデルをデプロイする](tutorial-deploy-machine-learning.md)
