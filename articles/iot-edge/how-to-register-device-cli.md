---
title: コマンドラインからから新しいデバイスを登録 - Azure IoT Edge | Microsoft Docs
description: Azure CLI の IoT 拡張機能を使用して新しい IoT Edge デバイスを登録し、接続文字列を取得する
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bbb8dcb410f17ba894210c9c090ec0975f83c0b3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796133"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Azure CLI を使用して新しい Azure IoT Edge デバイスを登録する

Azure IoT Edge で IoT デバイスを使用する前に、それらを IoT ハブに登録する必要があります。 デバイスを登録すると、IoT Edge ワークロード用にデバイスを設定するために使用できる接続文字列を受け取ります。

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 これを使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。 新しい IoT 拡張機能によって、Azure CLI には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

この記事では、Azure CLI を使用して新しい IoT Edge デバイスを登録する方法を示します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="create-a-device"></a>デバイスを作成する

[az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 例:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* **device-id**: IoT ハブに対して一意のわかりやすい名前を指定します。

* **hub-name**: IoT ハブの名前を指定します。

* **edge-enabled**: このパラメーターは、デバイスが IoT Edge に使用されることを宣言します。

   ![az iot hub device-identity create output](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>すべてのデバイスを表示する

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) コマンドを使用します。 例:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

## <a name="retrieve-the-connection-string"></a>接続文字列の取得

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` パラメーターの値は、大文字と小文字が区別されます。 接続文字列を囲む引用符はコピーしないでください。

## <a name="next-steps"></a>次の手順

[Azure CLI でモジュールをデバイスにデプロイ](how-to-deploy-modules-cli.md)する方法を確認します。
