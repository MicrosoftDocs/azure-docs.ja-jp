---
title: "Azure IoT Hub device-to-cloud オプション | Microsoft Docs"
description: "開発者ガイド - device-to-cloud メッセージ、報告されるプロパティ、または cloud-to-device 通信のファイルのアップロードを使用するタイミングに関するガイダンスです。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: aa3704fe844a41fef22b8cdd35838c68aebc7752
ms.lasthandoff: 03/10/2017


---
# <a name="device-to-cloud-communications-guidance"></a>device-to-cloud 通信に関するガイダンス
デバイス アプリからソリューション バック エンドに情報を送信する場合、IoT Hub では、次の&3; つのオプションを公開します。

* [device-to-cloud メッセージ][lnk-d2c]: 時系列のテレメトリとアラート用。
* [報告されるプロパティ][lnk-twins]: 使用できる機能、状態、実行時間の長いワークフローの状態など、デバイスの状態情報のレポート用 (例: 構成とソフトウェアの更新)。 For example, configuration and software updates.
* [ファイルのアップロード][lnk-fileupload]: 断続的に接続されるデバイスによってアップロードされたり、帯域幅を節約するために圧縮されているメディア ファイルと大容量のテレメトリ バッチ用。

さまざまな device-to-cloud 通信オプションの詳細な比較を次に示します。

|  | デバイスからクラウドへのメッセージ | 報告されるプロパティ | ファイルのアップロード |
| ---- | ------- | ---------- | ---- |
| シナリオ | テレメトリの時系列と警告。 たとえば、256 KB のセンサー データをひとまとめにして 5 分間隔で送信します。 | 利用できる機能や条件。 たとえば現在のデバイス接続モード (携帯ネットワークか WiFi かなど) を報告します。 構成やソフトウェア更新などの実行時間の長いワークフローと同期します。 | メディア ファイル。 大規模な (通常は圧縮された) テレメトリ バッチ。 |
| 格納と取得 | IoT Hub によって、最大 7 日間、一時的に格納されます。 シーケンシャルな読み取りのみ可能です。 | IoT Hub によってデバイス ツインに格納されます。 [IoT Hub クエリ言語][lnk-query]を使用して取得できます。 | ユーザー指定の Azure Storage アカウントに格納されます。 |
| サイズ | 最大 256 KB のメッセージ。 | 報告されるプロパティの最大サイズは 8 KB。 | Azure Blob Storage によってサポートされるファイルの最大サイズ。 |
| 頻度 | 高。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 | 中。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 | 低。 詳細については、[IoT Hub の制限][lnk-quotas]に関するページを参照してください。 |
| プロトコル | すべてのプロトコルで使用できます。 | 現時点では、MQTT を使用する場合のみ使用できます。 | どのプロトコルでも使用できますが、デバイス上に HTTP が必要です。 |

アプリケーションは、情報を時系列のテレメトリまたはアラートとして送信することと、デバイス ツインで使用できるようにすることの両方を要求できます。 このシナリオでは、次のいずれかの方法を選ぶことができます。

* デバイス アプリが device-to-cloud メッセージを送信し、プロパティの変更を報告する。 
* または、ソリューション バックエンドが、メッセージを受信したときにデバイス ツインのタグに情報を格納する。 

device-to-cloud メッセージのほうがデバイス ツインの更新よりもはるかにスループットが高いため、すべての device-to-cloud メッセージに対してデバイス ツインを更新することは避けることをお勧めします。


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages

