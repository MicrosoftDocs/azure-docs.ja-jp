---
title: チュートリアル - Azure IoT Edge デバイスを Azure IoT Central に追加する | Microsoft Docs
description: チュートリアル - オペレーターとして、Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加します
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724882"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>チュートリアル:Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する

"*この記事は、オペレーター、ソリューション ビルダー、デバイス開発者を対象としています。* "

このチュートリアルでは、Azure IoT Edge デバイスを構成して、お使いの Azure IoT Central アプリケーションに追加する方法について説明します。 このチュートリアルでは、IoT Edge 対応 Linux 仮想マシン (VM) を使用して、IoT Edge デバイスをシミュレートします。 この IoT Edge デバイスは、シミュレートされた環境のテレメトリを生成するモジュールを使用します。 テレメトリは、Azure IoT Central アプリケーションのダッシュボードに表示されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * IoT Edge デバイス用のデバイス テンプレートを作成する
> * IoT Central で IoT Edge デバイスを作成する
> * シミュレートされた IoT Edge デバイスを Linux VM にデプロイする

## <a name="prerequisites"></a>前提条件

**[カスタム アプリ] > [カスタム アプリケーション]** テンプレートを使用して IoT Central アプリケーションを作成するには、「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」クイックスタートを完了します。

このチュートリアルの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

GitHub から IoT Edge マニフェスト ファイルをダウンロードします。 次のリンクを右クリックし、 **[名前を付けてリンク先を保存]** を選択します: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>デバイス テンプレートを作成する

このセクションでは、IoT Edge デバイス用の IoT Central デバイス テンプレートを作成します。 開始するには IoT Edge マニフェストをインポートしてから、テレメトリの定義とビューを追加するようテンプレートを変更します。

### <a name="import-manifest-to-create-template"></a>マニフェストをインポートしてテンプレートを作成する

IoT Edge マニフェストからデバイス テンプレートを作成するには:

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** に移動し、 **[+ 新規]** を選択します。

1. **[Select template type]\(テンプレートの種類の選択\)** ページで **[Azure IoT Edge]** タイルを選択します。 次に、 **[次のステップ: カスタマイズ]** を選択します。

1. **[Upload an Azure IoT Edge deployment manifest]\(Azure IoT Edge 配置マニフェストをアップロードする\)** ページで、デバイス テンプレート名として「*Environmental Sensor Edge Device*」を入力します。 次に、 **[参照]** を選択して、先ほどダウンロードした **EnvironmentalSensorManifest.json** をアップロードします。 次に、**次のステップ: 確認\)** をクリックします。

1. **[Review]\(レビュー\)** ページで、 **[Create]\(作成\)** を選択します。

1. **SimulatedTemperatureSensor** モジュールで **[管理]** インターフェイスを選択して、そのマニフェストで定義されている 2 つのプロパティを表示します。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="IoT Edge マニフェストから作成されたデバイス テンプレート":::

> [!TIP]
> このデプロイ マニフェストは、接続のために資格情報を必要としない Azure Container Registry リポジトリからモジュール イメージをプルします。 プライベート リポジトリのモジュール イメージを使用する場合は、マニフェストでコンテナー レジストリの資格情報を設定します。

### <a name="add-telemetry-to-manifest"></a>マニフェストにテレメトリを追加する

IoT Edge マニフェストでは、モジュールが送信するテレメトリが定義されていません。 IoT Central で、デバイス テンプレートにテレメトリの定義を追加します。 **SimulatedTemperatureSensor** モジュールは、次の JSON のようなテレメトリ メッセージを送信します。

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

デバイス テンプレートにテレメトリの定義を追加するには:

1. **Environmental Sensor Edge Device** テンプレートで **[管理]** インターフェイスを選択します。

1. **[+ 機能の追加]** を選択します。 **[表示名]** として「*machine*」と入力し、 **[機能の種類]** が **[テレメトリ]** になっていることを確認します。

1. スキーマの種類として **[オブジェクト]** を選択し、 **[定義]** を選択します。 オブジェクトの定義ページで、**Double** 型の属性として *temperature* と *pressure* を追加し、 **[適用]** を選択します。

1. **[+ 機能の追加]** を選択します。 **[表示名]** として「*ambient*」と入力し、 **[機能の種類]** が **[テレメトリ]** になっていることを確認します。

1. スキーマの種類として **[オブジェクト]** を選択し、 **[定義]** を選択します。 オブジェクトの定義ページで、**Double** 型の属性として *temperature* と *humidity* を追加し、 **[適用]** を選択します。

1. **[+ 機能の追加]** を選択します。 **[表示名]** として「*timeCreated*」と入力し、 **[機能の種類]** が **[テレメトリ]** になっていることを確認します。

1. スキーマの種類として **[DateTime]\(日時\)** を選択します。

1. **[保存]** を選択してテンプレートを更新します。

これで、 **[管理]** インターフェイスにテレメトリの種類として **machine**、**ambient**、および **timeCreated** が表示されるようになりました。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="テレメトリの種類 machine と ambient が表示されているインターフェイス":::

### <a name="add-views-to-template"></a>ビューをテンプレートに追加する

デバイス テンプレートには、オペレーターが IoT Edge デバイスのテレメトリを確認できるビューがまだありません。 デバイス テンプレートにビューを追加するには:

1. **[Environmental Sensor Edge Device]** テンプレートで **[ビュー]** を選択します。

1. **[新しいビューを追加するための選択]** ページで、 **[デバイスの視覚化]** タイルを選択します。

1. ビュー名を「*View IoT Edge device telemetry*」に変更します。

1. テレメトリの種類として **[ambient]** と **[machine]** を選択します。 次に、 **[タイルの追加]** を選択します。

1. **[保存]** を選択して、**View IoT Edge device telemetry** ビューを保存します。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="テレメトリ ビューが表示されたデバイス テンプレート":::

### <a name="publish-the-template"></a>テンプレートを発行する

**Environmental Sensor Edge Device** テンプレートを使用するデバイスを追加できるように、テンプレートを発行しておく必要があります。

**[Environmental Sensor Edge Device]** テンプレートに移動し、 **[発行]** を選択します。 **[このデバイス テンプレートのアプリケーションへの発行]** パネルで **[発行]** を選択し、テンプレートを発行します。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="デバイス テンプレートを発行する":::

## <a name="add-iot-edge-device"></a>IoT Edge デバイスを追加する

**Environmental Sensor Edge Device** テンプレートを発行したので、お使いの IoT Central アプリケーションにデバイスを追加できます。

1. お使いの IoT Central アプリケーションの **[デバイス]** ページに移動し、使用可能なテンプレートの一覧で **[Environmental Sensor Edge Device]** を選択します。

1. テンプレートから新しいデバイスを追加するには、 **[+ 新規]** を選択します。 **[新しいデバイスの作成]** ページで **[作成]** を選択します。

これで、状態が **[登録済み]** の新しいデバイスが作成されました。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="新しい登録済みのデバイス":::

### <a name="get-the-device-credentials"></a>デバイスの資格情報を取得する

このチュートリアルで後ほど IoT Edge デバイスをデプロイする際、お使いの IoT Central アプリケーションへの接続をデバイスに許可する資格情報が必要になります。 デバイスの資格情報を取得するには、次の手順を実行します。

1. **[デバイス]** ページで、作成したデバイスを選択します。

1. **[接続]** を選択します。

1. **[デバイス接続]** ページの **[ID スコープ]** 、 **[デバイス ID]** 、 **[主キー]** を書き留めておきます。 これらの値は後で使用します。

1. **[閉じる]** を選択します。

これで、IoT Edge デバイスが接続できるようにするための IoT Central アプリケーションの構成が完了しました。

## <a name="deploy-an-iot-edge-device"></a>IoT Edge デバイスをデプロイする

このチュートリアルでは、Azure に作成された Azure IoT Edge 対応 Linux VM を使用して、IoT Edge デバイスをシミュレートします。 Azure サブスクリプションに IoT Edge 対応 VM を作成するには、次のボタンをクリックします。

[![iotedge-vm-deploy の [Deploy to Azure] ボタン](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

**[カスタム デプロイ]** ページで次の手順を実行します。

1. Azure サブスクリプションを選択します。

1. **[新規作成]** を選択して、*central-edge-rg* という新しいリソース グループを作成します。

1. 近くのリージョンを選択します。

1. 一意の **DNS ラベル プレフィックス** を追加します (例: *contoso-central-edge*)。

1. 仮想マシンの管理者ユーザー名を選択します。

1. 接続文字列として「*temp*」を入力します。 その後、DPS を使用して接続するようにデバイスを構成します。

1. VM サイズ、Ubuntu バージョン、場所には、既定値をそのまま使用します。

1. 認証の種類として **[パスワード]** を選択します。

1. VM のパスワードを入力します。

1. 次に、 **[Review + Create]\(確認と作成\)** を選択します。

1. 選択内容を確認し、 **[作成]** を選択します。

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="IoT Edge VM を作成する":::

デプロイが完了するまでに数分かかります。 デプロイが完了したら、Azure portal で **central-edge-rg** リソース グループに移動します。

### <a name="configure-the-iot-edge-vm"></a>IoT Edge VM を構成する

DPS を使用して IoT Central アプリケーションに登録、接続するように VM の IoT Edge を構成するには、次の手順に従います。

1. **contoso-edge-rg** リソース グループで、仮想マシン インスタンスを選択します。

1. **[サポート + トラブルシューティング]** セクションで、 **[シリアル コンソール]** を選択します。 ブート診断を構成するように求められた場合は、ポータルの指示に従ってください。

1. **Enter** キーを押して、`login:` プロンプトを表示します。 ユーザー名とパスワードを入力してサインインします。

1. 次のコマンドを実行して、IoT Edge ランタイムのバージョンを確認します。 この記事の執筆時点では、バージョンは 1.0.9.1 です。

    ```bash
    sudo iotedge --version
    ```

1. `nano` エディターを使用して、IoT Edge config.yaml ファイルを開きます。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `# Manual provisioning configuration` が表示されるまで下にスクロールします。 次のスニペットに表示されているように、その次の 3 行をコメント アウトします。

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. `# DPS symmetric key provisioning configuration` が表示されるまで下にスクロールします。 次のスニペットに表示されているように、その次の 8 行をコメント解除します。

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > `provisioning:` の前にスペースがないことを確認してください。

1. `{scope_id}` を、前に書き留めておいた **ID スコープ** に置き換えます。

1. `{registration_id}` を、前に書き留めておいた **デバイス ID** に置き換えます。

1. `{symmetric_key}` を、前に書き留めておいた **主キー** に置き換えます。

1. 変更内容を保存し (**Ctrl + O** キー)、`nano` エディターを終了します (**Ctrl + X** キー)。

1. 次のコマンドを実行して、IoT Edge デーモンを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge モジュールの状態を確認するには、次のコマンドを実行します。

    ```bash
    iotedge list
    ```

    次のサンプル出力は、実行中のモジュールを示しています。

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > すべてのモジュールの実行が開始されるまでお待ちください。

## <a name="view-the-telemetry"></a>テレメトリを表示する

シミュレートされた IoT Edge デバイスが VM で実行されるようになりました。 お使いの IoT Central アプリケーションの **[デバイス]** ページでは、デバイスの状態が **[プロビジョニング済み]** になっています。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="プロビジョニングされた IoT Edge デバイス":::

デバイスからのテレメトリは、 **[View IoT Edge device telemetry]** ページで確認できます。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="デバイス テレメトリ":::

**[モジュール]** ページには、デバイス上の IoT Edge モジュールの状態が表示されます。

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="デバイスのモジュールの状態":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続き IoT Edge VM を使用する場合は、このチュートリアルで使用したリソースをそのまま再利用することができます。 それ以外の場合、追加料金が発生するのを防ぐために、このチュートリアルで作成したリソースは削除してください。

* IoT Edge VM とその関連リソースを削除するには、Azure portal から **contoso-edge-rg** リソース グループを削除します。
* IoT Central アプリケーションを削除するには、アプリケーションの **[管理]** セクションの **[お客様のアプリケーション]** ページに移動して、 **[削除]** を選択します。

ソリューション開発者として、またはオペレーターとして、IoT Central で IoT Edge デバイスを操作および管理する方法について学習しました。推奨される次の手順として、以下のページにお進みください。

> [!div class="nextstepaction"]
> [デバイス グループを使用してデバイス テレメトリを分析する](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>次のステップ

デバイス開発者として IoT Central で IoT Edge デバイスを操作および管理する方法について学習しました。推奨される次の手順として、以下をお読みください。

> [!div class="nextstepaction"]
> [IoT Edge モジュールを開発する](../../iot-edge/tutorial-develop-for-linux.md)