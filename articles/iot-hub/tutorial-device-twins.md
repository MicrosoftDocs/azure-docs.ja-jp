---
title: チュートリアル - Azure IoT Hub のデバイス状態を同期する | Microsoft Docs
description: チュートリアル - デバイス ツインを使用してクラウドからデバイスを構成し、デバイスから状態およびコンプライアンス データを受信する方法について説明します。
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 7dbc0404679927bcef1647dfdf46ce3360216a79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733302"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>チュートリアル:バックエンド サービスからデバイスを構成する

場合によっては、デバイスからテレメトリを受信するだけでなく、バックエンド サービスからデバイスを構成する必要があります。 デバイスに必要な構成を送信するときに、それらのデバイスから状態とコンプライアンスの更新を受信したい場合もあります。 たとえば、デバイスの目標動作温度範囲を設定する場合や、デバイスからファームウェアのバージョン情報を収集する場合などです。

デバイスと IoT ハブ間で状態情報を同期するには、_デバイス ツイン_ を使用します。 [デバイス ツイン](iot-hub-devguide-device-twins.md)は、特定のデバイスに関連付けられた JSON ドキュメントであり、IoT Hub によってクラウドに格納されます。また、デバイス ツインに対して[クエリ](iot-hub-devguide-query-language.md)を実行することができます。 デバイス ツインには、_必要なプロパティ_、_報告されたプロパティ_、および _タグ_ が含まれています。 必要なプロパティは、バックエンド アプリケーションによって設定され、デバイスから読み取られます。 報告されたプロパティは、デバイスによって設定され、バックエンド アプリケーションによって読み取られます。 タグはバックエンド アプリケーションによって設定され、デバイスに送信されることはありません。 タグはデバイスの整理に使用します。 このチュートリアルでは、必要なプロパティと報告されたプロパティを使用して状態情報を同期する方法について説明します。

![ツインの概要](media/tutorial-device-twins/DeviceTwins.png)

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * IoT ハブを作成し、テスト デバイスを ID レジストリに追加する。
> * 必要なプロパティを使用して、シミュレートされたデバイスに状態情報を送信する。
> * 報告されたプロパティを使用して、シミュレートされたデバイスから状態情報を受信する。

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

サブスクリプションで IoT ハブをまだ設定していない場合は、次の CLI スクリプトを使用して IoT ハブを設定できます。 このスクリプトでは、IoT ハブに **tutorial-iot-hub** という名前を使用していますが、実行時にはこの名前を一意の名前に置き換える必要があります。 このスクリプトで、**米国中部** リージョンにリソース グループとハブが作成されますが、より近いリージョンに変更することもできます。 このスクリプトで、IoT ハブ サービス接続文字列が取得されます。この文字列をバックエンド サンプルで使用して IoT ハブに接続します。

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-iot

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later:
az iot hub connection-string show --name $hubname --policy-name service -o table

```

このチュートリアルでは、**MyTwinDevice** というシミュレートされたデバイスを使用します。 次のスクリプトで、このデバイスは ID レジストリに追加され、接続文字列が取得されます。

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity connection-string show --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>状態情報を送信する

必要なプロパティを使用して、バックエンド アプリケーションからデバイスに状態情報を送信します。 このセクションでは、次の方法について説明します。

* デバイスで必要なプロパティを受信して処理する。
* バックエンド アプリケーションから必要なプロパティを送信する。

必要なプロパティを受信するシミュレートされたデバイス サンプル コードを表示するには、ダウンロードしたサンプル Node.js プロジェクトの **iot-hub/Tutorials/DeviceTwins** フォルダーに移動します。 次にテキスト エディターで SimulatedDevice.js ファイルを開きます。

次のセクションでは、バックエンド アプリケーションから送信される必要なプロパティの変更に反応する、シミュレートされたデバイスで実行されるコードについて説明します。

### <a name="retrieve-the-device-twin-object"></a>デバイス ツイン オブジェクトを取得する

次のコードでは、デバイス接続文字列を使用して IoT ハブに接続します。

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

次のコードでは、クライアント オブジェクトからツインを取得します。

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>必要なプロパティのサンプル

アプリケーションに適した方法で必要なプロパティを構成することができます。 この例では、**fanOn** という 1 つの最上位プロパティを使用し、その他のプロパティを個別の **コンポーネント** にグループ化します。 次の JSON スニペットは、このチュートリアルで使用する必要なプロパティの構造を示しています。 JSON は、desired.json ファイルにあります。

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>ハンドラーを作成する

JSON 階層内のさまざまなレベルの更新に反応して必要なプロパティを更新するハンドラーを作成できます。 たとえば、このハンドラーは、バックエンド アプリケーションからデバイスに送信されるすべてのプロパティの変更を把握しています。 **delta** 変数には、ソリューションのバックエンドから送信された必要なプロパティが含まれています。

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

次のハンドラーは、必要なプロパティ **fanOn** に加えられた変更にのみ反応します。

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>複数のプロパティのハンドラー

前に示した必要なプロパティの JSON の例では、**components** の **climate** ノードに **minTemperature** と **maxTemperature** という 2 つのプロパティが含まれています。

デバイスのローカルの **twin** オブジェクトには、必要なプロパティと報告されたプロパティの完全なセットが格納されています。 バックエンドから送信された **delta** で、必要なプロパティのサブセットのみが更新される可能性があります。 次のコード スニペットでは、シミュレートされたデバイスが **minTemperature** と **maxTemperature** のいずれかの更新を受信した場合に、ローカルの twin の値をもう一方の値に使用し、デバイスを構成します。

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

ローカルの **twin** オブジェクトには、必要なプロパティと報告されたプロパティの完全なセットが格納されています。 バックエンドから送信された **delta** で、必要なプロパティのサブセットのみが更新される可能性があります。

### <a name="handle-insert-update-and-delete-operations"></a>挿入、更新、削除操作を処理する

バックエンドから送信される必要なプロパティは、特定の必要なプロパティに対して実行されている操作を示していません。 コードで、ローカルに格納されている現在の必要なプロパティとハブから送信された変更のセットから、操作を推測する必要があります。

次のスニペットは、シミュレートされたデバイスが、必要なプロパティの **components** のリストに対して挿入、更新、および削除操作を処理する方法を示しています。 コンポーネントを削除する必要があることを示すために、**null** 値を使用する方法も確認できます。

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>バックエンドからデバイスに必要なプロパティを送信する

これまでは、必要なプロパティの更新を受信するためにデバイスがハンドラーを実装する方法を見てきました。 このセクションでは、バックエンド アプリケーションからデバイスに必要なプロパティの変更を送信する方法について説明します。

必要なプロパティを受信するシミュレートされたデバイス サンプル コードを表示するには、ダウンロードしたサンプル Node.js プロジェクトの **iot-hub/Tutorials/DeviceTwins** フォルダーに移動します。 次に、テキスト エディターで ServiceClient.js ファイルを開きます。

次のコード スニペットは、デバイス ID レジストリに接続し、特定のデバイスのツインにアクセスする方法を示しています。

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

次のスニペットは、バックエンド アプリケーションがデバイスに送信するさまざまな必要なプロパティの *パッチ* を示しています。

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

次のスニペットは、バックエンド アプリケーションが必要なプロパティの更新をデバイスに送信する方法を示しています。

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>アプリケーションの実行

このセクションでは、2 つのサンプル アプリケーションを実行して、バックエンド アプリケーションが必要なプロパティの更新をシミュレートされたデバイス アプリケーションに送信するときを監視します。

シミュレートされたデバイスとバックエンド アプリケーションを実行するには、デバイスとサービス接続文字列が必要です。 このチュートリアルの冒頭でリソースの作成時に接続文字列をメモしました。

シミュレートされたデバイス アプリケーションを実行するには、シェルまたはコマンド プロンプト ウィンドウを開き、ダウンロードした Node.js プロジェクトの **iot-hub/Tutorials/DeviceTwins** フォルダーに移動します。 次のコマンドを実行します。

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

バックエンド アプリケーションを実行するには、別のシェルまたはコマンド プロンプト ウィンドウを開きます。 次に、ダウンロードした Node.js プロジェクトの **iot-hub/Tutorials/DeviceTwins** フォルダーに移動します。 次のコマンドを実行します。

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

次のスクリーンショットは、シミュレートされたデバイス アプリケーションからの出力を示しています。必要なプロパティ **maxTemperature** の更新を処理する方法が強調表示されています。 最上位のハンドラーと climate コンポーネント ハンドラーの両方がどのように動作するかがわかります。

![最上位のハンドラーと climate コンポーネント ハンドラーの両方がどのように動作するかを示すスクリーンショット。](./media/tutorial-device-twins/SimulatedDevice1.png)

次のスクリーンショットは、バックエンド アプリケーションからの出力を示しています。必要なプロパティ **maxTemperature** に更新が送信される方法が強調表示されています。

![バックエンド アプリケーションからの出力を示し、更新が送信される方法が強調表示されているスクリーンショット。](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>状態情報を受信する

バックエンド アプリケーションは、デバイスからの状態情報を報告されたプロパティとして受信します。 デバイスは報告されたプロパティを設定し、ハブに送信します。 バックエンド アプリケーションは、ハブに格納されているデバイス ツインから、報告されたプロパティの現在の値を読み取ることができます。

### <a name="send-reported-properties-from-a-device"></a>デバイスから報告されたプロパティを送信する

報告されたプロパティ値の更新をパッチとして送信できます。 次のスニペットは、シミュレートされたデバイスが送信するパッチのテンプレートを示しています。 シミュレートされたデバイスは、パッチのフィールドを更新してからハブに送信します。

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

シミュレートされたデバイスは、次の関数を使用して、報告されたプロパティを含むパッチをハブに送信します。

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>報告されたプロパティを処理する

バックエンド アプリケーションは、デバイス ツインを介して、デバイスの現在の報告されたプロパティ値にアクセスします。 次のスニペットは、バックエンド アプリケーションがシミュレーションされたデバイスの報告されたプロパティ値を読み取る方法を示しています。

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>アプリケーションの実行

このセクションでは、2 つのサンプル アプリケーションを実行して、シミュレートされたデバイスが報告されたプロパティの更新をバックエンド アプリケーションに送信するときを監視します。

実行した 2 つの同じサンプル アプリケーションを実行して、必要なプロパティがデバイスにどのように送信されるかを確認します。

シミュレートされたデバイスとバックエンド アプリケーションを実行するには、デバイスとサービス接続文字列が必要です。 このチュートリアルの冒頭でリソースの作成時に接続文字列をメモしました。

シミュレートされたデバイス アプリケーションを実行するには、シェルまたはコマンド プロンプト ウィンドウを開き、ダウンロードした Node.js プロジェクトの **iot-hub/Tutorials/DeviceTwins** フォルダーに移動します。 次のコマンドを実行します。

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

バックエンド アプリケーションを実行するには、別のシェルまたはコマンド プロンプト ウィンドウを開きます。 次に、ダウンロードした Node.js プロジェクトの **iot-hub/Tutorials/DeviceTwins** フォルダーに移動します。 次のコマンドを実行します。

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

次のスクリーンショットは、シミュレートされたデバイス アプリケーションからの出力を示しています。報告されたプロパティの更新をハブに送信する方法が強調表示されています。

![シミュレートされたデバイス](./media/tutorial-device-twins/SimulatedDevice2.png)

次のスクリーンショットは、バックエンド アプリケーションからの出力を示します。デバイスからレポートされたプロパティの更新を受信し、処理する方法が強調表示されています。

![バックエンド アプリケーション](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルを実行する場合は、リソース グループと IoT ハブをそのままにしておき、後で再利用します。

IoT ハブが必要でなくなった場合は、ポータルを使用して IoT ハブとリソース グループを削除します。 これを行うには、IoT ハブを含む **tutorial-iot-hub-rg** リソース グループを選択し、 **[削除]** をクリックします。

または、次の CLI を使用します。

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、デバイスと IoT ハブ間の状態情報を同期する方法について説明しました。 デバイス ツインを使用してファームウェアの更新プロセスを実装する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [デバイス ファームウェアの更新プロセスを実装する](tutorial-firmware-update.md)
