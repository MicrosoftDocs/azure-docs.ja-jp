---
title: ファイルをデバイスから Azure ストレージにアップロードする | Microsoft Docs
description: デバイスからクラウドへのファイルのアップロードを構成する方法。 ファイルのアップロードを構成したら、デバイス上でファイルのアップロードを実装します。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 6b717fd15b25ae4abd2af3520dba2e72f8f9f3a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556181"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>ファイルをデバイスからクラウドにアップロードする

*このトピックは、管理者とデバイス開発者を対象としています。*

IoT Central では、接続されているデバイスからクラウド ストレージにメディアやその他のファイルをアップロードできます。 IoT Central アプリケーションでファイルのアップロード機能を構成してから、デバイスのコードでファイルのアップロードを実装します。

## <a name="prerequisites"></a>前提条件

ファイル アップロードを構成する IoT Central アプリケーションの管理者であること。

アップロードされたファイルを保存するために、Azure ストレージ アカウントとコンテナーが必要です。 使用する既存のストレージ アカウントとコンテナーをお持ちでない場合は、[Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成してください。

## <a name="configure-device-file-uploads"></a>デバイスからのファイルのアップロードの構成

デバイスからのファイルのアップロードを構成するには、次の手順に従います。

1. アプリケーションの **[管理]** セクションに移動します。

1. **[Device file upload]\(デバイスからのファイルのアップロード\)** を選択します。

1. 使用するストレージ アカウントとコンテナーを選択します。 アプリケーションとは異なる Azure サブスクリプションにストレージ アカウントがある場合は、ストレージ アカウント接続文字列を入力します。

1. 必要に応じて、アップロードのタイムアウトを調整します。ここでは、アップロード要求の有効期間を設定します。 有効な値は 1 ～ 24 時間です。

1. **[保存]** を選択します。 ステータスが **[構成済み]** と表示されたら、デバイスからファイルをアップロードする準備ができています。

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="アプリケーションでのファイル アップロードの構成":::

## <a name="disable-device-file-uploads"></a>デバイスからのファイルのアップロードの無効化

デバイスから IoT Central アプリケーションへのファイルのアップロードを無効にする場合は、次の手順に従います。

1. アプリケーションの **[管理]** セクションに移動します。

1. **[Device file upload]\(デバイスからのファイルのアップロード\)** を選択します。

1. **[削除]** を選択します。

## <a name="upload-a-file-from-a-device"></a>デバイスからのファイルのアップロード

IoT Central では、IoT Hub のファイル アップロード機能を使用して、デバイスからファイルをアップロードできるようにします。 デバイスからファイルをアップロードする方法を示すサンプル コードについては、[IoT Central ファイル アップロード デバイス サンプル](https://docs.microsoft.com/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)を参照してください。

## <a name="next-steps"></a>次のステップ

IoT Central でデバイスからのファイルのアップロードを構成し、実装する方法を理解したら、次のステップとして、デバイスからのファイルのアップロードについてさらに理解を深めることをお勧めします。

- [IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)
