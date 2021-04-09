---
title: Azure IoT Hub 用デバイス更新のリソースについて | Microsoft Docs
description: Azure IoT Hub 用デバイス更新のリソースについて
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678607"
---
# <a name="device-update-resources"></a>デバイス更新のリソース

IoT Hub 用デバイス更新を使用するには、デバイス更新アカウントとインスタンス リソースを作成する必要があります。 

## <a name="device-update-account"></a>デバイス更新アカウント

デバイス更新アカウントは、Azure サブスクリプション内に作成されるリソースです。 デバイス更新アカウント レベルでは、自分のデバイス更新アカウントを作成するリージョンを選択できます。 また、デバイス更新にアクセスするユーザーを承認するアクセス許可を設定することもできます。


## <a name="device-update-instance"></a>デバイス更新インスタンス
アカウントが作成されたら、デバイス更新インスタンスを作成する必要があります。 インスタンスとは、特定の IoT ハブに関連付けられている更新プログラムとデプロイを含む論理コンテナーです。 デバイス更新では、デバイス ディレクトリとしての IoT ハブと、デバイスとの通信チャネルが使用されます。 

パブリック プレビュー期間中には、サブスクリプションあたり 2 つのデバイス更新アカウントを作成できます。 また、アカウントごとに 2 つのデバイス更新インスタンスを作成することができます。

## <a name="configuring-device-update-linked-iot-hub"></a>デバイス更新にリンクされた IoT Hub を構成する 

IoT Hub からデバイス更新に変更通知が送られるように、デバイス更新は "組み込みの" イベント ハブと統合されています。 ご利用のインスタンス内の [IoT Hub の構成] ボタンをクリックすると、IoT デバイスと通信するために必要なメッセージ ルートとアクセス ポリシーが構成されます。 

デバイス更新用に次のメッセージ ルートが構成されます。

|   ルート名    | ルーティング クエリ  | 説明  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |デジタル ツインの変更イベントをリッスンします  |
|  DeviceUpdate.DeviceLifeCycle | opType = 'deleteDeviceIdentity'  | 削除されたデバイスをリッスンします |
|  DeviceUpdate.TelemetryModelInformation | iothub-interface-id = "urn:azureiot:ModelDiscovery:ModelInformation:1 | 新しいデバイスの種類をリッスンします |
|  DeviceUpdate.DeviceTwinEvents| (opType = 'updateTwin' OR opType = 'replaceTwin') AND IS_DEFINED($body.tags.ADUGroup) | 新しいデバイス更新グループをリッスンします |

## <a name="next-steps"></a>次のステップ

[デバイス更新リソースを作成する](./create-device-update-account.md)
