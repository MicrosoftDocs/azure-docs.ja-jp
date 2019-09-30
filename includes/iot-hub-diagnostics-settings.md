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
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266825"
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

   * Connections
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

新しい設定は、10 分ほどで有効になります。 その後、構成したアーカイブ ターゲットのログが **[診断設定]** ブレードに表示されます。 診断の構成の詳細については、「[Azure リソースからのログ データの収集と使用](../articles/azure-monitor/platform/resource-logs-overview.md)」を参照してください。
