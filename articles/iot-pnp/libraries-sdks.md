---
title: IoT プラグ アンド プレイ ライブラリと SDK
description: IoT プラグ アンド プレイが有効なソリューションを開発するために使用できるデバイスとサービス ライブラリに関する情報。
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 31e06777a2f2e26f6ef546e60fd0bf4428d272c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503812"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>IoT プラグ アンド プレイ用の Microsoft SDK

IoT プラグ アンド プレイ ライブラリと SDK を使用すると、開発者は、複数のプラットフォーム上でさまざまなプログラミング言語を使用して IoT ソリューションを構築できます。 次の表には、作業を開始するために役立つサンプルとクイックスタートへのリンクが含まれています。

## <a name="device-sdks"></a>デバイスの SDK

| Language | Package | コード リポジトリ | サンプル | クイック スタート | リファレンス |
|---|---|---|---|---|---|
| C - デバイス | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [IoT Hub への接続](quickstart-connect-device.md) | [リファレンス](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - デバイス | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [IoT Hub への接続](quickstart-connect-device.md) | [リファレンス](/dotnet/api/microsoft.azure.devices.client) |
| Java - デバイス | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [IoT Hub への接続](quickstart-connect-device.md) | [リファレンス](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python - デバイス | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [IoT Hub への接続](quickstart-connect-device.md) | [リファレンス](/python/api/azure-iot-device/azure.iot.device) |
| Node - デバイス | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [IoT Hub への接続](quickstart-connect-device.md) | [リファレンス](/javascript/api/azure-iot-device/) |
| 埋め込み C - デバイス | 該当なし | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [サンプル](howto-use-embedded-c.md#samples) | [埋め込み C の使用方法](howto-use-embedded-c.md) | 該当なし

## <a name="service-sdks"></a>サービス SDK

| プラットフォーム  | Package | コード リポジトリ | サンプル | クイック スタート | リファレンス |
|---|---|---|---|---|---|
| .NET - IoT Hub サービス | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | なし | [参照](/dotnet/api/microsoft.azure.devices) |
| Java - IoT Hub サービス | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | なし | [参照](/java/api/com.microsoft.azure.sdk.iot.service) |
| ノード - IoT Hub サービス | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | なし | [参照](/javascript/api/azure-iothub/) |
| Python - Digital Twins サービス | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [IoT Hub Digital Twins API の操作](quickstart-service.md) | 該当なし |
| ノード - Digital Twins サービス | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [IoT Hub Digital Twins API の操作](quickstart-service.md) | 該当なし |

## <a name="next-steps"></a>次のステップ

SDK とライブラリを試すには、[開発者ガイド](concepts-developer-guide-device.md)、[デバイスのクイックスタート](quickstart-connect-device.md)、および[サービスのクイックスタート](quickstart-service.md)を参照してください。