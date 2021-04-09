---
title: Azure Automation の Update Management 機能を削除する
description: この記事では、Update Management の使用を停止し、Log Analytics ワークスペースから Automation アカウントをリンク解除する方法について説明します。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221693"
---
# <a name="remove-update-management-from-automation-account"></a>Automation アカウントから Update Management を削除する

Azure Automation の Update Management を使用して仮想マシンでの更新プログラムの管理を有効にした後は、その使用を停止し、アカウントおよびリンクされた Log Analytics ワークスペースからその構成を削除することができます。  この記事では、マネージド VM、ご自分の Automation アカウント、Log Analytics ワークスペースから Update Management を完全に削除する方法について説明します。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="remove-management-of-vms"></a>VM の管理を削除する

Update Management を削除する前に、まず VM の管理を停止する必要があります。 機能からそれらの登録を解除するには、「[Update Management から VM を削除する](remove-vms.md)」を参照してください。

## <a name="remove-updatemanagement-solution"></a>UpdateManagement ソリューションを削除する

ワークスペースから Automation アカウントのリンクを解除する前に、次の手順に従って、Update Management を完全に削除する必要があります。 **Updates** ソリューションをワークスペースから削除します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて一覧から候補が絞り込まれます。 **[Log Analytics]** を選択します。

3. Log Analytics ワークスペースの一覧で、Update Management を有効化するときに選択したワークスペースを選択します。

4. 左側で、 **[ソリューション]** を選択します。  

5. ソリューションの一覧から、**Updates(<ワークスペース名>)** を選択します。 ソリューションの **[概要]** ページで、 **[削除]** を選択します。 確認メッセージが表示されたら、 **[はい]** を選択します。

## <a name="unlink-workspace-from-automation-account"></a>Automation アカウントからワークスペースのリンクを解除する

1. Azure portal で、 **[Automation アカウント]** を選択します。

2. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースのリンクを解除]** ページで **[ワークスペースのリンクを解除]** を選択して、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/remove-feature/automation-unlink-workspace-blade.png)

Log Analytics ワークスペースとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

または、Automation アカウントと Log Analytics ワークスペースのリンクをワークスペース内から解除することもできます。

1. Azure Portal で **[Log Analytics]** を選択します。

2. ワークスペースから、 **[関連リソース]** の **[Automation アカウント]** を選択します。

3. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

Automation アカウントとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="cleanup-automation-account"></a>Automation アカウントをクリーンアップする

Update Management が以前のバージョンの Azure SQL Monitoring をサポートするように構成されていた場合、機能のセットアップ時に、Automation の資産が作成されている可能性があります。それらの資産はご自身で削除する必要があります。 Update Management では、以下の項目は不要であり、削除することができます。

   * 更新スケジュール - それぞれに、作成した更新プログラムのデプロイと一致する名前が付いています。
   * Update Management に作成されたハイブリッド worker グループ - 各グループの名前は *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8* のようになります。

## <a name="next-steps"></a>次のステップ

この機能を再度有効にするには、「[Automation アカウントから Update Management を有効にする](enable-from-automation-account.md)」、[Azure portal を参照して Update Management を有効にする方法](enable-from-portal.md)に関するページ、「[Runbook から Update Management を有効にする](enable-from-runbook.md)」、または「[Azure VM から Update Management を有効にする](enable-from-vm.md)」を参照してください。
