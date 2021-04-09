---
title: Device Update for Azure IoT Hub 構成ファイルについて理解する | Microsoft Docs
description: Device Update for Azure IoT Hub 構成ファイルについて理解します。
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101660547"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Device Update for IoT Hub 構成ファイル

"adu-conf.txt" は、次の構成を管理するために作成できる省略可能なファイルです。

* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]
* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]
* DeviceInformation.manufacturer
* DeviceInformation.model
* デバイス接続文字列 (Device Update エージェントによって認識されていない場合)。

## <a name="purpose"></a>目的
Device Update エージェントでは、まず、`manufacturer` と `model` の値を[インターフェイス レイヤー](device-update-agent-overview.md#the-interface-layer)に使用するデバイスから取得しようと試みられます。 それが失敗した場合、Device Update エージェントでは次に "adu-conf.txt" ファイルが検索され、そこから値が使用されます。 両方の試みが成功しなかった場合、Device Update エージェントでは、[既定](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) 値が使用されます。

詳細については、[ADU コア インターフェイス](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface)と[デバイス情報インターフェイス](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)に関するページを参照してください。

## <a name="file-location"></a>ファイルの場所

Linux システム内の `adu` というパーティションまたはディスクに、次のフィールドを含む "adu-conf.txt" というテキスト ファイルを作成します。

## <a name="list-of-fields"></a>フィールドの一覧

|名前|説明|
|-----------|--------------------|
|connection_string|デバイスで IoT Hub に接続するために使用できる事前プロビジョニングされた接続文字列。 注: [Azure IoT Identity Service](https://azure.github.io/iot-identity-service/) によって、Device Update エージェントをプロビジョニングする場合は必要ありません|
|aduc_manufacturer|`AzureDeviceUpdateCore:4.ClientMetadata:4` インターフェイスによって、更新デプロイをターゲットとするデバイスを分類するために報告されます。|
|aduc_model|`AzureDeviceUpdateCore:4.ClientMetadata:4` インターフェイスによって、更新デプロイをターゲットとするデバイスを分類するために報告されます。|
|manufacturer|Device Update エージェントによって `DeviceInformation` インターフェイスの一部として報告されます。|
|model|Device Update エージェントによって `DeviceInformation` インターフェイスの一部として報告されます。|

## <a name="example-adu-conftxt-file-contents"></a>"adu-conf.txt" ファイルの内容の例

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
