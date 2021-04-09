---
title: Azure IoT SDK を使用せずに開発する | Microsoft Docs
description: 開発者ガイド - Azure IoT SDK を使用しないでデバイス アプリやバックエンド アプリを構築するために使用できるトピックに関する情報とリンク。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461713"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Azure IoT Hub SDK を使用しないで開発する

このトピックでは、Azure IoT Sdk を使用しないでデバイスやバックエンドのアプリを開発したい開発者に役立つ情報とリンクを提供します。

Microsoft は、Azure IoT SDK を使用することを強くお勧めします。 Azure IoT デバイスとサービス SDK は、多くの一般的なプラットフォームで公開されています。 SDK には、デバイスの接続と再接続や、再試行ポリシーなど、基になる通信プロトコルの複雑な部分の多くが処理される便利なレイヤーが用意されています。 SDK は、IoT Hub によって公開される最新機能とセキュリティ更新プログラムを提供するために、定期的に更新されます。 SDK を使用すると、開発に要する時間と、コードのメンテナンスに必要な時間を、短縮することができます。 Azure IoT SDK の詳細については、[Azure IoT デバイスとサービス SDK](iot-hub-devguide-sdks.md) に関する記事を参照してください。 Azure IoT SDK を使用する利点の詳細については、ブログ記事「[Azure IoT SDK を使用する利点と、避けるべき落とし穴](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)」を参照してください。

IoT Hub によって、デバイスとの通信用に、AMQP、AMQP over WebSockets、HTTPS、MQTT、MQTT over WebSockets がサポートされていますが、デバイスでサポートされている場合は、MQTT を使用することをお勧めします。

## <a name="development-prerequisites"></a>開発の前提条件

開発を始める前に、IoT Hub と、デバイスまたはバックエンドのアプリで実装する機能について、十分な知識を得ておく必要があります。 理解しておく必要があるトピックのごく簡単な一覧を次に示します。

* IoT Hub によって公開されるエンドポイントと、各エンドポイントでサポートされるプロトコルについて、理解している必要があります。 詳細については、[IoT Hub のエンドポイント](iot-hub-devguide-endpoints.md)に関する記事を参照してください。

* デバイス アプリにプロトコルの選択が関係している場合は、MQTT を使用することを強くお勧めします。 ただし、プロトコルを選択する前に、それぞれによって課される制限を理解しておく必要があります。 詳細については、[通信プロトコルの選択](iot-hub-devguide-protocols.md)に関する記事を参照してください。

* IoT Hub での認証については、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>さまざまなプロトコルに関するヘルプ

Azure IoT SDK を使用しないで以下のプロトコルを使用する場合のヘルプについて:

* **AMQP** でのデバイス アプリまたはバックエンド アプリについては、[AMQP のサポート](iot-hub-amqp-support.md)に関する記事を参照してください。

* **MQTT** でのデバイス アプリについては、[MQTT のサポート](iot-hub-mqtt-support.md)に関する記事を参照してください。 このトピックの大部分は、MQTT プロトコルの直接使用に関するものです。 また、[IoT MQTT サンプル リポジトリ](https://github.com/Azure-Samples/IoTMQTTSample)の使用に関する情報も含まれます。 このリポジトリには、Eclipse Mosquitto ライブラリを使用して IoT Hub にメッセージを送信する C サンプルが含まれています。

* **HTTPS** でのデバイス アプリまたはバックエンド アプリについては、[Azure IoT Hub REST API](/rest/api/iothub/) に関する記事を参照してください。 「[開発の前提条件](#development-prerequisites)」で説明されているように、HTTPS で X.509 証明機関 (CA) 認証を使用することはできません。

デバイスでサポートされている場合は、MQTT を使用することを強くお勧めします。

## <a name="next-steps"></a>次のステップ

* [MQTT サポート](iot-hub-mqtt-support.md)