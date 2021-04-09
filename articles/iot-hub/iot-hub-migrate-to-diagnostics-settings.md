---
title: Azure IoT Hub 操作監視を Azure Monitor の IoT Hub リソース ログに移行する | Microsoft Docs
description: IoT ハブに対する操作の状態をリアルタイムで監視するために、操作監視の代わりに Azure Monitor を使用するように Azure IoT Hub を更新する方法。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591843"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>IoT Hub を操作監視から Azure Monitor リソース ログに移行する

[操作監視](iot-hub-operations-monitoring.md)を使用して IoT Hub での操作の状態を追跡するユーザーは、そのワークフローを、Azure Monitor の機能である [Azure Monitor リソース ログ](../azure-monitor/essentials/platform-logs-overview.md)に移行することができます。 リソース ログは、多数の Azure サービスについてリソースレベルの診断情報を提供します。

**IoT Hub の操作の監視機能は非推奨となっており**、今後 Portal から削除される予定です。 この記事では、ワークロードを操作監視から Azure Monitor リソース ログに移動する手順について説明します。 廃止のスケジュールについて詳しくは、「[Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)」(Azure Monitor および Azure Resource Health による Azure IoT ソリューションの監視) をご覧ください。

## <a name="update-iot-hub"></a>IoT Hub の更新

Azure portal で IoT Hub を更新するには、まず診断設定を作成し、次に操作監視を無効にします。  

### <a name="create-a--diagnostic-setting"></a>診断設定の作成

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のウィンドウの **[モニター]** の下で、 **[診断設定]** を選択します。 次に **[診断設定を追加する]** を選択します。

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="[モニター] セクションの [診断設定] が強調表示されているスクリーンショット。":::

1. **[診断設定]** ウィンドウで、診断設定に名前を付けます。

1. **[カテゴリの詳細]** で、監視する操作のカテゴリを選択します。 IoT Hub で使用可能な操作のカテゴリの詳細については、「[リソース ログ](monitor-iot-hub-reference.md#resource-logs)」を参照してください。

1. **[宛先の詳細]** で、ログを送信する場所を選択します。 これらの宛先を自由に組み合わせて選択できます。

   * ストレージ アカウントへのアーカイブ
   * イベント ハブへのストリーミング
   * Log Analytics ワークスペース経由で Azure Monitor に送信する

   次のスクリーンショットは、接続とデバイスのテレメトリ カテゴリの操作を Log Analytics ワークスペースにルーティングする診断設定を示しています。

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="完了した診断設定を示すスクリーンショット。":::

1. **[保存]** を選択して設定を保存します。

新しい設定は、10 分ほどで有効になります。 その後、構成された宛先にログが表示されます。 診断を構成することの詳細については、[Azure リソースからログ データを収集して使用する](../azure-monitor/essentials/platform-logs-overview.md)ことに関するページを参照してください。

PowerShell や Azure CLI などによる診断設定の作成方法の詳細については、Azure Monitor のドキュメントの「[診断設定](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。

### <a name="turn-off-operations-monitoring"></a>操作の監視を無効にする

> [!NOTE]
> 2019 年 3 月 11 日時点で、操作の監視機能は IoT Hub の Azure Portal インターフェイスから削除されています。 現在、以下の手順は適用されません。 移行するには、上記の Azure Monitor 診断設定で正しいカテゴリが宛先にルーティングされることを確認してください。

ワークフローで新しい診断設定をテストしたら、操作の監視機能を無効にすることができます。 

1. IoT Hub メニューで **[操作の監視]** を選択します。

2. 各監視カテゴリの下にある **[なし]** を選択します。

3. 操作の監視の変更を保存します。

## <a name="update-applications-that-use-operations-monitoring"></a>操作の監視を使用するアプリケーションを更新する

操作監視とリソース ログでは、スキーマがやや異なります。 操作監視を現在使用しているアプリケーションが、リソース ログで使用するスキーマにマップされるように更新することが重要です。

また、IoT Hub リソース ログには追跡のための 5 つの新しいカテゴリが用意されています。 既存のスキーマについてアプリケーションを更新した後、新しいカテゴリーも追加します。

* クラウドからデバイスへのツイン操作
* デバイスからクラウドへのツイン操作
* ツイン クエリ
* ジョブ操作
* ダイレクト メソッド

特定のスキーマ構造については、「[リソース ログ](monitor-iot-hub-reference.md#resource-logs)」を参照してください。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>待機時間が短いデバイスの接続イベントと切断イベントの監視

実稼働のデバイスの接続イベントと切断イベントを監視するには、Event Grid で [**デバイス切断** イベント](iot-hub-event-grid.md#event-types) をサブスクライブして、アラートを取得し、デバイスの接続状態を監視することをお勧めします。 IoT ソリューション内の IoT Hub からのデバイス接続イベントとデバイス切断イベントを統合する方法については、こちらの[チュートリアル](iot-hub-how-to-order-connection-state-events.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

[IoT Hub の監視](monitor-iot-hub.md)