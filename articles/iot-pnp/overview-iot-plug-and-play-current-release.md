---
title: IoT プラグ アンド プレイの現在のリリース | Microsoft Docs
description: IoT プラグ アンド プレイの現在のリリースに含まれる内容について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831877"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>IoT プラグ アンド プレイの現在のリリースに含まれる内容について

この記事では、IoT プラグ アンド プレイの現在のリリースをサポートしているツール、SDK、および API の概要について説明します。 表示されるバージョン番号は、IoT プラグ アンド プレイが一般公開された時点のバージョン番号を反映しています。 バージョン番号は、リリース後に増加する場合があります。

## <a name="modeling-language"></a>モデリング言語

[Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl)。

IoT プラグ アンド プレイ デバイスが DTDL と連携する方法の詳細については、「[IoT プラグ アンド プレイ規則](concepts-convention.md)」を参照してください。

## <a name="tools-and-utilities"></a>ツールとユーティリティ

- Azure IoT エクスプローラー 0.12.0。

    詳細については、「[Azure IoT エクスプローラーをインストールして使用する](howto-use-iot-explorer.md)」を参照してください。

- VS Code 拡張機能 1.0.0。

    詳細については、「[DTDL オーサリング ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)」を参照してください。

- Visual Studio 2019 拡張機能 1.0.0。

    詳細については、「[DTDL オーサリング ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)」を参照してください。

- Azure CLI IoT 拡張機能 0.10.0。

    Azure IoT 拡張機能には、デバイスの認定に役立つコマンドが含まれています。 以下を参照してください。`az iot product -h`

## <a name="libraries-and-sdks"></a>ライブラリと SDK

ライブラリと SDK の詳細については、「[IoT プラグ アンド プレイ用の Microsoft SDK](libraries-sdks.md)」を参照してください。

- C デバイス SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- 埋め込み C デバイス SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET デバイス SDK [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java デバイス SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python デバイス SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js デバイス SDK [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - IoT Hub サービス [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - IoT Hub サービス [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - IoT Hub サービス [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - IoT Hub/Digital Twins サービス [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL モデル パーサー [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)。

## <a name="rest-apis"></a>REST API

REST API [2020-09-30](/rest/api/iothub)。

詳細については、[IoT プラグ アンド プレイの開発者ガイド](concepts-developer-guide-service.md)を参照してください。

## <a name="iot-hub"></a>IoT Hub

IoT プラグ アンド プレイは、すべてのリージョンの IoT Hub でサポートされています。 IoT プラグ アンド プレイは、Standard または Free レベルの IoT ハブでのみサポートされています。

## <a name="announcements"></a>アナウンス

現在および以前の IoT プラグ アンド プレイのアナウンスについては、以下のブログ投稿を参照してください。

- [パブリック プレビューの更新 (2020 年 8 月 29 日投稿)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [IoT プラグ アンド プレイ用にデバイスを準備して認定する (2020 年 8 月 26 日投稿)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT プラグ アンド プレイがプレビューで使用可能に (2019 年 8 月 22 日投稿)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Azure IoT Central と IoT プラグ アンド プレイで構築する (2019 年 5 月 7 日投稿)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>次の手順

次の手順では、「[IoT プラグ アンド プレイとは](overview-iot-plug-and-play.md)」を確認することをお勧めします。