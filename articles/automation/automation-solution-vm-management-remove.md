---
title: Azure Automation の Start/Stop VMs during off-hours の削除の概要
description: この記事では、Start/Stop VMs during off-hours 機能を削除し、Log Analytics ワークスペースから Automation アカウントのリンクを解除する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122087"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Automation アカウントから Start/Stop VMs during off-hours を削除する

Azure VM の実行状態を管理するために Start/Stop VMs during off-hours 機能を有効にした後、使用を停止することもできます。 この機能を削除するには、サポートされているデプロイ モデルに基づいて、次のいずれかの方法を使用できます。

* Automation アカウントとリンクされた Azure Monitor Log Analytics ワークスペース (それぞれこの機能をサポートするための専用のもの) が含まれているリソースグループを削除します。
* Automation アカウントから Log Analytics ワークスペースのリンクを解除し、この機能専用の Automation アカウントを削除します。
* 他の管理および監視の目的をサポートしている Automation アカウントとリンクされたワークスペースから機能を削除します。

この機能を削除すると、関連付けられている Runbook のみが削除されますが、デプロイ中に作成されたスケジュールや変数、またはそれ以降に作成されたカスタム定義のものは削除されません。

## <a name="delete-the-dedicated-resource-group"></a>専用のリソース グループを削除する

リソース グループを削除するには、「[Azure Resource Manager のリソース グループとリソースの削除](../azure-resource-manager/management/delete-resource-group.md)」記事に記載されている手順に従います。

## <a name="delete-the-automation-account"></a>Automation アカウントを削除する

Start/Stop VMs during off-hours 専用の Automation アカウントを削除するには、次の手順を実行します。

1. [https://portal.azure.com](https://portal.azure.com) で Azure にサインインします。

2. 自分の Automation アカウントに移動して、 **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースに移動]** を選択します。

4. **[全般]** の下にある **[ソリューション]** をクリックします。

5. [ソリューション] ページで、**Start-Stop-VM[ワークスペース]** を選択します。

6. **VMManagementSolution[ワークスペース]** ページで、メニューから **[削除]** を選択します。

7. 情報が検証され、機能が削除されている間、メニューから **[通知]** を選択してその進行状況を追跡できます。 削除プロセスの後、[ソリューション] ページに戻ります。

### <a name="unlink-workspace-from-automation-account"></a>Automation アカウントからワークスペースのリンクを解除する

Automation アカウントから Log Analytics ワークスペースのリンクを解除するには、2 つのオプションがあります。 このプロセスは、Automation アカウントまたはリンクされたワークスペースから実行できます。

Automation アカウントからリンクを解除するには、次の手順を実行します。

1. Azure portal で、 **[Automation アカウント]** を選択します。

2. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースのリンクを解除]** ページで **[ワークスペースのリンクを解除]** を選択して、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics ワークスペースとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

ワークスペースからリンクを解除するには、次の手順を実行します。

1. Azure portal で、 **[Log Analytics ワークスペース]** を選択します。

2. ワークスペースから、 **[関連リソース]** の **[Automation アカウント]** を選択します。

3. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択し、プロンプトに応答します。

Automation アカウントとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="delete-automation-account"></a>Automation アカウントを削除する

1. Azure portal で、 **[Automation アカウント]** を選択します。

2. Automation アカウントを開き、メニューから **[削除]** を選択します。

情報が検証され、アカウントが削除されている間、メニューから **[通知]** を選択してその進行状況を追跡できます。

## <a name="delete-the-feature"></a>機能を削除する

Automation アカウントから Start/Stop VMs during off-hours を削除するには、次の手順を実行します。 Automation アカウントと Log Analytics ワークスペースは、このプロセスの一部として削除されません。 Log Analytics ワークスペースを保持しない場合は、手動で削除する必要があります。 ワークスペースの削除の詳細については、「[Azure Log Analytics ワークスペースの削除と復旧](../azure-monitor/logs/delete-workspace.md)」を参照してください。

1. 自分の Automation アカウントに移動して、 **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

2. **[ワークスペースに移動]** を選択します。

3. **[全般]** の下にある **[ソリューション]** をクリックします。

4. [ソリューション] ページで、**Start-Stop-VM[ワークスペース]** を選択します。

5. **VMManagementSolution[ワークスペース]** ページで、メニューから **[削除]** を選択します。

    ![VM 管理機能を削除する](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. [ソリューションの削除] ウィンドウで、機能を削除するかどうかを確定します。

7. 情報が検証され、機能が削除されている間、メニューから **[通知]** を選択してその進行状況を追跡できます。 削除プロセスの後、[ソリューション] ページに戻ります。

8. 機能によって、または後から自分によって (変数、スケジュールなどの) [リソース](automation-solution-vm-management.md#components)が作成されないようにするには、アカウントから手動で削除する必要があります。

## <a name="next-steps"></a>次のステップ

この機能を再度有効にするには、[Start/Stop during off-hours の有効化](automation-solution-vm-management-enable.md)に関するページを参照してください。