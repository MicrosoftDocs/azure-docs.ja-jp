---
title: Time Series Insights を使用してシミュレートされたテレメトリを視覚化する - Azure | Microsoft Docs
description: Time Series Insights 環境を構成して、デバイス シミュレーション ソリューション アクセラレータによって生成されたテレメトリを調査および分析する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854565"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights を使用して、デバイス シミュレーション ソリューション アクセラレータから送信されたテレメトリを視覚化する

デバイス シミュレーション ソリューション アクセラレータを使用すると、シミュレートされたデバイスからテレメトリを生成して IoT ソリューションをテストできます。 このハウツー ガイドでは、Time Series Insights 環境を使用してシミュレートされたテレメトリを視覚化および分析する方法を示します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、アクティブな Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このハウツー ガイドの手順は、Azure サブスクリプションにデバイス シミュレーション ソリューション アクセラレータがデプロイされているものと想定しています。 まだデバイス シミュレーションをデプロイしていない場合は、GitHub の[デバイス シミュレーションのデプロイ](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)に関する記事を参照してください。

この記事では、ソリューション アクセラレータの名前を **contoso-simulation** と想定しています。 以下の手順を実行するときは、**contoso-simulation** を実際のソリューション アクセラレータの名前に置き換えてください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>コンシューマー グループの作成

Time Series Insights にテレメトリをストリーム配信するには、専用のコンシューマー グループを IoT Hub 内に作成する必要があります。 Time Series Insights のイベント ソースは、IoT Hub コンシューマー グループを排他的に使用できる必要があります。

次の手順では、Azure Cloud Shell で Azure CLI を使用して、コンシューマー グループを作成します。

1. IoT Hub は、デバイス シミュレーション ソリューション アクセラレータをデプロイしたときに作成されたいくつかのリソースの 1 つです。 次のコマンドを実行して、IoT Hub の名前を確認します。使用しているソリューション アクセラレータの名前を使用してください。

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT Hub のリソースの種類は、**Microsoft.Devices/IotHubs** です。

1. **devicesimulationtsi** という名前のコンシューマー グループをハブに追加します。 次のコマンドで、使用しているハブとソリューション アクセラレータの名前を使用してください。

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    ここで、Azure Cloud Shell を閉じることができます。

## <a name="create-a-new-time-series-insights-environment"></a>新しい Time Series Insights 環境を作成する

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) は、IoT 規模の時系列データをクラウドで管理するための、フル マネージドの分析、ストレージ、視覚化サービスです。 新しい Time Series Insights 環境を作成するには:

1. [Azure portal](https://portal.azure.com/) にサインインする

1. **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Time Series Insights]** を選択します。

    ![新しい Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. ソリューション アクセラレータと同じリソース グループに Time Series Insights 環境を作成するには、次の表の値を使用します。

    | 設定 | 値 |
    | ------- | ----- |
    | 環境名 | 次のスクリーンショットでは、**Contoso-TSI** という名前を使用しています。 この手順を完了したら、独自の一意の名前を選択してください。 |
    | サブスクリプション | ドロップダウンから Azure サブスクリプションを選択します。 |
    | Resource group | **contoso-simulation**。 使用しているソリューション アクセラレータの名前を使用します。 |
    | Location | この記事では、 **[米国東部]** を使用します。 環境は、デバイス シミュレーション アクセラレータと同じリージョンに作成します。 |
    | Sku |**S1** |
    | 容量 | **1** |

    ![Time Series Insights を作成する](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Time Series Insights 環境をソリューション アクセラレータと同じリソース グループに追加した場合、ソリューション アクセラレータを削除するとこの環境も削除されます。

1. **Create** をクリックしてください。 環境が作成されるまでに数分かかることがあります。

## <a name="create-event-source"></a>イベント ソースの作成

IoT Hub に接続する新しいイベント ソースを作成します。 前の手順で作成したコンシューマー グループを使用してください。 Time Series Insights のイベント ソースには、他のサービスによって使用されない専用のコンシューマー グループを用意する必要があります。

1. Azure portal で、新しい Time Series 環境に移動します。

1. 左側で、 **[イベント ソース]** をクリックします。

    ![イベント ソースを表示する](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. **[追加]** をクリックします。

    ![イベント ソースを追加する](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub を新しいイベント ソースとして構成するには、次の表の値を使用します。

    | 設定 | 値 |
    | ------- | ----- |
    | イベント ソース名 | 次のスクリーンショットでは、**contoso-iot-hub** という名前を使用しています。 この手順を完了したら、独自の一意の名前を使用してください。 |
    | source | **IoT Hub** |
    | インポート オプション | **使用可能なサブスクリプションの IoT Hub を使用する** |
    | サブスクリプション ID | ドロップダウンから Azure サブスクリプションを選択します。 |
    | IoT Hub 名 | **contoso-simulation7d894**。 デバイス シミュレーション ソリューション アクセラレータからの実際の IoT Hub の名前を使用してください。 |
    | IoT Hub ポリシー名 | **iothubowner** |
    | IoT Hub ポリシー キー | このフィールドは自動的に設定されます。 |
    | IoT Hub コンシューマー グループ | **devicesimulationtsi** |
    | イベントのシリアル化の形式 | **JSON** |
    | タイムスタンプ プロパティ名 | 空白 |

    ![イベント ソースを作成する](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. **Create** をクリックしてください。

> [!NOTE]
> Time Series Insights エクスプローラーへの[アクセスを他のユーザーに許可する](../time-series-insights/concepts-access-policies.md#grant-data-access)ことができます。

## <a name="start-a-simulation"></a>シミュレーションの開始

Time Series Insights エクスプローラーを使用する前に、いくつかのテレメトリを生成するようにデバイス シミュレーション ソリューション アクセラレータを構成します。 次のスクリーンショットは、10 台の冷却装置のシミュレーションを実行している状態を示しています。

![実行中のデバイス シミュレーション](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights エクスプローラー

Time Series Insights エクスプローラーは、テレメトリを視覚化するために使用できる Web アプリです。

1. Azure portal で、Time Series Insights の **[概要]** タブを選択します。

1. Time Series Insights エクスプローラー Web アプリを開くには、 **[環境を開く]** をクリックします。

    ![Time Series Insights エクスプローラー](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. 時間選択パネルで、クイック タイム メニューから **[過去 30 分間]** を選択し、 **[検索]** をクリックします。

    ![Time Series Insights エクスプローラーでの検索](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. 左側の条件パネルで、 **[メジャー]** として **[温度]** を選択し、 **[分割基準]** の値として **[iothub-connection-device-id]** を選択します。

    ![[メジャー] と [分割基準] の値が強調表示されている Time Series Insights の [条件] パネルを示すスクリーンショット。](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. グラフを右クリックし、 **[イベントの探索]** を選択します。

    ![Time Series Insights エクスプローラーでのイベント](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. イベント データはグリッドに表示されます。

    ![Time Series Insights エクスプローラーでの表](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. パースペクティブ ビュー ボタンをクリックします。

    ![Time Series Insights エクスプローラーでのパースペクティブ](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. **[+]** をクリックして、パースペクティブに新しいクエリを追加します。

    ![Time Series Insights エクスプローラーでのクエリの追加](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. 時間間隔として **[過去 30 分間]** を選択し、 **[メジャー]** として **[湿度]** を選択し、 **[分割基準]** の値として **[iothub-connection-device-id]** を選択します。

    ![Time Series Insights エクスプローラーでのクエリ](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. パースペクティブ ビュー ボタンをクリックして、デバイス テレメトリ ダッシュボードを表示します。

    ![Time Series Insights エクスプローラーでのダッシュボード](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

さらに調べる場合は、ソリューション アクセラレータをデプロイしたままにします。

ソリューション アクセラレータが不要になった場合は、[[プロビジョニングされたソリューション]](https://www.azureiotsolutions.com/Accelerators#dashboard) ページでそれを選択し、 **[ソリューションの削除]** をクリックして削除してください。

Time Series Insights 環境をソリューション アクセラレータのリソース グループに追加している場合、ソリューション アクセラレータを削除するとこの環境も自動的に削除されます。 それ以外の場合は、Azure portal から Time Series Insights 環境を手動で削除する必要があります。

## <a name="next-steps"></a>次の手順

Time Series Insights エクスプローラーでのデータの探索とクエリ方法の詳細については、「[Azure Time Series Insights エクスプローラー](../time-series-insights/time-series-insights-explorer.md)」を参照してください。