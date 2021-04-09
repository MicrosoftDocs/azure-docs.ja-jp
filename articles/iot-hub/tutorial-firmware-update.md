---
title: チュートリアル - Azure IoT Hub を使用してデバイス ファームウェアを更新する | Microsoft Docs
description: チュートリアル - IoT ハブに接続されているバックエンド アプリケーションからトリガーできるデバイス ファームウェア更新プロセスを実装する方法について説明します。
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 3fc257ff192ccb1bb05b233c6ac802696ece0054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575723"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>チュートリアル:デバイス ファームウェアの更新プロセスを実装する

場合によっては、IoT ハブに接続されているデバイスのファームウェアを更新する必要があります。 たとえば、ファームウェアに新しい機能を追加したり、セキュリティ パッチを適用したりできます。 多くの IoT シナリオでは、対象のデバイスに物理的にアクセスして、手動でファームウェア更新を適用することは現実的ではありません。 このチュートリアルでは、ハブに接続されたバックエンド アプリケーションを介してファームウェアの更新プロセスをリモートで開始および監視する方法を示します。

このチュートリアルのバックエンド アプリケーションでは、ファームウェアの更新プロセスを作成および監視するために、IoT ハブに "_構成_" を作成します。 IoT Hub の [自動デバイス管理](./iot-hub-automatic-device-management.md)では、この構成を使用して、すべての冷却装置上の "_デバイス ツインの必要なプロパティ_" のセットを更新します。 必要なプロパティは、必要なファームウェアの更新の詳細を指定します。 冷却装置でファームウェアの更新プロセスが実行されている間、"_デバイス ツインの報告されたプロパティ_" を使用して、バックエンド アプリケーションに状態が報告されます。 バックエンド アプリケーションは、この構成を使用して、デバイスから送信された報告されたプロパティを監視し、ファームウェアの更新プロセスを完了まで追跡できます。

![ファームウェアの更新プロセス](media/tutorial-firmware-update/Process.png)

このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> * IoT ハブを作成し、テスト デバイスをデバイス ID レジストリに追加する。
> * ファームウェアの更新を定義する構成を作成する。
> * デバイスでのファームウェアの更新プロセスをシミュレートする。
> * ファームウェアの更新の進行状況に応じてデバイスから状態の更新を受信する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

このクイック スタートで実行する 2 つのサンプル アプリケーションは、Node.js を使って書かれています。 開発用コンピューター上に Node.js v10.x.x 以降が必要です。

複数のプラットフォームに対応する Node.js を [nodejs.org](https://nodejs.org) からダウンロードできます。

開発コンピューターに現在インストールされている Node.js のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
node --version
```

[https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip ) からサンプル Node.js プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

ポート 8883 がファイアウォールで開放されていることを確認してください。 このチュートリアルのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="set-up-azure-resources"></a>Azure リソースの設定

このチュートリアルを完了するには、Azure サブスクリプションに、デバイスがデバイス ID レジストリに追加されている IoT ハブが含まれている必要があります。 デバイス ID レジストリにエントリがあることで、このチュートリアルで実行するシミュレートされたデバイスがハブに接続できます。

サブスクリプションで IoT ハブをまだ設定していない場合は、次の CLI スクリプトを使用して IoT ハブを設定できます。 このスクリプトでは、IoT ハブに **tutorial-iot-hub** という名前を使用していますが、実行時にはこの名前を一意の名前に置き換える必要があります。 このスクリプトで、**米国中部** リージョンにリソース グループとハブが作成されますが、より近いリージョンに変更することもできます。 このスクリプトでは、IoT ハブ サービス接続文字列が取得されます。この文字列をバックエンド サンプル アプリケーションで使用して IoT ハブに接続します。

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create a free-tier IoT Hub. You can have only one free IoT Hub per subscription. Free tier hubs can have only 2 partitions.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later
az iot hub connection-string show --name $hubname --policy-name service -o table

```

このチュートリアルでは、**MyFirmwareUpdateDevice** というシミュレートされたデバイスを使用します。 次のスクリプトを実行すると、このデバイスがデバイス ID レジストリに追加され、タグ値が設定され、接続文字列が取得されます。

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"device type":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity connection-string show --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> これらのコマンドを Windows コマンド プロンプトまたは PowerShell プロンプトで実行する場合は、JSON 文字列を引用符で囲む方法について、[azure-iot-cli-extension のヒント](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips)に関するページを参照してください。

## <a name="start-the-firmware-update"></a>ファームウェアの更新を開始する

**デバイスの種類** として "chiller" のタグが付けられたすべてのデバイスでファームウェア更新プロセスを開始するには、バックエンド アプリケーションで [自動デバイス管理構成](iot-hub-automatic-device-management.md#create-a-configuration)を作成します。 このセクションでは、次の方法について説明します。

* バックエンド アプリケーションから構成を作成する。
* 完了するまでジョブを監視する。

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>必要なプロパティを使用して、バックエンド アプリケーションからファームウェアのアップグレードを開始する

構成を作成するバックエンド アプリケーション コードを表示するには、ダウンロードしたサンプル Node.js プロジェクトの **iot-hub/Tutorials/FirmwareUpdate** フォルダーに移動します。 次に、テキスト エディターで ServiceClient.js ファイルを開きます。

バックエンド アプリケーションでは、次の構成を作成します。

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

構成には、次のセクションが含まれます。

* `content`: 選択したデバイスに送信されるファームウェアの必要なプロパティを指定します。
* `metrics`: ファームウェア更新の状態を報告するために実行するクエリを指定します。
* `targetCondition`: ファームウェアの更新プログラムを受信するデバイスを選択します。
* `priorty`: 他の構成に対するこの構成の相対的な優先度を設定します。

バックエンド アプリケーションは、次のコードを使用して、必要なプロパティを設定するための構成を作成します。

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

構成を作成した後、アプリケーションは、ファームウェアの更新を監視します。

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

構成では、次の 2 種類のメトリックが報告されます。

* ターゲットとするデバイスの数と更新プログラムが適用されたデバイスの数を報告するシステム メトリック。
* 構成に追加したクエリによって生成されたカスタム メトリック。 このチュートリアルのクエリでは、更新プロセスの各段階にあるデバイスの数を報告します。

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>デバイス上のファームウェア アップグレード要求に応答する

バックエンド アプリケーションから送信されたファームウェアの必要なプロパティを処理するシミュレートされたデバイス コードを表示するには、ダウンロードしたサンプル Node.js プロジェクトの **iot-hub/Tutorials/FirmwareUpdate** フォルダーに移動します。 次にテキスト エディターで SimulatedDevice.js ファイルを開きます。

シミュレートされたデバイス アプリケーションは、デバイス ツインの **properties.desired.firmware** の必要なプロパティに対する更新のためのハンドラーを作成します。 ハンドラーでは、更新プロセスを開始する前に、必要なプロパティに対していくつかの基本的なチェックを実行します。

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>ファームウェアを更新する

更新を実行するのは **initiateFirmwareUpdateFlow** 関数です。 この関数は、**waterfall** 関数を使用して、更新プロセスの各フェーズを順番に実行します。 この例では、ファームウェアの更新には 4 つのフェーズがあります。 最初のフェーズでイメージをダウンロードし、2 番目のフェーズでチェックサムを使用してイメージを検証します。3 番目のフェーズでイメージを適用し、最後のフェーズでデバイスを再起動します。

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

更新プロセス中、シミュレートされたデバイスは、報告されたプロパティを使用して進捗状況を報告します。

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

次のスニペットは、ダウンロード フェーズの実装を示しています。 ダウンロード フェーズでは、シミュレートされたデバイスは、報告されたプロパティを使用して、状態情報をバックエンド アプリケーションに送信します。

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>サンプルを実行する

このセクションでは、2 つのサンプル アプリケーションを実行して、バックエンド アプリケーションがシミュレートされたデバイス上のファームウェア更新プロセスを管理するための構成を作成する操作を監視します。

シミュレートされたデバイスとバックエンド アプリケーションを実行するには、デバイスとサービス接続文字列が必要です。 このチュートリアルの冒頭でリソースの作成時に接続文字列をメモしました。

シミュレートされたデバイス アプリケーションを実行するには、シェルまたはコマンド プロンプト ウィンドウを開き、ダウンロードした Node.js プロジェクトの **iot-hub/Tutorials/FirmwareUpdate** フォルダーに移動します。 次のコマンドを実行します。

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

バックエンド アプリケーションを実行するには、別のシェルまたはコマンド プロンプト ウィンドウを開きます。 次に、ダウンロードした Node.js プロジェクトの **iot-hub/Tutorials/FirmwareUpdate** フォルダーに移動します。 次のコマンドを実行します。

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

次のスクリーンショットは、シミュレートされたデバイス アプリケーションからの出力を示しています。バックエンド アプリケーションからのファームウェアの必要なプロパティの更新にどのように応答するかが示されています。

![シミュレートされたデバイス](./media/tutorial-firmware-update/SimulatedDevice.png)

次のスクリーンショットは、バックエンド アプリケーションからの出力を示しています。ファームウェアの必要なプロパティを更新するための構成を作成する方法が強調表示されています。

![バックエンド アプリケーションからの出力を示すスクリーンショット。](./media/tutorial-firmware-update/BackEnd1.png)

次のスクリーンショットは、バックエンド アプリケーションからの出力を示しています。シミュレートされたデバイスからのファームウェア更新メトリックを監視する方法が強調表示されています。

![バックエンド アプリケーション](./media/tutorial-firmware-update/BackEnd2.png)

デバイスの自動構成は、作成時に実行された後は 5 分ごとに実行されるため、バックエンド アプリケーションに送信されるすべての状態更新を確認できるわけではありません。 ポータルの IoT ハブの **[Automatic device management]\(自動デバイス管理\) -> [IoT device configuration]\(IoT デバイス構成\)** セクションでメトリックを表示することもできます。

![ポータルで構成を表示する](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルを実行する場合は、リソース グループと IoT ハブをそのままにしておき、後で再利用します。

IoT ハブが必要でなくなった場合は、ポータルを使用して IoT ハブとリソース グループを削除します。 これを行うには、IoT ハブを含む **tutorial-iot-hub-rg** リソース グループを選択し、 **[削除]** をクリックします。

または、次の CLI を使用します。

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、接続されたデバイスのファームウェア更新プロセスを実装する方法を学習しました。 Azure IoT Hub ポータル ツールと Azure CLI コマンドを使用してデバイス接続をテストする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを使用して IoT ハブとの接続をテストする](tutorial-connectivity.md)
