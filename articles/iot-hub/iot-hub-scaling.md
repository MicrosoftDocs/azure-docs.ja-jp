---
title: "Azure IoT Hub のスケーリング | Microsoft Docs"
description: "期待されるメッセージ スループットをサポートするために IoT Hub をスケーリングする方法 階層ごとにサポートされているスループットとシャーディングのオプションの概要が含まれます。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: elioda
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: cbff931e7acc88b29ed6f51a16156b44c1596d3f
ms.lasthandoff: 03/02/2017


---
# <a name="scale-your-iot-hub-solution"></a>IoT Hub ソリューションのスケーリング
Azure IoT Hub は、100 万台のデバイスの同時接続をサポートできます。 詳細については、[IoT Hub の価格][lnk-pricing]に関するページを参照してください。 各 IoT Hub ユニットでは、毎日一定数のメッセージが許可されます。

ソリューションを適切に調整するには、IoT Hub の個別の用途を考慮します。 具体的には、次の操作カテゴリに対して必要なピーク スループットを検討してください。

* デバイスからクラウドへのメッセージ
* クラウドからデバイスへのメッセージ
* ID レジストリの操作

このスループット情報に加え、[IoT Hub のクォータとスロットル][IoT Hub quotas and throttles]に関するページを参照して、適切にソリューションを設計してください。

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>デバイスからクラウドおよびクラウドからデバイスへのメッセージのスループット
IoT Hub ソリューションのサイズを設定する方法として最適なのは、ユニットごとにトラフィックを評価することです。

デバイスからクラウドへのメッセージは、次の持続スループット ガイドラインに従います。

| レベル | 持続スループット | 持続送信レート |
| --- | --- | --- |
| S1 |ユニットあたり最大 1111 KB/分<br/>(1.5 GB/日/ユニット) |ユニットあたり平均 278 メッセージ/分<br/>(ユニットあたり&400;,000 メッセージ/日) |
| S2 |ユニットあたり最大 16 MB/分<br/>(22.8 GB/日/ユニット) |ユニットあたり平均 4,167 メッセージ/分<br/>(ユニットあたり&600; 万メッセージ/日) |
| S3 |ユニットあたり最大 814 MB/分<br/>(1144.4 GB/日/ユニット) |ユニットあたり平均 208,333 メッセージ/分<br/>(ユニットあたり&3; 億件のメッセージ/日) |

## <a name="identity-registry-operation-throughput"></a>ID レジストリ操作のスループット
IoT Hub の ID レジストリの操作は、ほとんどがデバイス プロビジョニングに関連しているため、ランタイム操作にはなりません。

特定のバースト パフォーマンスの数値については、[IoT Hub のクォータと調整][IoT Hub quotas and throttles]に関するページを参照してください。

## <a name="sharding"></a>シャーディング
1 つの IoT Hub を数百万のデバイスに拡張できますが、使用しているソリューションに、1 つの IoT Hub では保証できない特定のパフォーマンス特性が必要になる場合があります。 その場合は、デバイスを複数の IoT hub にパーティション分割することをお勧めします。 複数の IoT hub は、トラフィックの急増を抑え、必要なスループットまたは必要とされる操作の速度を取得します。

## <a name="next-steps"></a>次のステップ
IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

