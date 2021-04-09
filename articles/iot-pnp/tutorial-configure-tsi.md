---
title: チュートリアル - Azure Time Series Insights を使用して Azure IoT プラグ アンド プレイ デバイスのテレメトリを格納および分析する
description: チュートリアル - Time Series Insights 環境を設定し、IoT ハブを接続して、IoT プラグ アンド プレイ デバイスからのテレメトリを表示、分析します。
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 55fa10cce038c83f0758a9537a916e2dca7e13f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181688"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>チュートリアル:Time Series Insights Gen2 環境を作成および構成する

このチュートリアルでは、[Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) 環境を作成し、IoT プラグ アンド プレイ ソリューションと連携するよう構成する方法について説明します。 Time Series Insights を使用すると、モノのインターネット (IoT) のスケールで時系列データを収集、処理、格納、照会、視覚化できます。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Time Series Insights 環境をプロビジョニングし、ストリーミング イベント ソースとして IoT ハブを接続します。
> * モデルの同期を実行して、[時系列モデル](../time-series-insights/concepts-model-overview.md)を作成します。
> * 温度コントローラーおよびサーモスタット デバイスに使用した [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) のサンプル モデル ファイルを使用します。

> [!NOTE]
> この Time Series Insights と IoT プラグ アンド プレイの統合はプレビュー段階です。 DTDL デバイス モデルを Time Series Insights の時系列モデルにマップする方法は変更される可能性があります。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

この時点で、次を用意します。

* Azure IoT Hub。
* 自分の IoT ハブにリンクされている Device Provisioning Service (DPS) インスタンス。 DPS インスタンスには、自分の IoT プラグ アンド プレイ デバイスに対する個別のデバイス登録が必要です。
* シミュレートされたデータをストリーム配信する単一コンポーネント デバイスまたは複数コンポーネント デバイスから IoT ハブへの接続。

Azure CLI をローカルにインストールせずに済むように、Azure Cloud Shell を使用してクラウド サービスを設定できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>イベント ソースの準備

先ほど作成した IoT ハブが Time Series Insights 環境の[イベント ソース](../time-series-insights/concepts-streaming-ingestion-event-sources.md)になります。

> [!IMPORTANT]
> 既存の IoT Hub ルートをすべて無効にします。 [ルーティング](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints)が構成されている IoT ハブの使用には、既知の問題があります。 ルーティング エンドポイントを一時的に無効にします。 自分の IoT ハブが Time Series Insights に接続されている場合は、ルーティング エンドポイントを再度有効にできます。

自分の IoT ハブで、Time Series Insights で使用する一意のコンシューマー グループを作成します。 次の例の `my-pnp-hub` を、先ほど使用した IoT ハブの名前に置き換えてください。

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>タイム シリーズ ID の選択

Time Series Insights 環境をプロビジョニングするときに、"*時系列 ID*" を選択する必要があります。 適切な時系列 ID を選択することが重要です。 このプロパティは変更不可であり、設定した後に変更することはできません。 時系列 ID は、データベースのパーティション キーのようなものです。 時系列 ID は、時系列モデルの主キーとして機能します。 詳しくは、「[Best practices for choosing a Time Series ID](../time-series-insights/how-to-select-tsid.md)」(タイム シリーズ ID の選択のベスト プラクティス) をご覧ください。

IoT プラグ アンド プレイ ユーザーとして、`iothub-connection-device-id` と `dt-subject` で構成される "_複合キー_" を時系列 ID に指定します。 IoT ハブによって、それぞれ IoT プラグ アンド プレイ デバイス ID とデバイス コンポーネント名を含むこれらのシステム プロパティが追加されます。

IoT プラグ アンド プレイ デバイス モデルで現在コンポーネントを使用していない場合でも、後でコンポーネントを使用できるように、複合キーの一部として `dt-subject` を含めておくことが推奨されます。 時系列 ID は変更不可能であるため、後で必要になる場合に備えて、このオプションを有効にすることをお勧めします。

> [!NOTE]
> この記事の例は、複数コンポーネントの `TemperatureController` デバイス用です。 ただし、考え方はコンポーネントなしの `Thermostat` デバイスの場合でも同じです。

## <a name="provision-your-time-series-insights-environment"></a>Time Series Insights 環境のプロビジョニング

このセクションでは、Azure Time Series Insights Gen2 環境をプロビジョニングする方法について説明します。

次のコマンドを実行して以下を行います。

* 自分の環境の[コールド ストア](../time-series-insights/concepts-storage.md#cold-store)用 Azure ストレージ アカウントを作成する。 このアカウントは、履歴データの長期保有と分析を目的として設計されています。
  * コード内の `mytsicoldstore` をコールド ストレージ アカウントの一意の名前に置き換えます。
* Azure Time Series Insights Gen2 環境を作成する。 環境は、保持期間が 7 日間のウォーム ストレージで作成されます。 コールド ストレージ アカウントは、無期限の保持のためにアタッチされます。
  * コード内の `my-tsi-env` を自分の Time Series Insights 環境の一意の名前に置き換えます。
  * コード内の `my-pnp-resourcegroup` を、設定時に使用したリソース グループの名前に置き換えます。
  * 自分の時系列 ID プロパティは `iothub-connection-device-id, dt-subject` です。

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

IoT Hub イベント ソースを接続します。 `my-pnp-resourcegroup`、`my-pnp-hub`、`my-tsi-env` は、選択した値に置き換えます。 次のコマンドでは、前に作成した Time Series Insights のコンシューマー グループを参照します。

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

[Azure portal](https://portal.azure.com) で自分のリソース グループに移動し、新しい Time Series Insights 環境を選択します。 インスタンスの概要に示されている **[Time Series Insights エクスプローラーの URL]** にアクセスします。

![ポータルの概要ページのスクリーンショット。](./media/tutorial-configure-tsi/view-environment.png)

エクスプローラーには、3 つのインスタンスが表示されます。

* &lt;実際の pnp デバイス ID&gt;、thermostat1
* &lt;実際の pnp デバイス ID&gt;、thermostat2
* &lt;実際の pnp デバイス ID&gt;、`null`

> [!NOTE]
> 3 番目のタグは、デバイス メモリのワーキング セットなど、`TemperatureController` 自体からのテレメトリを表します。 これは最上位レベルのプロパティであるため、コンポーネント名の値は null になります。 後の手順で、これをもっとわかりやすい名前に変更します。

![エクスプローラーに 3 つのインスタンスが表示されているスクリーンショット。](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>モデル変換の構成

次に、DTDL デバイス モデルを Azure Time Series Insights の資産モデルに変換します。 Time Series Insights の時系列モデルは、データをコンテキスト化するためのセマンティック モデリング ツールです。 このモデルには、3 つの主要なコンポーネントがあります。

* [時系列モデルのインスタンス](../time-series-insights/concepts-model-overview.md#time-series-model-instances)は、時系列自体の仮想表現です。 インスタンスは、時系列 ID によって一意に識別されます。
* [時系列モデルの階層](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies)では、プロパティ名とそのリレーションシップを指定して、インスタンスを整理できます。
* [時系列モデルの型](../time-series-insights/concepts-model-overview.md#time-series-model-types)は、計算を行うための[変数](../time-series-insights/concepts-variables.md)や数式を定義するのに役立ちます。 型は特定のインスタンスに関連付けられています。

### <a name="define-your-types"></a>型の定義

Azure Time Series Insights Gen2 へのデータの取り込みは、モデルを事前に定義していなくても開始できます。 テレメトリが到着すると、時系列 ID プロパティの値に基づいて時系列インスタンスの自動解決が Time Series Insights によって試行されます。 すべてのインスタンスには、"*既定の型*" が割り当てられます。 ご利用のインスタンスを正しく分類するために新しい型を手動で作成する必要があります。 

次の詳細では、デバイスの DTDL モデルを時系列モデルの型と同期させる最も簡単な方法を説明しています。

* デジタル ツイン モデル識別子は型の ID になります。
* 型の名前には、モデルの名前または表示名を指定できます。
* モデルの説明は、型の説明になります。
* 数値のスキーマを備えたテレメトリごとに、型変数が少なくとも 1 つ作成されます。
  * 変数に使用できるのは数値データ型だけですが、変換可能な別の型として値が送信される場合 (`"0"` など)、`toDouble` などの[変換](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions)関数を使用することができます。
* 変数の名前には、テレメトリの名前または表示名を指定できます。
* 時系列式の変数を定義する際は、送信中のテレメトリの名前とそのテレメトリのデータ型を参照します。

| DTDL の JSON | 時系列モデルの型の JSON | 値の例 |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (配列)| `variables` (オブジェクト)  | 次の例を参照してください。

![D T D L と時系列モデルの型を示すスクリーンショット。](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> この例に示されている変数は 3 つですが、1 つの型につき最大 100 個の変数を定義できます。 それぞれの変数で同じテレメトリ値を参照して、必要に応じてさまざまな計算を行うことができます。 フィルター、集計、スカラー関数の全一覧については、[Time Series Insights Gen2 の時系列式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax)に関するページを参照してください。

テキスト エディターを開き、次の JSON をローカル ドライブに保存します。

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Time Series Insights Explorer で、左側のモデル アイコンを選択して **[モデル]** タブを開きます。 **[型]** を選択し、 **[JSON をアップロードします]** を選択します。

![JSON をアップロードする方法を示すスクリーンショット。](./media/tutorial-configure-tsi/upload-type.png)

**[ファイルの選択]** を選択し、先ほど保存した JSON を選択して、 **[アップロード]** を選択します。

新しく定義された **Temperature Controller** 型が表示されます。

### <a name="create-a-hierarchy"></a>階層の作成

タグを整理するための階層を、親である `TemperatureController` の下に作成します。 次の単純な例はレベルが 1 つですが、IoT ソリューションでは一般的に、組織内の物理的およびセマンティックな位置内でタグをコンテキスト化するために、入れ子のレベルがたくさんあります。

**[階層]** を選択し、 **[階層の追加]** を選択します。 名前として「*Device Fleet*」と入力します。 *Device Name* という名前のレベルを 1 つ作成します。 次に、 **[保存]** を選択します。

![階層を追加する方法を示すスクリーンショット。](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>インスタンスを正しい型に割り当てる

次に、インスタンスの型を変更し、それらを階層内に配置します。

**[インスタンス]** タブを選択します。デバイスのワーキング セットを表すインスタンスを見つけて、右端にある **[編集]** アイコンを選択します。

![インスタンスを編集する方法を示すスクリーンショット。](./media/tutorial-configure-tsi/edit-instance.png)

**[型]** ドロップダウン メニューを開き、 **[Temperature Controller]** を選択します。 「*defaultComponent, <your device name>* 」と入力して、デバイスに関連付けられているすべての最上位レベルのタグを表すインスタンスの名前を更新します。

![インスタンスの型を変更する方法を示すスクリーンショット。](./media/tutorial-configure-tsi/change-type.png)

**[保存]** を選択する前に、 **[インスタンス フィールド]** タブを選択し、 **[Device Fleet]** を選択します。 テレメトリをグループ化するには、「 *\<your device name> - Temp Controller*」と入力します。 次に、 **[保存]** を選択します。

![インスタンスを階層に割り当てる方法を示すスクリーンショット](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

前の手順を繰り返して、サーモスタットのタグに正しい型と階層を割り当てます。

## <a name="view-your-data"></a>データの表示

グラフ ペインに戻り、 **[Device Fleet]** 、自分のデバイスの順に展開します。 **thermostat1** を選択して **Temperature** 変数を選択し、 **[追加]** を選択して値をグラフ化します。 **thermostat2** と **defaultComponent** **workingSet** 値に対して同じことを行います。

![thermostat2 のインスタンスの型を変更する方法を示すスクリーンショット。](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> 間隔のサイズ設定や Y 軸コントロールなど、さまざまなグラフ作成オプションの詳細については、「[Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md)」を参照してください。
