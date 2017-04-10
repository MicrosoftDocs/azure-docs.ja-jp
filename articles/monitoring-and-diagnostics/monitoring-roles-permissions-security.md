---
title: "Azure Monitor での役割、アクセス許可、およびセキュリティの使用 | Microsoft Docs"
description: "Azure Monitor の組み込みの役割とアクセス許可を使用して、監視リソースへのアクセスを制限する方法について説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 26e12a27693fe94ae88b70328ed5dd0d9d5b4c10


---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Azure Monitor での役割、アクセス許可、およびセキュリティの使用
チームの多くが、監視データおよび設定へのアクセスを厳密に管理する必要があります。 たとえば、チームの中に監視のみを行うメンバー (サポート エンジニア、開発エンジニアなど) がいる場合、またはマネージ サービス プロバイダーを使用する場合は、監視データへのアクセス権のみを付与し、リソースを作成、変更、削除する機能については制限が必要になることがあります。 この記事では、Azure のユーザーに対して、組み込みの監視 RBAC 役割をすばやく適用する方法、または限定的な監視アクセス許可を必要とするユーザーに対して、独自のカスタム ロールを作成する方法について説明します。 その後、Azure Monitor 関連のリソースのセキュリティに関する考慮事項と、そのリソースに含まれるデータへのアクセスを制限する方法を取り上げます。

## <a name="built-in-monitoring-roles"></a>組み込みの監視の役割
Azure Monitor に組み込まれた役割は、サブスクリプションのリソースへのアクセスを制限しながら、インフラストラクチャの監視担当者が引き続き、必要なデータを取得して構成できるように設計されています。 Azure Monitor には、すぐに使用できる役割として、監視閲覧者と監視共同作成者の 2 つが用意されています。

### <a name="monitoring-reader"></a>監視閲覧者
監視閲覧者の役割が割り当てられている場合は、サブスクリプション内の監視データすべてを表示できますが、リソースを変更したり、監視リソースに関連する設定を編集したりすることはできません。 この役割は、次の作業を行う必要がある、サポート エンジニア、運用エンジニアなどの組織内のユーザーに適しています。

* ポータルで監視ダッシュボードを表示し、独自のプライベート監視ダッシュボードを作成する。
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)、[PowerShell コマンドレット](insights-powershell-samples.md)、または[クロスプラットフォーム CLI](insights-cli-samples.md) を使用して、メトリックにクエリを実行する。
* ポータル、Azure Monitor REST API、PowerShell コマンドレット、またはクロスプラットフォーム CLI を使用して、アクティビティ ログにクエリを実行する。
* リソースの [診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) を表示する。
* サブスクリプションの [ログ プロファイル](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) を表示する。
* 自動スケールの設定を表示する。
* アラート アクティビティと設定を表示する。
* Application Insights データにアクセスし、AI Analytics のデータを表示する。
* ワークスペースの使用状況データなど、Log Analytics (OMS) ワークスペースのデータを検索する。
* Log Analytics (OMS) 管理グループを表示する。
* Log Analytics (OMS) 検索スキーマを取得する。
* Log Analytics (OMS) インテリジェンス パックを一覧表示する。
* Log Analytics (OMS) に保存された検索を取得および実行する。
* Log Analytics (OMS) ストレージ構成を取得する。

> [!NOTE]
> この役割では、イベント ハブにストリーミングされたログ データ、またはストレージ アカウントに保存されたログ データへの読み取りアクセス権は付与されません。 [以下を参照](#security-considerations-for-monitoring-data) してください。
> 
> 

### <a name="monitoring-contributor"></a>監視共同作業者
監視共同作業者の役割が割り当てられている場合、サブスクリプション内の監視データすべてを表示し、監視の設定を作成または変更できます。ただし、他のリソースについては何も変更することはできません。 この役割は監視閲覧者の役割のスーパーセットで、上記のアクセス許可以外に次の作業を行う必要がある、組織の監視チームまたはマネージ サービス プロバイダーのメンバーに適しています。

* 共有ダッシュボードとして監視ダッシュボードを発行する。
* リソースの[診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)を設定する。*
* サブスクリプションの[ログ プロファイル](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)を設定する。*
* アラート アクティビティと設定を指定する。
* Application Insights の Web テストとコンポーネントを作成する。
* Log Analytics (OMS) ワークスペースの共有キーを一覧表示する。
* Log Analytics (OMS) インテリジェンス パックを有効または無効にする。
* Log Analytics (OMS) に保存された検索を作成および削除する。
* Log Analytics (OMS) ストレージ構成を作成および削除する。

* 診断設定またはログ プロファイルを設定するには、ターゲット リソース (ストレージ アカウントまたはイベント ハブ名前空間) で、ListKeys アクセス許可がユーザーに対して個別に付与されている必要があります。

> [!NOTE]
> この役割では、イベント ハブにストリーミングされたログ データ、またはストレージ アカウントに保存されたログ データへの読み取りアクセス権は付与されません。 [以下を参照](#security-considerations-for-monitoring-data) してください。
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>アクセス許可とカスタムの RBAC 役割の監視
上記の組み込みの役割がチームの正確なニーズに対応していない場合は、 [カスタム RBAC 役割を作成](../active-directory/role-based-access-control-custom-roles.md) して、さらに細かくアクセス許可を指定できます。 一般的な Azure Monitor RBAC 操作とその説明を次に示します。

| 操作 | Description |
| --- | --- |
| Microsoft.Insights/AlertRules/[Read、Write、Delete] |アラート ルールの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/AlertRules/Incidents/Read |アラート ルールのインシデント (トリガーされたアラート ルールの履歴) を一覧表示します。 これは、ポータルにのみ適用されます。 |
| Microsoft.Insights/AutoscaleSettings/[Read、Write、Delete] |自動スケール設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/DiagnosticSettings/[Read、Write、Delete] |診断設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/eventtypes/digestevents/Read |このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 |
| Microsoft.Insights/eventtypes/values/Read |サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
| Microsoft.Insights/LogDefinitions/Read |このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 |
| Microsoft.Insights/MetricDefinitions/Read |メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
| Microsoft.Insights/Metrics/Read |リソースのメトリックを読み取ります。 |

> [!NOTE]
> リソースのアラート、診断設定、およびメトリックにアクセスするユーザーには、そのリソースの種類とスコープへの読み取りアクセスが必要です。 ストレージ アカウントにアーカイブする、またはイベント ハブにストリーミングする診断設定やログ プロファイルを作成する ("書き込む") ユーザーには、ターゲット リソースにおける ListKeys アクセス許可も必要です。
> 
> 

たとえば、上記の表を使用すると、"アクティビティ ログ閲覧者" に対して、次のようなカスタム RBAC 役割を作成できます。

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>データ監視のセキュリティに関する考慮事項
監視データ、特にログ ファイルには、IP アドレス、ユーザー名などの機密情報が含まれている場合があります。 Azure の監視データは、次の 3 つの基本フォームで提供されます。

1. アクティビティ ログ。Azure サブスクリプションのすべてのコントロール プレーン アクションが記述されています。
2. 診断ログ。リソースによって出力されるログです。
3. メトリック。リソースによって出力されます。

この 3 つのデータ型はすべて、ストレージ アカウントに保存するか、Event Hub にストリーミングできます。このストレージ アカウントと Event Hub は両方とも 汎用 Azure リソースです。 このような汎用リソースに作成、削除、およびアクセスするには、通常、管理者のために予約された特権が必要です。 監視関連のリソースの場合、誤用を防ぐために、以下を実践することをお勧めします。

* 1 つの専用ストレージ アカウントを使用して、データを監視します。 複数のストレージ アカウントに監視データを分割する必要がある場合は、ストレージ アカウントの使用状況を、監視データと監視対象外データで共有しないでください。 監視データにのみアクセスする必要があるユーザー (サード パーティ SIEM など) に対して、監視対象外データへのアクセス権が誤って付与されることがあります。
* 1 つの専用 Service Bus または Event Hub 名前空間を使用します。
* 監視関連のストレージ アカウントまたはイベント ハブを別のリソース グループに保持することで、そのストレージ アカウントとイベント ハブへのアクセスを制限したうえで、監視の役割の [スコープを使用して](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) 、アクセスをそのリソース グループだけに限定します。
* ユーザーによるアクセス対象が監視データだけの場合、ListKeys アクセス許可は、サブスクリプション スコープでストレージ アカウントまたはイベント ハブに付与しないでください。 代わりに、このアクセス許可は、リソースまたはリソース グループ (専用監視リソース グループの場合) スコープでユーザーに付与します。

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>監視関連のストレージ アカウントに対するアクセスの制限
ユーザーまたはアプリケーションがストレージ アカウントの監視データにアクセスする必要がある場合は、Blob Storage へのサービス レベルの読み取り専用アクセスが付与されている、監視データが含まれるストレージ アカウントで、 [アカウント SAS を生成](https://msdn.microsoft.com/library/azure/mt584140.aspx) します。 PowerShell では次のようになります。

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

その後、ストレージ アカウントからの読み取りが必要なエンティティにトークンを割り当てることができます。これにより、そのストレージ アカウントのすべての BLOB からの表示と読み取りが可能になります。

また、このアクセス許可を RBAC で制御する必要がある場合は、そのエンティティに、特定のストレージ アカウントの Microsoft.Storage/storageAccounts/listkeys/action 権限を付与します。 これは、診断設定またはログ プロファイルを、ストレージ アカウントにアーカイブされるように設定しなければならないユーザーに必要です。 たとえば、1 つのストレージ アカウントからの読み取りのみが必要なユーザーまたはアプリケーションに対しては、次のカスタム RBAC 役割を作成できます。

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys アクセス許可により、ユーザーがプライマリ ストレージ アカウントとセカンダリ ストレージ アカウントのキーの一覧を表示できます。 これらのキーは、そのストレージ アカウントのすべての署名済みサービス (BLOB、キュー、テーブル、ファイル) にわたる、すべての署名済みアクセス許可 (読み取り、書き込み、BLOB の作成、BLOB の削除など) を、ユーザーに付与します。 可能な場合は、前述したアカウント SAS を使用することをお勧めします。
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>監視関連のイベント ハブに対するアクセスの制限
同様のパターンの後にイベント ハブを続けることができますが、最初は、専用 Listen 承認規則を作成する必要があります。 監視関連のイベント ハブのリッスンのみが必要なアプリケーションについて、アクセス権を付与する必要がある場合は、次の操作を行います。

1. Listen 要求のみの監視データをストリーミングするために、作成されたイベント ハブで共有アクセス ポリシーを作成します。 この操作は、ポータルで行うことができます。 たとえば、これを "monitoringReadOnly" と呼ぶとします。 可能な場合は、そのキーをコンシューマーに直接提供し、次の手順をスキップします。
2. コンシューマーがアドホックでキーを取得する必要がある場合は、そのイベント ハブの ListKeys アクションをユーザーに付与します。 これは、診断設定またはログ プロファイルを、イベント ハブにストリーミングされるように設定しなければならないユーザーにも必要です。 たとえば、RBAC ルールを作成する場合があります。
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>次のステップ
* [Resource Manager で RBAC とアクセス許可を確認します](../active-directory/role-based-access-control-what-is.md)
* [Azure で監視の概要を確認します](monitoring-overview.md)




<!--HONumber=Nov16_HO3-->


