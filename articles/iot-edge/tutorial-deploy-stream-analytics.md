---
title: チュートリアル - Azure IoT Edge を使用したエッジでの Stream Analytics
description: このチュートリアルでは、Azure Stream Analytics をモジュールとして IoT Edge デバイスに展開します
author: kgremban
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 323973b7646acee07a0c4dbc59834e0aceca75ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462050"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>チュートリアル:Azure Stream Analytics を IoT Edge モジュールとしてデプロイする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT ソリューションの多くが、分析サービスを使用して、IoT デバイスからクラウドに送信されたデータの分析情報を得ます。 Azure IoT Edge を使用すると、[Azure Stream Analytics](../stream-analytics/index.yml) ロジックを取得し、そのロジックをデバイス自体に移動できます。 エッジで利用統計情報のストリームを処理することで、アップロードされるデータの量を削減し、アクションにつながる分析情報への対応にかかる時間を短縮できます。

Azure IoT Edge と Azure Stream Analytics が統合され、ワークロードの開発が簡素化されます。 Azure portal で Azure Stream Analytics ジョブを作成し、追加のコードなしで IoT Edge モジュールとしてデプロイできます。  

Azure Stream Analytics には、クラウド上と IoT Edge デバイス上の両方でのデータ分析のために十分に構造化されたクエリ構文が用意されています。 詳細については、[Azure Stream Analytics のドキュメント](../stream-analytics/stream-analytics-edge.md)を参照してください。

このチュートリアルの Stream Analytics モジュールでは、30 秒にわたって平均気温が繰り返し計算され、 その平均値が 70 に達すると、モジュールからアラートが送信され、デバイスでアクションが実行されます。 ここでは、シミュレートされた温度センサーをリセットするというアクションです。 この機能を運用環境で使って、温度が危険なレベルに達したときにマシンを停止させたり予防策を講じたりできます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * エッジでデータを処理するための Azure Stream Analytics ジョブを作成します。
> * 新しい Azure Stream Analytics ジョブを他の IoT Edge モジュールと接続します。
> * Azure Stream Analytics ジョブを Azure portal から IoT Edge デバイスに展開します。

<center>

![図 - チュートリアルのアーキテクチャ: ASA ジョブのステージとデプロイ](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) または [Windows デバイス](quickstart.md)用のクイック スタートに記載された手順に従うことで、Azure 仮想マシンを IoT Edge デバイスとして使用できます。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブの作成

このセクションでは、以下の手順を実行する Azure Stream Analytics ジョブを作成します。

* IoT Edge デバイスからデータを受信します。
* テレメトリ データに対して、設定範囲外の値を照会します。
* クエリ結果に基づいて、IoT Edge デバイス上で操作を実行します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure Stream Analytics ジョブを作成して IoT Edge デバイスで実行する場合は、デバイスから呼び出すことができるようにジョブを格納する必要があります。 既存の Azure Storage アカウントを使用するか、ここで新しいストレージ アカウントを作成することができます。

1. Azure portal で、 **[リソースの作成]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順に移動します。

1. ストレージ アカウントを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | ----- | ----- |
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。 |
   | Resource group | IoT Edge のクイックスタートおよびチュートリアルのすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | 名前 | ストレージ アカウント用に一意の名前を指定します。 |
   | 場所 | 近くの場所を選択します。 |

1. 他のフィールドは既定値のままにして、 **[確認と作成]** を選択します。

1. 設定を確認して **[作成]** を選択します。

### <a name="create-a-new-job"></a>新しいジョブを作成する

1. Azure portal で、 **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Stream Analytics ジョブ]** の順に移動します。

1. ジョブを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | ----- | ----- |
   | ジョブ名 | ジョブの名前を指定します。 たとえば、「**IoTEdgeJob**」と指定します。 |
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。 |
   | Resource group | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | 場所 | 近くの場所を選択します。 |
   | ホスティング環境 | **[Edge]** を選択します。 |

1. **［作成］** を選択します

### <a name="configure-your-job"></a>ジョブを構成する

Azure portal で Stream Analytics ジョブが作成されたら、ジョブの入力、出力、および通過するデータに対して実行するクエリを構成することができます。

このセクションでは、入力、出力、クエリという 3 つの要素を使用して、IoT Edge デバイスから温度データを受け取るジョブを作成します。 そのデータは、反復的に 30 秒間隔で分析されます。 その間隔内で、平均温度が 70 度を超えると、アラートが IoT Edge デバイスに送信されます。 次のセクションで、ジョブをデプロイするときに、データがどこから来てどこに行くかを正確に指定します。  

1. Azure portal で Stream Analytics ジョブに移動します。

1. **[ジョブ トポロジ]** で、 **[入力]** 、 **[ストリーム入力の追加]** の順に選択します。

   ![Azure Stream Analytics - 入力の追加](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. ドロップダウン リストで、 **[Edge Hub]** を選択します。

1. **[新しい入力]** ウィンドウで、入力のエイリアスとして「**温度**」と入力します。

1. 他のフィールドは既定値のままにして、 **[保存]** を選択します。

1. **[ジョブ トポロジ]** で **[出力]** を開き、 **[追加]** を選択します。

   ![Azure Stream Analytics - 出力の追加](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. ドロップダウン リストで、 **[Edge Hub]** を選択します。

1. **[新しい出力]** ウィンドウで、出力のエイリアスとして「**アラート**」と入力します。

1. 他のフィールドは既定値のままにして、 **[保存]** を選択します。

1. **[ジョブ トポロジ]** で、 **[クエリ]** を選択します。

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

1. **[クエリの保存]** を選択します。

### <a name="configure-iot-edge-settings"></a>IoT Edge の設定を構成する

IoT Edge デバイスにデプロイされるように Stream Analytics ジョブを準備するには、ジョブをストレージ アカウント内のコンテナーに関連付ける必要があります。 ジョブをデプロイしようとすると、ジョブ定義がストレージ コンテナーにエクスポートされます。

1. **[構成]** で **[ストレージ アカウントの設定]** を選択し、 **[ストレージ アカウントの追加]** を選択します。

   ![Azure Stream Analytics - ストレージ アカウントの追加](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. このチュートリアルの開始時に作成した **ストレージ アカウント** をドロップダウン メニューから選択します。

1. **[コンテナー]** フィールドで、 **[新規作成]** を選択し、ストレージ コンテナーの名前を指定します。

1. **[保存]** を選択します。

## <a name="deploy-the-job"></a>ジョブのデプロイ

IoT Edge デバイスに Azure Stream Analytics ジョブをデプロイする準備が整いました。

このセクションでは、Azure portal の **モジュールの設定** ウィザードを使用して、*配置マニフェスト* を作成します。 配置マニフェストは、デバイスにデプロイされるすべてのモジュール、モジュール イメージを格納するコンテナー レジストリ、モジュールの管理方法、およびモジュール間の通信方法を記述した JSON ファイルです。 IoT Edge デバイスは、IoT Hub からその配置マニフェストを取得し、その中の情報を使用して、割り当てられているすべてのモジュールをデプロイおよび構成します。

このチュートリアルでは、2 つのモジュールをデプロイします。 1 つ目は、**SimulatedTemperatureSensor** です。これは、温度と湿度のセンサーをシミュレートするモジュールです。 2 つ目は、Stream Analytics ジョブです。 センサー モジュールは、ジョブ クエリが分析するデータのストリームを提供します。

1. Azure Portal で、お使いの IoT ハブに移動します。

1. **[IoT Edge]** に移動し、ご自身の IoT Edge デバイスの詳細ページを開きます。

1. **[Set modules]\(モジュールの設定\)** を選びます。  

1. 以前、このデバイスに SimulatedTemperatureSensor モジュールをデプロイしたことがある場合は、自動入力されている可能性があります。 そうでない場合は、次の手順でモジュールを追加します。

   1. **[追加]** をクリックし、 **[IoT Edge モジュール]** を選択します。
   1. 名前は、「**SimulatedTemperatureSensor**」と入力します。
   1. イメージ URI として「**mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**」と入力します。
   1. 他の設定はそのままにして、 **[追加]** を選択します。

1. 次の手順に従って、ご自身の Azure Stream Analytics Edge ジョブを追加します。

   1. **[追加]** をクリックし、 **[Azure Stream Analytics モジュール]** を選択します。
   1. 自分のサブスクリプションと、作成した Azure Stream Analytics Edge ジョブを選びます。
   1. **[保存]** を選択します。

   変更を保存すると、Stream Analytics ジョブの詳細が、作成したストレージ コンテナーに発行されます。

1. Stream Analytics モジュールがモジュールの一覧に追加されたら、その名前を選択して構造を確認し、 **[IoT Edge モジュールの更新]** ページで設定を更新します。

   **[モジュールの設定]** タブには、標準の Azure Stream Analytics イメージを指す **[イメージの URI]** があります。 この 1 つのイメージが、IoT Edge デバイスにデプロイされるすべての Stream Analytics モジュールに使用されます。

   **[モジュール ツインの設定]** タブには、**ASAJobInfo** という Azure Stream Analytics (ASA) プロパティを定義する JSON が表示されます。 そのプロパティの値は、ストレージ コンテナー内のジョブ定義を指しています。 このプロパティによって、特定のジョブの詳細で Stream Analytics イメージが構成されます。

   既定では、Stream Analytics モジュールは、基になるジョブと同じ名前になります。 必要に応じて、このページでモジュール名を変更できますが、必須ではありません。

1. **[更新]** または **[キャンセル]** を選択します。

1. 後の手順で必要になるため、Stream Analytics モジュールの名前を書き留めます。 次に、 **[Next:ルート]** を選択して続行します。

1. **[ルート]** タブで、モジュールと IoT Hub の間でメッセージが渡される方法を定義します。 メッセージは、名前と値のペアを使用して作成されます。 既定の `route` および `upstream` の名前と値を、次の表に示すペア (次の名前と値のペア) に置き換えます。また、 _{moduleName}_ のインスタンスを実際の Azure Stream Analytics モジュールの名前に置き換えます。

    | 名前 | 値 |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    ここで宣言するルートは、IoT Edge デバイスを通過するデータのフローを定義します。 SimulatedTemperatureSensor のテレメトリ データは、IoT Hub に送信されます。また、Stream Analytics ジョブで構成された **温度** 入力にも送信されます。 **アラート** 出力メッセージは、IoT Hub に送信され、リセット コマンドをトリガーするように SimulatedTemperatureSensor モジュールにも送信されます。

1. **確認と作成** を選択します。

1. **[確認と作成]** タブでは、ウィザードで指定した情報がどのように JSON 配置マニフェストに変換されるかを確認できます。 マニフェストの確認が完了したら、 **[作成]** を選択します。

1. これでデバイスの詳細ページに戻ります。 **[最新の情報に更新]** を選択します。  

    新しい Stream Analytics モジュールが、IoT Edge エージェント モジュールおよび IoT Edge ハブ モジュールと共に実行されていることがわかります。 IoT Edge デバイスに情報が到着し、新しいモジュールが開始されるまでに、数分かかる場合があります。 モジュールがすぐに実行されない場合は、ページを更新し続けます。

    ![デバイスごとにレポートされる SimulatedTemperatureSensor および ASA モジュール](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>データの表示

ここで、IoT Edge デバイスに移動し、Azure Stream Analytics モジュールと SimulatedTemperatureSensor モジュールの間のやり取りを確認できます。

1. すべてのモジュールが Docker で実行されていることを確認します。

   ```cmd/sh
   iotedge list  
   ```

1. すべてのシステム ログとメトリック データを表示します。 Stream Analytics モジュールの名前を使用してください。

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. センサー ログを表示して、リセット コマンドの SimulatedTemperatureSensor への影響を表示します。

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   マシンの温度が徐々に上昇し、70 度に達して 30 秒間経過するのを観察できます。 次に、Stream Analytics モジュールによってリセットがトリガーされ、マシンの温度が 21 度まで下降します。

   ![モジュール ログへのコマンド出力をリセットする](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

そうでない場合は、課金されないようにするために、ローカル構成と、この記事で使用した Azure リソースを削除できます。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスのデータを分析するように、Azure Streaming Analytics ジョブを構成しました。 その後、温度上昇にローカルで対処するために、IoT Edge デバイスでこの Azure Stream Analytics モジュールを読み込みました。また、集計されたデータ ストリームをクラウドに送信しました。 Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [モジュールとして Azure Machine Learning モデルをデプロイする](tutorial-deploy-machine-learning.md)