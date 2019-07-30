---
title: Azure Monitor で Log Analytics ワークスペースを管理する | Microsoft Docs
description: ユーザー、アカウント、ワークスペース、Azure アカウントでのさまざまな管理タスクを使用して、Azure Monitor で Log Analytics ワークスペースを管理できます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 22802950c68dc5a3cf0df8ee26ff38ccb937b551
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295518"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Azure Monitor でログ データとワークスペースを管理する
Azure Monitor のログ データは、Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは基本的に、データと構成情報が含まれるコンテナーです。 ログ データへのアクセスを管理するには、ワークスペースに関するさまざまな管理タスクを実行します。 組織のメンバーは、複数のワークスペースを使用して、IT インフラストラクチャの一部またはすべてから収集されるデータのさまざまなセットを管理する場合があります。

この記事では、ログへのアクセスを管理する方法と、ログを含むワークスペースを管理する方法について説明します。 

## <a name="create-a-workspace"></a>ワークスペースの作成
Log Analytics ワークスペースを作成するには、以下を行う必要があります。

1. Azure サブスクリプションを取得する。
2. ワークスペース名を選択する。
3. ワークスペースをサブスクリプションおよびリソース グループのいずれかに関連付ける。
4. 地理的な場所を選択する。

ワークスペースの作成について詳しくは以下の記事を参照してください。

- [Azure portal で Log Analytics ワークスペースを作成する](../learn/quick-create-workspace.md)
- [Azure CLI 2.0 を使用して Log Analytics ワークスペースを作成する](../learn/quick-create-workspace-cli.md)
- [Azure PowerShell を使用して Log Analytics ワークスペースを作成する](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>必要なワークスペースの数を決定する
Log Analytics ワークスペースは、Azure のリソースであり、データが収集、集計、分析されて、Azure Monitor に表示されるコンテナーです。 1 つの Azure サブスクリプションで複数のワークスペースを使用でき、複数のワークスペースにアクセスして、容易にクエリを実行できます。 このセクションでは、複数のワークスペースを作成すると便利な状況について説明します。

Log Analytics ワークスペースには次の情報が示されます。

* データ ストレージの地理的な場所。
* ワークスペース中心モードでさまざまなユーザー アクセス権を定義するためのデータの分離。 リソース中心モードで作業するときは関係ありません。
* [価格レベル](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[リテンション期間](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)、[データ キャッピング](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)などの設定の構成のスコープ。
* ワークスペース リソースで行われるデータ インジェストとデータ保持に関連する課金。

消費の観点から、作成するワークスペースの数はできるだけ少なくすることをお勧めします。 これにより、管理とクエリのエクスペリエンスがより簡単かつ迅速になります。 ただし、上記の特性に基づき、次の条件に当てはまる場合は複数のワークスペースを作成してみてください。

* 世界規模の企業が、データ主権またはコンプライアンス上の理由から特定のリージョンにログ データを格納する必要がある。
* Azure を使用しているときに、管理対象の Azure リソースと同じリージョンにワークスペースを配置することによって送信データ転送の料金が生じるのを回避したい。
* マネージド サービス プロバイダーが、管理する各顧客の Log Analytics データを他の顧客のデータから切り離しておく必要がある。
* 複数の顧客を管理しており、各顧客、部門、ビジネス グループが各自のデータを確認できるが、他の顧客、部門、ビジネス グループのデータを見られらないようにする。顧客、部門、ビジネス グループにまたがって統合するビジネス ニーズはない。

Windows エージェントを使用してデータを収集する場合は、[1 つ以上のワークスペースにレポートを生成するように各エージェントを構成](../../azure-monitor/platform/agent-windows.md)できます。

System Center Operations Manager を使用している場合、各 Operations Manager 管理グループを 1 つのワークスペースのみに接続できます。 Operations Manager を使って管理するコンピューターに Microsoft Monitoring Agent をインストールし、Operations Manager と別の Log Analytics ワークスペースの両方にレポートを生成するようにエージェントを構成できます。

ワークスペース アーキテクチャが定義されたら、このポリシーを Azure リソースに適用するために [Azure Policy](../../governance/policy/overview.md) を使用します。 これによって、すべての Azure リソースに自動的に適用される組み込み定義が提供されます。 たとえば、ポリシーを設定することで、特定のリージョンのすべての Azure リソースがすべての診断ログを特定のワークスペースに送信するようにできます。

## <a name="view-workspace-details"></a>ワークスペースの詳細を表示する
Log Analytics ワークスペース内のデータの分析は Azure portal の **[Azure Monitor]** メニューで行いますが、ワークスペースの作成と管理は **[Log Analytics ワークスペース]** メニューで行います。
 

1. [Azure portal](https://portal.azure.com) にサインインし、 **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **Log Analytics** ワークスペースを選択します。  

    ![Azure ポータル](media/manage-access/azure-portal-01.png)  

3. 一覧からお使いのワークスペースを選択します。

4. ワークスペースのページに、ワークスペースの詳細、作業開始、構成、およびその他の情報へのリンクが表示されます。  

    ![ワークスペースの詳細](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>ワークスペースのアクセス許可と範囲
ユーザーがアクセスできるデータは、次の表に記載されている複数の要因によって決まります。 それぞれについて後のセクションで説明します。

| 係数 | 説明 |
|:---|:---|
| [アクセス モード](#access-modes) | ユーザーがワークスペースにアクセスするために使用する方法。  使用可能なデータの範囲と適用されるアクセス制御モードを定義します。 |
| [アクセス制御モード](#access-control-mode) | アクセス許可をワークスペース レベルとリソース レベルのどちらで適用するかを定義するワークスペースの設定。 |
| [アクセス許可](#manage-accounts-and-users) | ワークスペースまたはリソースについて、個々のユーザーまたはユーザー グループに適用される許可。 ユーザーがアクセスできるデータを定義します。 |
| [テーブル レベルの RBAC](#table-level-rbac) | アクセス モードまたはアクセス制御モードに関係なく、すべてのユーザーに適用されるオプションの詳細なアクセス許可。 ユーザーがアクセスできるデータ型を定義します。 |



## <a name="access-modes"></a>アクセス モード
_アクセス モード_は、ユーザーが Log Analytics ワークスペースにアクセスする方法を参照して、アクセスできるデータの範囲を定義します。 

**ワークスペース中心**:このモードでは、ユーザーはアクセス許可を持つワークスペース内のすべてのログを表示できます。 このモードのクエリの範囲は、ワークスペース内のすべてのテーブルのすべてのデータです。 このアクセス モードが使用されるのは、Azure portal の **[Azure Monitor]** メニューで **[ログ]** を選択する場合など、ワークスペースを範囲としてログにアクセスするときです。

**リソース中心**:Azure portal のリソース メニューで **[ログ]** を選択する場合など、特定のリソースに関してワークスペースにアクセスするときは、アクセス許可を持っているすべてのテーブルのそのリソースのログのみが表示されます。 このモードのクエリの範囲は、そのリソースに関連するデータのみです。 このモードでは、きめ細かなロールベースのアクセス制御 (RBAC) も有効です。 

> [!NOTE]
> リソース中心のクエリでログが使用できるのは、関連するリソースに適切に関連付けられている場合のみです。 現在、次のリソースには制限があります。 
> - Azure 外部のコンピューター
> - Service Fabric
> - Application Insights
> - Containers
>
> ログがリソースに適切に関連付けられているかをテストするために、クエリを実行して、関心があるレコードを調べることができます。 正しいリソース ID が [_ResourceId](log-standard-properties.md#_resourceid) プロパティにあれば、リソース中心クエリでデータを使用できます。

### <a name="comparing-access-modes"></a>アクセス モードを比較する

アクセス モードを以下の表にまとめています。

| | ワークスペース中心 | リソース中心 |
|:---|:---|:---|
| モデルの利用対象者 | 全体管理。 データ収集を構成する必要がある管理者と多様なリソースにアクセスする必要があるユーザー。 また、現時点では、Azure 外部のリソースのログにアクセスするユーザーにとって必要です。 | アプリケーション チーム。 監視されている Azure リソースの管理者。 |
| ログを表示するために必要なもの | ワークスペースに対するアクセス許可。 「[アカウントとユーザーの管理](#manage-accounts-and-users)」の**ワークスペースのアクセス許可**に関する説明をご覧ください。 | リソースへの読み取りアクセス。 「[アカウントとユーザーの管理](#manage-accounts-and-users)」の**リソースのアクセス許可**に関する説明をご覧ください。 アクセス許可は継承する (含まれているリソース グループなどから) ことも、リソースに直接割り当てることもできます。 リソースのログに対するアクセス許可は自動的に割り当てられます。 |
| アクセス許可の範囲 | ワークスペース。 ワークスペースへのアクセス権を持つユーザーは、アクセス許可を持っているテーブルについて、ワークスペース内のすべてのログを照会できます。 [テーブル アクセス制御](#table-level-rbac)に関する説明をご覧ください。 | Azure リソース。 ユーザーは、アクセス権を持つリソースのログをすべてのワークスペースで照会できますが、その他のリソースのログは照会できません。 |
| ログにアクセスする方法 | **[Azure Monitor]** メニューの **[ログ]** または **[Log Analytics ワークスペース]** を開始します。 | Azure リソースのメニューで **[ログ]** を開始します。 resource. |


## <a name="access-control-mode"></a>アクセス制御モード
"_アクセス制御モード_" は、ワークスペースのアクセス許可の決定方法を定義するワークスペースごとの設定です。

**ワークスペースのアクセス許可が必要**:この制御モードでは、きめ細かい RBAC は使用できません。 ユーザーがワークスペースにアクセスするためには、ワークスペースまたは特定のテーブルへのアクセス許可を付与される必要があります。 

ユーザーがワークスペース中心モードでワークスペースにアクセスする場合は、アクセス権を与えられたすべてのテーブルのすべてのデータにアクセスします。 ユーザーがリソース中心モードでワークスペースにアクセスする場合は、アクセス権を与えられたすべてのテーブルの該当リソースのデータのみにアクセスします。

これは、2019 年 3 月よりも前に作成されたすべてのワークスペースの既定設定です。

**リソースまたはワークスペースのアクセス許可を使用**:この制御モードでは、きめ細かい RBAC を使用できます。 ユーザーには、Azure アクセス許可で表示できるリソース (つまり `read` アクセス許可を持っているリソース) に関連付けられているデータのみのアクセス権が付与されます。 

ユーザーがワークスペース中心モードでワークスペースにアクセスするときは、ワークスペースのアクセス許可が適用されます。 ユーザーがリソース中心モードでワークスペースにアクセスするときは、リソースのアクセス許可のみが確認され、ワークスペースのアクセス許可は無視されます。 ユーザーに対して RBAC を有効にするには、ワークスペースのアクセス許可からユーザーを削除して、リソースのアクセス許可が認識されるようにします。

これは、2019 年 3 月よりも後に作成されたすべてのワークスペースの既定設定です。

> [!NOTE]
> ユーザーがワークスペースに対してリソースのアクセス許可しか持たない場合は、[リソース中心モード](#access-modes)のみを使用してワークスペースにアクセスできます。


### <a name="define-access-control-mode-in-azure-portal"></a>Azure portal でアクセス制御モードを定義する
現在のワークスペース アクセス制御モードは、 **[Log Analytics ワークスペース]** メニューのワークスペースの **[概要]** ページで確認できます。

![ワークスペースのアクセス制御モードの表示](media/manage-access/view-access-control-mode.png)

ワークスペースの **[プロパティ]** ページでこの設定を変更できます。 ワークスペースを構成するアクセス許可を持たない場合、設定の変更は無効になります。

![ワークスペースのアクセス モードの変更](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>PowerShell でアクセス制御モードを定義する

次のコマンドを使用して、サブスクリプション内のすべてのワークスペースのアクセス制御モードを調べます。

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

次のスクリプトを使用して、特定のワークスペースのアクセス制御モードを設定します。

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

次のスクリプトを使用して、サブスクリプション内のすべてのワークスペースのアクセス制御モードを設定します。

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Resource Manager テンプレートでアクセス モードを定義する
Azure Resource Manager テンプレートでアクセス モードを構成するには、ワークスペースに対する**enableLogAccessUsingOnlyResourcePermissions** 機能フラグを次のいずれかの値に設定します。

- **false**:ワークスペースをワークスペース中心のアクセス許可に設定します。 これはフラグが設定されない場合の既定設定です。
- **true**:ワークスペースをリソース中心のアクセス許可に設定します。


## <a name="manage-accounts-and-users"></a>アカウントとユーザーの管理
特定のユーザーに適用されるワークスペースのアクセス許可は、ユーザーのアクセス モードとワークスペースの[アクセス制御モード](#access-control-mode)によって定義されます。 **ワークスペースのアクセス許可**は、ユーザーが[ワークスペース中心モード](#access-modes)で**ワークスペース中心**を使用するワークスペースにアクセスするときに適用されます。 **リソースのアクセス許可**は、ユーザーが **[リソースまたはワークスペースのアクセス許可を使用]** [アクセス制御モード](#access-control-mode)で、[リソース中心モード](#access-modes)を使用してワークスペースにアクセスするときに適用されます。

### <a name="workspace-permissions"></a>ワークスペースのアクセス許可
各ワークスペースには、複数のアカウントを関連付けることができます。また、各アカウントは、複数のワークスペースにアクセスできます。 アクセスは、[Azure のロールベースのアクセス](../../role-based-access-control/role-assignments-portal.md)によって管理されます。 


次のアクティビティにも、Azure のアクセス許可が必要です。

| Action                                                          | 必要とされる Azure のアクセス許可 | メモ |
|-----------------------------------------------------------------|--------------------------|-------|
| 監視ソリューションの追加と削除                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | これらのアクセス許可は、リソース グループまたはサブスクリプション レベルで付与する必要があります。 |
| 価格レベルの変更                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* ソリューション タイルと *Site Recovery* ソリューション タイルのデータの表示 | 管理者/共同管理者 | クラシック デプロイ モデルを使用してデプロイされたリソースにアクセスします |
| Azure Portal でのワークスペースの作成                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Azure のアクセス許可を使用して Log Analytics ワークスペースへのアクセスを管理する 
Azure のアクセス許可を使用して Log Analytics ワークスペースへのアクセス権を付与するには、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../../role-based-access-control/role-assignments-portal.md)」の手順に従ってください。

Azure には、Log Analytics ワークスペース用に、次の 2 つの組み込みユーザー ロールがあります。
- Log Analytics 閲覧者
- Log Analytics 共同作成者

"*Log Analytics 閲覧者*" ロールのメンバーは、以下の操作を行うことができます。
- すべての監視データを表示および検索する 
- 監視設定を表示する。これには、すべての Azure リソースに対する Azure Diagnostics の構成の表示などが含まれます。

Log Analytics 閲覧者ロールには、次の Azure アクションが含まれています。

| Type    | アクセス許可 | 説明 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | すべての Azure リソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure Diagnostics の構成 <br> すべてのリソースのすべてのプロパティと設定 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | ログ検索 v2 クエリを実行する機能 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | ログ検索 v1 クエリを実行する機能 |
| Action | `Microsoft.Support/*` | サポート ケースを開く機能 |
|非アクション | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | データ コレクション API の使用とエージェントのインストールに必要なワークスペース キーの読み取りを防ぎます。 これにより、ユーザーは新しいリソースをワークスペースに追加できなくなります |


"*Log Analytics 共同作成者*" ロールのメンバーは、以下の操作を行うことができます。
- すべての監視データの読み取り (Log Analytics 閲覧者と同様)  
- Automation アカウントの作成と構成  
- 管理ソリューションの追加と削除    
    > [!NOTE] 
    > 最後の 2 つのアクションを正常に実行するには、このアクセス許可をリソース グループまたはサブスクリプション レベルで付与する必要があります。  

- ストレージ アカウント キーの読み取り   
- Azure Storage からのログの収集の構成  
- 次のような、Azure リソースの監視設定の編集
  - VM への VM 拡張機能の追加
  - すべての Azure リソースに対する Azure Diagnostics の構成

> [!NOTE] 
> 仮想マシンに仮想マシン拡張機能を追加する機能を使用して、仮想マシンに対するフル コントロールを取得することができます。

Log Analytics 共同作成者ロールには、次の Azure アクションが含まれています。

| アクセス許可 | 説明 |
| ---------- | ----------- |
| `*/read`     | すべてのリソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure Diagnostics の構成 <br> すべてのリソースのすべてのプロパティと設定 |
| `Microsoft.Automation/automationAccounts/*` | Runbook の追加と編集など、Azure Automation アカウントを作成および構成する機能 |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Microsoft Monitoring Agent 拡張機能、OMS Agent for Linux 拡張機能など、仮想マシン拡張機能を追加、更新、および削除する |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | ストレージ アカウント キーを表示する。 Azure Storage アカウントからログを読み取るように Log Analytics を構成するために必要です |
| `Microsoft.Insights/alertRules/*` | アラート ルールを追加、更新、および削除する |
| `Microsoft.Insights/diagnosticSettings/*` | Azure リソースに対する診断設定を追加、更新、および削除する |
| `Microsoft.OperationalInsights/*` | Log Analytics ワークスペースの構成を追加、更新、および削除する |
| `Microsoft.OperationsManagement/*` | 管理ソリューションを追加および削除する |
| `Microsoft.Resources/deployments/*` | デプロイを作成および削除する。 ソリューション、ワークスペース、および Automation アカウントを追加および削除するために必要です |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | デプロイを作成および削除する。 ソリューション、ワークスペース、および Automation アカウントを追加および削除するために必要です |

ユーザー ロールに対してユーザーの追加と削除を行うには、`Microsoft.Authorization/*/Delete` および `Microsoft.Authorization/*/Write` アクセス許可が必要です。

これらのロールを使用すると、次のようなさまざまなスコープでユーザーにアクセスを許可することができます。
- サブスクリプション - サブスクリプション内のすべてのワークスペースへのアクセス
- リソース グループ - リソース グループ内のすべてのワークスペースへのアクセス
- リソース - 指定されたワークスペースのみへのアクセス

正確なアクセス制御を行うために、割り当てをリソース レベル (ワークスペース) で実行する必要があります。  必要な特定のアクセス許可を持つロールを作成するには、[カスタム ロール](../../role-based-access-control/custom-roles.md)を使用します。

### <a name="resource-permissions"></a>リソースのアクセス許可 
ユーザーがリソース中心アクセスを使用するワークスペースのログを照会するときは、リソースに対する次のアクセス許可を持っています。

| アクセス許可 | 説明 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>次に例を示します。<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | リソースのすべてのログ データを表示可能。  |


通常、このアクセス許可は、組み込みの[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールまたは[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールなど、 _\*/read or_ _\*_ アクセス許可を含むロールによって付与されます。 特定の操作を含むカスタム ロールまたは専用の組み込みロールには、このアクセス許可が含まれないことがあります。

さまざまな表に対して異なるアクセス制御を作成する場合は、下記の[テーブルごとのアクセス制御の定義](#table-level-rbac)に関する説明を参照してください。


## <a name="table-level-rbac"></a>テーブル レベルの RBAC
**テーブル レベルの RBAC** では他のアクセス許可に加えて、Log Analytics ワークスペースのデータをさらにきめ細かく制御できます。 この制御を使用すると、特定のユーザーのグループのみがアクセスできる特定のデータ型を定義できます。

[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)を使用したテーブル アクセス制御を実装して、ワークスペース内の特定の[テーブル](../log-query/logs-structure.md)に対するアクセスを付与または拒否します。 これらのロールは、ユーザーの[アクセス モード](#access-modes)とは関係なく、ワークスペース中心またはリソース中心どちらかの[アクセス制御モード](#access-control-mode)のワークスペースに適用されます。

次の操作の[カスタム ロール](../../role-based-access-control/custom-roles.md)を作成して、テーブル アクセス制御を定義します。

- テーブルへのアクセス権を付与するには、ロール定義の **[Actions]** セクションにそのテーブルを含めます。
- テーブルへのアクセス権を拒否するには、ロール定義の **[NotActions]** セクションにそのテーブルを含めます。
- すべてのテーブルを指定するには * を使用します。

たとえば、_Heartbeat_ テーブルと _AzureActivity_ テーブルへのアクセス権を持つロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

_SecurityBaseline_ のみのアクセス権を持ち、他のテーブルのアクセス権は持たないロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>カスタム ログ
 カスタム ログは、カスタム ログや HTTP Data Collector API などのデータ ソースによって作成されます。 ログの種類を特定する最も簡単な方法は、[ログ スキーマの [カスタム ログ]](../log-query/get-started-portal.md#understand-the-schema)に一覧表示されるテーブルを確認することです。

 現時点では、個々のカスタム ログに対するアクセスを付与または拒否することはできませんが、すべてのカスタム ログに対するアクセスを付与または拒否することはできます。 すべてのカスタム ログへのアクセス権を持つロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>考慮事項

- _\*/read_ アクションを含む標準の閲覧者ロールまたは共同作成者ロールを使用してグローバル読み取りアクセス許可がユーザーに付与されると、テーブルごとのアクセス制御がオーバーライドされ、ユーザーにすべてのログへのアクセス権が付与されます。
- テーブルごとのアクセス権がユーザーに付与されるが、その他のアクセス許可は付与されない場合、ユーザーは API からログ データにアクセスできますが、Azure portal からはアクセスできません。 Azure portal へのアクセス権を提供するには、基本ロールとして Log Analytics 閲覧者を使用します。
- サブスクリプションの管理者は、他のすべてのアクセス許可設定に関係なくすべてのデータ型に対するアクセス権を持ちます。
- ワークスペース所有者は、テーブルとごのアクセス制御では他のすべてのユーザーと同様に扱われます。
- 割当て数を減らすために、ロールは個々のユーザーではなくセキュリティ グループに割り当てる必要があります。 こうすると、既存のグループ管理ツールを使用して、アクセス権の構成と確認する際に役立ちます。




## <a name="next-steps"></a>次の手順
* [Log Analytics エージェントの概要](../../azure-monitor/platform/log-analytics-agent.md)に関するページを参照して、データセンターや他のクラウド環境内のコンピューターからデータを収集します。
* 「[Azure Virtual Machines に関するデータの収集](../../azure-monitor/learn/quick-collect-azurevm.md)」を参照して、Azure VM からのデータ コレクションを構成します。  

