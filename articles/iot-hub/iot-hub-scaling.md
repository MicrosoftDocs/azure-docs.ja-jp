---
title: Azure IoT Hub のスケーリング | Microsoft Docs
description: 期待されるメッセージ スループットと望ましい機能をサポートするために IoT Hub をスケーリングする方法です。 階層ごとにサポートされているスループットとシャーディングのオプションの概要が含まれます。
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: ea7b38f509fcdaa4e41ce17db3beca44b05a59b2
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514484"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>ソリューションに適した IoT Hub のレベルを選択する

すべての IoT ソリューションは異なるので、Azure IoT Hub には価格とスケールに基づいて複数のオプションが用意されています。 この記事は、IoT Hub のニーズを評価するときに役立ちます。 IoT Hub の各レベルの価格については、「[IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub)」をご覧ください。

ソリューションに適した IoT Hub のレベルを決定するには、次の 2 つの質問に答えてください。

**どのような機能を使う予定か?**

Azure IoT Hub には Basic と Standard の 2 つのレベルがあり、サポートする機能の数が異なります。 IoT ソリューションが主にデバイスからデータを収集して集中的に分析するものである場合は、おそらく Basic レベルが適しています。 さらに高度な構成を使って、IoT デバイスをリモート制御したり、ワークロードの一部をデバイス自体に分散させたりしたい場合は、Standard レベルを検討する必要があります。 各レベルに含まれる機能について詳しくは、後の「[Basic レベルと Standard レベル](#basic-and-standard-tiers)」をご覧ください。

**移動するデータの 1 日当たりの量はどれくらいか?**

各 IoT Hub レベルは、1 日に処理できるデータ スループットの量に基づいて、3 つのサイズで利用できます。 これらのサイズは、1、2、3 という数字で示されます。 たとえば、レベル 1 の IoT Hub の各ユニットは 1 日に 40 万件のメッセージを処理できるのに対し、レベル 3 のユニットは 3 億件を処理できます。 データのガイドラインについて詳しくは、後の「[メッセージのスループット](#message-throughput)」をご覧ください。

## <a name="basic-and-standard-tiers"></a>Basic レベルと Standard レベル

IoT Hub の Standard レベルではすべての機能が有効になり、双方向通信機能を利用する IoT ソリューションの場合はこのレベルが必要です。 Basic レベルでは機能のサブセットが有効になり、デバイスからクラウドへの単方向通信だけが必要な IoT ソリューション用です。 提供されるセキュリティ機能と認証機能は、どちらのレベルも同じです。

1 つのレベル内の[エディション](https://azure.microsoft.com/pricing/details/iot-hub/)は IoT Hub あたり 1 種類だけ選択できます。 たとえば、S1 と B3 や S1 と S2 などの異なるエディションのユニットの組み合わせではなく、S1 のユニットを複数持つ IoT Hub を作成できます。

| 機能 | Basic レベル | Free または Standard レベル |
| ---------- | ---------- | ------------- |
| [device-to-cloud テレメトリ](iot-hub-devguide-messaging.md) | はい | はい |
| [デバイスごとの ID](iot-hub-devguide-identity-registry.md) | はい | はい |
| [メッセージ ルーティング](iot-hub-devguide-messages-read-custom.md)と [Event Grid の統合](iot-hub-event-grid.md) | はい | はい |
| [HTTP、AMQP、MQTT プロトコル](iot-hub-devguide-protocols.md) | はい | はい |
| [Device Provisioning Service](../iot-dps/about-iot-dps.md) | はい | はい |
| [監視と診断](iot-hub-monitor-resource-health.md) | はい | はい |
| [cloud-to-device メッセージング](iot-hub-devguide-c2d-guidance.md) |   | はい |
| [デバイス ツイン](iot-hub-devguide-device-twins.md)、[モジュール ツイン](iot-hub-devguide-module-twins.md)、および[デバイス管理](iot-hub-device-management-overview.md) |   | はい |
| [デバイス ストリーム (プレビュー)](iot-hub-device-streams-overview.md) |   | はい |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | はい |

IoT Hub では、テストおよび評価のための Free レベルも提供されています。 Free レベルは、機能的には Standard レベルと同じですが、メッセージングの許容量が限られます。 Free レベルから Basic レベルまたは Standard レベルにアップグレードすることはできません。

## <a name="partitions"></a>パーティション

Azure IoT Hub には、[パーティション](../event-hubs/event-hubs-features.md#partitions)を含む [Azure Event Hubs](../event-hubs/event-hubs-features.md) の多数のコア コンポーネントが含まれています。 IoT Hubs のイベント ストリームには、通常はさまざまな IoT デバイスによって報告される受信テレメトリ データが入力されます。 イベント ストリームに対する読み取りと書き込みが同時に行われるときに発生する競合を削減するために、イベント ストリームのパーティション分割が使用されます。

パーティション数の制限は IoT Hub の作成時に選択され、変更することはできません。 Basic レベルの IoT Hub と Standard レベルの IoT Hub のパーティションの上限は 32 個であることにご注意ください。 ほとんどの IoT Hub では、4 つのパーティションのみ必要です。 パーティション数を決定する方法の詳細については、Event Hubs の FAQ ([パーティションはいくつ必要ですか](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)) を参照してください。

## <a name="tier-upgrade"></a>レベルのアップグレード

IoT Hub の作成後に、既存の操作を中断することなく、Basic レベルから Standard レベルにアップグレードできます。 詳しくは、「[IoT Hub のアップグレード方法](iot-hub-upgrade.md)」をご覧ください。

Basic レベルから Standard レベルに移行しても、パーティション構成は変わりません。

> [!NOTE]
> Free レベルでは、Basic または Standard へのアップグレードはサポートされません。

## <a name="iot-hub-rest-apis"></a>IoT Hub の REST API

IoT Hub の Basic レベルと Standard レベルでサポートされる機能が違うことは、一部の API 呼び出しが Basic レベルのハブでは動作しないことを意味します。 次の表では、利用できる API を示します。

| API | Basic レベル | Free または Standard レベル |
| --- | ---------- | ------------- |
| [デバイスの削除](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | はい | はい |
| [デバイスの取得](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | はい | はい |
| [モジュールの削除](https://docs.microsoft.com/rest/api/iothub/service/deletemodule) | はい | はい |
| [モジュールの取得](https://docs.microsoft.com/rest/api/iothub/service/getmodule) | はい | はい |
| [レジストリの統計情報の取得](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | はい | はい |
| [サービスの統計情報の取得](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | はい | はい |
| [デバイスの作成または更新](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | はい | はい |
| [モジュールの作成または更新](https://docs.microsoft.com/rest/api/iothub/service/createorupdatemodule) | はい | はい |
| [IoT Hub にクエリを実行する](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | はい | はい |
| [ファイル アップロード SAS URI の作成](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | はい | はい |
| [デバイス バインド通知の受け取り](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | はい | はい |
| [デバイス イベントの送信](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | はい | はい |
| モジュール イベントの送信 | AMQP と MQTT のみ | AMQP と MQTT のみ |
| [ファイル アップロード ステータスの更新](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | はい | はい |
| [デバイスの一括操作](https://docs.microsoft.com/rest/api/iothub/service/bulkcreateorupdatedevices) | はい (IoT Edge 機能を除く) | はい |
| [コマンド キューの消去](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | はい |
| [デバイス ツインの取得](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | はい |
| [モジュール ツインの取得](https://docs.microsoft.com/rest/api/iothub/service/getmoduletwin) |   | はい |
| [デバイス メソッドの呼び出し](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | はい |
| [デバイス ツインの更新](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | はい |
| [モジュール ツインの更新](https://docs.microsoft.com/rest/api/iothub/service/updatemoduletwin) |   | はい |
| [デバイス バインド通知の破棄](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | はい |
| [デバイス バインド通知の完了](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | はい |
| [ジョブの取り消し](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | はい |
| [ジョブの作成](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | はい |
| [ジョブの取得](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | はい |
| [ジョブのクエリ](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | はい |

## <a name="message-throughput"></a>メッセージのスループット

IoT Hub ソリューションのサイズを設定する方法として最適なのは、ユニットごとにトラフィックを評価することです。 具体的には、次の操作カテゴリに対して必要なピーク スループットを検討してください。

* デバイスからクラウドへのメッセージ
* クラウドからデバイスへのメッセージ
* ID レジストリの操作

トラフィックは、ハブ単位ではなく、ユニット単位で測定されます。 レベル 1 または 2 の IoT Hub インスタンスには、最大で 200 個のユニットを関連付けることができます。 レベル 3 の IoT Hub インスタンスは、最大で 10 個のユニットを持つことができます。 IoT Hub を作成した後は、既存の操作を中断することなく、ユニットの数を変更したり、特定のレベル内で 1、2、3 のサイズ間を移動したりできます。 詳しくは、「[IoT Hub のアップグレード方法](iot-hub-upgrade.md)」をご覧ください。

各レベルのトラフィック機能の例として、device-to-cloud メッセージは以下の持続スループット ガイドラインに従います。

| レベル | 持続スループット | 持続送信レート |
| --- | --- | --- |
| B1、S1 |ユニットあたり最大 1111 KB/分<br/>(1.5 GB/日/ユニット) |ユニットあたり平均 278 メッセージ/分<br/>(ユニットあたり 400,000 メッセージ/日) |
| B2、S2 |ユニットあたり最大 16 MB/分<br/>(22.8 GB/日/ユニット) |ユニットあたり平均 4,167 メッセージ/分<br/>(ユニットあたり 600 万メッセージ/日) |
| B3、S3 |ユニットあたり最大 814 MB/分<br/>(1144.4 GB/日/ユニット) |ユニットあたり平均 208,333 メッセージ/分<br/>(ユニットあたり 3 億件のメッセージ/日) |

このスループット情報に加え、「 [IoT Hub のクォータとスロットル](iot-hub-devguide-quotas-throttling.md) に関するページを参照して、適切にソリューションを設計してください。

### <a name="identity-registry-operation-throughput"></a>ID レジストリ操作のスループット

IoT Hub の ID レジストリの操作は、ほとんどがデバイス プロビジョニングに関連しているため、ランタイム操作にはなりません。

特定のバースト パフォーマンスの数値については、 [IoT Hub のクォータとスロットル](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。

## <a name="auto-scale"></a>自動スケール

お使いの IoT Hub で許可されているメッセージ上限に到達しそうな場合、[自動スケーリングの手順](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/)を使用して、同じ IoT Hub レベルにある IoT Hub ユニットを増分します。

## <a name="sharding"></a>シャーディング

1 つの IoT Hub を数百万のデバイスに拡張できますが、使用しているソリューションに、1 つの IoT Hub では保証できない特定のパフォーマンス特性が必要になる場合があります。 その場合は、複数の IoT ハブにデバイスをパーティション分割できます。 複数の IoT hub は、トラフィックの急増を抑え、必要なスループットまたは必要とされる操作の速度を取得します。

## <a name="next-steps"></a>次の手順

* IoT Hub の機能とパフォーマンスの詳細については、「[IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub)」または「[IoT Hub のクォータと調整](iot-hub-devguide-quotas-throttling.md)」を参照してください。

* IoT Hub のレベルを変更する方法については、「[IoT Hub のアップグレード方法](iot-hub-upgrade.md)」をご覧ください。
