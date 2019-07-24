---
title: Azure IoT Hub と Azure Event Hubs の比較 | Microsoft Docs
description: 機能の相違点とユース ケースを中心とした、Azure の IoT Hub サービスと Event Hubs サービスの比較です。 比較には、サポートされているプロトコル、デバイス管理、監視、およびファイルのアップロードが含まれます。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669740"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT デバイスの Azure への接続: IoT Hub と Event Hubs

Azure では、データとクラウドの機能をつなぐ助けとなるように、さまざまな種類の接続と通信のために特に開発されたサービスを提供しています。 Azure IoT Hub と Azure Event Hubs はどちらも、大量のデータを取り込み、ビジネス情報分析のためにそのデータを処理または保存できるクラウド サービスです。 2 つのサービスは、どちらも少ない待ち時間と高い信頼性でデータの取り込みをサポートするという点で類似していますが、異なる目的のために設計されています。 IoT Hub が、IoT デバイスを Azure クラウドに接続するという固有の要件に対応するために開発されたのに対して、Event Hubs は、ビッグ データのストリーミング用に設計されました。 マイクロソフトでは、Azure IoT Hub を使用して IoT デバイスを Azure に接続することをお勧めしています。

Azure IoT Hub は、IoT デバイスを接続してデータを収集し、ビジネスの分析情報と自動化を推進するためのクラウド ゲートウェイです。 さらに、IoT Hub には、デバイスとバックエンド システム間のリレーションシップを強化する機能が含まれています。 双方向通信機能は、デバイスからデータを受信している間でも、デバイスにコマンドとポリシーを返送できることを意味します。 たとえば、cloud-to-device メッセージングを使用して、プロパティを更新したりデバイス管理操作を起動したりします。 cloud-to-device 通信では、Azure IoT Edge を使ってクラウド インテリジェンスをエッジ デバイスに送信することも可能です。 IoT Hub によって提供されるデバイス レベルの一意の ID は、潜在的な攻撃からの IoT ソリューションのセキュリティ保護を強化する助けになります。 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) は、Azure のビッグ データ ストリーミング サービスです。 これは、ユーザーが 1 日あたり何十億もの要求を送信する可能性がある高スループットのデータ ストリーミング シナリオに向けて設計されています。 Event Hubs は、パーティション分割されたコンシューマー モデルを使用してストリームをスケールアウトし、ビッグ データと、Databricks、Stream Analytics、ADLS、HDInsight を含む Azure の分析サービスに統合されます。 Event Hubs Capture や自動インフレのような機能を備えたこのサービスは、ビッグ データのアプリとソリューションをサポートするために設計されています。 さらに、IoT Hub ではテレメトリ フロー パスのために Event Hubs を利用するので、IoT ソリューションでも、Event Hubs の強力な処理機能のメリットを享受できます。

要約すると、どちらのソリューションも大量のデータ インジェストに対応する設計になっています。 IoT Hub のみが、IoT デバイスを Azure クラウドに接続するというビジネス価値を最大化するように設計された、IoT 固有の豊富な機能を備えています。  IoT への取り組みを開始したばかりの場合は、データ インジェストのシナリオをサポートするために IoT Hub から着手することで、ビジネス面や技術面のニーズによって必要になり次第、確実に、多彩な IoT 機能をすばやく利用できるようになります。

次の表では、IoT 機能を評価するときのために、IoT Hub の 2 つの階層と Event Hubs の詳細な比較を示します。 IoT Hub の Standard レベルと Basic レベルについて詳しくは、[適切な IoT Hub レベルを選択する方法](iot-hub-scaling.md)に関するページをご覧ください。

| IoT の機能 | IoT Hub Standard レベル | IoT Hub Basic レベル | Event Hubs |
| --- | --- | --- | --- |
| デバイスからクラウドへのメッセージ | ![○][checkmark] | ![○][checkmark] | ![○][checkmark] |
| Protocols:HTTPS、AMQP、AMQP over webSockets | ![○][checkmark] | ![○][checkmark] | ![○][checkmark] |
| Protocols:MQTT、MQTT over webSockets | ![○][checkmark] | ![○][checkmark] |  |
| デバイスごとの ID | ![○][checkmark] | ![○][checkmark] |  |
| 複数デバイスからのファイルのアップロード | ![○][checkmark] | ![○][checkmark] |  |
| Device Provisioning Service | ![○][checkmark] | ![○][checkmark] |  |
| クラウドからデバイスへのメッセージ | ![○][checkmark] |  |  |
| デバイス ツインとデバイス管理 | ![○][checkmark] |  |  |
| デバイス ストリーム (プレビュー) | ![○][checkmark] |  |  |
| IoT Edge | ![○][checkmark] |  |  |

唯一のユース ケースがデバイスからクラウドへのデータ インジェストである場合でも、IoT Hub は IoT デバイス接続用に設計されたサービスを提供するので、IoT Hub を使うことを強くお勧めします。 

### <a name="next-steps"></a>次の手順

IoT Hub の機能を詳しく調べるには、「[Azure IoT Hub 開発者ガイド](iot-hub-devguide.md)」をご覧ください。

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
