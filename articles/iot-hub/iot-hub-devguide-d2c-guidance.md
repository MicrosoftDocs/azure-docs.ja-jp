---
title: Azure IoT Hub device-to-cloud オプション | Microsoft Docs
description: 開発者ガイド - device-to-cloud メッセージ、報告されるプロパティ、または cloud-to-device 通信のファイルのアップロードを使用するタイミングに関するガイダンスです。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd4f4ad7f84ebc1f5e254843e7afa4aa0f3f224f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87319135"
---
# <a name="device-to-cloud-communications-guidance"></a>device-to-cloud 通信に関するガイダンス

デバイス アプリからソリューション バック エンドに情報を送信する場合、IoT Hub では、次の 3 つのオプションを公開します。

* [device-to-cloud メッセージ](iot-hub-devguide-messages-d2c.md): 時系列のテレメトリとアラート用。

* [デバイス ツインの報告されるプロパティ](iot-hub-devguide-device-twins.md): 使用できる機能、状態、実行時間の長いワークフローの状態など、デバイスの状態情報のレポート用。 たとえば、構成とソフトウェアの更新など。

* [ファイルのアップロード](iot-hub-devguide-file-upload.md): 断続的に接続されるデバイスによってアップロードされたり、帯域幅を節約するために圧縮されているメディア ファイルと大容量のテレメトリ バッチ用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

さまざまな device-to-cloud 通信オプションの詳細な比較を次に示します。

| 要素 | デバイスからクラウドへのメッセージ | デバイス ツインの報告されるプロパティ | ファイルのアップロード |
| ---- | ------- | ---------- | ---- |
| シナリオ | テレメトリの時系列と警告。 たとえば、256 KB のセンサー データをひとまとめにして 5 分間隔で送信します。 | 利用できる機能や条件。 たとえば現在のデバイス接続モード (携帯ネットワークか WiFi かなど) を報告します。 構成やソフトウェア更新などの実行時間の長いワークフローと同期します。 | メディア ファイル。 大規模な (通常は圧縮された) テレメトリ バッチ。 |
| 格納と取得 | IoT Hub によって、最大 7 日間、一時的に格納されます。 シーケンシャルな読み取りのみ可能です。 | IoT Hub によってデバイス ツインに格納されます。 [IoT Hub クエリ言語](iot-hub-devguide-query-language.md)を使用して取得できます。 | ユーザー指定の Azure Storage アカウントに格納されます。 |
| サイズ | 最大 256 KB のメッセージ。 | レポートされるプロパティの最大サイズは 32 KB です。 | Azure Blob Storage によってサポートされるファイルの最大サイズ。 |
| 頻度 | 高。 詳細については、[IoT Hub の制限](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。 | 中。 詳細については、[IoT Hub の制限](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。 | 低。 詳細については、[IoT Hub の制限](iot-hub-devguide-quotas-throttling.md)に関するページを参照してください。 |
| Protocol | すべてのプロトコルで使用できます。 | MQTT または AMQP を使用して利用できます。 | どのプロトコルでも使用できますが、デバイス上に HTTPS が必要です。 |

アプリケーションは、情報を時系列のテレメトリまたはアラートとして送信することと、デバイス ツインで使用できるようにすることの両方を要求できます。 このシナリオでは、次のいずれかの方法を選ぶことができます。

* デバイス アプリが device-to-cloud メッセージを送信し、プロパティの変更を報告する。
* ソリューション バックエンドが、メッセージを受信したときにデバイス ツインのタグに情報を格納する。

device-to-cloud メッセージのほうがデバイス ツインの更新よりもはるかにスループットが高いため、すべての device-to-cloud メッセージに対してデバイス ツインを更新することは避けることをお勧めします。
