---
title: Azure IoT Hub との切断に関する診断とトラブルシューティング
description: Azure IoT Hub のデバイス接続に関する一般的なエラーを診断し、解決する方法について説明します
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 6cc5e45ab28a1c83125a37cefb289b1662096eb0
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648821"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub との切断の検出とトラブルシューティング

IoT デバイスの接続の問題は、考えられる障害点が多数あるため、トラブルシューティングが難しい場合があります。 デバイス側のアプリケーション ロジック、物理ネットワーク、プロトコル、ハードウェア、Azure IoT Hub のすべてが問題の原因となる可能性があります。 この記事では、(デバイス側からではなく) クラウド側からデバイス接続の問題を検出し、トラブルシューティングを行う方法に関する推奨事項について説明します。

## <a name="get-alerts-and-error-logs"></a>アラートとエラー ログを取得する

Azure Monitor を使用すると、デバイス接続が切断されたときにアラートを取得し、ログを書き込むことができます。

### <a name="turn-on-diagnostic-logs"></a>診断ログを有効にする

デバイス接続のイベントとエラーをログに記録するには、IoT Hub の診断を有効にします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. IoT ハブに移動します。
1. **[診断設定]** を選択します。
1. **[診断を有効にする]** を選択します。
1. **[接続]** ログの収集を有効にします。
1. 分析しやすくするために、**[Log Analytics への送信]** を有効にします ([価格を参照](https://azure.microsoft.com/pricing/details/log-analytics/))。 「[接続に関するエラーを解決する](#resolve-connectivity-errors)」の例を参照してください。

   ![推奨設定][2]

詳細については、「[Azure IoT Hub の正常性を監視し、問題をすばやく診断する](iot-hub-monitor-resource-health.md)」を参照してください。

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>_接続されているデバイス_ 数のメトリックに関するアラートを設定する

デバイスが切断されたときにアラートを受け取るには、**接続されているデバイス (プレビュー)** のメトリックに関するアラートを構成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. IoT ハブに移動します。
1. **[Alerts]**(アラート) を選択します。
1. **[新しいアラート ルール]** を選択します。
1. **条件の追加** を選択して、Connected devices (preview)\(接続されているデバイス (プレビュー)\) を選択します。
1. プロンプトに従って、目的のしきい値とアラート オプションの設定を完了します。

詳細については、「[Microsoft Azure のクラシック アラートの概要](../azure-monitor/platform/alerts-overview.md)」を参照してください。

## <a name="resolve-connectivity-errors"></a>接続に関するエラーを解決する

接続されたデバイスの診断ログとアラートを有効にすると、エラーが発生したときにアラートを受け取ります。 このセクションでは、アラートを受け取ったときに一般的な問題を解決する方法について説明します。 実際の診断ログに合わせて Azure Monitor ログを設定済みであることを前提として、以下の手順を説明します。

1. Azure portal で、**Log Analytics** のワークスペースに移動します。
1. **[ログ検索]** を選択します。
1. IoT Hub の接続エラー ログを分離するために、次のクエリを入力してから **[実行]** を選択します。

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. 結果がある場合は、`OperationName`、`ResultType` (エラー コード)、および `ResultDescription` (エラー メッセージ) を探してエラーの詳細を確認します。

   ![エラー ログの例][4]

1. この表を参照して、一般的なエラーを理解し、解決してください。

    | Error | 根本原因 | 解決策 |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | 接続はデバイスによって閉じられましたが、IoT Hub では理由が認識されていません。 一般的な原因には、MQTT/AMQP のタイムアウトやインターネット接続の喪失などがあります。 | [接続をテスト](tutorial-connectivity.md)して、デバイスが IoT Hub に接続できることを確認します。 接続は正常でも、デバイスが断続的に切断される場合は、必ず選択したプロトコル (MQTT/AMPQ) に適したキープ アライブ デバイス ロジックを実装してください。 |
    | 401003 IoTHubUnauthorized | IoT Hub は接続を認証できませんでした。 | 使用する SAS などのセキュリティ トークンが期限切れでないことを確認してください。 [Azure IoT SDK](iot-hub-devguide-sdks.md) を使用すると、特別な構成を行うことなく自動でトークンを生成できます。 |
    | 409002 LinkCreationConflict | デバイスに複数の接続が存在します。 新しい接続要求がデバイスに送信されると、IoT Hub はこのエラーで前の接続を閉じます。 | 最も一般的な事例では、デバイスによって切断が検出され、接続の再確立が試行されますが、IoT Hub では引き続きデバイスが接続されていると見なされます。 IoT Hub では前の接続が閉じられ、このエラーが記録されます。 このエラーは通常、別の一時的な問題の副作用として現れます。そのため、問題解決を続けるには、ログで他のエラーを探してください。 そうでなければ、接続が切断された場合にのみ、新しい接続要求を発行してください。 |
    | 500001 ServerError | IoT Hub はサーバー側の問題に遭遇しました。 ほとんどの場合、問題は一時的です。 IoT ハブ チームは [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/) を維持するために努力していますが、IoT Hub ノードのごく一部で一時的な障害が発生することがあります。 問題のあるノードにデバイスが接続しようとすると、このエラーが発生します。 | 一時的な障害を軽減するには、デバイスから再試行を発行します。 [自動的に再試行を管理](iot-hub-reliability-features-in-sdks.md#connection-and-retry)するには、最新バージョンの [Azure IoT SDK](iot-hub-devguide-sdks.md) を使用してください。<br><br>一時的な障害処理と再試行のベスト プラクティスについては、「[Transient Fault Handling (一時的な障害処理)](/azure/architecture/best-practices/transient-faults)」を参照してください。  <br><br>再試行しても問題が解決しない場合は、[[リソース正常性]](iot-hub-monitor-resource-health.md#use-azure-resource-health) と [[Azure の状態]](https://azure.microsoft.com/status/history/) で IoT Hub に既知の問題があるかどうかを確認します。 既知の問題がなく、問題が引き続き発生する場合は、[サポートに問い合わせて](https://azure.microsoft.com/support/options/)さらに調査してください。 |
    | 500008 GenericTimeout | IoT Hub は、タイムアウトする前に接続要求を完了できませんでした。500001 ServerError と同様に、ほとんどの場合、このエラーは一時的なものです。 | 500001 ServerError のトラブルシューティング手順に従って根本原因を調べ、このエラーを解決してください。|

## <a name="other-steps-to-try"></a>その他の試す手順

前述の手順が役に立たなかった場合は、以下の手順を試すことができます。

* 問題のあるデバイスに物理的にまたは (SSH などを使用して) リモートからアクセスできる場合は、[デバイス側のトラブルシューティング ガイド](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)の記事に従ってトラブルシューティングを続けます。
* Azure portal の IoT ハブで IoT デバイスを選択し、デバイスが **[有効]** であることを確認します。
* [Azure IoT Hub フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)、または [Azure サポート](https://azure.microsoft.com/support/options/)で相談します。

このガイドが役に立たなかった場合は、すべてのお客様のためにこのドキュメントを改善できるように、以下のフィードバック セクションでコメントを残してください。

## <a name="next-steps"></a>次の手順

* 一時的な問題の解決に関する詳細については、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」を参照してください。
* Azure IoT SDK と再試行管理に関する詳細については、「[Azure IoT Hub device SDK を利用して接続と信頼できるメッセージ処理を管理する](iot-hub-reliability-features-in-sdks.md#connection-and-retry)」を参照してください。

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
