---
title: 'クイックスタート: Azure RTOS 用の Defender-IoT-micro-agent を構成して有効にする'
description: Azure IoT ハブで Azure RTOS 用の Defender-IoT-micro-agent をオンボードし、有効にする方法について説明します。
services: defender-for-iot
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: db0fc0be3cd10bc57a46cb5a056ca5e057ae255a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781162"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>クイックスタート: Azure RTOS 用の Defender-IoT-micro-agent (プレビュー)

この記事では、最初に前提条件について説明し、IoT ハブで Azure RTOS 用の Defender-IoT-micro-agent を有効にする方法について説明します。 現在 IoT Hub がない場合、作業を開始するには、「[Azure portal を使用して IoT Hub を作成する](../iot-hub/iot-hub-create-through-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件 

### <a name="supported-devices"></a>サポートされるデバイス

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

[Azure RTOS 用の Defender-IoT-micro-agent GitHub リソース](https://github.com/azure-rtos/azure-iot-preview/releases)から、選択した特定のボードとツール (IAR、セミの IDE、または PC) の .zip ファイルの 1 つをダウンロードしてコンパイルし、実行します。

### <a name="azure-resources"></a>Azure リソース

作業を開始するための次のステージでは、Azure リソースを準備します。 IoT Hub が必要であり、Log Analytics ワークスペースを使用することをお勧めします。 IoT Hub を使用するには、デバイスに接続するための IoT Hub 接続文字列が必要です。 
  
### <a name="iot-hub-connection"></a>IoT Hub 接続

作業を始めるには、IoT Hub 接続が必要です。 

1. Azure portal で **[IoT Hub]** を開きます。

1. **[IoT デバイス]** に移動します。

1. **［作成］** を選択します

1. IoT 接続文字列を[構成ファイル](how-to-azure-rtos-security-module.md)にコピーします。

接続の資格情報は、ユーザー アプリケーション構成の **HOST_NAME**、**DEVICE_ID**、および **DEVICE_SYMMETRIC_KEY** から取得します。

Azure RTOS 用の Defender-IoT-micro-agent では、**MQTT** プロトコルに基づく Azure IoT ミドルウェア接続が使用されます。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、ソリューションの構成とカスタマイズを完了します。

> [!div class="nextstepaction"]
> [Azure RTOS 用の Defender-IoT-micro-agent を構成およびカスタマイズする (プレビュー)](how-to-azure-rtos-security-module.md)
