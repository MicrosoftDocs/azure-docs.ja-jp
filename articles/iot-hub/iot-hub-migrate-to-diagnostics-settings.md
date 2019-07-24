---
title: Azure IoT Hub を診断設定に移行する | Microsoft Docs
description: IoT ハブに対する操作の状態をリアルタイムで監視するために、操作の監視の代わりに Azure Diagnostics の設定を使用するように Azure IoT Hub を更新する方法。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: b6cde8402c699a7477cd0efc79a44b3f5e150ad0
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792652"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>IoT Hub を操作の監視から診断設定に移行する

[操作の監視](iot-hub-operations-monitoring.md)を使用して IoT Hub での操作の状態を追跡するユーザーは、そのワークフローを、Azure Monitor の機能である [Azure 診断設定](../azure-monitor/platform/diagnostic-logs-overview.md)に移行することができます。 診断設定は、多数の Azure サービスについてリソース レベルの診断情報を提供します。

**IoT Hub の操作の監視機能は非推奨となっており**、今後 Portal から削除される予定です。 この記事では、ワークロードを操作の監視から診断設定に移動する手順について説明します。 廃止のスケジュールについて詳しくは、「[Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)」(Azure Monitor および Azure Resource Health による Azure IoT ソリューションの監視) をご覧ください。

## <a name="update-iot-hub"></a>IoT Hub の更新

Azure Portal で IoT Hub を更新するには、まず診断設定を有効にし、次に操作の監視を無効にします。  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>操作の監視を無効にする

> [!NOTE]
> 2019 年 3 月 11 日時点で、操作の監視機能は IoT Hub の Azure Portal インターフェイスから削除されています。 現在、以下の手順は適用されません。 移行するには、上記の Azure Monitor 診断設定で正しいカテゴリがオンになっていることを確認してください。

ワークフローで新しい診断設定をテストしたら、操作の監視機能を無効にすることができます。 

1. IoT Hub メニューで **[操作の監視]** を選択します。

2. 各監視カテゴリの下にある **[なし]** を選択します。

3. 操作の監視の変更を保存します。

## <a name="update-applications-that-use-operations-monitoring"></a>操作の監視を使用するアプリケーションを更新する

操作の監視と診断設定では、スキーマがやや異なります。 操作の監視を現在使用しているアプリケーションが、診断設定で使用するスキーマにマップされるように更新することが重要です。 

また、診断設定には追跡のための 5 つの新しいカテゴリが用意されています。 既存のスキーマについてアプリケーションを更新した後、新しいカテゴリーも追加します。

* クラウドからデバイスへのツイン操作
* デバイスからクラウドへのツイン操作
* ツイン クエリ
* ジョブ操作
* ダイレクト メソッド

具体的なスキーマ構造については、[診断設定のスキーマの理解](iot-hub-monitor-resource-health.md#understand-the-logs)に関するページを参照してください。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>待機時間が短いデバイスの接続イベントと切断イベントの監視

実稼働のデバイスの接続イベントと切断イベントを監視するには、Event Grid で[**デバイス切断**イベント](iot-hub-event-grid.md#event-types) をサブスクライブして、アラートを取得し、デバイスの接続状態を監視することをお勧めします。 IoT ソリューション内の IoT Hub からのデバイス接続イベントとデバイス切断イベントを統合する方法については、こちらの[チュートリアル](iot-hub-how-to-order-connection-state-events.md)をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure IoT Hub の正常性を監視し、問題をすばやく診断する](iot-hub-monitor-resource-health.md)
