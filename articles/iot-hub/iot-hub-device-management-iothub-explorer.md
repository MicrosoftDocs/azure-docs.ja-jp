---
title: "iothub-explorer を使用した Azure IoT デバイス管理 | Microsoft Docs"
description: "Azure IoT Hub デバイス管理用の iothub-explorer CLI を使用します。このツールでは、ダイレクト メソッドとデバイス ツインの必要なプロパティを管理するためのオプションを使用できます。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Azure IoT デバイス管理, Azure IoT Hubデバイス管理, デバイス管理 IoT, IoT Hub デバイス管理"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 5b7a5057bdfb5920fbb5759bed1f5561cfa1d7e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Azure IoT Hub デバイス管理用の iothub-explorer を使用する

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) は、デバイス ID をIoT Hub レジストリで管理するためにホスト コンピューター上で実行する CLI ツールです。 さまざまなタスクを実行するために使用できる管理オプションが付属しています。

| 管理オプション          | タスク                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| ダイレクト メソッド             | メッセージの送信開始や停止、デバイスの再起動などの機能をデバイスに実行させます。                                        |
| デバイス ツインの必要なプロパティ    | デバイスを特定の状態にします (LED を緑に設定したり、テレメトリの送信間隔を 30 分に設定したりします)。         |
| デバイス ツインの報告されるプロパティ   | デバイスの報告される状態を取得します。 たとえば、デバイスは、現在 LED が点滅していることを報告します。                                    |
| デバイス ツインのタグ                  | デバイス固有のメタデータをクラウドに格納します。 例: 自動販売機の設置場所。                         |
| クラウドからデバイスへのメッセージ   | デバイスに通知を送信します。 例: "今日はほぼ雨が降ります。 必ず傘を持っていってください。"              |
| デバイス ツイン クエリ        | すべてのデバイス ツインをクエリして、任意の条件と一致するデバイスを取得します (例: 使用可能なデバイスを識別する)。 |

これらのオプションの相違点の詳細な説明と使用するためのガイダンスについては、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」と「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

> [!NOTE]
> デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 デバイス ツインの詳細については、「[デバイス ツインの使用](iot-hub-node-node-twin-getstarted.md)」を参照してください。

## <a name="what-you-learn"></a>学習内容

開発マシン上で iothub-explorer をさまざまな管理オプションで使用します。

## <a name="what-you-do"></a>作業内容

iothub-explorer をさまざまな管理オプションで実行します。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT Hub にメッセージを送信するクライアント アプリケーション。
- このチュートリアルの実行時にクライアント アプリケーションでデバイスが実行されていることを確認します。
- iothub-explorer (開発コンピューターに [iothub-explorer をインストールします](https://github.com/azure/iothub-explorer))。

## <a name="connect-to-your-iot-hub"></a>IoT Hub に接続する

次のコマンドを実行して、IoT Hub に接続します。

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>iothub-explorer をダイレクト メソッドで使用する

メッセージを IoT Hub に送信するには、次のコマンドを使用して、デバイス アプリの `start` メソッドを呼び出します。

```bash
iothub-explorer device-method <your device Id> start
```

メッセージの IoT Hub への送信を停止するには、次のコマンドを使用して、デバイス アプリの `stop` メソッドを呼び出します。

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>iothub-explorer とデバイス ツインの必要なプロパティを使用する

次のコマンドを実行して、目的のプロパティ interval = 3000 を設定します。

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

このプロパティをデバイスで読み取ることができます。

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>iothub-explorer とデバイス ツインの報告されるプロパティを使用する

次のコマンドを実行して、デバイスの報告されるプロパティを取得します。

```bash
iothub-explorer get-twin <your device id>
```

プロパティの 1 つに、このデバイスが最後にメッセージを送信または受信した時間を示す $metadata.$lastUpdated があります。

## <a name="use-iothub-explorer-with-twins-tags"></a>iothub-explorer とデバイス ツインのタグを使用する

次のコマンドを実行して、デバイスのタグとプロパティを取得します。

```bash
iothub-explorer get-twin <your device id>
```

次のコマンドを実行して、フィールド role = temperature&humidity をデバイスに追加します。

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"

```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>iothub-explorer と cloud-to-device メッセージを使用する

次のコマンドを実行して、"Hello World" メッセージをデバイスに送信します。

```bash
iothub-explorer send <device-id> "Hello World"
```

このコマンドを使用する現実的なシナリオについては、「[iothub-explorer を使用してデバイスと IoT Hub 間でメッセージを送受信する](iot-hub-explorer-cloud-device-messaging.md)」を参照してください。

## <a name="use-iothub-explorer-with-device-twins-queries"></a>iothub-explorer とデバイス ツインのクエリを使用する

次のコマンドを実行して、タグ role = 'temperature&humidity' のクエリを実行します。

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

次のコマンドを実行して、タグ role = 'temperature&humidity' 以外のすべてのデバイスのクエリを実行します。

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>次のステップ

iothub-explorer をさまざまな管理オプションで使用する方法を学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
