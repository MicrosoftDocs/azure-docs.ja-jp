---
title: Azure Automation アカウントを別のサブスクリプションに移動する
description: この記事では、Automation アカウントを別のサブスクリプションに移動する方法について説明します
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2d1c747a52a1e8dedd0b5ba411b673eee463a2b6
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849583"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation アカウントを別のサブスクリプションに移動する

Azure では、新しいリソース グループまたはサブスクリプションに一部のリソースを移動する機能を提供しています。 Azure portal、PowerShell、Azure CLI、または REST API を使用して、リソースを移動できます。 方法の詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

Azure の Automation アカウントは、移動できるリソースの 1 つです。 この記事では、Automation アカウントを別のリソースまたはサブスクリプションに移動する手順について説明します。

Automation アカウントを移行する手順の概要は次のとおりです。

1. ソリューションを削除します。
2. ワークスペースのリンクを解除します。
3. Automation アカウントを移動します。
4. 実行アカウントを削除して再作成します。
5. ソリューションを再度有効にします。

## <a name="remove-solutions"></a>ソリューションを削除する

Automation アカウントからワークスペースのリンクを解除するには、これらのソリューションをワークスペースから削除する必要があります。
- **変更履歴とインベントリ**
- **更新管理**
- **勤務時間外の VM の起動/停止**

リソース グループで、各ソリューションを検索して **[削除]** を選択します。 **[リソースの削除]** ページで、削除するリソースを確認し、 **[削除]** を選択します。

![Azure portal からソリューションを削除する](../media/move-account/delete-solutions.png)

次の例で示しているように、[Remove-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) コマンドレットで同じタスクを完了できます。

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>VM の開始/停止の追加手順

**[Start/Stop VMs]** (VM の開始/停止) ソリューションの場合、ソリューションによって作成されたアラート ルールを削除する必要もあります。

Azure portal で、リソース グループに移動し、 **[監視]**  >  **[アラート]**  >  **[アラート ルールの管理]** を選択します。

![[アラート ルールの管理] の選択を示した [アラート] ページ](../media/move-account/alert-rules.png)

**[ルール]** ページに、そのリソース グループで構成されているアラートの一覧を表示する必要があります。 **[Start/Stop VMs]** (VM の開始/停止) ソリューションは、次の 3 つのアラート ルールを作成します。

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

これら 3 つのアラート ルールを選択し、 **[削除]** を選択します。 このアクションは、これらのアラート ルールを削除します。

![選択したルールの削除の確認を要求する [ルール] ページ](../media/move-account/delete-rules.png)

> [!NOTE]
> **[ルール]** ページにアラート ルールが表示されていない場合、削除した可能性があるので、 **[無効]** アラートを表示するように **[状態]** を変更します。

アラート ルールが削除されたら、 **[Start/Stop VMs]** (VM の開始/停止) ソリューション通知に対して作成されたアクション グループを削除します。

Azure portal で、 **[監視]**  >  **[アラート]**  >  **[アクション グループの管理]** の順に選択します。

一覧から **StartStop_VM_Notification** を選択します。 [アクション グループ] ページで、 **[削除]** を選択します。

![[アクション グループ] ページ、[削除] を選択します](../media/move-account/delete-action-group.png)

同様に、次の例に示すように、PowerShell を [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) コマンドレットと共に使用してアクション グループを削除することができます。

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>ワークスペースのリンクを解除する

Azure portal で、 **[Automation アカウント]**  >  **[関連リソース]**  >  **[Linked workspace]** (リンクされたワークスペース) の順に選択します。 **[ワークスペースのリンクを解除]** を選択して、Automation アカウントからワークスペースのリンクを解除します。

![Automation アカウントからワークスペースのリンクを解除する](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation アカウントを移動する

前の項目を削除した後、Automation アカウントとその Runbook を引き続き削除できます。 Azure portal で、Automation アカウントのリソース グループを参照します。 **[移動]**  >  **[別のサブスクリプションに移動する]** の順に選択します。

![[リソース グループ] ページ、別のサブスクリプションに移動します](../media/move-account/move-resources.png)

移動するリソース グループ内のリソースを選択します。 必ず、 **[Automation アカウント]** 、 **[Runbook]** 、および **[Log Analytics ワークスペース]** リソースを含めてください。

移行が完了したら、すべてを機能させるために必要な追加手順があります。

## <a name="re-create-run-as-accounts"></a>実行アカウントを再作成する

[[実行アカウント]](../manage-runas-account.md) は、Azure リソースで認証するために、Azure Active Directory にサービス プリンシパルを作成します。 サブスクリプションを変更すると、Automation アカウントは既存の実行アカウントを使用しなくなります。

新しいサブスクリプションの Automation アカウントに移動し、 **[アカウント設定]** の下で **[実行アカウント]** を選択します。 実行アカウントが現在、不完全と表示されていることがわかります。

![実行アカウントが不完全です](../media/move-account/run-as-accounts.png)

各実行アカウントを選択します。 **[プロパティ]** ページで、 **[削除]** を選択して実行アカウントを削除します。

> [!NOTE]
> 実行アカウントを作成または表示するアクセス許可がない場合、次のメッセージが表示されます。`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 実行アカウントを構成するために必要なアクセス許可については、「[実行アカウントを構成するために必要なアクセス許可](../manage-runas-account.md#permissions)」を参照してください。

実行アカウントが削除された後、 **[Azure 実行アカウント]** の下で **[作成]** を選択します。 **[Azure 実行アカウントを追加する]** ページで、 **[作成]** を選択して実行アカウントとサービス プリンシパルを作成します。 **Azure クラシック実行アカウント**で上記の手順を繰り返します。

## <a name="enable-solutions"></a>ソリューションの有効化

実行アカウントを再作成した後、移動前に、削除したソリューションを再び有効にします。 **[変更履歴とインベントリ]** および **[更新管理]** を有効にするには、Automation アカウントでそれぞれの機能を選択します。 上に移動した Log Analytics ワークスペースを選択し、 **[有効化]** を選択します。

![移動した Automation アカウントでソリューションを再度有効にする](../media/move-account/reenable-solutions.png)

既存の Log Analytics ワークスペースを接続したときに、ソリューションにオンボードされるマシンが表示されます。

勤務時間外ソリューション中に **[Start/Stop VMs]** \(VM の開始/停止) を有効にするには、ソリューションを再デプロイする必要があります。 **[関連リソース]** の下で、 **[Start/Stop VMs]\(VM の開始/停止)**  >  **[Learn more about and enable the solution]\(ソリューションの詳細と有効化)**  >  **[作成]** の順に選択してデプロイを開始します。

**[ソリューションの追加]** ページで、Log Analytics ワークスペースと Automation アカウントを選択します。

![[ソリューションの追加] メニュー](../media/move-account/add-solution-vm.png)

ソリューションの構成の詳細については、「[Azure Automation でのピーク時間外 VM 起動/停止ソリューション](../automation-solution-vm-management.md)」を参照してください。

## <a name="post-move-verification"></a>移動後の検証

移動が完了したら、検証する必要のあるタスクの次の一覧を確認します。

|機能|テスト|トラブルシューティング リンク|
|---|---|---|
|Runbooks|Runbook が正常に実行し、Azure リソースに接続できる。|[Runbook のトラブルシューティング](../troubleshoot/runbooks.md)
|ソース管理|ソース管理リポジトリで手動同期を実行できる。|[ソース管理の統合](../source-control-integration.md)|
|変更履歴とインベントリ|マシンから現在のインベントリ データを表示を確認する。|[Change Tracking とインベントリのトラブルシューティング](../troubleshoot/change-tracking.md)|
|更新管理|マシンを表示し、正常な状態であることを確認する。</br>テスト ソフトウェア更新プログラムのデプロイを実行する。|[Update Management の問題をトラブルシューティングする](../troubleshoot/update-management.md)|
|共有リソース|[資格情報](../shared-resources/credentials.md)や[変数](../shared-resources/variables.md)などのすべての共有リソースが表示されていることを確認します。|

## <a name="next-steps"></a>次の手順

Azure でのリソースの移動の詳細については、[Azure でのリソースの移動](../../azure-resource-manager/move-support-resources.md)に関するページを参照してください。
