---
title: Update と Change Tracking ソリューションの Azure Automation への配布準備をする
description: Update と Change Tracking ソリューションの Azure Automation への配布準備をする方法について説明します。
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: d247369647106cf1671a8770a6dce21f1a34a4b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082196"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Update と Change Tracking ソリューションの Azure Automation への配布準備をする

このチュートリアルでは、VM から Azure Automation への Update、Change Tracking、および Inventory ソリューションの配布準備を自動的に行う方法について説明します。

> [!div class="checklist"]
> * Azure VM の配布準備
> * ソリューションの有効化
> * モジュールのインストールと更新
> * オンボード Runbook のインポート
> * Runbook の起動

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="onboard-an-azure-vm"></a>Azure VM の配布準備

マシンの使用準備を行う方法は複数あります。[仮想マシンから](automation-onboard-solutions-from-vm.md)、[Automation アカウントから](automation-onboard-solutions-from-automation-account.md)[複数のマシンから参照することから](automation-onboard-solutions-from-browse.md)、または Runbook によって、ソリューションの使用準備を行うことができます。 このチュートリアルでは、Runbook を使用して Update Management を有効にする方法について説明します。 さらに大きな規模で Azure Virtual Machines の配布準備を行うには、Change Tracking または Update Management ソリューションで既存の VM の配布準備をする必要があります。 この手順では、Update Management および Change Tracking で仮想マシンの配布準備を行います。

### <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

Change Tracking と Inventory ソリューションには、仮想マシンで[変更を追跡](automation-vm-change-tracking.md)し、[インベントリを作成](automation-vm-inventory.md)する機能が用意されています。 この手順では、仮想マシンでソリューションを有効にします。

1. 左側のメニューから、**[Automation アカウント]** を選択し、一覧からご自身の Automation アカウントを選択します。
1. **[構成管理]** で **[Inventory]** を選択します。
1. 既存の Log Analytics ワークスペースを選択するか、新規に作成します。 **[有効]** ボタンをクリックします。

![Update ソリューションの配布準備](media/automation-onboard-solutions/inventory-onboard.png)

Change Tracking と Inventory ソリューションの配布準備通知が完了したら、**[構成管理]** で **[Update Management]** をクリックします。

### <a name="enable-update-management"></a>Update Management の有効化

Update Management ソリューションでは、Azure Windows VM の更新プログラムとパッチを管理できます。 利用可能な更新プログラムの状態を迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。 この手順では、VM のソリューションを有効にします。

1. Automation アカウントから、**[更新の管理]** で **[Update Management]** を選択します。
1. 選択されている Log Analytics ワークスペースは、前の手順で使用されたワークスペースと同じです。 **[有効]** をクリックして、Update Management ソリューションの配布準備を行います。

![Update ソリューションの配布準備](media/automation-onboard-solutions/update-onboard.png)

Update Management ソリューションのインストール中、青いバナーが表示されます。 ソリューションが有効のときに、**[構成管理]** で **[Change Tracking]** を選択して、次の手順に移動します。

### <a name="select-azure-vm-to-be-managed"></a>管理する Azure VM を選択します

ソリューションが有効になったら、そのソリューションに、配布準備を行う Azure VM を追加できます。

1. Automation アカウントから、**[Change Tracking ]** ページで、**[+ Azure VM の追加]** を選択して仮想マシンを追加します。

1. 一覧から VM を選択し、**[有効]** を選択します。 このアクションにより、仮想マシンに対して Change Tracking と Inventory ソリューションが有効になります。

   ![VM の Update ソリューションの有効化](media/automation-onboard-solutions/enable-change-tracking.png)

1. VM オンボード通知が完了したら、Automation アカウントから、**[更新の管理]** で **[Update Management]** を選択します。

1. **[+ Azure VM の追加]** を選択して、仮想マシンを追加します。

1. 一覧から VM を選択し、**[有効]** を選択します。 このアクションにより、仮想マシンに対して Update Management ソリューションが有効になります。

   ![VM の Update ソリューションの有効化](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> 他のソリューションが完了するのを待たずに、次のソリューションを有効にしようとすると、"*仮想マシンまたは別の仮想マシンで別のソリューションをインストール中です。そのインストールが完了したら、[有効] ボタンが有効になり、この仮想マシンでソリューションのインストールを要求できるようになります*" というメッセージが表示されます。

## <a name="install-and-update-modules"></a>モジュールのインストールと更新

ソリューションの配布準備を適切に自動化するには、最新の Azure モジュールに更新し、`AzureRM.OperationalInsights` をインポートする必要があります。

## <a name="update-azure-modules"></a>Azure モジュールの更新

Automation アカウントから、**[共有リソース]** で **[モジュール]** を選択します。 **[Azure モジュールの更新]** を選択して、Azure モジュールを最新バージョンに更新します。 既存の Azure モジュールすべてを最新バージョンに更新するかどうかを確認するプロンプトが表示されたら、**[はい]** を選択します。

![モジュールの更新](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>AzureRM.OperationalInsights モジュールのインストール

**[モジュール]** ページで、**[ギャラリーを参照]** を選択して、モジュール ギャラリーを開きます。 AzureRM.OperationalInsights を検索し、そのモジュールを Automation アカウントにインポートします。

![OperationalInsights モジュールのインポート](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>オンボード Runbook のインポート

1. Automation アカウントから、**[プロセスの自動化]** で **[Runbook]** を選択します。
1. **[ギャラリーの参照]** を選択します。
1. **Update と Change Tracking** を検索し、Runbook をクリックして、**[ソースの表示]** ページで **[インポート]** を選択します。 **[OK]** を選択して、その Runbook を Automation アカウントにインポートします。

   ![オンボード Runbook のインポート](media/automation-onboard-solutions/import-from-gallery.png)

1. **[Runbook]** ページで、**[編集]**、**[発行]** の順に選択します。 **[Runbook の発行]** ダイアログで、**[はい]** を選択して、Runbook を発行します。

## <a name="start-the-runbook"></a>Runbook の起動

この Runbook を開始するには、Change Tracking または Update ソリューションの Azure VM への配布準備を完了している必要があります。 パラメーターのために、ソリューションが配布準備された既存の仮想マシンとリソース グループが必要です。

1. Enable-MultipleSolution Runbook を開きます。

   ![複数のソリューション Runbook](media/automation-onboard-solutions/runbook-overview.png)

1. [開始] ボタンをクリックし、パラメーターに次の値を入力します。

   * **VMNAME** - 空のままにします。 Update または Change Tracking ソリューションに配布準備する既存の VM 名。 この値を空のままにすると、リソース グループの VM すべての配布準備が行われます。
   * **VMRESOURCEGROUP** - VM の配布準備が行われるリソース グループの名前。
   * **SUBSCRIPTIONID** - 空のままにします。 配布準備が行われる新しい VM のサブスクリプション ID。 空のままにすると、ワークスペースのサブスクリプションが使用されます。 別のサブスクリプション ID を指定すると、この Automation アカウントの RunAs アカウントも、このサブスクリプションの共同作成者として追加されます。
   * **ALREADYONBOARDEDVM** - Updates または ChangeTracking ソリューションに手動で配布準備された VM の名前。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM がメンバーであるリソース グループの名前。
   * **SOLUTIONTYPE** - 「**Updates**」または「**ChangeTracking**」と入力します

   ![Enable-MultipleSolution Runbook パラメーター](media/automation-onboard-solutions/runbook-parameters.png)

1. **[OK]** を選択して、Runbook ジョブを開始します。
1. Runbook ジョブ ページで進行状況とエラーを監視します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure 仮想マシンの配布準備を手動で行う。
> * 必要な Azure モジュールをインストールして更新する。
> * Azure VM の配布準備を行う Runbook をインポートする。
> * Azure VM の配布準備を自動的に行う Runbook を開始する。

Runbook のスケジュール設定の詳細については、このリンクに従ってください。

> [!div class="nextstepaction"]
> [Runbook のスケジュールを設定する](automation-schedules.md)。
