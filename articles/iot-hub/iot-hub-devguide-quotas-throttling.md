---
title: "Azure IoT Hub クォータと調整について | Microsoft Docs"
description: "開発者ガイド - IoT Hub に適用されるクォータと予想される調整動作の説明。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: dfe06ee6b8fadfe6d34397661c974181f870239e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="reference---iot-hub-quotas-and-throttling"></a>参照 - IoT Hub のクォータと調整

## <a name="quotas-and-throttling"></a>クォータと調整
各 Azure サブスクリプションに最大 10 個の IoT Hub と最大 1 個の Free ハブを割り当てることができます。

各 IoT Hub は、特定の SKU のユニット数でプロビジョニングされます (詳細については、「[Azure IoT Hub の価格][lnk-pricing]」を参照してください)。 SKU とユニット数により、送信できるメッセージの1 日あたりの最大クォータが決定されます。

また、IoT Hub がすべての操作に適用するスロットル制限は、SKU によっても決まります。

## <a name="operation-throttles"></a>操作のスロットル
操作のスロットルは、ごく限られた範囲に適用される、不正使用を回避するためのレート制限です。 IoT Hub は、可能な限りエラーを返さないようにしようとしますが、長時間にわたりスロットル違反が続く場合は、例外が返され始めます。

次の表は、適用されるスロットルを示しています。 値は個々のハブのものです。

| スロットル | Free および S1 ハブ | S2 ハブ | S3 ハブ | 
| -------- | ------- | ------- | ------- |
| ID レジストリの操作 (作成、取得、一覧表示、更新、削除) | 1.67/sec/unit (100/分/単位) | 1.67/sec/unit (100/分/単位) | 83.33/sec/unit (5000/分/単位) |
| デバイスの接続 | 最大 100/秒または 12/秒/ユニット <br/> たとえば、2 ユニットの S1 では 2 \* 12 = 24/秒ですが、ユニット全体では少なくとも 100/秒となります。 9 ユニットの S1 の場合、ユニット全体で 108/秒 (9 \* 12) となります。 | 120/秒/ユニット | 6000/秒/ユニット |
| デバイスからクラウドへの送信 | 最大 100/秒または 12/秒/ユニット <br/> たとえば、2 ユニットの S1 では 2 \* 12 = 24/秒ですが、ユニット全体では少なくとも 100/秒となります。 9 ユニットの S1 の場合、ユニット全体で 108/秒 (9 \* 12) となります。 | 120/秒/ユニット | 6000/秒/ユニット |
| クラウドからデバイスへの送信 | 1.67/sec/unit (100/分/単位) | 1.67/sec/unit (100/分/単位) | 83.33/sec/unit (5000/分/単位) |
| クラウドからデバイスへの受信 <br/> (デバイスが HTTP を使用する場合のみ)| 16.67/sec/unit (1000/分/単位) | 16.67/sec/unit (1000/分/単位) | 833.33/sec/unit (50000/分/単位) |
| ファイルのアップロード | 1.67 ファイル アップロード通知/秒/単位 (100/分/単位) | 1.67 ファイル アップロード通知/秒/単位 (100/分/単位) | 83.33 ファイル アップロード通知/秒/単位 (5000/分/単位) |
| ダイレクト メソッド | 20/秒/ユニット | 60/秒/ユニット | 3,000/秒/ユニット | 
| デバイス ツインの読み取り | 10/秒 | 最大 10 数/秒または 1/秒/ユニット | 50/秒/ユニット |
| デバイス ツインの更新 | 10/秒 | 最大 10 数/秒または 1/秒/ユニット | 50/秒/ユニット |
| ジョブ操作 <br/> (作成、更新、一覧表示、削除) | 1.67/sec/unit (100/分/単位) | 1.67/sec/unit (100/分/単位) | 83.33/sec/unit (5000/分/単位) |
| デバイスごとのジョブの操作のスループット | 10/秒 | 最大 10 数/秒または 1/秒/ユニット | 50/秒/ユニット |

"*デバイスの接続*" スロットルは、IoT Hub を使用して新しいデバイス接続を確立できる速度を制御するものであるということを明確にしておくことが重要です。 "*デバイスの接続*" スロットルでは、同時に接続されるデバイスの最大数は制御されません。 スロットルは、IoT Hub にプロビジョニングされたユニット数に依存します。

たとえば、1 つの S1 ユニットを購入した場合、1 秒あたり 100 接続のスロットルを利用できます。 したがって、100,000 個のデバイスに接続するには、少なくとも 1,000 秒 (約 16 分) かかります。 ただし、ID レジストリに登録されたデバイスの数だけ、同時に接続されたデバイスを持つことができます。

IoT Hub スロットルの動作の詳細については、ブログ投稿「[IoT Hub throttling and you (IoT Hub スロットルの操作)][lnk-throttle-blog]」を参照してください。

> [!NOTE]
> 任意の時点で、IoT Hub にプロビジョニングされたユニットを増やすことで、クォータやスロットルの制限値を増やすことができます。
> 
> [!IMPORTANT]
> ID レジストリの操作は、デバイスの管理とプロビジョニングのシナリオにおける実行時の使用を目的としています。 多数のデバイス ID の読み取りまたは更新は、[ジョブのインポートとエクスポート][lnk-importexport]によってサポートされています。
> 
> 

## <a name="other-limits"></a>その他の制限

IoT Hub により、その他の運用上の制限が適用されます。

| 操作 | 制限 |
| --------- | ----- |
| ファイルのアップロード URI | 1 つのストレージ アカウントに対して 10000 個の SAS URI を一度に提供できます。 <br/> デバイスあたり 10 個の SAS URI を一度に提供できます。 |
| [ジョブ] | ジョブ履歴は、最大で 30 日間保持されます。 <br/> 同時ジョブの最大数は 1 (Free および S1)、5 (S2)、10 (S3) です。 |
| エンドポイントの追加 | 有料の SKU ハブには、エンドポイントを 10 個追加できます。 無料の SKU ハブには、エンドポイントを 1 個追加できます。 |
| メッセージ ルーティング ルール | 有料の SKU ハブには、100 個のルーティング ルールを設定できます。 無料の SKU ハブには、5 個のルーティング ルールを設定できます。 |
| デバイスからクラウドへのメッセージ | 最大メッセージ サイズは 256 KB |
| クラウドからデバイスへのメッセージ | 最大メッセージ サイズは 64 KB |
| クラウドからデバイスへのメッセージ | 配信の保留中のメッセージの最大数は 50 |

> [!NOTE]
> 現時点では、1 つの IoT ハブに接続できるデバイスの最大数は、500,000 です。 この制限を引き上げるには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="latency"></a>待機時間
IoT Hub は、すべての操作の待機時間を短くするように努めています。 ただし、ネットワークの状態およびその他の予測不能な要因のため、最大待機時間を保証することはできません。 ソリューションを設計するときに、次のようなことを行う必要があります。

* どの IoT Hub 操作についても最大待機時間を想定しない。
* IoT Hub は、デバイスに最も近い Azure リージョンにプロビジョニングする。
* デバイスまたはデバイスに最も近いゲートウェイで Azure IoT Edge を使用して待機時間に依存する操作を実行することを検討する。

複数の IoT Hub ユニットは、前に述べたように調整に影響を与えますが、待機時間に関する追加のメリットや保証が提供されることはありません。
操作の待機時間が予想外に増加した場合は、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="next-steps"></a>次のステップ
この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-devguide-endpoints]
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-devguide-query]
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

