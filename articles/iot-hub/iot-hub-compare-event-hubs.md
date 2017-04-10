---
title: "Azure IoT Hub と Azure Event Hubs の比較 | Microsoft Docs"
description: "機能の相違点とユース ケースを中心とした、Azure の IoT Hub サービスと Event Hubs サービスの比較です。 比較には、サポートされているプロトコル、デバイス管理、監視、およびファイルのアップロードが含まれます。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 2075c7a1b8f3393e100ab92ae7d497c56965f887
ms.lasthandoff: 01/31/2017


---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT Hub と Azure Event Hubs の比較
デバイスからのテレメトリ データの収集は、IoT Hub の主なユース ケースの&1; つです。 このため、IoT Hub はしばしば [Azure Event Hubs][Azure Event Hubs] と比較されます。 IoT Hub のように、Event Hubs は、低遅延の動作と高い信頼性を確保しながら、クラウドに対する膨大なイベントとテレメトリ データの入口として利用できるイベント処理サービスです。

ただし、サービスには異なる点も多数あります。以下の表で、この違いについて詳しく説明します。

| 領域 | IoT Hub | Event Hubs |
| --- | --- | --- |
| 通信パターン | [デバイスからクラウドへの通信][lnk-d2c-guidance] (メッセージング、ファイルのアップロード、および報告されるプロパティ) と [クラウドからデバイスへの通信][lnk-c2d-guidance] (ダイレクト メソッド、必要なプロパティ、メッセージング) を有効にします。 |イベント イングレスのみを有効にします (通常はデバイスからクラウドへのシナリオの対象になります)。 |
| デバイスの状態情報 | [デバイス ツイン][lnk-twins]は、デバイスの状態情報を格納および照会できます。 | デバイスの状態情報を格納することはできません。 |
| デバイスのプロトコルのサポート |MQTT、MQTT over WebSockets、AMQP、AMQP over WebSockets、および HTTP をサポートします。 さらに、IoT Hub は [Azure IoT プロトコル ゲートウェイ][lnk-azure-protocol-gateway] (カスタム プロトコルをサポートする、カスタマイズ可能なプロトコル ゲートウェイの実装) でも動作します。 |AMQP、AMQP over WebSockets、および HTTP をサポートします。 |
| セキュリティ |デバイスごとの ID と取り消し可能なアクセス制御を提供します。 IoT Hub 開発者ガイドの「 [セキュリティ]」セクションを参照してください。 |Event Hubs 全体の[共有アクセス ポリシー][Event Hubs - security]を提供します。[発行元のポリシー][Event Hubs publisher policies]による取り消しが限定的にサポートされます。 IoT ソリューションでは、デバイスごとの資格情報となりすまし対策をサポートするために、カスタム ソリューションの実装が求められることがよくあります。 |
| 操作の監視 |デバイス ID 管理および接続の豊富なイベント (個々のデバイス認証エラー、スロットル、および不適切な形式の例外など) を、IoT ソリューションでサブスクライブできるようにします。 これらのイベントを使用すれば、個々のデバイス レベルで接続の問題をすばやく識別することができます。 |集計メトリックのみを公開します。 |
| スケール |同時接続された数百万のデバイスをサポートするように最適化されています。 |制限された数の同時接続をサポートできます。[Azure Service Bus のクォータ][Azure Service Bus quotas]に基づいて最大 5,000 の AMQP 接続をサポートします。 一方、Event Hubs を使用すると、送信される各メッセージのパーティションを指定できます。 |
| デバイスの SDK |MQTT、AMQP、および HTTP API のほかに、さまざまなプラットフォームおよび言語用の[デバイス SDK][Azure IoT SDKs] が用意されています。 |AMQP と HTTP の送信インターフェイスのほかに、.NET、Java、および C でサポートされます。 |
| ファイルのアップロード |IoT ソリューションでデバイスからクラウドにファイルをアップロードできるようにします。 ワークフロー統合用のファイル通知エンドポイントと、デバッグ サポート用の操作監視カテゴリが含まれます。 | サポートされていません。 |
| 複数エンドポイントへのメッセージのルーティング | 最大 10 個のカスタム エンドポイントがサポートされます。 カスタム エンドポイントへのメッセージのルーティングの方法はルールにより決定されます。 詳細については、「[IoT Hub でのメッセージの送受信][lnk-devguide-messaging]」を参照してください。 | メッセージ ディスパッチ用のコードを別途作成してホストする必要があります。 |

まとめると、唯一のユース ケースがデバイスからクラウドへのテレメトリ イングレスである場合でも、IoT Hub は IoT デバイス接続用に設計されたサービスを提供します。 IoT 固有の機能を使用するこれらのシナリオのための価値提案は、引き続き拡張されます。 Event Hubs は、大規模なイベント イングレス向けに設計されており、データ センター内およびデータ センター間の両方のシナリオに対応できます。

同じソリューションで IoT Hub と Event Hubs の両方を使用することはよくあります。 IoT Hub がデバイスからクラウドへの通信を処理し、Event Hubs が後半のリアルタイム処理エンジンへのイベント イングレスを処理します。

### <a name="next-steps"></a>次のステップ
IoT Hub のデプロイの計画に関する詳細については、[HA と DR のスケーリング][lnk-scaling]に関するページをご覧ください。

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[セキュリティ]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-what-is-event-hubs.md#event-publishers
[Azure Service Bus quotas]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md

