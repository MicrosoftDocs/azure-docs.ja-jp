---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792041"
---
### <a name="enable-logging-with-diagnostics-settings"></a>診断設定を使用してログ記録を有効にする

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

2. **[診断設定]** を選択します。

3. **[診断を有効にする]** を選択します。

   ![診断の有効化](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. 診断設定の名前を付けます。

5. ログの送信先を選択します。 3 つのオプションを自由に組み合わせて選択できます。

   * ストレージ アカウントへのアーカイブ
   * イベント ハブへのストリーミング
   * Log Analytics への送信

6. 監視する操作を選択し、それらの操作のログを有効にします。 診断設定では、次の操作をレポートできます。

   * 接続
   * デバイス テレメトリ
   * クラウドからデバイスへのメッセージ
   * デバイス ID の操作
   * ファイルのアップロード
   * メッセージ ルーティング
   * クラウドからデバイスへのツイン操作
   * デバイスからクラウドへのツイン操作
   * ツイン操作
   * ジョブ操作
   * ダイレクト メソッド  
   * 分散トレース (プレビュー)
   * 構成
   * デバイス ストリーム
   * デバイス メトリック

6. 新しい設定を保存します。 

PowerShell を使用して診断設定を有効にする場合は、次のコードを使用します。

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

新しい設定は、10 分ほどで有効になります。 その後、構成したアーカイブ ターゲットのログが **[診断設定]** ブレードに表示されます。 診断を構成することの詳細については、[Azure リソースからログ データを収集して使用する](../articles/azure-monitor/platform/platform-logs-overview.md)ことに関するページを参照してください。
