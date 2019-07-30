---
title: Azure IoT SDK について | Microsoft Docs
description: 開発者ガイド - デバイス アプリやバックエンド アプリを構築するために使用できる、さまざまな Azure IoT device SDK およびサービス SDK に関する情報とリンク。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: e51313bbed21459de9f717edd123887caed18f4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400662"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDK の概要と使用方法

IoT Hub を使用する場合、以下の 2 つのカテゴリのソフトウェア開発キット (SDK) があります。

* **IoT Hub Device SDK** では、デバイス クライアントまたはモジュール クライアントを使用して、ご利用の IoT デバイス上で実行するアプリを構築できます。 これらのアプリは IoT Hub にテレメトリを送信し、必要に応じて IoT Hub からメッセージ、ジョブ、メソッド、ツインの更新を受信します。  また、モジュール クライアントを使用して、[Azure IoT Edge ランタイム](../iot-edge/about-iot-edge.md)用の[モジュール](../iot-edge/iot-edge-modules.md)を作成することもできます。

* **IoT Hub Service SDK** を使用すると、ご利用の IoT ハブを管理するバックエンド アプリケーションを構築できます。また、必要に応じてメッセージを送信し、ジョブのスケジュールを設定し、ダイレクト メソッドを呼び出し、ご利用の IoT デバイスまたはモジュールに必要なプロパティの更新情報を送信することができます。

また、[デバイス プロビジョニング サービス](../iot-dps/about-iot-dps.md)を操作するための一連の SDK も利用できます。
* **Device SDK をプロビジョニング**すると、IoT デバイス上で動作して、デバイス プロビジョニング サービスと通信するアプリを構築できます。

* **Service SDK をプロビジョニング**すると、デバイス プロビジョニング サービスで自分の登録を管理するバックエンド アプリケーションを構築できます。

[Azure IoT SDK を使用した開発の利点](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)について、詳細を説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>OS プラットフォームとハードウェアの互換性

SDK でサポートされるプラットフォームは、「[Azure IoT SDK プラットフォームのサポート](iot-hub-device-sdk-platform-support.md)」で確認できます。

特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)または個々のリポジトリを参照してください。

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub デバイス SDK

Microsoft Azure IoT device SDK には、Azure IoT Hub サービスに接続および管理されるアプリケーションの構築を容易にするコードが含まれています。

.NET 用 Azure IoT Hub デバイス SDK: 

* [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) からダウンロードします。  名前空間は、IoT Hub デバイス クライアント (DeviceClient、ModuleClient) を含む Microsoft.Azure.Devices.Clients です。
* [ソース コード](https://github.com/Azure/azure-iot-sdk-csharp)
* [API リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [モジュール リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

C 用 Azure IoT Hub デバイス SDK (ANSI C - C99):

* [apt-get、MBED、Arduino IDE、または iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) からのインストール
* [ソース コード](https://github.com/Azure/azure-iot-sdk-c)
* [C デバイス SDK のコンパイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [モジュール リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [C SDK から他のプラットフォームへの移植](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* クロスコンパイル、さまざまなプラットフォームでの基本など、[開発者向けドキュメント](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc)
* [Azure IoT Hub C SDK のリソース消費情報](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Java 用 Azure IoT Hub デバイス SDK: 

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) プロジェクトに追加する
* [ソース コード](https://github.com/Azure/azure-iot-sdk-java)
* [API リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [モジュール リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Node.js 用 Azure IoT Hub デバイス SDK: 

* [npm](https://www.npmjs.com/package/azure-iot-device) からインストールする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-node)
* [API リファレンス](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [モジュール リファレンス](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Python 用 Azure IoT Hub デバイス SDK: 

* [pip](https://pypi.python.org/pypi/azure-iothub-device-client/) からインストールする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-python)
* API のリファレンス: [C API リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)に関するページを参照してください。

iOS 用 Azure IoT Hub デバイス SDK: 

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) からインストールする
* [サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API のリファレンス: [C API リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)に関するページを参照してください。

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub サービス SDK

Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

.NET 用 Azure IoT Hub サービス SDK:

* [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/) からダウンロードします。  名前空間は、IoT Hub サービス クライアント (RegistryManager、ServiceClients) を含む Microsoft.Azure.Devices です。
* [ソース コード](https://github.com/Azure/azure-iot-sdk-csharp)
* [API リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Java 用 Azure IoT Hub サービス SDK: 

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) プロジェクトに追加する
* [ソース コード](https://github.com/Azure/azure-iot-sdk-java)
* [API リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Node.js 用 Azure IoT Hub サービス SDK: 

* [npm](https://www.npmjs.com/package/azure-iothub) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-node)
* [API リファレンス](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Python 用 Azure IoT Hub サービス SDK: 

* [pip](https://pypi.python.org/pypi/azure-iothub-service-client/) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-python)

C 用 Azure IoT Hub サービス SDK: 

* [apt-get、MBED、Arduino IDE、または Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) からダウンロードする
* [ソース コード](https://github.com/Azure/azure-iot-sdk-c)

iOS 用 Azure IoT Hub サービス SDK: 

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) からインストールする
* [サンプル](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure Provisioning SDK

**Microsoft Azure Provisioning SDK** では、[デバイス プロビジョニング サービス](../iot-dps/about-iot-dps.md)を使用してデバイスをご利用の IoT Hub にプロビジョニングすることができます。

C# 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* NuGet から [Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) および [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) をダウンロードします。
* [ソース コード](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

C 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [apt-get、MBED、Arduino IDE、または iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) からのインストール
* [ソース コード](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API リファレンス](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Java 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) プロジェクトに追加する
* [ソース コード](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API リファレンス](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Node.js 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [ソース コード](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API リファレンス](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* npm から [Device SDK](https://badge.fury.io/js/azure-iot-provisioning-device) および [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) をダウンロードします。

Python 用の Azure Provisioning Device SDK および Azure Provisioning Service SDK:

* [ソース コード](https://github.com/Azure/azure-iot-sdk-python)
* pip から [Device SDK](https://pypi.org/project/azure-iot-provisioning-device-client/) および [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) をダウンロードします。

## <a name="next-steps"></a>次の手順

Azure IoT SDK では、開発に役立つ一連のツールも提供しています。
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): IoT Hub との接続に関連する問題の診断に役立つ、クロスプラットフォーム コマンド ライン ツール。
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): IoT Hub に接続するための Windows デスクトップ アプリケーション。

Azure IoT SDK を使用した開発に関連するドキュメント
* IoT Hub デバイス SDK を使用して[接続と信頼できるメッセージングを管理する方法](iot-hub-reliability-features-in-sdks.md)に関する記事を参照してください。
* iOS や Android などの[モバイル プラットフォーム向けの開発](iot-hub-how-to-develop-for-mobile-devices.md)方法に関する記事を参照してください。
* [Azure IoT SDK プラットフォームのサポート](iot-hub-device-sdk-platform-support.md)


この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)
* [クォータとスロットル](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md)
* [IoT Hub REST API リファレンス](/rest/api/iothub/)
