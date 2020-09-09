---
title: Azure IoT Hub との切断を監視してトラブルシューティングを行う
description: Azure IoT Hub のデバイス接続に関する一般的なエラーを監視し、解決する方法について説明します
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: b22573fc98d4647c2b05763d9b67c37f6f023f98
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322722"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub との切断に関する監視、診断、およびトラブルシューティング

IoT デバイスの接続の問題は、考えられる障害点が多数あるため、トラブルシューティングが難しい場合があります。 アプリケーション ロジック、物理ネットワーク、プロトコル、ハードウェア、IoT Hub、およびその他のクラウド サービスのすべてが、問題の原因となる可能性があります。 問題の原因を検出して特定する能力は非常に重要です。 ただし、大規模な IoT ソリューションには数千のデバイスが含まれる可能性があるため、個々のデバイスを手動でチェックするのは現実的ではありません。 これらの問題を大規模に検出、診断、およびトラブルシューティングするために、IoT Hub によって Azure Monitor を通して提供される監視機能を使用します。 これらの機能は IoT Hub が監視できるものに限定されます。そのため、デバイスとその他の Azure サービスを監視するためのベスト プラクティスに従うこともお勧めします。

## <a name="get-alerts-and-error-logs"></a>アラートとエラー ログを取得する

Azure Monitor を使用して、デバイスの接続が切断されたときにアラートを取得し、ログを書き込みます。

### <a name="turn-on-diagnostic-logs"></a>診断ログを有効にする

デバイス接続のイベントとエラーをログに記録するには、IoT Hub の診断を有効にします。 診断ログが有効になっていない場合、デバイスの接続が切断された場合に問題のトラブルシューティングを行うための情報がないため、できるだけ早い段階でこれらのログを有効にすることをお勧めします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. IoT ハブに移動します。

3. **[診断設定]** を選択します。

4. **[診断を有効にする]** を選択します。

5. **[接続]** ログの収集を有効にします。

6. 分析しやすくするために、 **[Log Analytics への送信]** を有効にします ([価格を参照](https://azure.microsoft.com/pricing/details/log-analytics/))。 「[接続に関するエラーを解決する](#resolve-connectivity-errors)」の例を参照してください。

   ![推奨設定](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

詳細については、「[Azure IoT Hub の正常性を監視し、問題をすばやく診断する](iot-hub-monitor-resource-health.md)」を参照してください。

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>デバイスの切断に関するアラートを大規模に設定する

デバイスが切断されたときにアラートを受け取るには、**接続されているデバイス (プレビュー)** のメトリックに関するアラートを構成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. IoT ハブに移動します。

3. **[Alerts]** (アラート) を選択します。

4. **[新しいアラート ルール]** を選択します。

5. **条件の追加** を選択して、Connected devices (preview)\(接続されているデバイス (プレビュー)\) を選択します。

6. プロンプトに従って、しきい値とアラートを設定します。

詳細については、[Microsoft Azure のアラートの概要](../azure-monitor/platform/alerts-overview.md)に関するページを参照してください。

#### <a name="detecting-individual-device-disconnects"></a>個々のデバイスの切断の検出

"*デバイスごと*" の切断を検出する (工場がたった今オフラインになったことを知る必要がある場合など) には、[Event Grid でデバイス切断イベントを構成](iot-hub-event-grid.md)します。

## <a name="resolve-connectivity-errors"></a>接続に関するエラーを解決する

接続されたデバイスの診断ログとアラートを有効にすると、エラーが発生したときにアラートを受け取ります。 このセクションでは、アラートを受け取った場合の一般的な問題を探す方法について説明します。 実際の診断ログに合わせて Azure Monitor ログを設定済みであることを前提として、以下の手順を説明します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. IoT ハブに移動します。

1. **[ログ]** を選択します。

1. IoT Hub の接続エラー ログを分離するために、次のクエリを入力してから **[実行]** を選択します。

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 結果がある場合は、`OperationName`、`ResultType` (エラー コード)、および `ResultDescription` (エラー メッセージ) を探してエラーの詳細を確認します。

   ![エラー ログの例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. 最も一般的なエラーのための問題解決ガイドに従ってください。

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>手順を実行しましたが、うまくいきませんでした

前述の手順が役に立たなかった場合は、以下を試してください。

* 問題のあるデバイスに物理的にまたは (SSH などを使用して) リモートからアクセスできる場合は、[デバイス側のトラブルシューティング ガイド](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)の記事に従ってトラブルシューティングを続けます。

* Azure portal の IoT ハブで IoT デバイスを選択し、デバイスが **[有効]** であることを確認します。

* デバイスで MQTT プロトコルを使用している場合は、ポート 8883 が開いていることを確認します。 詳細については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

* [Azure IoT Hub に関する Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-iot-hub.html)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)、または [Azure サポート](https://azure.microsoft.com/support/options/)から支援を受けてください。

このガイドが役に立たなかった場合は、すべてのお客様のためにこのドキュメントを改善できるように、以下のフィードバック セクションでコメントを残してください。

## <a name="next-steps"></a>次のステップ

* 一時的な問題の解決に関する詳細については、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」を参照してください。

* Azure IoT SDK と再試行管理に関する詳細については、「[Azure IoT Hub device SDK を利用して接続と信頼できるメッセージ処理を管理する](iot-hub-reliability-features-in-sdks.md#connection-and-retry)」を参照してください。
