---
title: 認定の準備が整った Azure IoT プラグ アンド プレイ プレビュー デバイスの構築 | Microsoft Docs
description: デバイス開発者として、認定の準備が整った IoT プラグ アンド プレイ プレビュー デバイスを構築する方法について学習します。
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: bfa611eba8e7a990626fbace8b930962615e0594
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878753"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>認定の準備が整った IoT プラグ アンド プレイ プレビュー デバイスの構築

このチュートリアルでは、デバイス開発者として、認定の準備が整った IoT プラグ アンド プレイ プレビュー デバイスを構築する方法について説明します。

認定テストでは、次のことを確認します。

- IoT プラグ アンド プレイ デバイス コードがデバイスにインストールされていること。
- IoT プラグ アンド プレイ デバイス コードが Azure IoT SDK を使用して構築されていること。
- デバイス コードで [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) がサポートされること。
- デバイス コードでデバイス情報インターフェイスが実装されていること。
- 機能モデルとデバイス コードが IoT Central で動作すること。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- [Visual Studio Code](https://code.visualstudio.com/download)
- [VS Code 用 Azure IoT Workbench 拡張機能](https://github.com/Azure/Azure-IoT-PnP-Preview/blob/master/VSCode/README.md#installation)

また、[デバイス機能モデルを使用してデバイスを作成する方法のクイックスタート](quickstart-create-pnp-device.md)で作成した IoT プラグ アンド プレイ デバイスも必要です。

## <a name="store-a-capability-model-and-interfaces"></a>機能モデルとインターフェイスの格納

IoT プラグ アンド プレイ デバイスの場合、デバイスの機能を定義する機能モデルとインターフェイスを JSON ファイルとして作成する必要があります。

これらの JSON ファイルは、3 つの異なる場所に格納できます。

- パブリック モデル リポジトリ。
- 会社のモデル リポジトリ。
- 自分のデバイス。

現時点で、デバイスを認定するには、これらのファイルを会社のモデル リポジトリまたはパブリック モデル リポジトリのいずれかに格納する必要があります。

## <a name="include-the-required-interfaces"></a>必要なインターフェイスの組み込み

認定プロセスに合格するには、**デバイス情報**インターフェイスを機能モデルに組み込んで実装する必要があります。 このインターフェイスには、次の ID があります。

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> [デバイス機能モデルを使用してデバイスを作成する方法のクイックスタート](quickstart-create-pnp-device.md)を完了している場合は、**デバイス情報**インターフェイスが既に対象のモデルに組み込まれています。

**デバイス情報**インターフェイスを対象のデバイス モデルに組み込むには、機能モデルの `implements` プロパティにインターフェイス ID を追加します。

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

VS Code で**デバイス情報**インターフェイスを表示するには:

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**プラグ アンド プレイ**」と入力し、 **[IoT Plug and Play Open Model Repository]\(IoT プラグ アンド プレイのモデル リポジトリを開く\)** コマンドを選択します。 **[Open Public Model Repository]\(パブリック モデル リポジトリを開く\)** を選択します。 パブリック モデル リポジトリが VS Code で開かれます。

1. パブリック モデル リポジトリで、 **[インターフェイス]** タブを選択します。フィルター アイコンを選択し、フィルター フィールドに「**デバイス情報**」と入力します。

1. **デバイス情報**インターフェイスのローカル コピーを作成するために、フィルター処理された一覧でこれを選択し、 **[ダウンロード]** を選択します。 VS Code に、インターフェイス ファイルが表示されます。

## <a name="update-device-code"></a>デバイス コードの更新

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Azure IoT Device Provisioning Service (DPS) を介したデバイス プロビジョニングを有効にする

デバイスを認定するには、[Azure IoT Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) を介したプロビジョニングを有効にする必要があります。 DPS を使用する機能を追加するには、VS Code で C コード スタブを生成します。 次の手順に従います。

1. DCM ファイルがあるフォルダーを VS Code で開き、**Ctrl + Shift + P** キーを押してコマンド パレットを開きます。「**IoT プラグ アンド プレイ**」と入力し、 **[Generate Device Code Stub]\(デバイス コード スタブを生成する\)** を選択します。

1. デバイス コード スタブの生成に使用する DCM ファイルを選択します。

1. プロジェクト名を入力します。これは、デバイス アプリケーションの名前です。

1. 言語として、 **[ANSI C]** を選択します。

1. 使用するプロジェクトの種類として、 **[CMake プロジェクト]** を選択します。

1. 接続方法として、 **[Via DPS (Device Provisioning Service) symmetric key]\(DPS (デバイス プロビジョニング サービス) の対称キーを使用する\)** を選択します。

1. VS Code で新しいウィンドウが開き、生成されたデバイス コード スタブ ファイルが表示されます。

1. `main.c` を開き、準備した **dpsIdScope**、**sasKey**、および **registrationId** を入力します。 この情報は、認定ポータルから取得できます。 詳細については、[IoT プラグ アンド プレイ デバイスの接続とテスト](tutorial-certification-test.md#connect-and-discover-interfaces)に関するページを参照してください。

    ```c
    // TODO: Specify DPS scope ID if you intend on using DPS / IoT Central.
    static const char *dpsIdScope = "[DPS Id Scope]";
    
    // TODO: Specify symmetric keys if you intend on using DPS / IoT Central and symmetric key based auth.
    static const char *sasKey = "[DPS symmetric key]";
    
    // TODO: specify your device registration ID
    static const char *registrationId = "[device registration Id]";
    ```

1. ファイルを保存します。

### <a name="implement-standard-interfaces"></a>標準インターフェイスを実装する

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>モデル情報と SDK 情報のインターフェイスを実装する

Azure IoT device SDK は、モデル情報と SDK 情報のインターフェイスを実装します。 VS Code のコード生成関数を使用すると、デバイス コードで Azure IoT プラグ アンド プレイ デバイス SDK が使用されます。

Azure IoT device SDK を使用しないことを選択した場合は、SDK のソース コードを独自の実装の参照用に使用できます。

#### <a name="implement-the-device-information-interface"></a>デバイス情報インターフェイスを実装する

デバイスに**デバイス情報**インターフェイスを実装して、実行時にデバイスからデバイス固有の情報を提供します。

[Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) 用の**デバイス情報**インターフェイスの実装例を参照用に使用できます。

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>定義されているすべての機能をモデルに実装する

認定中、プログラムによって対象のデバイスがテストされ、インターフェイスに定義されている機能が実装されていることが確認されます。 対象のデバイスにこれらが実装されていない場合は、HTTP 状態コード 501 を使用して、読み取りおよび書き込みプロパティとコマンド要求に応答します。

## <a name="next-steps"></a>次の手順

これで、認定の準備が整った IoT プラグ アンド プレイ デバイスを構築できました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [デバイスの認定方法を学習する](tutorial-certification-test.md)
