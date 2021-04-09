---
title: Azure Monitor でのロール、アクセス許可、セキュリティ
description: Azure Monitor の組み込みの役割とアクセス許可を使用して、監視リソースへのアクセスを制限する方法について説明します。
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.openlocfilehash: d0877059dca01bf01df7a4f2aeaa5114278ecc97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040279"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Azure Monitor でのロール、アクセス許可、セキュリティ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

チームの多くが、監視データおよび設定へのアクセスを厳密に管理する必要があります。 たとえば、チームの中に監視のみを行うメンバー (サポート エンジニア、DevOps エンジニア) がいる場合、またはマネージド サービス プロバイダーを使用する場合は、監視データへのアクセス権のみを付与し、リソースを作成、変更、削除する機能については制限が必要になることがあります。 この記事では、Azure のユーザーに組み込みの監視 Azure ロールをすばやく適用したり、限定的な監視アクセス許可を必要とするユーザーのために独自のカスタム ロールを作成したりする方法について説明します。 その後、Azure Monitor 関連のリソースのセキュリティに関する考慮事項と、そのリソースに含まれるデータへのアクセスを制限する方法を取り上げます。

## <a name="built-in-monitoring-roles"></a>組み込みの監視の役割
Azure Monitor に組み込まれた役割は、サブスクリプションのリソースへのアクセスを制限しながら、インフラストラクチャの監視担当者が引き続き、必要なデータを取得して構成できるように設計されています。 Azure Monitor には、すぐに使用できる役割として、監視閲覧者と監視共同作成者の 2 つが用意されています。

### <a name="monitoring-reader"></a>Monitoring Reader
監視閲覧者の役割が割り当てられている場合は、サブスクリプション内の監視データすべてを表示できますが、リソースを変更したり、監視リソースに関連する設定を編集したりすることはできません。 この役割は、次の作業を行う必要がある、サポート エンジニア、運用エンジニアなどの組織内のユーザーに適しています。

* ポータルで監視ダッシュボードを表示し、独自のプライベート監視ダッシュボードを作成する。
* [Azure アラート](alerts/alerts-overview.md)に定義されているアラート ルールを表示する。
* [Azure Monitor REST API](/rest/api/monitor/metrics)、[PowerShell コマンドレット](powershell-samples.md)、または[クロスプラットフォーム CLI](cli-samples.md) を使用して、メトリックにクエリを実行する。
* ポータル、Azure Monitor REST API、PowerShell コマンドレット、またはクロスプラットフォーム CLI を使用して、アクティビティ ログにクエリを実行する。
* リソースの [診断設定](essentials/diagnostic-settings.md) を表示する。
* サブスクリプションの [ログ プロファイル](essentials/activity-log.md#legacy-collection-methods) を表示する。
* 自動スケールの設定を表示する。
* アラート アクティビティと設定を表示する。
* Application Insights データにアクセスし、AI Analytics のデータを表示する。
* ワークスペースの使用状況データなど、Log Analytics ワークスペースのデータを検索する。
* Log Analytics 管理グループを表示する。
* Log Analytics ワークスペースの検索スキーマを取得する。
* Log Analytics ワークスペースの監視パックの一覧を表示する。
* Log Analytics ワークスペースに保存済みの検索を取得および実行する。
* Log Analytics ワークスペースのストレージ構成を取得する。

> [!NOTE]
> この役割では、イベント ハブにストリーミングされたログ データ、またはストレージ アカウントに保存されたログ データへの読み取りアクセス権は付与されません。 [以下を参照](#security-considerations-for-monitoring-data) してください。
> 
> 

### <a name="monitoring-contributor"></a>Monitoring Contributor
監視共同作業者の役割が割り当てられている場合、サブスクリプション内の監視データすべてを表示し、監視の設定を作成または変更できます。ただし、他のリソースについては何も変更することはできません。 この役割は監視閲覧者の役割のスーパーセットで、上記のアクセス許可以外に次の作業を行う必要がある、組織の監視チームまたはマネージド サービス プロバイダーのメンバーに適しています。

* 共有ダッシュボードとして監視ダッシュボードを発行する。
* リソースの[診断設定](essentials/diagnostic-settings.md)を設定する。\*
* サブスクリプションの[ログ プロファイル](essentials/activity-log.md#legacy-collection-methods)を設定する。\*
* [Azure アラート](alerts/alerts-overview.md)を使用して、アラート ルール アクティビティと設定を指定する。
* Application Insights の Web テストとコンポーネントを作成する。
* Log Analytics ワークスペースの共有キーを一覧表示する。
* Log Analytics ワークスペースの監視パックを有効または無効にする。
* Log Analytics ワークスペースに保存する検索を作成し、それらの削除および実行を行う。
* Log Analytics ワークスペースのストレージ構成を作成および削除する。

\*診断設定またはログ プロファイルを設定するには、ターゲット リソース (ストレージ アカウントまたはイベント ハブ名前空間) で、ListKeys アクセス許可がユーザーに対して個別に付与されている必要があります。

> [!NOTE]
> この役割では、イベント ハブにストリーミングされたログ データ、またはストレージ アカウントに保存されたログ データへの読み取りアクセス権は付与されません。 [以下を参照](#security-considerations-for-monitoring-data) してください。
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>アクセス許可と Azure カスタム ロールの監視
上記の組み込みの役割がチームの正確なニーズに対応していない場合は、[Azure カスタム ロールを作成](../role-based-access-control/custom-roles.md)して、さらに細かくアクセス許可を指定できます。 Azure Monitor での一般的な Azure RBAC 操作とその説明を次に示します。

| 操作 | 説明 |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |アクション グループの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |アクティビティ ログ アラートの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/AlertRules/[Read、Write、Delete] |(クラシック アラートの) アラート ルール の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/AlertRules/Incidents/Read |アラート ルールのインシデント (トリガーされたアラート ルールの履歴) を一覧表示します。 これは、ポータルにのみ適用されます。 |
| Microsoft.Insights/AutoscaleSettings/[Read、Write、Delete] |自動スケール設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/DiagnosticSettings/[Read、Write、Delete] |診断設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/EventCategories/Read |アクティビティ ログの考えられるすべてのカテゴリを列挙します。 Azure Portal で使用されます。 |
| Microsoft.Insights/eventtypes/digestevents/Read |このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 |
| Microsoft.Insights/eventtypes/values/Read |サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | ネットワーク フロー ログの診断設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/LogDefinitions/Read |このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |ログ プロファイル (イベント ハブまたはストレージ アカウントへのアクティビティ ログのストリーミング) の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |ほぼリアルタイムのメトリック アラートの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/MetricDefinitions/Read |メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
| Microsoft.Insights/Metrics/Read |リソースのメトリックを読み取ります。 |
| Microsoft.Insights/Register/Action |Azure Monitor リソース プロバイダーを登録します。 |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Azure Monitor のログ アラートを読み取り/書き込み/削除します。 |



> [!NOTE]
> リソースのアラート、診断設定、およびメトリックにアクセスするユーザーには、そのリソースの種類とスコープへの読み取りアクセスが必要です。 ストレージ アカウントにアーカイブする、またはイベント ハブにストリーミングする診断設定やログ プロファイルを作成する ("書き込む") ユーザーには、ターゲット リソースにおける ListKeys アクセス許可も必要です。
> 
> 

たとえば、上記の表を使用すると、"アクティビティ ログ閲覧者" に対して、次のような Azure カスタム ロールを作成できます。

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>データ監視のセキュリティに関する考慮事項
監視データ、特にログ ファイルには、IP アドレス、ユーザー名などの機密情報が含まれている場合があります。 Azure の監視データは、次の 3 つの基本フォームで提供されます。

1. アクティビティ ログ。Azure サブスクリプションのすべてのコントロール プレーン アクションが記述されています。
2. リソース ログ。リソースによって出力されるログです。
3. メトリック。リソースによって出力されます。

この 3 つのデータ型はすべて、ストレージ アカウントに保存するか、Event Hub にストリーミングできます。このストレージ アカウントと Event Hub は両方とも 汎用 Azure リソースです。 このような汎用リソースに作成、削除、およびアクセスするには、管理者のために予約された特権が必要です。 監視関連のリソースの場合、誤用を防ぐために、以下を実践することをお勧めします。

* 1 つの専用ストレージ アカウントを使用して、データを監視します。 複数のストレージ アカウントに監視データを分割する必要がある場合は、ストレージ アカウントの使用状況を、監視データと監視対象外データで共有しないでください。監視データ (サード パーティ SIEM など) にのみアクセスする必要があるユーザーに対して、監視対象外データへのアクセス権が誤って付与されることがあります。
* 1 つの専用 Service Bus または Event Hub 名前空間を使用します。
* 監視関連のストレージ アカウントまたはイベント ハブを別のリソース グループに保持することで、そのストレージ アカウントとイベント ハブへのアクセスを制限したうえで、監視の役割の [スコープを使用して](../role-based-access-control/overview.md#scope) 、アクセスをそのリソース グループだけに限定します。
* ユーザーによるアクセス対象が監視データだけの場合、ListKeys アクセス許可は、サブスクリプション スコープでストレージ アカウントまたはイベント ハブに付与しないでください。 代わりに、このアクセス許可は、リソースまたはリソース グループ (専用監視リソース グループの場合) スコープでユーザーに付与します。

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>監視関連のストレージ アカウントに対するアクセスの制限
ユーザーまたはアプリケーションがストレージ アカウントの監視データにアクセスする必要がある場合は、Blob Storage へのサービス レベルの読み取り専用アクセスが付与されている、監視データが含まれるストレージ アカウントで、 [アカウント SAS を生成](/rest/api/storageservices/create-account-sas) します。 PowerShell では次のようになります。

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

その後、ストレージ アカウントからの読み取りが必要なエンティティにトークンを割り当てることができます。これにより、そのストレージ アカウントのすべての BLOB からの表示と読み取りが可能になります。

また、このアクセス許可を Azure RBAC で制御する必要がある場合は、そのエンティティに、特定のストレージ アカウントの Microsoft.Storage/storageAccounts/listkeys/action のアクセス許可を付与します。 これは、診断設定またはログ プロファイルを、ストレージ アカウントにアーカイブされるように設定しなければならないユーザーに必要です。 たとえば、1 つのストレージ アカウントからの読み取りのみが必要なユーザーまたはアプリケーションに対しては、次の Azure カスタム ロールを作成できます。

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys アクセス許可により、ユーザーがプライマリ ストレージ アカウントとセカンダリ ストレージ アカウントのキーの一覧を表示できます。 これらのキーは、そのストレージ アカウントのすべての署名済みサービス (BLOB、キュー、テーブル、ファイル) にわたる、すべての署名済みアクセス許可 (読み取り、書き込み、BLOB の作成、BLOB の削除など) を、ユーザーに付与します。 可能な場合は、前述したアカウント SAS を使用することをお勧めします。
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>監視関連のイベント ハブに対するアクセスの制限
同様のパターンの後にイベント ハブを続けることができますが、最初は、専用 Listen 承認規則を作成する必要があります。 監視関連のイベント ハブのリッスンのみが必要なアプリケーションについて、アクセス権を付与する必要がある場合は、次の操作を行います。

1. Listen 要求のみの監視データをストリーミングするために、作成されたイベント ハブで共有アクセス ポリシーを作成します。 この操作は、ポータルで行うことができます。 たとえば、これを "monitoringReadOnly" と呼ぶとします。 可能な場合は、そのキーをコンシューマーに直接提供し、次の手順をスキップします。
2. コンシューマーがアドホックでキーを取得する必要がある場合は、そのイベント ハブの ListKeys アクションをユーザーに付与します。 これは、診断設定またはログ プロファイルを、イベント ハブにストリーミングされるように設定しなければならないユーザーにも必要です。 たとえば、Azure RBAC ルールを作成する場合があります。
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>セキュリティで保護された仮想ネットワーク内での監視

有効にしたサービスを提供するには、Azure Monitor が Azure リソースにアクセスできる必要があります。 Azure リソースを監視しながら、そのリソースがパブリック インターネットにアクセスできないようにセキュリティで保護するには、以下の設定を有効にします。

### <a name="secured-storage-accounts"></a>セキュリティで保護されたストレージ アカウント 

多くの場合、監視データはストレージ アカウントに書き込まれます。 ストレージ アカウントにコピーされたデータに、権限を持たないユーザーがアクセスできないようにすることができます。 さらにセキュリティを強化するために、"選択されたネットワーク" を使用できるストレージ アカウントを制限することによって、権限を持つリソースと信頼できる Microsoft サービスのみがストレージ アカウントにアクセスできるように、ネットワーク アクセスをロックすることができます。
![Azure Storage の [設定] ダイアログ](./media/roles-permissions-security/secured-storage-example.png) Azure Monitor は、このような "信頼された Microsoft サービス" の 1 つと見なされます。信頼された Microsoft サービスがセキュリティで保護されたストレージにアクセスできるようにする場合、Azure Monitor にはセキュリティで保護されたストレージ アカウントへのアクセス権が付与されます。このような保護された条件下で、Azure Monitor リソース ログ、アクティビティ ログ、およびメトリックをストレージ アカウントに書き込むことができます。 これにより、Log Analytics から、セキュリティで保護されたストレージのログを読み取ることもできます。   


詳細については、[ネットワーク セキュリティと Azure Storage](../storage/common/storage-network-security.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* [Resource Manager で Azure RBAC とアクセス許可を確認します](../role-based-access-control/overview.md)
* [Azure で監視の概要を確認します](overview.md)

