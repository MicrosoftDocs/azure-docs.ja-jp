---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "70049062"
---
<!-- put the ## header in the file that includes this file -->

このセクションでは、IoT ハブの ID レジストリにデバイス ID を作成します。 ハブに接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)を参照してください。

1. IoT ハブのナビゲーション メニューの **[IoT デバイス]** を開き、 **[新規]** を選択して IoT ハブにデバイスを追加します。

    ![ポータルでデバイス ID を作成する](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. **[デバイスの作成]** で、新しいデバイスの名前 (**myDeviceId** など) を入力し、 **[保存]** を選択します。 この操作で、IoT ハブのデバイス ID が作成されます。

   ![新しいデバイスを追加する](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. デバイスが作成された後、 **[IoT デバイス]** ウィンドウの一覧からデバイスを開きます。 後で使用するために**プライマリ接続文字列**をコピーします。

    ![デバイスの接続文字列](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)をご覧ください。
