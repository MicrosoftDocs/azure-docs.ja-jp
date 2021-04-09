---
title: Windows 上に Azure IoT Edge デバイスを作成するクイックスタート | Microsoft Docs
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
ms.openlocfilehash: aa8c4abf57308ca3da05db6ecede7a330af04bdb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463546"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>クイックスタート: 初めての IoT Edge モジュールを Windows デバイスにデプロイする (プレビュー)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

このクイックスタートで、コンテナー化されたコードを Linux on Windows IoT Edge デバイスにデプロイして、Azure IoT Edge を試してみましょう。 IoT Edge を使用すると、ご利用のデバイス上のコードをリモートで管理できるため、より多くのワークロードをエッジに送信できます。 Azure IoT Edge for Linux on Windows を使うことがいかに簡単かを知っていただくために、このクイックスタートにはご自身のデバイスを使用することをお勧めします。

このクイック スタートでは、次の方法について説明します。

* IoT Hub を作成します。
* IoT Edge デバイスを IoT ハブに登録します。
* IoT Edge for Linux on Windows ランタイムをデバイスにインストールして起動します。
* IoT Edge デバイスにリモートでモジュールをデプロイし、テレメトリを送信します。

![このクイックスタートにおけるデバイスとクラウドのアーキテクチャを示す図。](./media/quickstart/install-edge-full.png)

このクイックスタートでは、Azure IoT Edge for Linux on Windows デバイスの設定方法について説明します。 その後、Azure portal からご自身のデバイスにモジュールをデプロイします。 これから使用するモジュールは、温度、湿度、圧力のデータを生成するシミュレートされたセンサーです。 これ以外の Azure IoT Edge チュートリアルは、ここで行う作業を基に作成されており、ビジネスの分析情報を得る目的でシミュレートされたデータを分析するためのモジュールをデプロイします。

アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free)を作成してください。

>[!NOTE]
>IoT Edge for Linux on Windows は、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。

## <a name="prerequisites"></a>前提条件

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

このクイックスタートで使用するすべてのリソースを管理するためのクラウド リソース グループを作成します。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

ご自身の IoT Edge デバイスが次の要件を満たしていることを確認します。

* エディション
  * Windows 10 バージョン 1809 以降、ビルド 17763 以降
    * Professional、Enterprise、IoT Enterprise
  * Windows Server 2019 ビルド 17763 以降

  
* ハードウェア要件
  * 最小空きメモリ容量: 2 GB
  * 最小空きディスク領域:10 GB


>[!NOTE]
>このクイックスタートでは、Windows Admin Center を使用して、IoT Edge for Linux on Windows 用のデプロイを作成します。 PowerShell を使用することもできます。 PowerShell を使用してデプロイを作成する場合は、[Windows デバイスに Azure IoT Edge for Linux をインストールしてプロビジョニングする方法](how-to-install-iot-edge-on-windows.md)に関する攻略ガイドの手順に従ってください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

最初に Azure CLI を使用して IoT ハブを作成します。

![I o T ハブを作成する手順を示す図。](./media/quickstart/create-iot-hub.png)

このクイックスタートでは無料レベルの Azure IoT Hub を使用できます。 IoT Hub を以前に使用したことがあり、ハブを作成済みである場合は、その IoT ハブを使用することができます。

次のコードにより、無料の **F1** ハブがリソース グループ `IoTEdgeResources` に作成されます。 `{hub_name}` は、実際の IoT ハブの一意の名前に置き換えてください。 IoT ハブの作成には数分かかることがあります。

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

ご自分のサブスクリプションに無料のハブが既に 1 つあるためにエラーが発生する場合は、SKU を `S1` に変更します。 IoT ハブの名前が利用できないというエラーが発生した場合、自分以外のだれかが既にその名前のハブを所有しています。 新しい名前を試してください。

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

新しく作成された IoT ハブに IoT Edge デバイスを登録します。

![IoT ハブ ID でデバイスを登録する手順を示す図。](./media/quickstart/register-device.png)

お使いの IoT ハブと通信できるようにシミュレートされたデバイスのデバイス ID を作成します。 デバイス ID はクラウドに置かれるので、デバイスの一意の接続文字列を使用して、物理デバイスとデバイス ID とを関連付けることになります。

IoT Edge デバイスは、通常の IoT デバイスとは異なる方法で動作し、管理方法も異なる場合があります。 `--edge-enabled` フラグを使用して、この ID が IoT Edge デバイス用であることを宣言します。

1. Azure Cloud Shell で、次のコマンドを入力して、**myEdgeDevice** という名前のデバイスをハブに作成します。

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     `iothubowner` ポリシー キーに関するエラーが表示された場合は、Cloud Shell で最新バージョンの Azure IoT 拡張機能が実行されていることを確認してください。

1. デバイスの接続文字列を確認します。この接続文字列により、IoT Hub 内で物理デバイスとその ID をリンクさせます。 これには、IoT ハブの名前、デバイスの名前、そしてそれら 2 つの間の接続を認証する共有キーが含まれています。

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. JSON 出力から `connectionString` キーの値をコピーして保存します。 この値はデバイスの接続文字列です。 次のセクションで、IoT Edge ランタイムを構成するときにこれを使用します。

     ![Cloud Shell 内の connectionString の出力を示すスクリーンショット。](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールして開始する

IoT Edge for Linux on Windows を自分のデバイスにインストールし、デバイスの接続文字列を使用して構成します。

![IoT Edge ランタイムを起動する手順を示す図。](./media/quickstart/start-runtime.png)

1. [Windows Admin Center をダウンロード](https://aka.ms/wacdownload)します。

1. インストール ウィザードのプロンプトに従って、Windows Admin Center を自分のデバイスで設定します。

1. Windows Admin Center を開きます。

1. 右上隅にある **設定の歯車アイコン** を選択し、 **[拡張機能]** を選択します。

1. **[フィード]** タブで **[追加]** を選択します。

1. テキスト ボックスに「`https://aka.ms/wac-insiders-feed`」と入力し、 **[追加]** を選択します。

1. フィードが追加されたら、 **[使用可能な拡張機能]** タブに移動して、拡張機能の一覧が更新されるまで待ちます。

1. **[使用可能な拡張機能]** の一覧から **[Azure IoT Edge]** を選択します。

1. 拡張機能をインストールします。

1. 拡張機能がインストールされたら、左上隅の **[Windows Admin Center]** を選択して、メイン ダッシュボード ページに移動します。

     Windows Admin Center が実行されている PC を表す **localhost** 接続が表示されます。

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Windows Admin のスタート ページのスクリーンショット。":::

1. **[追加]** を選択します。

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Windows Admin Center で [追加] ボタンを選択する場面を示すスクリーンショット。":::

1. [Azure IoT Edge] タイルで、 **[新規作成]** を選択してインストール ウィザードを開始します。

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Azure IoT Edge タイルで新しいデプロイを作成する場面を示すスクリーンショット。":::

1. インストール ウィザードを続行して、Microsoft ソフトウェア ライセンス条項に同意し、 **[次へ]** を選択します。

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="[次へ] を選択して、インストール ウィザードを続行する場面を示すスクリーンショット。":::

1. **[Optional diagnostic data]\(オプションの診断データ\)** を選択し、 **[Next: Deploy]\(次へ: デプロイ\) に進みます。デプロイ**」に進みます。 この選択により、Microsoft がサービスの品質を監視および維持するのに役立つ拡張診断データが提供されるようになります。

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="診断データのオプションを示すスクリーンショット。":::

1. **[Select target device]\(ターゲット デバイスの選択\)** 画面で、必要なターゲット デバイスを選択し、最小要件を満たしていることを確認します。 このクイックスタートでは、IoT Edge をローカル デバイスにインストールするため、**localhost** 接続を選択します。 ターゲット デバイスが要件を満たしている場合は、 **[次へ]** を選択して続行します。

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="ターゲット デバイスの一覧を示すスクリーンショット。":::

1. 既定の設定をそのまま使用するには、 **[次へ]** を選択します。 デプロイ画面に、パッケージのダウンロード、パッケージのインストール、ホストの構成、Linux 仮想マシン (VM) の最終設定の各プロセスが表示されます。 デプロイが成功すると、次のようになります。

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="デプロイが成功した場面を示すスクリーンショット。":::

1. **Next:接続** をクリックして最後の手順に進み、IoT ハブ インスタンスから取得したデバイス ID を使用して、Azure IoT Edge デバイスをプロビジョニングします。

1. [先ほどこのクイックスタートで](#register-an-iot-edge-device)コピーした接続文字列を、 **[Device connection string]\(デバイスの接続文字列\)** フィールドに貼り付けます。 続けて、 **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="[Device connection string]\(デバイスの接続文字列\) フィールドの接続文字列を示すスクリーンショット。":::

1. プロビジョニングが完了したら、 **[完了]** を選択して終了し、Windows Admin Center のスタート画面に戻ります。 自分のデバイスが IoT Edge デバイスとして表示されているはずです。

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Windows Admin Center 内のすべての接続を示すスクリーンショット。":::

1. Azure IoT Edge デバイスを選択してそのダッシュボードを表示します。 Azure IoT Hub 内のデバイス ツインからのワークロードがデプロイされていることがわかります。 **[IoT Edge Module List]\(IoT Edge モジュールの一覧\)** には、**edgeAgent** というモジュールが 1 つ実行されていることが示され、 **[IoT Edge Status]\(IoT Edge の状態\)** が **[active (running)]\(アクティブ (実行中)\)** になります。

IoT Edge デバイスの構成はこれで完了です。 クラウドからモジュールをデプロイして実行することができます。

## <a name="deploy-a-module"></a>モジュールを展開する

Azure IoT Edge デバイスをクラウドから管理し、IoT Hub に利用統計情報を送信するモジュールをデプロイします。

![モジュールをデプロイする手順を示す図。](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-the-generated-data"></a>生成されたデータを表示する

このクイック スタートでは、新しい IoT Edge デバイスを作成し、そこに IoT Edge ランタイムをインストールしました。 その後、Azure portal を使用して、IoT Edge モジュールをデプロイし、デバイス自体に変更を加えずにモジュールをデバイスで実行しました。

プッシュしたモジュールによって、後でテストするために使用できるサンプル環境データが生成されます。 シミュレートされたセンサーは、マシンと、マシンの周囲の環境の両方を監視します。 たとえば、このセンサーは、サーバー ルーム、工場のフロア、または風力タービンに配置されている可能性があります。 送信されるメッセージには、周囲の温度と湿度、機械の温度と圧力、タイムスタンプが含まれます。 IoT Edge のチュートリアルでは、このモジュールによって作成されたデータを分析用のテスト データとして使用します。

Windows Admin Center のコマンド シェルから、IoT Edge デバイス上で、クラウドからデプロイされたモジュールが実行されていることを確認します。

1. 新しく作成した IoT Edge デバイスに接続します。

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Windows Admin Center で [接続] を選択する場面を示すスクリーンショット。":::

     **[概要]** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュールの一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** が表示されます。 デプロイされているモジュールとデバイスの状態を見ることができます。  

1. **[ツール]** の **[コマンド シェル]** を選択します。 このコマンド シェルは PowerShell ターミナルとなっていて、Windows PC 上にある Azure IoT Edge デバイスの Linux VM に、自動的に Secure Shell (SSH) を使用して接続します。

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="コマンド シェルが開かれている画面のスクリーンショット。":::

1. 3 つのモジュールがデバイスにあることを確認するために、次の Bash コマンドを実行します。

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="コマンドシェルの I o T Edge 一覧の出力を示すスクリーンショット。":::

1. 温度センサー モジュールからクラウドに送信されているメッセージを確認します。

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge のコマンドでは、モジュール名を参照する際に大文字と小文字が区別されます。

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="モジュールからクラウドに送信されたメッセージの一覧を示すスクリーンショット。":::

[Visual Studio Code 用の Azure IoT Hub の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、自分の IoT ハブに到着したメッセージを監視することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

IoT Edge チュートリアルを続行する場合、この手順はスキップしてください。 このクイックスタートで登録して設定したデバイスを使用することができます。 それ以外の場合は、課金されないようにするために、作成した Azure リソースを削除してもかまいません。

新しいリソース グループで仮想マシンと IoT ハブを作成した場合、そのグループと関連するすべてのリソースを削除できます。 グループ全体を削除したくない場合は、リソースを個別に削除してもかまいません。

> [!IMPORTANT]
> リソース グループの内容を確認して、残しておくべきものがないことを確認してください。 リソース グループを削除すると、元に戻すことができません。

次のコマンドを使用して **IoTEdgeResources** グループを削除します。 削除には数分かかることがあります。

```azurecli-interactive
az group delete --name IoTEdgeResources
```

リソース グループが削除されたことは、このコマンドを使用してリソース グループの一覧を表示することによって確認できます。

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows を削除する

Windows Admin Center のダッシュボード拡張機能を使用して、Azure IoT Edge for Linux on Windows をアンインストールします。

1. Windows Admin Center の Azure IoT Edge デバイスに接続します。 Azure ダッシュボード ツール拡張機能が読み込まれます。

1. **[アンインストール]** を選択します。 Azure IoT Edge が削除されると、Windows Admin Center の **[開始]** ページから Azure IoT Edge デバイスの接続エントリが削除されます。

>[!Note]
>また、ご自身の IoT Edge デバイスで **[スタート]**  >  **[設定]**  >  **[アプリ]**  >  **[Azure IoT Edge]**  >  **[アンインストール]** の順に選択することで、Windows システムから Azure IoT Edge を削除することもできます。 この方法では、Azure IoT Edge が IoT Edge デバイスから削除されますが、その接続は Windows Admin Center に残ったままとなります。 削除を完了するには、 **[設定]** メニューから Windows Admin Center もアンインストールします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、IoT Edge デバイスを作成し、Azure IoT Edge クラウド インターフェイスを使用してコードをデバイスにデプロイしました。 これで、環境に関する生データを生成するテスト デバイスができあがりました。

次は、ビジネス ロジックを実行する IoT Edge モジュールの作成を開始できるように、ローカル開発環境を設定します。

> [!div class="nextstepaction"]
> [IoT Edge モジュールの開発を始める](tutorial-develop-for-linux.md)
