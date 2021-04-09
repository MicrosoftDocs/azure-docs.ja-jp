---
title: Azure IoT プロトコル ゲートウェイ | Microsoft Docs
description: Azure IoT プロトコル ゲートウェイを使用して IoT Hub の機能およびプロトコルのサポートを拡張し、IoT Hub ではネイティブでサポートされていないプロトコルを使用してデバイスとハブの接続を有効にする方法。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325034"
---
# <a name="support-additional-protocols-for-iot-hub"></a>IoT Hub に対するその他のプロトコルのサポート

Azure IoT Hub は MQTT、AMQP、および HTTPS プロトコルを介した通信をネイティブでサポートします。 場合によっては、デバイスまたはフィールド ゲートウェイでこれらの標準プロトコルをいずれも使用できず、プロトコルの適応が必要になることがあります。 このような場合は、カスタム ゲートウェイを使用できます。 カスタム ゲートウェイによって IoT Hub との間のトラフィックをブリッジすることにより、IoT Hub エンドポイントにプロトコルを適応できます。 IoT Hub に対するプロトコルの適用を可能にするには、 [Azure IoT プロトコル ゲートウェイ](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) をカスタム ゲートウェイとして使用します。

## <a name="azure-iot-protocol-gateway"></a>Azure IoT プロトコル ゲートウェイ

Azure IoT プロトコル ゲートウェイは、IoT Hub との大規模双方型デバイス通信用に設計された、プロトコル適応用フレームワークです。 プロトコル ゲートウェイは、特定のプロトコルを介してデバイスの接続を受け入れるパススルー コンポーネントです。 AMQP 1.0 経由の IoT Hub へのトラフィックをブリッジします。

プロトコル ゲートウェイは、Azure Service Fabric、Azure Cloud Services の worker ロール、または Windows Virtual Machines を使用することで、高度にスケーラブルな方法で Azure にデプロイできます。 さらに、プロトコル ゲートウェイはフィールド ゲートウェイなどのオンプレミス環境にデプロイできます。

Azure IoT プロトコル ゲートウェイには、必要な場合は MQTT プロトコルの動作をカスタマイズできるようにする MQTT プロトコル アダプターが含まれています。 IoT Hub では MQTT v3.1.1 プロトコルの組み込みサポートを提供しているため、プロトコルのカスタマイズが必要な場合または追加機能に対して特定の要件が必要な場合にのみ MQTT プロトコル アダプターの使用を検討してください。

MQTT アダプターは、その他のプロトコルのプロトコル アダプターを構築するためのプログラミング モデルにもなっています。 さらに、Azure IoT プロトコル ゲートウェイのプログラミング モデルでは、カスタム認証、メッセージの変換、圧縮/圧縮解除、デバイスと IoT Hub 間のトラフィックの暗号化/暗号化解除などの特殊な処理を行うために、カスタム コンポーネントを接続することができます。

柔軟性を高めるために、Azure IoT プロトコル ゲートウェイと MQTT の実装はオープン ソース ソフトウェア プロジェクトで提供されます。 オープンソース プロジェクトを使用して、さまざまなプロトコルやプロトコル バージョンのサポートを追加したり、シナリオに合わせて実装をカスタマイズしたりできます。 

## <a name="next-steps"></a>次のステップ

Azure IoT プロトコル ゲートウェイについて、およびそれを IoT ソリューションの一部として使用およびデプロイする方法については、以下をご覧ください。

* [Azure IoT protocol gateway repository on GitHub (GitHub の Azure IoT プロトコル ゲートウェイ リポジトリ)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Azure IoT protocol gateway developer guide (Microsoft Azure IoT プロトコル ゲートウェイ開発者ガイド)](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

* [Event Hubs との比較](iot-hub-compare-event-hubs.md)

* [スケーリング、高可用性、およびディザスター リカバリー](iot-hub-scaling.md)

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)