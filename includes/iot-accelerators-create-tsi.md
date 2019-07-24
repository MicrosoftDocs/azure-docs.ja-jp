---
title: インクルード ファイル
description: インクルード ファイル
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ab3d4cbe8a200e91d02177e49446065cd9c228df
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609074"
---
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

[Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) は、IoT 規模の時系列データをクラウドで管理するための、フル マネージドの分析、ストレージ、視覚化サービスです。 新しい Time Series Insights 環境を作成するには:

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]** > **[モノのインターネット (IoT)]** > **[Time Series Insights]** を選択します。

    ![新しい Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. ソリューション アクセラレータと同じリソース グループに Time Series Insights 環境を作成するには、次の表の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | 環境名 | 次のスクリーンショットでは、**Contoso-TSI** という名前を使用しています。 この手順を完了したら、独自の一意の名前を選択してください。 |
    | サブスクリプション | ドロップダウンから Azure サブスクリプションを選択します。 |
    | リソース グループ | **contoso-simulation**。 使用しているソリューション アクセラレータの名前を使用します。 |
    | 場所 | この記事では、**[米国東部]** を使用します。 環境は、デバイス シミュレーション アクセラレータと同じリージョンに作成します。 |
    | SKU |**S1** |
    | 容量 | **1** |

    ![Time Series Insights を作成する](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Time Series Insights 環境をソリューション アクセラレータと同じリソース グループに追加した場合、ソリューション アクセラレータを削除するとこの環境も削除されます。

1. **Create** をクリックしてください。 環境が作成されるまでに数分かかることがあります。

## <a name="create-event-source"></a>イベント ソースの作成

IoT Hub に接続する新しいイベント ソースを作成します。 前の手順で作成したコンシューマー グループを使用してください。 Time Series Insights のイベント ソースには、他のサービスによって使用されない専用のコンシューマー グループを用意する必要があります。

1. Azure portal で、新しい Time Series 環境に移動します。

1. 左側で、**[イベント ソース]** をクリックします。

    ![イベント ソースを表示する](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. **[追加]** をクリックします。

    ![イベント ソースを追加する](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. IoT Hub を新しいイベント ソースとして構成するには、次の表の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | イベント ソース名 | 次のスクリーンショットでは、**contoso-iot-hub** という名前を使用しています。 この手順を完了したら、独自の一意の名前を使用してください。 |
    | ソース | **IoT Hub** |
    | インポート オプション | **使用可能なサブスクリプションの IoT Hub を使用する** |
    | サブスクリプション ID | ドロップダウンから Azure サブスクリプションを選択します。 |
    | IoT Hub 名 | **contoso-simulation7d894**。 デバイス シミュレーション ソリューション アクセラレータからの実際の IoT Hub の名前を使用してください。 |
    | IoT Hub ポリシー名 | **iothubowner** |
    | IoT Hub ポリシー キー | このフィールドは自動的に設定されます。 |
    | IoT Hub コンシューマー グループ | **devicesimulationtsi** |
    | イベントのシリアル化の形式 | **JSON** |
    | タイムスタンプ プロパティ名 | 空白 |

    ![イベント ソースを作成する](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. **Create** をクリックしてください。

> [!NOTE]
> Time Series Insights エクスプローラーへの[アクセスを他のユーザーに許可する](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access)ことができます。