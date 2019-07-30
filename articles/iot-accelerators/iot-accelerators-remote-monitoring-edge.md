---
title: ソリューションのエッジで異常を検出するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して IoT Edge デバイスを監視する方法を学習します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117540"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>チュートリアル:リモート監視ソリューション アクセラレータを使用してエッジで異常を検出する

このチュートリアルでは、IoT Edge デバイスによって検出された異常に対応するようリモート監視ソリューションを構成します。 IoT Edge デバイスを使用すると、テレメトリをエッジで処理することによって、ソリューションに送信されるテレメトリの量を減らし、デバイスのイベントへの対応を迅速化することができます。 エッジ処理の利点の詳細については、「[Azure IoT Edge とは](../iot-edge/about-iot-edge.md)」を参照してください。

リモート監視を使用したエッジ処理を紹介するために、このチュートリアルでは、シミュレートしたオイル ポンプ ジャック装置を使用します。 このオイル ポンプ ジャックは Contoso という組織によって管理され、リモート監視ソリューション アクセラレータに接続されています。 オイル ポンプ ジャックでは、センサーによって温度と圧力が測定されます。 Contoso のオペレーターは、異常な温度上昇がオイル ポンプ ジャックの速度低下を招く場合があることを把握しています。 装置の温度が正常範囲内にあれば、Contoso のオペレーターが温度を監視する必要はありません。

Contoso では、温度の異常を検出するインテリジェント エッジ モジュールをオイル ポンプ ジャックにデプロイしたいと考えています。 別のエッジ モジュールからはリモート監視ソリューションにアラートを送信します。 Contoso のオペレーターは、アラートを受信するとメンテナンス技術者を派遣することができます。 また、Contoso では、ソリューションがアラートを受信したときに自動的に実行されるアクション (メールの送信など) を構成することもできます。

次の図は、チュートリアルのシナリオの主要コンポーネントを示しています。

![概要](media/iot-accelerators-remote-monitoring-edge/overview.png)

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * IoT Edge デバイスをソリューションに追加する
> * Edge マニフェストの作成
> * デバイス上で実行するモジュールを定義するパッケージとしてマニフェストをインポートする
> * IoT Edge デバイスにパッケージをデプロイする
> * デバイスからのアラートを表示する

IoT Edge デバイス上:

* ランタイムはパッケージを受け取り、モジュールをインストールします。
* ストリーム分析モジュールは、ポンプ内の温度の異常を検出し、コマンドを送信して問題を解決します。
* ストリーム分析モジュールは、フィルターされたデータをソリューション アクセラレータに転送します。

このチュートリアルでは、Linux 仮想マシンを IoT Edge デバイスとして使用します。 また、Edge モジュールをインストールしてオイル ポンプ ジャック デバイスをシミュレートします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>IoT Edge デバイスの追加

リモート監視ソリューション アクセラレータに IoT Edge デバイスを追加するには、2 つの手順があります。 このセクションでは、次の使用方法について説明します。

* リモート監視 Web UI の **[Device Explorer]** ページで IoT Edge デバイスを追加します。
* Linux 仮想マシン (VM) に IoT Edge ランタイムをインストールします。

### <a name="add-an-iot-edge-device-to-your-solution"></a>IoT Edge デバイスをソリューションに追加する

リモート監視ソリューション アクセラレータに IoT Edge デバイスを追加するには、Web UI の **[Device Explorer]** ページに移動し、 **[+ 新規デバイス]** をクリックします。

**[新規デバイス]** パネルで **[IoT Edge デバイス]** を選択し、デバイス ID として「**oil-pump**」と入力します。 その他の設定は既定値のままでかまいません。 次に、 **[適用]** をクリックします。

[![IoT Edge デバイスの追加](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

デバイスの接続文字列は、このチュートリアルの次のセクションで必要になるので、書き留めておいてください。

リモート監視ソリューション アクセラレータの IoT ハブにデバイスを登録すると、それが Web UI の **[Device Explorer]** ページに表示されます。

[![新しい IoT Edge デバイス](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

ソリューションの IoT Edge デバイスを管理しやすくするために、デバイス グループを作成して、IoT Edge デバイスを追加します。

1. **[Device Explorer]** ページにある一覧で **[oil-pump]** デバイスを選択し、 **[ジョブ]** をクリックします。

1. 次の設定を使用して、デバイスに **IsEdge** タグを追加するジョブを作成します。

    | Setting | 値 |
    | ------- | ----- |
    | ジョブ     | タグ  |
    | ジョブ名 | AddEdgeTag |
    | キー     | IsOilPump |
    | 値   | Y     |
    | type    | Text  |

    [![タグの追加](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. **[適用]** 、 **[閉じる]** の順にクリックします。

1. **[Device Explorer]** ページで、 **[Manage device groups]\(デバイス グループの管理\)** をクリックします。

1. **[Create new device group]\(新しいデバイス グループの作成\)** をクリックします。 次の設定を使用して新しいデバイス グループを作成します。

    | Setting | 値 |
    | ------- | ----- |
    | Name    | OilPumps |
    | フィールド   | Tags.IsOilPump |
    | operator | = Equals |
    | 値    | Y |
    | type     | Text |

    [![デバイス グループの作成](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. **[Save]** をクリックします。

これで IoT Edge デバイスが **OilPumps** グループに追加されました。

### <a name="install-the-edge-runtime"></a>Edge ランタイムをインストールする

Edge デバイスでは、Edge ランタイムがインストールされている必要があります。 このチュートリアルでは、Azure Linux VM に Edge ランタイムをインストールしてシナリオをテストします。 以降の手順では、VM のインストールと構成に Azure Cloud Shell を使用します。

1. Azure に Linux VM を作成するには、次のコマンドを実行します。 所在地に近い場所を使用してください。

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. デバイス接続文字列を使用して Edge ランタイムを構成するには、以前書き留めたデバイス接続文字列を使用して次のコマンドを実行します。

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    接続文字列は、必ず二重引用符で囲んでください。

以上で IoT Edge ランタイムを Linux デバイスにインストールし、構成が完了しました。 このチュートリアルでは後ほど、リモート監視ソリューションを使用してこのデバイスに IoT Edge モジュールをデプロイします。

## <a name="create-an-edge-manifest"></a>Edge マニフェストの作成

オイル ポンプ ジャック装置をシミュレートするためには、Edge デバイスに次のモジュールを追加する必要があります。

* 温度シミュレーション モジュール。
* Azure Stream Analytics の異常検出。

次の手順では、これらのモジュールを含んだ Edge デプロイ マニフェストを作成する方法について説明します。 このマニフェストでは後ほど、リモート監視ソリューション アクセラレータにパッケージとしてインポートすることになります。

### <a name="create-the-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブを作成する

Stream Analytics ジョブは、ポータルで定義した後に Edge モジュールとしてパッケージ化します。

1. Azure portal で、既定のオプションを使用して **IoTEdgeDevices** リソース グループに Azure ストレージ アカウントを作成します。 選択した名前を書き留めておきます。

1. Azure portal で、**IoTEdgeDevices** リソース グループに **Stream Analytics ジョブ**を作成します。 次の構成値を使用します。

    | オプション | 値 |
    | ------ | ----- |
    | ジョブ名 | EdgeDeviceJob |
    | サブスクリプション | お使いの Azure サブスクリプション |
    | リソース グループ | IoTEdgeDevices |
    | Location | 米国東部 |
    | ホスティング環境 | Edge |
    | [ストリーミング ユニット] | 1 |

1. ポータルで **EdgeDeviceJob** Stream Analytics ジョブを開き、[入力] をクリックして、**telemetry** という **Edge Hub** ストリーム入力を追加します。

1. ポータルの **EdgeDeviceJob** Stream Analytics ジョブで、 **[出力]** をクリックして、**output** という **Edge Hub** 出力を追加します。

1. ポータルの **EdgeDeviceJob** Stream Analytics ジョブで、 **[出力]** をクリックして、**alert** という、もう 1 つの **Edge Hub** 出力を追加します。

1. ポータルの **EdgeDeviceJob** Stream Analytics ジョブで、 **[クエリ]** をクリックし、次の **select** ステートメントを追加します。

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. ポータルの **EdgeDeviceJob** Stream Analytics ジョブで、 **[ストレージ アカウントの設定]** をクリックします。 このセクションの開始時に **IoTEdgeDevices** リソース グループに追加したストレージ アカウントを追加します。 **edgeconfig** という新しいコンテナーを作成します。

次のスクリーンショットは、保存済みの Stream Analytics ジョブを示しています。

[![Stream Analytics ジョブ](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

これで、エッジ デバイスで実行する Stream Analytics ジョブの定義が完了しました。 このジョブでは、5 秒間の平均温度が計算されます。 また、3 秒間の平均温度が 400 を超えた場合はアラートが送信されます。

### <a name="create-the-iot-edge-deployment"></a>IoT Edge デプロイを作成する

次に、Edge デバイスで実行するモジュールを定義する IoT Edge デプロイ マニフェストを作成します。 次のセクションでは、このマニフェストをリモート監視ソリューションにパッケージとしてインポートします。

1. Azure portal で、対象となるリモート監視ソリューションの IoT ハブに移動します。 リソース グループには、リモート監視ソリューションと同じ名前の IoT ハブが見つかります。

1. IoT ハブの **[デバイスの自動管理]** セクションで **[IoT Edge]** をクリックします。 **[Add an IoT Edge deployment]\(IoT Edge デプロイの追加\)** をクリックします。

1. **[デプロイの作成] の [Name and Label]\(名前とラベル\)** ページで、「**oil-pump-device**」という名前を入力します。 **[次へ]** をクリックします。

1. **[デプロイの作成] の [モジュールの追加]** ページで、 **[+ 追加]** をクリックします。 **[IoT Edge モジュール]** を選択します。

1. **[IoT Edge のカスタム モジュール]** パネルで、名前として「**temperatureSensor**」を、イメージの URI として「**asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64**」を入力します。 **[Save]** をクリックします。

1. **[デプロイの作成] の [モジュールの追加]** ページで、 **[+ 追加]** をクリックしてモジュールをもう 1 つ追加します。 **[Azure Stream Analytics モジュール]** を選択します。

1. **[Edge deployment]\(Edge のデプロイ\)** パネルで、対象のサブスクリプションと前のセクションで作成した **EdgeDeviceJob** を選択します。 **[Save]** をクリックします。

1. **[デプロイの作成] の [モジュールの追加]** ページで、 **[次へ]** をクリックします。

1. **[デプロイの作成] の [ルートの指定]** ページで、次のコードを追加します。

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    このコードによって、Stream Analytics モジュールからの出力が正しい場所にルーティングされます。

    **[次へ]** をクリックします。

1. **[デプロイの作成] の [メトリックの指定]** ページで、 **[次へ]** をクリックします。

1. **[デプロイの作成] の [ターゲット デバイス]** ページで、優先度として「10」を入力します。 **[次へ]** をクリックします。

1. **[デプロイの作成] の [デプロイの確認]** ページで、 **[送信]** をクリックします。

    [![デプロイの確認](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. メインの **[IoT Edge]** ページで、 **[IoT Edge デプロイ]** をクリックします。 デプロイの一覧に **oil-pump-device** が表示されます。

1. **[oil-pump-device]** デプロイをクリックし、 **[IoT Edge マニフェストをダウンロードする]** をクリックします。 ファイルに **oil-pump-device.json** という名前を付けて、お使いのローカル コンピューター上の適切な場所に保存します。 このファイルは、このチュートリアルの次のセクションで必要になります。

リモート監視ソリューションにパッケージとしてインポートする IoT Edge マニフェストを作成しました。 通常、IoT Edge モジュールとマニフェスト ファイルは開発者が作成します。

## <a name="import-a-package"></a>パッケージのインポート

このセクションでは、リモート監視ソリューションにパッケージとして Edge マニフェストをインポートします。

1. リモート監視の Web UI で **[パッケージ]** ページに移動し、 **[+ 新しいパッケージ]** をクリックします。

    [![新しいパッケージ](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. **[新しいパッケージ]** パネルで、パッケージの種類として **[Edge Manifest]\(Edge マニフェスト\)** を選択し、 **[参照]** をクリックしてローカル コンピューター上の **oil-pump-device.json** ファイルを見つけ、 **[アップロード]** をクリックします。

    [![パッケージのアップロード](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    これで、パッケージの一覧に **oil-pump-device.json** パッケージが含まれるようになりました。

次のセクションでは、対象の Edge デバイスにパッケージを適用するデプロイを作成します。

## <a name="deploy-a-package"></a>パッケージのデプロイ

パッケージをデバイスにデプロイする準備ができました。

1. リモート監視の Web UI で **[デプロイ]** ページに移動し、 **[+ 新しいデプロイ]** をクリックします。

    [![新しいデプロイ](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. **[新しいデプロイ]** パネルで、次の設定を使用してデプロイを作成します。

    | オプション | 値 |
    | ------ | ----- |
    | Name   | OilPumpDevices |
    | パッケージの種類 | Edge Manifest (Edge マニフェスト) |
    | Package | oil-pump-device.json |
    | デバイス グループ | OilPumps |
    | 優先順位 | 10 |

    [![デプロイの作成](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    **[Apply]** をクリックします。

パッケージがデバイスにデプロイされて、デバイスからテレメトリが送信され始めるまで数分待つ必要があります。

[![デプロイがアクティブ](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

**[デプロイ]** ページには、次のメトリックが表示されます。

* **[Targeted]\(対象設定済み\)** には、デバイス グループ内のデバイスの数が表示されます。
* **[適用済み]** には、デプロイ内容が適用されたデバイスの数が表示されます。
* **[成功]** には、デプロイ内の Edge デバイスのうち、IoT Edge クライアント ランタイムから成功がレポートされているデバイスの数が表示されます。
* **[失敗]** には、デプロイ内の Edge デバイスのうち、IoT Edge クライアント ランタイムから失敗がレポートされているデバイスの数が表示されます。

## <a name="monitor-the-device"></a>デバイスの監視

オイル ポンプ装置からの温度テレメトリは、リモート監視の Web UI で確認できます。

1. **[Device Explorer]** ページに移動して、対象のオイル ポンプ装置を選択します。
1. **[デバイスの詳細]** パネルの **[テレメトリ]** セクションで、 **[Temperature]** をクリックします。

    [![テレメトリの表示](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

温度がしきい値に達するまでどのように上昇するかがわかります。 Stream Analytics Edge モジュールは、温度がこのしきい値に達したことを検出すると、すぐに温度を下げるためのコマンドをデバイスに送信します。 この処理はすべて、クラウドと通信することなくデバイス上で行われます。

しきい値に達したときにオペレーターに通知したい場合は、リモート監視の Web UI でルールを作成できます。

1. **[ルール]** ページに移動して **[+ 新しいルール]** をクリックします。
1. 次の設定を使用して新しいルールを作成します。

    | オプション | 値 |
    | ------ | ----- |
    | 規則の名前 | Oil pump temperature |
    | 説明 | Oil pump temperature exceeded 300 |
    | デバイス グループ | OilPumps |
    | 計算 | すぐに |
    | フィールド | 温度 |
    | operator | > |
    | 値 | 300 |
    | 重大度レベル | 情報 |

    [![ルールの作成](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    **[Apply]** をクリックします。

1. **[ダッシュボード]** ページに移動します。 **oil-pump** 装置の温度が 300 を超えると、 **[アラート]** パネルにアラートが表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューション アクセラレータに IoT Edge デバイスを追加して構成する方法を説明しました。 リモート監視ソリューションにおける IoT Edge パッケージの使用の詳細については、次の攻略ガイドを参照してください。

> [!div class="nextstepaction"]
> [リモート監視ソリューション アクセラレータに IoT Edge パッケージをインポートする](iot-accelerators-remote-monitoring-import-edge-package.md)

IoT Edge ランタイムのインストールの詳細については、「[Linux に Azure IoT Edge ランタイムをインストールする (x64)](../iot-edge/how-to-install-iot-edge-linux.md)」を参照してください。

Azure Stream Analytics on Edge デバイスの詳細については、[Azure Stream Analytics を IoT Edge モジュールとしてデプロイする](../iot-edge/tutorial-deploy-stream-analytics.md)方法に関するページを参照してください。
