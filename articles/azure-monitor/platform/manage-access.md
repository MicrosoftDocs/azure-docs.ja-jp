---
title: Azure Monitor で Log Analytics ワークスペースを管理する | Microsoft Docs
description: リソース、ワークスペース、またはテーブルレベルのアクセス許可を使用して、Log Analytics ワークスペースに格納されているデータへのアクセスを Azure Monitor で管理できます。 この記事では、方法について詳しく説明します。
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
ms.date: 08/26/2019
ms.author: magoedte
ms.openlocfilehash: 9bf278b76846b98f58126957c589df87524bb8a4
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034709"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Azure Monitor でログ データとワークスペースへのアクセスを管理する

Azure Monitor の[ログ](data-platform-logs.md) データは、Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは基本的に、データと構成情報が含まれるコンテナーです。 ログ データへのアクセスを管理するには、ワークスペースに関するさまざまな管理タスクを実行します。

この記事では、次のように、ログへのアクセスを管理する方法と、ログを含むワークスペースを管理する方法について説明します。

* Azure のロールベースのアクセス制御 (RBAC) を使用して、特定のリソースのデータにアクセスする必要があるユーザーにアクセス権を付与する方法について説明します。

* ワークスペースのアクセス許可を使用して、ワークスペースへのアクセス権を付与する方法。

* Azure RBAC を使用して、ワークスペース内の特定のテーブルのログ データにアクセスする必要があるユーザーにアクセス権を付与する方法。

## <a name="configure-access-control-mode"></a>アクセス制御モードを構成する

Azure portal から、あるいは Azure PowerShell を利用し、ワークスペース上で構成されたアクセス制御モードを表示できます。  この設定は、サポートされている次のいずれかの方法で変更できます。

* Azure ポータル

* Azure PowerShell

* Azure Resource Manager テンプレート

### <a name="from-the-azure-portal"></a>Azure portal から

現在のワークスペース アクセス制御モードは、 **[Log Analytics ワークスペース]** メニューのワークスペースの **[概要]** ページで確認できます。

![ワークスペースのアクセス制御モードの表示](media/manage-access/view-access-control-mode.png)

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
1. Azure portal で、[Log Analytics ワークスペース]、目的のワークスペース の順に選択します。

ワークスペースの **[プロパティ]** ページからこの設定を変更できます。 ワークスペースを構成するアクセス許可を持たない場合、設定の変更は無効になります。

![ワークスペースのアクセス モードの変更](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell の使用

次のコマンドを使用して、サブスクリプション内のすべてのワークスペースのアクセス制御モードを調べます。

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

出力は次のようになります。

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

値が `False` の場合、ワークスペースは、ワークスペースコンテキスト アクセス モードで構成されていることを意味します。  値が `True` の場合、ワークスペースは、リソースコンテキスト アクセス モードで構成されていることを意味します。

> [!NOTE]
> ブール値のないワークスペースが返され、空白の場合は、`False` 値の結果とも一致します。
>

特定のワークスペースのアクセス制御モードをリソースコンテキストのアクセス許可に設定するには、次のスクリプトを使用します。

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

次のスクリプトを使用して、サブスクリプション内のすべてのワークスペースに対するアクセス制御モードをリソースコンテキスト アクセス許可に設定します。

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>Resource Manager テンプレートの使用

Azure Resource Manager テンプレートでアクセス モードを構成するには、ワークスペースに対する**enableLogAccessUsingOnlyResourcePermissions** 機能フラグを次のいずれかの値に設定します。

* **false**:ワークスペースをワークスペースコンテキストのアクセス許可に設定します。 これはフラグが設定されない場合の既定設定です。
* **true**:ワークスペースをリソースコンテキストのアクセス許可に設定します。

## <a name="manage-access-using-workspace-permissions"></a>ワークスペースのアクセス許可を使用してアクセスを管理する

各ワークスペースには、複数のアカウントを関連付けることができます。また、各アカウントは、複数のワークスペースにアクセスできます。 アクセスは、[Azure のロールベースのアクセス](../../role-based-access-control/role-assignments-portal.md)を使用して管理されます。

次のアクティビティにも、Azure のアクセス許可が必要です。

|Action |必要とされる Azure のアクセス許可 |メモ |
|-------|-------------------------|------|
| 監視ソリューションの追加と削除 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | これらのアクセス許可は、リソース グループまたはサブスクリプション レベルで付与する必要があります。 |
| 価格レベルの変更 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* ソリューション タイルと *Site Recovery* ソリューション タイルのデータの表示 | 管理者/共同管理者 | クラシック デプロイ モデルを使用してデプロイされたリソースにアクセスします |
| Azure Portal でのワークスペースの作成 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| ワークスペースの基本的なプロパティを表示し、ポータルにワークスペース ブレードを入力する | `Microsoft.OperationalInsights/workspaces/read` ||
| 任意のインターフェイスを使用し、ログにクエリを実行する | `Microsoft.OperationalInsights/workspaces/query/read` ||
| クエリを使用し、すべてのログ タイプにアクセスする | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 特定のログ テーブルにアクセスする | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| ワークスペース キーを読み取り、このワークスペースへのログ送信を許可する | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Azure のアクセス許可を使用してアクセスを管理する

Azure のアクセス許可を使用して Log Analytics ワークスペースへのアクセス権を付与するには、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../../role-based-access-control/role-assignments-portal.md)」の手順に従ってください。 カスタム ロールの例については、「[カスタム ロールの例](#custom-role-examples)」を参照してください。

Azure には、Log Analytics ワークスペース用に、次の 2 つの組み込みユーザー ロールがあります。

* Log Analytics 閲覧者
* Log Analytics 共同作成者

"*Log Analytics 閲覧者*" ロールのメンバーは、以下の操作を行うことができます。

* すべての監視データを表示および検索する
* 監視設定を表示する。これには、すべての Azure リソースに対する Azure Diagnostics の構成の表示などが含まれます。

Log Analytics 閲覧者ロールには、次の Azure アクションが含まれています。

| Type    | アクセス許可 | 説明 |
| ------- | ---------- | ----------- |
| Action | `*/read`   | すべての Azure リソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure Diagnostics の構成 <br> すべてのリソースのすべてのプロパティと設定。 <br> ワークスペースの場合、ワークスペース設定の読み取りとデータへのクエリ実行について、制限なしの完全なアクセスが許可されます。 上の詳細なオプションを参照してください。 |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 非推奨です。ユーザーに割り当てる必要はありません。 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | 非推奨です。ユーザーに割り当てる必要はありません。 |
| Action | `Microsoft.Support/*` | サポート ケースを開く機能 |
|非アクション | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | データ コレクション API の使用とエージェントのインストールに必要なワークスペース キーの読み取りを防ぎます。 これにより、ユーザーは新しいリソースをワークスペースに追加できなくなります |

"*Log Analytics 共同作成者*" ロールのメンバーは、以下の操作を行うことができます。

* すべての監視データの読み取り (Log Analytics 閲覧者と同様)
* Automation アカウントの作成と構成
* 管理ソリューションの追加と削除

    > [!NOTE]
    > 最後の 2 つのアクションを正常に実行するには、このアクセス許可をリソース グループまたはサブスクリプション レベルで付与する必要があります。

* ストレージ アカウント キーの読み取り
* Azure Storage からのログの収集の構成
* 次のような、Azure リソースの監視設定の編集
  * VM への VM 拡張機能の追加
  * すべての Azure リソースに対する Azure Diagnostics の構成

> [!NOTE]
> 仮想マシンに仮想マシン拡張機能を追加する機能を使用して、仮想マシンに対するフル コントロールを取得することができます。

Log Analytics 共同作成者ロールには、次の Azure アクションが含まれています。

| アクセス許可 | 説明 |
| ---------- | ----------- |
| `*/read`     | すべてのリソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure Diagnostics の構成 <br> すべてのリソースのすべてのプロパティと設定。 <br> ワークスペースの場合、ワークスペース設定の読み取りとデータへのクエリ実行について、制限なしの完全なアクセスが許可されます。 上の詳細なオプションを参照してください。 |
| `Microsoft.Automation/automationAccounts/*` | Runbook の追加と編集など、Azure Automation アカウントを作成および構成する機能 |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Microsoft Monitoring Agent 拡張機能、OMS Agent for Linux 拡張機能など、仮想マシン拡張機能を追加、更新、および削除する |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | ストレージ アカウント キーを表示する。 Azure Storage アカウントからログを読み取るように Log Analytics を構成するために必要です |
| `Microsoft.Insights/alertRules/*` | アラート ルールを追加、更新、および削除する |
| `Microsoft.Insights/diagnosticSettings/*` | Azure リソースに対する診断設定を追加、更新、および削除する |
| `Microsoft.OperationalInsights/*` | Log Analytics ワークスペースの構成を追加、更新、および削除する。 ワークスペースの詳細設定を編集するには、ユーザーに `Microsoft.OperationalInsights/workspaces/write` が必要です。 |
| `Microsoft.OperationsManagement/*` | 管理ソリューションを追加および削除する |
| `Microsoft.Resources/deployments/*` | デプロイを作成および削除する。 ソリューション、ワークスペース、および Automation アカウントを追加および削除するために必要です |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | デプロイを作成および削除する。 ソリューション、ワークスペース、および Automation アカウントを追加および削除するために必要です |

ユーザー ロールに対してユーザーの追加と削除を行うには、`Microsoft.Authorization/*/Delete` および `Microsoft.Authorization/*/Write` アクセス許可が必要です。

これらのロールを使用すると、次のようなさまざまなスコープでユーザーにアクセスを許可することができます。

* サブスクリプション - サブスクリプション内のすべてのワークスペースへのアクセス
* リソース グループ - リソース グループ内のすべてのワークスペースへのアクセス
* リソース - 指定されたワークスペースのみへのアクセス

正確なアクセス制御を行うために、割り当てをリソース レベル (ワークスペース) で実行する必要があります。  必要な特定のアクセス許可を持つロールを作成するには、[カスタム ロール](../../role-based-access-control/custom-roles.md)を使用します。

### <a name="resource-permissions"></a>リソースのアクセス許可

ユーザーがリソースコンテキストのアクセスを使用するワークスペースのログを照会するときは、リソースに対する次のアクセス許可を持っています。

| アクセス許可 | 説明 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>次に例を示します。<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | リソースのすべてのログ データを表示可能。  |
| `Microsoft.Insights/diagnosticSettings/write` | 診断設定を構成し、このリソースに設定を許可する機能。 |

通常、`/read` のアクセス許可は、組み込みの[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールまたは[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールなど、 _\*/read or_ _\*_ アクセス許可を含むロールによって付与されます。 特定の操作を含むカスタム ロールまたは専用の組み込みロールには、このアクセス許可が含まれないことがあります。

さまざまな表に対して異なるアクセス制御を作成する場合は、下記の[テーブルごとのアクセス制御の定義](#table-level-rbac)に関する説明を参照してください。

## <a name="custom-role-examples"></a>カスタム ロールの例

1. ユーザーに対して自身のリソースからログ データへのアクセスを許可するには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する**よう構成します。

    * ユーザーに対して、自身のリソースに対する `*/read` または `Microsoft.Insights/logs/*/read` アクセス許可を付与します。 ワークスペースの [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#reader) ロールが既に割り当てられている場合はそれで十分です。

2. ユーザーに対して自身のリソースからログ データへのアクセスを許可し、ログをワークスペースに送信するようそのリソースを構成するには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する**よう構成します。

    * ユーザーに対して、ワークスペースに対するアクセス許可 `Microsoft.OperationalInsights/workspaces/read` および `Microsoft.OperationalInsights/workspaces/sharedKeys/action` を付与します。 これらのアクセス許可を持つユーザーはワークスペースレベルのクエリを実行できません。

    * ユーザーに対して、自身のリソースに対するアクセス許可 `Microsoft.Insights/logs/*/read` および `Microsoft.Insights/diagnosticSettings/write` を付与します。 このリソースの [Log Analytics Contributor](../../role-based-access-control/built-in-roles.md#contributor) ロールが既に割り当てられている場合はそれで十分です。

3. ユーザーに対して自身のリソースからログ データへのアクセスを許可し、Azure AD のすべてのサインインを読み取り、更新管理ソリューションのログ データを読み取るには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する**よう構成します。

    * ユーザーに対して、ワークスペースに対する以下のアクセス許可を付与します。 

        * `Microsoft.OperationalInsights/workspaces/read` - ユーザーがワークスペースを列挙して Azure portal でワークプレース ブレードを開くことができるようにするために必要です
        * `Microsoft.OperationalInsights/workspaces/query/read` - クエリを実行できるすべてのユーザーに必要です
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` - Azure AD のサインイン ログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` - 更新管理ソリューションのログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` - 更新管理ソリューションのログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` - 更新管理ログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` - 更新管理ソリューションを使用できるようにするために必要です
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` - 更新管理ソリューションを使用できるようにするために必要です

    * ユーザーに対して、自身のリソースに対するアクセス許可 `*/read` または `Microsoft.Insights/logs/*/read` を付与します。 ワークスペースの [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#reader) ロールが割り当てられている場合はそれで十分です。

## <a name="table-level-rbac"></a>テーブル レベルの RBAC

**テーブル レベルの RBAC** では他のアクセス許可に加えて、Log Analytics ワークスペースのデータをさらにきめ細かく定義できます。 この制御を使用すると、特定のユーザーのグループのみがアクセスできる特定のデータ型を定義できます。

[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)を使用したテーブル アクセス制御を実装して、ワークスペース内の特定の[テーブル](../log-query/logs-structure.md)に対するアクセスを付与または拒否します。 これらのロールは、ユーザーの[アクセス モード](design-logs-deployment.md#access-mode)とは関係なく、ワークスペースコンテキストまたはリソースコンテキストどちらかの[アクセス制御モード](design-logs-deployment.md#access-control-mode)のワークスペースに適用されます。

次の操作の[カスタム ロール](../../role-based-access-control/custom-roles.md)を作成して、テーブル アクセス制御を定義します。

* テーブルへのアクセス権を付与するには、ロール定義の **[Actions]** セクションにそのテーブルを含めます。
* テーブルへのアクセス権を拒否するには、ロール定義の **[NotActions]** セクションにそのテーブルを含めます。
* すべてのテーブルを指定するには * を使用します。

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

 カスタム ログは、カスタム ログや HTTP Data Collector API などのデータ ソースから作成されます。 ログの種類を特定する最も簡単な方法は、[ログ スキーマの [カスタム ログ]](../log-query/get-started-portal.md#understand-the-schema)に一覧表示されるテーブルを確認することです。

 現時点では、個々のカスタム ログに対するアクセスを付与または拒否することはできませんが、すべてのカスタム ログに対するアクセスを付与または拒否することはできます。 すべてのカスタム ログへのアクセス権を持つロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>考慮事項

* _\*/read_ アクションを含む標準の閲覧者ロールまたは共同作成者ロールを使用してグローバル読み取りアクセス許可がユーザーに付与されると、テーブルごとのアクセス制御がオーバーライドされ、ユーザーにすべてのログへのアクセス権が付与されます。
* テーブルごとのアクセス権がユーザーに付与されるが、その他のアクセス許可は付与されない場合、ユーザーは API からログ データにアクセスできますが、Azure portal からはアクセスできません。 Azure portal からアクセス権を提供するには、基本ロールとして Log Analytics 閲覧者を使用します。
* サブスクリプションの管理者は、他のすべてのアクセス許可設定に関係なくすべてのデータ型に対するアクセス権を持ちます。
* ワークスペース所有者は、テーブルとごのアクセス制御では他のすべてのユーザーと同様に扱われます。
* 割当て数を減らすために、ロールは個々のユーザーではなくセキュリティ グループに割り当てる必要があります。 こうすると、既存のグループ管理ツールを使用して、アクセス権の構成と確認する際に役立ちます。

## <a name="next-steps"></a>次の手順

* [Log Analytics エージェントの概要](../../azure-monitor/platform/log-analytics-agent.md)に関するページを参照して、データセンターや他のクラウド環境内のコンピューターからデータを収集します。

* 「[Azure Virtual Machines に関するデータの収集](../../azure-monitor/learn/quick-collect-azurevm.md)」を参照して、Azure VM からのデータ コレクションを構成します。
