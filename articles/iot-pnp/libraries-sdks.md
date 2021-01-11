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
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407797"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>IoT プラグ アンド プレイ用の Microsoft SDK

IoT プラグ アンド プレイ ライブラリと SDK を使用すると、開発者は、複数のプラットフォーム上でさまざまなプログラミング言語を使用して IoT ソリューションを構築できます。 次の表には、作業を開始するために役立つサンプルとクイックスタートへのリンクが含まれています。

## <a name="device-sdks-ga"></a>デバイスの SDK (GA)

| Language | Package | コード リポジトリ | サンプル | クイック スタート | リファレンス |
|---|---|---|---|---|---|
| C - デバイス | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [IoT Hub への接続](quickstart-connect-device-c.md) | [リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - デバイス | [NuGet 1.27.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [IoT Hub への接続](quickstart-connect-device-csharp.md) | [リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java - デバイス | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [IoT Hub への接続](quickstart-connect-device-java.md) | [リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python - デバイス | [pip 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [IoT Hub への接続](quickstart-connect-device-python.md) | [リファレンス](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Node - デバイス | [npm 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [IoT Hub への接続](quickstart-connect-device-node.md) | [リファレンス](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>デバイス SDK (プレビュー)

| Language | コード リポジトリ/サンプル |
|---|---|
|Azure SDK for Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTOS IoT ミドルウェア| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Azure RTOS ファースト ステップ ガイド | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>サービス SDK (プレビュー)

| Language | Package | コード リポジトリ | サンプル | クイック スタート | リファレンス |
|---|---|---|---|---|---|
| .NET - IoT Hub サービス プレビュー | [NuGet 1.27.1-preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [サンプル](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | なし | 該当なし |
| Java - IoT Hub サービス プレビュー | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | なし | 該当なし |
| Node - IoT Hub サービス プレビュー | [npm 1.12.4-pnp-refresh.4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | なし | 該当なし |
| Python - IoT Hub/Digital Twins サービス プレビュー | [pip 2.2.1rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [IoT Hub Digital Twins API の操作](quickstart-service-python.md) | 該当なし |
| Node - Digital Twins サービス プレビュー | [npm 1.0.0-pnp-refresh.3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [IoT Hub Digital Twins API の操作](quickstart-service-node.md) | 該当なし |

## <a name="next-steps"></a>次のステップ

SDK とライブラリを試すには、[開発者ガイド](concepts-developer-guide.md)、[デバイスのクイックスタート](quickstart-connect-device-c.md)、および[サービスのクイックスタート](quickstart-service-node.md)を参照してください。
