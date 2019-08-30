---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1e919f22d5dd7975f055f262ec9ba69230aebd17
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780846"
---
このセクションでは、Azure CLI を使用して、この記事用のデバイス ID を作成します。 デバイス ID には大文字と小文字の区別があります。

1. [Azure Cloud Shell](https://shell.azure.com/) を開きます。

1. Azure Cloud Shell で次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI をインストールします。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

2. 次のコマンドを実行して、`myDeviceId` という新しいデバイス ID を作成し、デバイス接続文字列を取得します。

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

結果として得られたデバイスの接続文字列をメモしておきます。 このデバイス接続文字列は、デバイス アプリからデバイスとして IoT Hub に接続する際に使用します。

<!-- images and links -->
