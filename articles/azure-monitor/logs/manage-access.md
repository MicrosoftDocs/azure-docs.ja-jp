---
title: Azure Monitor で Log Analytics ワークスペースを管理する | Microsoft Docs
description: リソース、ワークスペース、またはテーブル レベルのアクセス許可を使用して、Log Analytics ワークスペースに格納されているデータへのアクセスを Azure Monitor で管理できます。 この記事では、完了する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 79bcca688146b2c2382876b6a61bc0eee34382a1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040959"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Azure Monitor でログ データとワークスペースへのアクセスを管理する

Azure Monitor は、[ログ](../logs/data-platform-logs.md) データを Log Analytics ワークスペースに格納します。 ワークスペースは、データと構成情報が含まれるコンテナーです。 ログ データへのアクセスを管理するには、ワークスペースに関するさまざまな管理タスクを実行します。

この記事では、以下のアクセスを許可する方法など、ログへのアクセスを管理する方法と、ログを含むワークスペースを管理する方法について説明します。 

* ワークスペース (ワークスペースのアクセス許可を使用)
* Azure のロールベースのアクセス制御 (Azure RBAC) を使用して特定のリソースのログ データにアクセスする ([リソース コンテキスト](../logs/design-logs-deployment.md#access-mode)とも呼ばれる) 必要があるユーザー
* ワークスペース内の特定のテーブルのログ データにアクセスする必要があるユーザー (Azure RBAC を使用)

Azure RBAC に関するログの概念とアクセス戦略を理解するには、「[Azure Monitor ログのデプロイの設計](../logs/design-logs-deployment.md)」を参照してください

## <a name="configure-access-control-mode"></a>アクセス制御モードを構成する

Azure portal から、または Azure PowerShell を利用し、ワークスペース上で構成された[アクセス制御モード](../logs/design-logs-deployment.md)を表示できます。  この設定は、サポートされている次のいずれかの方法で変更できます。

* Azure portal

* Azure PowerShell

* Azure Resource Manager テンプレート

### <a name="from-the-azure-portal"></a>Azure portal から

現在のワークスペース アクセス制御モードは、 **[Log Analytics ワークスペース]** メニューのワークスペースの **[概要]** ページで確認できます。

![ワークスペースのアクセス制御モードの表示](media/manage-access/view-access-control-mode.png)

1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
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
}
```

### <a name="using-a-resource-manager-template"></a>Resource Manager テンプレートの使用

Azure Resource Manager テンプレートでアクセス モードを構成するには、ワークスペースに対する **enableLogAccessUsingOnlyResourcePermissions** 機能フラグを次のいずれかの値に設定します。

* **false**:ワークスペースをワークスペースコンテキストのアクセス許可に設定します。 これはフラグが設定されない場合の既定設定です。
* **true**:ワークスペースをリソースコンテキストのアクセス許可に設定します。

## <a name="manage-access-using-workspace-permissions"></a>ワークスペースのアクセス許可を使用してアクセスを管理する

各ワークスペースには、複数のアカウントを関連付けることができます。また、各アカウントは、複数のワークスペースにアクセスできます。 アクセスは、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) を使用して管理されます。

次のアクティビティにも、Azure のアクセス許可が必要です。

|アクション |必要とされる Azure のアクセス許可 |Notes |
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

Azure のアクセス許可を使用して Log Analytics ワークスペースへのアクセス権を付与するには、[Azure サブスクリプション リソースへのアクセスを管理するための Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)に関するページの手順に従ってください。 カスタム ロールの例については、「[カスタム ロールの例](#custom-role-examples)」を参照してください。

Azure には、Log Analytics ワークスペース用に、次の 2 つの組み込みユーザー ロールがあります。

* Log Analytics 閲覧者
* Log Analytics 共同作成者

"*Log Analytics 閲覧者*" ロールのメンバーは、以下の操作を行うことができます。

* すべての監視データを表示および検索する
* 監視設定を表示する。これには、すべての Azure リソースに対する Azure Diagnostics の構成の表示などが含まれます。

Log Analytics 閲覧者ロールには、次の Azure アクションが含まれています。

| Type    | 権限 | 説明 |
| ------- | ---------- | ----------- |
| アクション | `*/read`   | すべての Azure リソースとリソース構成を表示する機能。 次のものを表示できます。 <br> 仮想マシン拡張機能の状態 <br> リソースに対する Azure Diagnostics の構成 <br> すべてのリソースのすべてのプロパティと設定。 <br> ワークスペースの場合、ワークスペース設定の読み取りとデータへのクエリ実行について、制限なしの完全なアクセスが許可されます。 上の詳細なオプションを参照してください。 |
| アクション | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 非推奨です。ユーザーに割り当てる必要はありません。 |
| アクション | `Microsoft.OperationalInsights/workspaces/search/action` | 非推奨です。ユーザーに割り当てる必要はありません。 |
| アクション | `Microsoft.Support/*` | サポート ケースを開く機能 |
|非アクション | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | データ コレクション API の使用とエージェントのインストールに必要なワークスペース キーの読み取りを防ぎます。 これにより、ユーザーは新しいリソースをワークスペースに追加できなくなります |

"*Log Analytics 共同作成者*" ロールのメンバーは、以下の操作を行うことができます。

* "*Log Analytics Reader ロール*" のすべての特権を含め、ユーザーがすべての監視データを読み取ることができるようにする
* Automation アカウントを作成および構成する
* 管理ソリューションを追加および削除する

    > [!NOTE]
    > 最後の 2 つのアクションを正常に実行するには、このアクセス許可をリソース グループまたはサブスクリプション レベルで付与する必要があります。

* ストレージ アカウント キーを読み取る
* Azure Storage からのログの収集を構成する
* 次のような、Azure リソースの監視設定の編集
  * VM への VM 拡張機能の追加
  * すべての Azure リソースに対する Azure Diagnostics の構成

> [!NOTE]
> 仮想マシンに仮想マシン拡張機能を追加する機能を使用して、仮想マシンに対するフル コントロールを取得することができます。

Log Analytics 共同作成者ロールには、次の Azure アクションが含まれています。

| 権限 | 説明 |
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

正確なアクセス制御を行うために、割り当てをリソース レベル (ワークスペース) で実行することをお勧めします。 必要な特定のアクセス許可を持つロールを作成するには、[カスタム ロール](../../role-based-access-control/custom-roles.md)を使用します。

### <a name="resource-permissions"></a>リソースのアクセス許可

ユーザーがリソースコンテキストのアクセスを使用するワークスペースのログを照会するときは、リソースに対する次のアクセス許可を持っています。

| 権限 | 説明 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>例 :<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | リソースのすべてのログ データを表示可能。  |
| `Microsoft.Insights/diagnosticSettings/write` | 診断設定を構成し、このリソースに設定を許可する機能。 |

通常、`/read` のアクセス許可は、組み込みの [閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールまたは [共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールなど、 _\*/read または_ _\*_ アクセス許可を含むロールによって付与されます。 特定の操作を含むカスタム ロールまたは専用の組み込みロールには、このアクセス許可が含まれないことがあります。

さまざまな表に対して異なるアクセス制御を作成する場合は、下記の[テーブルごとのアクセス制御の定義](#table-level-azure-rbac)に関する説明を参照してください。

## <a name="custom-role-examples"></a>カスタム ロールの例

1. ユーザーに対して自身のリソースからログ データへのアクセスを許可するには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する** よう構成します。

    * ユーザーに対して、自身のリソースに対する `*/read` または `Microsoft.Insights/logs/*/read` アクセス許可を付与します。 ワークスペースの [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#reader) ロールが既に割り当てられている場合はそれで十分です。

2. ユーザーに対して自身のリソースからログ データへのアクセスを許可し、ログをワークスペースに送信するようそのリソースを構成するには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する** よう構成します。

    * ユーザーに対して、ワークスペースに対するアクセス許可 `Microsoft.OperationalInsights/workspaces/read` および `Microsoft.OperationalInsights/workspaces/sharedKeys/action` を付与します。 これらのアクセス許可を持つユーザーはワークスペースレベルのクエリを実行できません。 ワークスペースを列挙して、診断設定またはエージェント構成の行先として使用することのみできます。

    * ユーザーに対して、自身のリソースに対するアクセス許可 `Microsoft.Insights/logs/*/read` および `Microsoft.Insights/diagnosticSettings/write` を付与します。 このリソースの [Log Analytics 共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールが既に割り当てられていたり、閲覧者ロールが割り当てられたり、`*/read` アクセス許可が付与されている場合は、それで十分です。

3. ユーザーに対して、セキュリティ イベントを読み取り、データを送信することなく、リソースからログ データへのアクセス権を付与するには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する** よう構成します。

    * ユーザーに対して、自身のリソースに対するアクセス許可 `Microsoft.Insights/logs/*/read` を付与します。

    * 次に示す非アクション `Microsoft.Insights/logs/SecurityEvent/read` を追加して、ユーザーが SecurityEvent の種類を読み取れないようにします。 非アクションは、読み取り権限 (`Microsoft.Insights/logs/*/read`) を提供するアクションと同じカスタム ロールに存在する必要があります。 このリソースまたはサブスクリプションまたはリソース グループに割り当てられている別のロールからユーザーが読み取り操作を継承する場合は、すべてのログの種類を読み取ることができます。 たとえば、これは、閲覧者または共同作成者ロールと共に存在する `*/read` を継承する場合にも当てはまります。

4. ユーザーに対して自身のリソースからデータをログ記録し、Azure AD のすべてのサインインを読み取り、ワークスペースから Update Management ソリューションのログ データを読み取るアクセスを許可するには、以下を実行します。

    * ワークスペースのアクセス制御モードを、**ワークスペースまたはリソースのアクセス許可を使用する** よう構成します。

    * ユーザーに対して、ワークスペースに対する以下のアクセス許可を付与します。 

        * `Microsoft.OperationalInsights/workspaces/read` - ユーザーがワークスペースを列挙して Azure portal でワークスペース ブレードを開くことができるようにするために必要です
        * `Microsoft.OperationalInsights/workspaces/query/read` - クエリを実行できるすべてのユーザーに必要です
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` - Azure AD のサインイン ログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` - 更新管理ソリューションのログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` - 更新管理ソリューションのログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` - 更新管理ログを読み取ることができるようにします
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` - 更新管理ソリューションを使用できるようにするために必要です
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` - 更新管理ソリューションを使用できるようにするために必要です

    * ユーザーに対して、自身のリソースに対するアクセス許可 `*/read` (閲覧者ロールに割り当てられている) または `Microsoft.Insights/logs/*/read` を付与します。 

## <a name="table-level-azure-rbac"></a>テーブル レベルの Azure RBAC

**テーブル レベルの Azure RBAC** では他のアクセス許可に加えて、Log Analytics ワークスペースのデータをさらにきめ細かく定義できます。 この制御を使用すると、特定のユーザーのグループのみがアクセスできる特定のデータ型を定義できます。

[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)を使用したテーブル アクセス制御を実装して、ワークスペース内の特定の[テーブル](../logs/data-platform-logs.md)に対するアクセスを付与します。 これらのロールは、ユーザーの[アクセス モード](../logs/design-logs-deployment.md#access-mode)とは関係なく、ワークスペースコンテキストまたはリソースコンテキストどちらかの[アクセス制御モード](../logs/design-logs-deployment.md#access-control-mode)のワークスペースに適用されます。

次の操作の[カスタム ロール](../../role-based-access-control/custom-roles.md)を作成して、テーブル アクセス制御を定義します。

* テーブルへのアクセス権を付与するには、ロール定義の **[Actions]** セクションにそのテーブルを含めます。 許可されている **アクション** からアクセスを削除するには、 **[NotActions]** セクションに含めます。
* すべてのテーブルを指定するには、Microsoft.OperationalInsights/workspaces/query/* を使用します。

たとえば、_Heartbeat_ テーブルと _AzureActivity_ テーブルへのアクセス権を持つロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

_SecurityBaseline_ テーブルのみのアクセス権を持つロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
上記の例では、許可されるテーブルのリストを定義しています。 次の例は、ユーザーが _SecurityAlert_ テーブルを除くすべてのテーブルにアクセスできる場合のブロック リストの定義を示しています。

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>カスタム ログ

 カスタム ログは、カスタム ログや HTTP Data Collector API などのデータ ソースから作成されます。 ログの種類を特定する最も簡単な方法は、[ログ スキーマの [カスタム ログ]](./log-analytics-tutorial.md#table-schema)に一覧表示されるテーブルを確認することです。

 個々のカスタム ログに対するアクセスは付与できませんが、すべてのカスタム ログに対するアクセスを付与することはできます。 すべてのカスタム ログへのアクセス権を持つロールを作成するには、次の操作を使用してカスタム ロールを作成します。

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
カスタム ログへのアクセスを管理するもうひとつのアプローチは、それを Azure リソースに割り当て、リソース コンテキスト パラダイムを使用してアクセスを管理することです。 この方法を使用するには、[HTTP データ コレクター API](../logs/data-collector-api.md) を介してデータを Log Analytics に取り込む際、[x-ms-AzureResourceId](../logs/data-collector-api.md#request-headers) で指定してリソース ID を含める必要があります。 リソース ID は有効で、アクセス規則が適用されていなくてはなりません。 ログが取り込まれた後、ここで説明されているようにリソースの読み取りアクセスのあるユーザーはログにアクセスできます。

カスタム ログは、特定のリソースに直接関連付けられていないソースから取得されることがあります。 この場合は、このようなログへのアクセスを管理するためだけにリソース グループを作成します。 リソース グループにはコストはかかりませんが、カスタム ログへのアクセスを制御するための有効なリソース ID が付与されます。 たとえば、特定のファイアウォールがカスタム ログを送信する場合は、「MyFireWallLogs」と呼ばれるリソース グループを作成し、API 要求に「MyFireWallLogs」のリソース ID が含まれていることを確認します。 その後、ファイアウォールのログ レコードは、MyFireWallLogs へのアクセスが許可されたユーザー、またはワークスペースへのフル アクセスを持つユーザーのみがアクセスできます。          

### <a name="considerations"></a>考慮事項

* _\*/read_ アクションを含む標準の閲覧者ロールまたは共同作成者ロールを使用してグローバル読み取りアクセス許可がユーザーに付与されると、テーブルごとのアクセス制御がオーバーライドされ、ユーザーにすべてのログへのアクセス権が付与されます。
* テーブルごとのアクセス権がユーザーに付与されるが、その他のアクセス許可は付与されない場合、ユーザーは API からログ データにアクセスできますが、Azure portal からはアクセスできません。 Azure portal からアクセス権を提供するには、基本ロールとして Log Analytics 閲覧者を使用します。
* サブスクリプションの管理者と所有者は、他のすべてのアクセス許可設定に関係なくすべてのデータ型に対するアクセス権を持ちます。
* ワークスペース所有者は、テーブルとごのアクセス制御では他のすべてのユーザーと同様に扱われます。
* 割当て数を減らすには、ロールは個々のユーザーではなくセキュリティ グループに割り当てることをお勧めします。 こうすると、既存のグループ管理ツールを使用して、アクセス権の構成と確認する際に役立ちます。

## <a name="next-steps"></a>次のステップ

* [Log Analytics エージェントの概要](../agents/log-analytics-agent.md)に関するページを参照して、データセンターや他のクラウド環境内のコンピューターからデータを収集します。

* 「[Azure Virtual Machines に関するデータの収集](../vm/quick-collect-azurevm.md)」を参照して、Azure VM からのデータ コレクションを構成します。