---
title: Windows 上に Azure IoT Edge デバイスを作成するクイック スタート | Microsoft Docs
description: このクイック スタートでは、IoT Edge デバイスを作成した後、Azure portal から事前作成されたコードをリモートで展開する方法を学習します。
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 71e38059aceb7da63f3545610b9acfe48c5d3150
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663215"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>クイックスタート: 初めての IoT Edge モジュールを Windows デバイスにデプロイする (プレビュー)

このクイックスタートで、コンテナー化されたコードを Linux on Windows IoT Edge デバイスにデプロイして、Azure IoT Edge を試してみましょう。 IoT Edge を使用すると、ご利用のデバイス上のコードをリモートで管理できるため、より多くのワークロードをエッジに送信できます。 Azure IoT Edge for Linux on Windows を使うことがいかに簡単かを知っていただくために、このクイックスタートにはご自身のデバイスを使用することをお勧めします。

このクイック スタートでは、次の方法について説明します。

* IoT Hub を作成します。
* IoT Edge デバイスを IoT ハブに登録します。
* IoT Edge for Linux on Windows ランタイムをデバイスにインストールして起動します。
* IoT Edge デバイスにリモートでモジュールをデプロイし、テレメトリを送信します。

![図 - デバイスとクラウドのクイック スタートのアーキテクチャ](./media/quickstart/install-edge-full.png)

このクイックスタートでは、Azure IoT Edge for Linux on Windows デバイスの設定方法について説明します。 その後、Azure portal からご自身のデバイスにモジュールを配置します。 このクイックスタートで使用されているモジュールは、温度、湿度、圧力のデータを生成するシミュレートされたセンサーです。 他の Azure IoT Edge チュートリアルでは、ここで行う作業を基盤としており、ビジネスに関する分析情報を得るためにシミュレートされたデータを分析する追加のモジュールを配置しています。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。

>[!NOTE]
>IoT Edge for Linux on Windows は、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。

## <a name="prerequisites"></a>前提条件

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

クラウド リソース:

* このクイック スタートで使用するすべてのリソースを管理するためのリソース グループです。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge デバイス:

* お使いのデバイスが Windows PC またはサーバーのバージョン 1809 以降である必要があります
* 4 GB 以上のメモリ (8 GB のメモリを推奨)
* 10 GB の空きディスク容量

## <a name="create-an-iot-hub"></a>IoT Hub の作成

このクイック スタートでは、最初に Azure CLI を使用して IoT ハブを作成します。

![図 - クラウドで IoT ハブを作成する](./media/quickstart/create-iot-hub.png)

このクイック スタートでは無料レベルの IoT Hub を使用できます。 IoT Hub を以前に使用したことがあり、ハブを作成済みである場合は、その IoT ハブを使用することができます。

次のコードにより、無料の **F1** ハブがリソース グループ `IoTEdgeResources` に作成されます。 `{hub_name}` は、実際の IoT ハブの一意の名前に置き換えてください。 IoT ハブの作成には数分かかることがあります。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   サブスクリプションに無料のハブが既に 1 つあるためにエラーが発生する場合は、SKU を **S1** に変更します。 IoT ハブの名前が利用できないというエラーが発生した場合、自分以外のだれかが既にその名前のハブを所有していることを意味します。 新しい名前を試してください。

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT ハブに IoT Edge デバイスを登録します。

![図 - IoT ハブ ID でデバイスを登録する](./media/quickstart/register-device.png)

お使いの IoT ハブと通信できるようにシミュレートされたデバイスのデバイス ID を作成します。 デバイス ID はクラウドに置かれるので、デバイスの一意の接続文字列を使用して、物理デバイスとデバイス ID とを関連付けることになります。

IoT Edge デバイスは、一般的な IoT デバイスとは異なる動作をし、別に管理できるため、この ID は IoT Edge デバイス用として `--edge-enabled` フラグで宣言します。

1. Azure Cloud Shell で、次のコマンドを入力して、**myEdgeDevice** という名前のデバイスをハブに作成します。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   iothubowner ポリシー キーに関するエラーが表示された場合は、Cloud Shell で最新バージョンの azure-iot 拡張機能が実行されていることを確認してください。

2. デバイスの接続文字列を確認します。この接続文字列により、IoT Hub 内で物理デバイスとその ID をリンクさせます。 これには、IoT ハブの名前、デバイスの名前、2 つの間の接続を認証する共有キーが含まれています。

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. JSON 出力から `connectionString` キーの値をコピーして保存します。 この値はデバイスの接続文字列です。 次のセクションでは、この接続文字列を使用して IoT Edge ランタイムを構成します。

   ![CLI の出力から接続文字列を取得する](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールして開始する

IoT Edge for Linux on Windows を自分のデバイスにインストールし、デバイスの接続文字列を使用して構成します。

![図 - デバイス上で IoT Edge ランタイムを起動する](./media/quickstart/start-runtime.png)

1. [Windows Admin Center をダウンロード](https://aka.ms/WACDownloadEFLOW)します。

1. インストール ウィザードに従って、Windows Admin Center を自分のデバイスで設定します。

1. Windows Admin Center にアクセスしたら、画面右上にある **設定の歯車アイコン** を選択します。

1. 設定メニューの [ゲートウェイ] から **[拡張機能]** を選択します。

1. **[フィード]** タブを選択し、 **[追加]** を選択します。

1. テキスト ボックスに「 https://aka.ms/wac-insiders-feed 」と入力し、 **[追加]** を選択します。

1. フィードを追加した後、 **[利用できる拡張]** タブに移動します。拡張機能一覧の更新に少し時間がかかる場合があります。

1. **[利用できる拡張]** の一覧から **[Azure IoT Edge]** を選択します。

1. 拡張機能を **インストール** します。

1. 拡張機能がインストールされたら、画面左上隅にある **[Windows Admin Center]** を選択してメイン ダッシュボード ページに移動します。

1. ローカル ホスト接続が表示されます。これは、Windows Admin Center が実行されている PC を表します。

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="スクリーンショット - Windows Admin のスタート ページ":::

1. **[追加]** を選択します。

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="スクリーンショット - Windows Admin のスタート ページの [追加] ボタン":::

1. Azure IoT Edge タイルを探して **[Create new]\(新規作成\)** を選択します。 インストール ウィザードが起動します。

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="スクリーンショット - Azure IoT Edge for Linux on Windows タイル":::

1. インストール ウィザードの指示に従って EULA に同意し、 **[次へ]** を選択します。

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="スクリーンショット - ウィザード: ウェルカム ページ":::

1. 追加の診断データを提供するために、 **[オプションの診断データ]** を選択します。このデータは、Microsoft がサービスの品質を監視、維持するために利用されます。その後、 **[次へ: デプロイ]** をクリックします。

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="スクリーンショット - 診断データ":::

1. **[Select target device]\(ターゲット デバイスの選択\)** 画面で、必要なターゲット デバイスを選択し、最小要件を満たしていることを確認します。 このクイックスタートでは、IoT Edge をローカル デバイスにインストールするため、localhost 接続を選択します。 確認したら、 **[次へ]** を選択して続行します。

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="スクリーンショット - ターゲット デバイスの選択":::

1. **[次へ]** を選択して、既定の設定を受け入れます。

1. デプロイ画面に、パッケージのダウンロード、パッケージのインストール、ホストの構成、Linux VM の最終設定の各プロセスが表示されます。  デプロイが正常に行われると、次のように表示されます。

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="スクリーンショット - ウィザード: デプロイに成功":::

1. **[次へ: 接続]** をクリックして最後の手順に進み、IoT ハブ インスタンスから取得したデバイス ID を使用して、Azure IoT Edge デバイスをプロビジョニングします。

1. Azure IoT Hub からデバイスの接続文字列をコピーし、デバイスの接続文字列フィールドに貼り付けます。 続けて、 **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

    > [!NOTE]
    > 前のセクション「[IoT Edge デバイスを登録する](#register-an-iot-edge-device)」の手順 3. を参照して、接続文字列を取得します。

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="スクリーンショット - ウィザード: プロビジョニング":::

1. プロビジョニングが完了したら、 **[完了]** を選択して終了し、Windows Admin Center のスタート画面に戻ります。 自分のデバイスが IoT Edge デバイスとして表示されているはずです。

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="スクリーンショット - Windows Admin Center の Azure IoT Edge デバイス":::

1. Azure IoT Edge デバイスを選択してそのダッシュボードを表示します。 Azure IoT Hub 内のデバイス ツインからのワークロードがデプロイされていることがわかります。 **[IoT Edge Module List]\(IoT Edge モジュール リスト\)** には、**edgeAgent** というモジュールが 1 つ実行され、 **[IoT Edge Status]\(IoT Edge の状態\)** が **[active (running)]\(アクティブ (実行中)\)** と表示されます。

IoT Edge デバイスの構成はこれで完了です。 クラウドからモジュールをデプロイして実行することができます。

## <a name="deploy-a-module"></a>モジュールを展開する

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールをデプロイします。

![図 - クラウドからデバイスにモジュールを配置する](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure portal を使用して、IoT Edge モジュールをデプロイし、デバイス自体を変更せずにモジュールをデバイスで実行しました。

この場合、プッシュしたモジュールによって、後でテストするために使用できるサンプル環境データが生成されます。 シミュレートされたセンサーは、マシンと、マシンの周囲の環境の両方を監視します。 たとえば、このセンサーは、サーバー ルーム、工場のフロア、または風力タービンに配置されている可能性があります。 メッセージには、周囲の温度と湿度、機械の温度と圧力、タイムスタンプが含まれます。 IoT Edge のチュートリアルでは、このモジュールによって作成されたデータを分析用のテスト データとして使用します。

Windows Admin Center のコマンド シェルに移動し、IoT Edge デバイス上で、クラウドからデプロイされたモジュールが実行されていることを確認します。

1. 新しく作成した IoT Edge デバイスに接続します

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="スクリーンショット - デバイスの接続":::

2. **[概要]** ページに、 **[IoT Edge Module List]\(IoT Edge モジュール リスト\)** と **[IoT Edge Status]\(IoT Edge の状態\)** が表示され、デプロイされているさまざまなモジュールとデバイスの状態をそこで確認できます。  

3. **[ツール]** の **[コマンド シェル]** を選択します。 このコマンド シェルは PowerShell ターミナルとなっていて、Windows PC 上にある Azure IoT Edge デバイスの Linux VM に対し、自動的に ssh (Secure Shell) を使用して接続します。

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="スクリーンショット - コマンド シェル":::

4. 3 つのモジュールがデバイスにあることを確認するために、次の **bash コマンド** を実行します。

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="スクリーンショット - コマンド シェル リスト":::

5. 温度センサー モジュールからクラウドに送信されているメッセージを確認します。

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge のコマンドでは、モジュール名を参照する際に大文字と小文字が区別されます。

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="スクリーンショット - 温度センサー":::

[Visual Studio Code 用の Azure IoT Hub の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、IoT ハブに到着したメッセージを監視することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

IoT Edge のチュートリアルに進む場合は、このクイック スタートで登録および設定したデバイスを使用できます。 それ以外の場合は、課金されないようにするために、作成した Azure リソースを削除してもかまいません。

新しいリソース グループで仮想マシンと IoT ハブを作成した場合、そのグループと関連するすべてのリソースを削除できます。 リソース グループの内容を再確認して、残しておくべきものがないことを確認してください。 グループ全体を削除したくない場合は、リソースを個別に削除してもかまいません。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。

**IoTEdgeResources** グループを削除します。 リソース グループを削除するのに数分かかる場合があります。

```azurecli-interactive
az group delete --name IoTEdgeResources
```

リソース グループが削除されたことは、リソース グループの一覧を表示することによって確認できます。

```azurecli-interactive
az group list
```

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows を削除する

Azure IoT Edge for Linux on Windows は、Windows Admin Center のダッシュボード拡張機能を使用して IoT Edge デバイスからアンインストールすることができます。

1. Windows Admin Center で Azure IoT Edge for Linux on Windows デバイスに接続します。 Azure ダッシュボード ツール拡張機能が読み込まれます。
2. **[アンインストール]** を選択します。 Azure IoT Edge for Linux on Windows が削除されると、Windows Admin Center のスタート ページが表示され、Azure IoT Edge デバイスの接続エントリが一覧から削除されます。

また、IoT Edge デバイスの **[スタート]**  >  **[設定]**  >  **[アプリ]**  >  **[Azure IoT Edge]**  >  **[アンインストール]** に移動することで、Windows システムから Azure IoT Edge を削除することもできます。 この場合、Azure IoT Edge が IoT Edge デバイスから削除されますが、その接続は Windows Admin Center に残ったままとなります。 [設定] メニューから Windows Admin Center をアンインストールすることもできます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスにデプロイしました。 その環境に関する生データを生成するテスト デバイスができあがりました。

次の手順では、ビジネス ロジックを実行する IoT Edge モジュールの作成を開始できるように、ローカル開発環境を設定します。

> [!div class="nextstepaction"]
> [IoT Edge モジュールの開発を始める](tutorial-develop-for-linux.md)
