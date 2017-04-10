---
title: "Event Hubs への Azure アクティビティ ログのストリーミング | Microsoft Docs"
description: "Azure アクティビティ ログを Event Hubs にストリーミングする方法について説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 51a7a274c0cdbec169154bd1100abb4534283cff
ms.openlocfilehash: ac478dc5c6691ef9f6c4829bd47a81912774d032


---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Event Hubs への Azure アクティビティ ログのストリーミング
[**Azure アクティビティ ログ**](monitoring-overview-activity-logs.md)は、ポータルに組み込まれた [エクスポート] オプションを使用するか、Azure PowerShell コマンドレットまたは Azure CLI を使用してログ プロファイルで Service Bus 規則 ID を有効にすることによって、任意のアプリケーションにほぼリアルタイムでストリーミングできます。

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>アクティビティ ログと Event Hubs で実行できること
アクティビティ ログでストリーミング機能を使用する場合、次のような方法があります。

* **サード パーティのログおよびテレメトリ システムにストリーミングする** - 将来的に、Event Hubs のストリーミングは、アクティビティ ログをサード パーティの SIEM やログ分析ソリューションにパイプ処理するためのメカニズムになります。
* **カスタムのテレメトリおよびログ プラットフォームを構築する** - カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、アクティビティ ログを柔軟に取り込むことができます。 [グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法については、Dan Rosanova によるガイドをご覧ください。](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>アクティビティ ログのストリーミングの有効化
アクティビティ ログのストリーミングは、プログラムによって有効にすることも、ポータルで有効にすることもできます。 どちらの方法でも、Service Bus 名前空間と、その名前空間の共有アクセス ポリシーを選択します。この名前空間には、新しいアクティビティ ログ イベントが初めて発生したときに、Event Hub が作成されます。 Service Bus 名前空間が存在しない場合は、最初に作成する必要があります。 この Service Bus 名前空間にアクティビティ ログ イベントをストリーミングしたことがある場合は、以前に作成された Event Hub が再利用されます。 共有アクセス ポリシーでは、ストリーミング メカニズムのアクセス許可が定義されます。 現在、Event Hubs にストリーミングするには、**管理**、**送信**、**リッスン**の各アクセス許可が必要です。 Service Bus 名前空間の共有アクセス ポリシーは、クラシック ポータルの Service Bus 名前空間の [構成] タブで作成または変更できます。 アクティビティ ログのログ プロファイルを更新してストリーミングを含めるには、その Service Bus の承認規則に対する ListKey アクセス許可が、変更を行うユーザーに必要です。

設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、Service Bus またはイベント ハブ名前空間は、ログを出力するのと同じサブスクリプションに属している必要はありません。

### <a name="via-azure-portal"></a>Azure ポータルの使用
1. ポータルの左側のメニューを使用して、 **[アクティビティ ログ]** ブレードに移動します。
   
    ![ポータルの [アクティビティ ログ] に移動](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. ブレードの上部にある **[エクスポート]** ボタンをクリックします。
   
    ![ポータルの [エクスポート] ボタン](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 表示されるブレードで、イベントをストリーミングするリージョンと、これらのイベントをストリーミングするための Event Hubs を作成する Service Bus 名前空間を選択できます。
   
    ![[Export Activity Log (アクティビティ ログのエクスポート)] ブレード](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. **[保存]** をクリックして設定を保存します。 設定はサブスクリプションにすぐに適用されます。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用
ログ プロファイルが既に存在する場合は、まず、そのプロファイルを削除する必要があります。

1. `Get-AzureRmLogProfile` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. 存在する場合は、 `Remove-AzureRmLogProfile` を使用して削除します。
3. `Set-AzureRmLogProfile` を使用して、プロファイルを作成します。

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

Service Bus 規則 ID は、{Service Bus のリソース ID}/authorizationrules/{キー名} の形式の文字列です。 

### <a name="via-azure-cli"></a>Azure CLI の使用
ログ プロファイルが既に存在する場合は、まず、そのプロファイルを削除する必要があります。

1. `azure insights logprofile list` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. 存在する場合は、 `azure insights logprofile delete` を使用して削除します。
3. `azure insights logprofile add` を使用して、プロファイルを作成します。

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

Service Bus 規則 ID は、 `{service bus resource ID}/authorizationrules/{key name}`の形式の文字列です。

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Event Hubs からログ データを使用する方法
アクティビティ ログのスキーマは[こちら](monitoring-overview-activity-logs.md)で入手できます。 各イベントは、"レコード" と呼ばれる JSON BLOB の配列に含まれます。

## <a name="next-steps"></a>次のステップ
* [ストレージ アカウントにアクティビティ ログをアーカイブする](monitoring-archive-activity-log.md)
* [Azure アクティビティ ログの概要を確認する](monitoring-overview-activity-logs.md)
* [アクティビティ ログ イベントに基づいてアラートを設定する](insights-auditlog-to-webhook-email.md)




<!--HONumber=Dec16_HO2-->


