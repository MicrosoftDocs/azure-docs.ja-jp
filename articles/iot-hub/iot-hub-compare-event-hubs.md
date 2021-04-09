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
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 56bb179c50862f09f1b789c359db97976017bbb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142758"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT デバイスを Azure に接続する: IoT Hub と Event Hubs

Azure では、データとクラウドの機能をつなぐ助けとなるように、さまざまな種類の接続と通信のために特に開発されたサービスを提供しています。 Azure IoT Hub と Azure Event Hubs はどちらも、大量のデータを取り込み、そのデータを処理または格納してビジネス上の分析情報を得ることができるクラウド サービスです。 この 2 つのサービスは、短い待ち時間と高い信頼性を備えたデータの取り込みをサポートしている点では同様ですが、異なる目的のために設計されています。 IoT Hub が、IoT デバイスを Azure クラウドに接続するという固有の要件に対応するために開発されたのに対して、Event Hubs は、ビッグ データのストリーミング用に設計されました。 マイクロソフトでは、Azure IoT Hub を使用して IoT デバイスを Azure に接続することをお勧めしています。

Azure IoT Hub は、IoT デバイスを接続してデータを収集し、ビジネスの分析情報と自動化を推進するためのクラウド ゲートウェイです。 さらに、IoT Hub には、デバイスとバックエンド システム間のリレーションシップを強化する機能が含まれています。 双方向通信機能は、デバイスからデータを受信している間でも、デバイスにコマンドとポリシーを返送できることを意味します。 たとえば、cloud-to-device メッセージングを使用して、プロパティを更新したりデバイス管理操作を起動したりします。 cloud-to-device 通信では、Azure IoT Edge を使ってクラウド インテリジェンスをエッジ デバイスに送信することも可能です。 IoT Hub によって提供されるデバイス レベルの一意の ID は、潜在的な攻撃からの IoT ソリューションのセキュリティ保護を強化する助けになります。 

[Azure Event Hubs](../event-hubs/event-hubs-about.md) は、Azure のビッグ データ ストリーミング サービスです。 これは、ユーザーが 1 日あたり何十億もの要求を送信する可能性がある高スループットのデータ ストリーミング シナリオに向けて設計されています。 Event Hubs は、パーティション分割されたコンシューマー モデルを使用してストリームをスケールアウトし、ビッグ データと、Databricks、Stream Analytics、ADLS、HDInsight を含む Azure の分析サービスに統合されます。 Event Hubs Capture や自動インフレのような機能を備えたこのサービスは、ビッグ データのアプリとソリューションをサポートするために設計されています。 さらに、IoT Hub ではテレメトリ フロー パスのために Event Hubs を利用するので、IoT ソリューションでも、Event Hubs の強力な処理機能のメリットを享受できます。

要約すると、どちらのソリューションも大量のデータ インジェストに対応する設計になっています。 IoT Hub のみが、IoT デバイスを Azure クラウドに接続するというビジネス価値を最大化するように設計された、IoT 固有の豊富な機能を備えています。  IoT への取り組みを開始したばかりの場合は、データ インジェストのシナリオをサポートするために IoT Hub から着手することで、ビジネス面や技術面のニーズによって必要になり次第、確実に、多彩な IoT 機能をすばやく利用できるようになります。

次の表では、IoT 機能を評価するときのために、IoT Hub の 2 つの階層と Event Hubs の詳細な比較を示します。 IoT Hub の Standard レベルと Basic レベルについて詳しくは、[適切な IoT Hub レベルを選択する方法](iot-hub-scaling.md)に関するページをご覧ください。

| IoT の機能 | IoT Hub Standard レベル | IoT Hub Basic レベル | Event Hubs |
| --- | --- | --- | --- |
| デバイスからクラウドへのメッセージ | ![○][checkmark] | ![○][checkmark] | ![○][checkmark] |
| プロトコル: HTTPS、AMQP、AMQP over webSockets | ![○][checkmark] | ![○][checkmark] | ![○][checkmark] |
| プロトコル: MQTT、MQTT over webSockets | ![○][checkmark] | ![○][checkmark] |  |
| デバイスごとの ID | ![○][checkmark] | ![○][checkmark] |  |
| 複数デバイスからのファイルのアップロード | ![○][checkmark] | ![○][checkmark] |  |
| デバイス プロビジョニング サービス | ![○][checkmark] | ![○][checkmark] |  |
| クラウドからデバイスへのメッセージ | ![○][checkmark] |  |  |
| デバイス ツインとデバイス管理 | ![○][checkmark] |  |  |
| デバイス ストリーム (プレビュー) | ![○][checkmark] |  |  |
| IoT Edge | ![○][checkmark] |  |  |

唯一のユース ケースがデバイスからクラウドへのデータ インジェストである場合でも、IoT Hub は IoT デバイス接続用に設計されたサービスを提供するので、IoT Hub を使うことを強くお勧めします。 

### <a name="next-steps"></a>次のステップ

IoT Hub の機能を詳しく調べるには、「[Azure IoT Hub 開発者ガイド](iot-hub-devguide.md)」をご覧ください。

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png