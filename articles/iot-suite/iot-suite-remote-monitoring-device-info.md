---
title: "リモート監視ソリューションのデバイス情報メタデータ | Microsoft Docs"
description: "Azure IoT リモート監視の事前構成済みソリューションとそのアーキテクチャの説明です。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: e41f5d5e6c5e1da8763c73978d2be9c7e61b8fff
ms.lasthandoff: 02/27/2017


---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>リモート監視構成済みソリューションのデバイス情報メタデータ
Azure IoT Suite リモート監視構成済みソリューションは、デバイス メタデータを管理する手法を示します。 この記事では、次のことがわかるように、このソリューションの手法について概説します。

* ソリューションが格納するデバイス メタデータ。
* ソリューションがデバイス メタデータを管理する方法。

## <a name="context"></a>Context
リモート監視構成済みソリューションでは、対象のデバイスからクラウドにデータを送信できるようにする [Azure IoT Hub][lnk-iot-hub] を使用します。 このソリューションでは、次の&3; つの異なる場所にデバイスに関する情報を格納します。

| 場所 | 格納される情報 | 実装 |
| -------- | ------------------ | -------------- |
| ID レジストリ | デバイス ID、認証キー、有効化の状態 | IoT Hub に組み込まれている |
| デバイス ツイン | メタデータ: 報告されるプロパティ、必要なプロパティ、タグ | IoT Hub に組み込まれている |
| DocumentDB | コマンドとメソッドの履歴 | ソリューション用のカスタム |

IoT Hub は、IoT Hub へのアクセスを管理する[デバイス ID レジストリ][lnk-identity-registry]を含み、[デバイス ツイン][lnk-device-twin]を使用して、デバイス メタデータを管理します。 また、コマンドとメソッドの履歴を格納する*デバイス レジストリ*は、リモート監視ソリューションに固有です。 リモート監視ソリューションでは、[DocumentDB][lnk-docdb] データベースを使用して、コマンドとメソッドの履歴のカスタム ストアを実装します。

> [!NOTE]
> リモート監視事前構成済みソリューションは、デバイス ID レジストリと DocumentDB データベース内の情報の同期状態を維持します。 どちらのレジストリも、同一のデバイス ID を使用して、対象の IoT Hub に接続された各デバイスを一意に識別します。
> 
> 

## <a name="device-metadata"></a>デバイス メタデータ
IoT Hub では、リモート監視ソリューションに接続されているシミュレートされたデバイスと物理デバイスごとに[デバイス ツイン][lnk-device-twin]を保持します。 このソリューションでは、デバイス ツインを使用して、デバイスに関連付けられているメタデータを管理します。 デバイス ツインは、IoT Hub によって保持される JSON ドキュメントです。このソリューションでは、IoT Hub API を使用して、デバイス ツインを操作します。

デバイス ツインには、次の&3; つの種類のメタデータが格納されます。

- *報告されるプロパティ*は、デバイスによって IoT Hub に送信されます。 リモート監視ソリューションでは、起動時と、**デバイス状態の変更**コマンドとメソッドへの応答時に、シミュレートされたデバイスから報告されるプロパティが送信されます。 報告されるプロパティは、ソリューション ポータルの **[デバイスの一覧]** と **[デバイスの詳細]** で確認できます。 報告されるプロパティは読み取り専用です。
- *必要なプロパティ*は、デバイスによって IoT Hub から取得されます。 デバイスに対して必要な構成の変更を行うのは、デバイスの役目です。 また、変更内容を報告されるプロパティとしてハブに報告するのもデバイスの役目です。 必要なプロパティの値は、ソリューション ポータルで設定できます。
- *タグ*は、デバイス ツインにのみ存在します。デバイスと同期されることはありません。 タグの値は、ソリューション ポータルで設定し、デバイスの一覧をフィルター処理するときに使用できます。 このソリューションでは、ソリューション ポータルでデバイスに対して表示するアイコンを特定するためにもタグを使用します。

シミュレートされたデバイスから報告されるプロパティの例としては、製造元、モデル番号、緯度、経度が挙げられます。 また、シミュレートされたデバイスは、報告されるプロパティとしてサポートされているメソッドの一覧も返します。

> [!NOTE]
> シミュレートされたデバイスのコードは、IoT Hub に返信される報告されるプロパティを更新するために、必要なプロパティである **Desired.Config.TemperatureMeanValue** と **Desired.Config.TelemetryInterval** のみを使用します。 他の必要なプロパティの変更要求はすべて無視されます。

デバイス レジストリ DocumentDB データベースに格納されるデバイス情報メタデータ JSON ドキュメントの構造は、次のようになっています。

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```


> [!NOTE]
> デバイス情報には、デバイスから IoT Hub に送信されたテレメトリを示すメタデータを含めることもできます。 リモート監視ソリューションでは、このテレメトリ メタデータを使用して、ダッシュボードでの[動的テレメトリ][lnk-dynamic-telemetry]の表示方法をカスタマイズできます。
> 
> 

## <a name="lifecycle"></a>ライフサイクル
ソリューション ポータルでデバイスを初めて作成すると、コマンドとメソッドの履歴を格納するエントリが DocumentDB データベース内に作成されます。 またこの時点で、デバイス ID レジストリのデバイスのエントリも作成されます。これにより、デバイスが IoT Hub での認証に使用するキーが生成されます。 デバイス ツインも作成されます。

初めてソリューションに接続するとき、デバイスは報告されるプロパティとデバイス情報メッセージを送信します。 報告されるプロパティの値は、デバイス ツインに自動的に保存されます。 報告されるプロパティには、デバイスの製造元、モデル番号、シリアル番号、およびサポートされているメソッドの一覧が含まれています。 デバイス情報メッセージには、すべてのコマンド パラメーターの情報が付属した、デバイスでサポートされているコマンドの一覧が含まれています。 このメッセージを受信すると、ソリューションは DocumentDB データベース内のデバイス情報を更新します。

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>ソリューション ポータルでのデバイス情報の表示と編集
ソリューション ポータルのデバイス一覧には、**[状態]**、**[DeviceId]**、**[製造元]**、**[モデル番号]**、**[シリアル番号]**、**[ファームウェア]**、**[プラットフォーム]**、**[プロセッサ]**、**[インストールされている RAM]** の各プロパティが、列として既定で表示されています。 列をカスタマイズするには、**[列エディター]** をクリックします。 **[緯度]** と **[経度]** のデバイス プロパティによって、ダッシュボードの Bing 地図内での位置が定まります。

![デバイス一覧の [列エディター]][img-device-list]

ソリューション ポータルの **[デバイスの詳細]** ウィンドウでは、必要なプロパティとタグを編集できます (報告されるプロパティは読み取り専用です)。

![[デバイスの詳細] ウィンドウ][img-device-edit]

ソリューション ポータルを使用して、ソリューションからデバイスを削除できます。 デバイスを削除すると、ID レジストリからデバイス エントリが削除され、次にデバイス ツインが削除されます。 また、DocumentDB データベースからデバイスに関連する情報も削除されます。 デバイスを削除するには、無効にしておく必要があります。

![デバイスの削除][img-device-remove]

## <a name="device-information-message-processing"></a>デバイス情報メッセージの処理
デバイスによって送信されるデバイス情報メッセージは、テレメトリ メッセージとは異なります。 デバイス情報メッセージには、デバイスが応答できるコマンドと、コマンドの履歴が含まれます。 IoT Hub 自体は、デバイス情報メッセージに含まれたメタデータを把握しません。このメッセージは、デバイスからクラウドへのメッセージを処理するのと同じ方法で処理されます。 リモート監視ソリューションでは、[Azure Stream Analytics][lnk-stream-analytics] (ASA) ジョブが IoT Hub からのメッセージを読み取ります。 **DeviceInfo** Stream Analytics ジョブは、**"ObjectType": "DeviceInfo"** が含まれたメッセージをフィルター処理し、Web ジョブで実行される **EventProcessorHost** ホスト インスタンスにそれらを転送します。 **EventProcessorHost** インスタンス内のロジックは、デバイス ID を使用して特定のデバイスの DocumentDB レコードを特定し、そのレコードを更新します。

> [!NOTE]
> デバイス情報メッセージは、標準的なデバイスからクラウドへのメッセージです。 このソリューションでは、ASA クエリを使用してデバイス情報メッセージとテレメトリ メッセージが区別されます。
> 
> 

## <a name="next-steps"></a>次のステップ
構成済みのソリューションをカスタマイズする方法を学習し終えたので、次のリンク先で IoT Suite の構成済みのソリューションのその他の機能のいくつかについて調べることができます。

* [予測的なメンテナンスの構成済みソリューションの概要][lnk-predictive-overview]
* [IoT スイートに関してよく寄せられる質問][lnk-faq]
* [IoT の徹底的なセキュリティ][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

