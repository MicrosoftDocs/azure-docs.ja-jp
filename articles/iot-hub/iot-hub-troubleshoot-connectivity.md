---
title: Azure IoT Hub との切断を監視してトラブルシューティングを行う
description: Azure IoT Hub のデバイス接続に関する一般的なエラーを監視し、解決する方法について説明します
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 8bd20e3c7207c75e87a2132fca89906885de2676
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579254"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub との切断に関する監視、診断、およびトラブルシューティング

IoT デバイスの接続の問題は、考えられる障害点が多数あるため、トラブルシューティングが難しい場合があります。 アプリケーション ロジック、物理ネットワーク、プロトコル、ハードウェア、IoT Hub、およびその他のクラウド サービスのすべてが、問題の原因となる可能性があります。 問題の原因を検出して特定する能力は非常に重要です。 ただし、大規模な IoT ソリューションには数千のデバイスが含まれる可能性があるため、個々のデバイスを手動でチェックするのは現実的ではありません。 IoT Hub は、ユーザーを支援する 2 つの Azure サービスと統合されています。

* **Azure Monitor**: これらの問題を大規模に検出、診断、およびトラブルシューティングするために、IoT Hub によって Azure Monitor を通して提供される監視機能を使用します。 これには、切断が発生したときに通知とアクションをトリガーするアラートの設定と、切断の原因となった状態を検出するために使用できるログの構成が含まれます。

* **Azure Event Grid**: 重要なインフラストラクチャとデバイスごとの切断については、Azure Event Grid を使用して、IoT Hub によって生成されるデバイスの接続および切断イベントをサブスクライブします。

どちらの場合も、これらの機能は IoT Hub が監視できるものに限定されます。そのため、デバイスとその他の Azure サービスに対する監視のベスト プラクティスに従うこともお勧めします。

## <a name="event-grid-vs-azure-monitor"></a>Event Grid と Azure Monitor

Event Grid は、重要なデバイスとインフラストラクチャのデバイス接続を追跡するために使用できる、低遅延かつデバイスごとの監視ソリューションを提供します。 Azure Monitor には、"*接続されているデバイス*" のメトリックが用意されています。これを使用して、IoT Hub に接続されているデバイスの数を監視し、その数が静的なしきい値を下回るとアラートをトリガーできます。

特定のシナリオで Event Grid または Azure Monitor のどちらを使用するか決定する際には、次の点を考慮してください。

* アラートの待機時間:Event Grid によって、IoT Hub 接続イベントがより迅速に配信されます。 このため、迅速な通知が望ましいシナリオの場合は Event Grid が適しています。

* デバイスごとの通知: Event Grid には、個々のデバイスの接続と切断を追跡する機能が用意されています。 このため、重要なデバイスの接続を監視する必要があるシナリオの場合は Event Grid が適しています。

* 簡易設定: Azure Monitor メトリック アラートは、電子メール、SMS、音声、およびその他の通知を通じて通知を配信するために他のサービスと統合する必要がない、簡易セットアップ エクスペリエンスを提供します。  Event Grid では、通知を配信するために他の Azure サービスと統合する必要があります。 どちらのサービスも他のサービスと統合して、より複雑なアクションをトリガーできます。

低遅延かつデバイスごとの機能があるため、運用環境では、接続の監視には Event Grid を使用することを強くお勧めします。 もちろん、選択は排他的ではなく、Azure Monitor メトリック アラートと Event Grid の両方を使用できます。 切断を追跡するかどうかにかかわらず、Azure Monitor リソース ログを使用して、予期しないデバイス切断の理由のトラブルシューティングに役立てることができます。 次のセクションでは、これらのオプションのそれぞれについて詳しく説明します。

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: デバイスの接続と切断のイベントを監視する

実稼働のデバイスの接続と切断のイベントを監視するには、Event Grid で [**DeviceConnected** と **DeviceDisconnected** イベント](iot-hub-event-grid.md#event-types)をサブスクライブして、アラートをトリガーし、デバイスの接続状態を監視することをお勧めします。 Event Grid を使用すると、イベント待機時間が Azure Monitor よりもはるかに短くなり、接続されているデバイスの合計数ではなく、デバイスごとに監視できます。 これらの要因により、Event Grid は重要なデバイスとインフラストラクチャを監視するための推奨される方法となっています。

Event Grid を使用してデバイス切断の監視またはアラートのトリガーを行う場合、Azure IoT SDK を使用するデバイス上での SAS トークンの更新による定期的な切断をフィルター除外する方法が組み込まれていることを確認してください。 詳細については、「[Azure IoT SDK での MQTT デバイスの切断動作](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)」を参照してください。

Event Grid を使用したデバイス接続イベントの監視の詳細については、次のトピックを参照してください。

* IoT Hub での Event Grid の使用の概要については、[Event Grid を使用した IoT Hub のイベントへの対応](iot-hub-event-grid.md)に関する記事を参照してください。 「[デバイス接続およびデバイス切断イベントの制限事項](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events)」セクションに特に注意してください。

* デバイス接続イベントの順序付けに関するチュートリアルについては、「[Azure IoT Hub から Azure Cosmos DB を使用してデバイス接続イベントを順序付ける](iot-hub-how-to-order-connection-state-events.md)」を参照してください。

* 電子メール通知の送信に関するチュートリアルについては、Event Grid のドキュメントの「[Event Grid および Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](../event-grid/publish-iot-hub-events-to-logic-apps.md)」を参照してください。

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor:接続イベントをログにルーティングする

IoT ハブは、複数のカテゴリの操作について、リソース ログを継続的に出力します。 しかし、このログ データを収集するには、それを分析またはアーカイブ可能な宛先にルーティングするための診断設定を作成する必要があります。 このような宛先の 1 つが、Log Analytics ワークスペースを経由した Azure Monitor ログです ([価格を参照](https://azure.microsoft.com/pricing/details/log-analytics/))。ここで、Kusto クエリを使用してデータを分析することができます。

IoT Hub の[リソース ログ接続カテゴリ](monitor-iot-hub-reference.md#connections)では、デバイス接続に関する操作とエラーが出力されます。 次のスクリーンショットは、これらのログを Log Analytics ワークスペースにルーティングするための診断設定を示しています。

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Log Analytics ワークスペースに接続ログを送信するための推奨設定。":::

IoT ハブを作成した後は、できるだけ早く診断設定を作成することをお勧めします。IoT Hub は常にリソース ログを出力しますが、それは宛先にルーティングされるまで Azure Monitor によって収集されないためです。

宛先へのログのルーティングの詳細については、「[収集とルーティング](monitor-iot-hub.md#collection-and-routing)」を参照してください。 診断設定を作成する詳細な手順については、[メトリックとログの使用に関するチュートリアル](tutorial-use-metrics-and-diags.md)を参照してください。

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor:デバイスの切断に関するメトリック アラートを大規模に設定する

IoT Hub によって出力されたプラットフォーム メトリックに基づいてアラートを設定できます。 メトリック アラートを使用すると、関心のある状態が発生したことをユーザーに通知したり、その状態に自動的に対応できるアクションをトリガーしたりすることができます。

"[*接続されているデバイス (プレビュー)*](monitor-iot-hub-reference.md#device-metrics)" メトリックは、IoT Hub に接続されているデバイスの数を示します。 このメトリックがしきい値を下回った場合にトリガーされるアラートを作成できます。

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="接続されているデバイス メトリックに関するアラート ロジックの設定。":::

メトリック アラート ルールを使用して、デバイスの切断時の異常を大規模に監視できます。 つまり、多数のデバイスが予期せず切断された場合です。 このような現象が検出された場合は、ログを確認して問題のトラブルシューティングに役立てることができます。 ただし、デバイスごとの切断および重要なデバイスの切断を監視するには、Event Grid を使用する必要があります。 また Event Grid は、Azure メトリックよりもリアルタイムのエクスペリエンスを提供します。

IoT Hub でのアラートの詳細については、[IoT Hub の監視でのアラート](monitor-iot-hub.md#alerts)に関するページを参照してください。 IoT Hub でのアラート作成のチュートリアルについては、[メトリックとログの使用に関するチュートリアル](tutorial-use-metrics-and-diags.md)を参照してください。 アラートの詳細な概要については、Azure Monitor のドキュメントの「[Microsoft Azure のアラートの概要](../azure-monitor/alerts/alerts-overview.md)」を参照してください。

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor:ログを使用して接続に関するエラーを解決する

デバイスの切断を検出した場合、Azure Monitor メトリック アラートでも Event Grid でも、ログを使用してその理由のトラブルシューティングを行うことができます。 このセクションでは、Azure Monitor ログで一般的な問題を探す方法について説明します。 下の手順では、IoT Hub 接続ログを Log Analytics ワークスペースに送信するように、[診断設定](#azure-monitor-route-connection-events-to-logs)が既に作成されていることを前提としています。

Azure Monitor ログに IoT Hub リソース ログをルーティングする診断設定を作成したら、これらの手順に従って Azure portal でログを表示します。

1. [Azure portal](https://portal.azure.com) で、お使いの IoT ハブに移動します。

1. お使いの IoT ハブの左ペインの **[監視]** で、 **[ログ]** を選択します。

1. IoT Hub の接続エラー ログを分離するために、クエリ エディターで次のクエリを入力してから **[実行]** を選択します。

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 結果がある場合は、`OperationName`、`ResultType` (エラー コード)、および `ResultDescription` (エラー メッセージ) を探してエラーの詳細を確認します。

   ![エラー ログの例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

エラーを特定したら、最も一般的なエラーの解決に役立つ問題解決ガイドに従ってください。

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Azure IoT SDK での MQTT デバイスの切断動作

Azure IoT デバイス SDK は、MQTT (および MQTT over WebSocket) プロトコルを介して SAS トークンを更新する際に、IoT Hub から切断して再接続します。 ログでは、これはデバイスの切断と接続の情報イベントとして表示され、時としてエラー イベントを伴うことがあります。

既定では、トークンの有効期間はすべての SDK で 60 分です。ただし一部の SDK ではこれを開発者が変更できます。 次の表は、各 SDK のトークンの有効期間、トークンの更新、トークンの更新の動作をまとめたものです。

| SDK | トークンの有効期間 | トークンの更新 | 更新の動作 |
|-----|----------|---------------------|---------|
| .NET | 60 分、構成可能 | 有効期間の 85 %、構成可能 | SDK は、トークンの有効期間に 10 分の猶予期間を加算した期間で接続と切断を行います。 情報イベントとエラーがログに生成されます。 |
| Java | 60 分、構成可能 | 有効期間の 85%、構成不可 | SDK は、トークンの有効期間に 10 分の猶予期間を加算した期間で接続と切断を行います。 情報イベントとエラーがログに生成されます。 |
| Node.js | 60 分、構成可能 | 構成可能 | SDK は、トークンの更新時に接続して切断します。 情報イベントのみがログに生成されます。 |
| Python | 60 分、構成不可 | -- | SDK はトークンの有効期間で接続して切断します。 |

次のスクリーンショットは、さまざまな SDK についての Azure Monitor ログにおけるトークンの更新動作を示しています。 トークンの有効期間と更新のしきい値は、注記のとおり既定値から変更されています。

* トークンの有効期間が 1200 秒 (20 分) で、有効期間の 90% で更新が発生するように設定されている .NET デバイス SDK。 切断は 30 分ごとに行われます。

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text=".NET SDK を使用した Azure Monitor ログでの MQTT 経由のトークンの更新に関するエラー動作。":::

* トークンの有効期間が 300 秒 (5 分) で、既定で有効期間の 85% で更新が発生するように設定されている Java SDK。 切断は 15 分ごとに行われます。

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Java SDK を使用した Azure Monitor ログでの MQTT 経由のトークンの更新に関するエラー動作。":::

* トークンの有効期間が 300 秒 (5 分) で、トークンの更新が 3 分で発生するように設定されている Node SDK。 トークンの更新時に切断が発生します。 また、エラーは発生せず、接続または切断の情報イベントのみが出力されます。

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Node SDK を使用した Azure Monitor ログでの MQTT 経由のトークンの更新に関するエラー動作。":::

次のクエリは、結果を収集するために使用されました。 このクエリはプロパティ バッグから SDK の名前とバージョンを抽出します。詳細については、「[IoT Hub ログの SDK バージョン](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)」を参照してください。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

IoT ソリューションの開発者またはオペレーターは、ログの接続または切断イベントおよび関連するエラーを解釈するためには、この動作に注意する必要があります。 デバイスのトークンの有効期間または更新の動作を変更する場合は、デバイスがデバイス ツイン設定を実装しているかどうか、またはこれを可能にするデバイス メソッドを実装しているかどうかを確認してください。

Event Hub を使用してデバイスの接続を監視している場合は、SAS トークンの更新による定期的な切断をフィルターで除外する方法が組み込まれていることを確認してください。たとえば、切断イベントの後に特定の期間内に接続イベントが続く限り、切断に基づいてアクションをトリガーしないようにします。

> [!NOTE]
> IoT Hub は、デバイスごとにアクティブな MQTT 接続を 1 つだけサポートします。 同じデバイス ID で新しい MQTT 接続が行われると、IoT Hub は既存の接続を破棄します。
>
> 400027 ConnectionForcefullyClosedOnNewConnection が IoT Hub ログに記録されます。

## <a name="i-tried-the-steps-but-they-didnt-work"></a>手順を実行しましたが、うまくいきませんでした

前述の手順が役に立たなかった場合は、以下を試してください。

* 問題のあるデバイスに物理的にまたは (SSH などを使用して) リモートからアクセスできる場合は、[デバイス側のトラブルシューティング ガイド](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)の記事に従ってトラブルシューティングを続けます。

* Azure portal の IoT ハブで IoT デバイスを選択し、デバイスが **[有効]** であることを確認します。

* デバイスで MQTT プロトコルを使用している場合は、ポート 8883 が開いていることを確認します。 詳細については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

* [Azure IoT Hub に関する Microsoft Q&A 質問ページ](/answers/topics/azure-iot-hub.html)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)、または [Azure サポート](https://azure.microsoft.com/support/options/)から支援を受けてください。

このガイドが役に立たなかった場合は、すべてのお客様のためにこのドキュメントを改善できるように、以下のフィードバック セクションでコメントを残してください。

## <a name="next-steps"></a>次のステップ

* 一時的な問題の解決に関する詳細については、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」を参照してください。

* Azure IoT SDK と再試行管理に関する詳細については、「[Azure IoT Hub device SDK を利用して接続と信頼できるメッセージ処理を管理する](iot-hub-reliability-features-in-sdks.md#connection-and-retry)」を参照してください。