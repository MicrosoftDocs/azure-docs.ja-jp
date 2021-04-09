---
title: Runbook から Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Runbook で変更履歴とインベントリを有効にする方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100585921"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Runbook で変更履歴とインベントリを有効にする

この記事では、Runbook を使用して、お客様の環境内の VM に対して[変更履歴とインベントリ](overview.md)を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

この方法では、次の 2 つの Runbook を使用します。

* **Enable-MultipleSolution** - 構成情報の入力を求め、指定された VM に対してクエリを実行し、その他の検証チェックを実行してから、**Enable-AutomationSolution** Runbook を呼び出して、指定されたリソース グループ内の各 VM に対して変更履歴とインベントリを構成するプライマリ Runbook です。
* **Enable-AutomationSolution** - ターゲット リソース グループに指定された 1 つ以上の VM に対して変更履歴とインベントリ を有効にします。 前提条件が満たされていることが確認され、Log Analytics VM 拡張機能がインストールされていることが確認され、見つからない場合はインストールされ、Automation アカウントにリンクされている指定された Log Analytics ワークスペースのスコープ構成に VM が追加されます。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* [Log Analytics ワークスペース](../../azure-monitor/logs/design-logs-deployment.md)
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。
* **Enable-AutomationSolution** Runbook によって使用される 2 つの Automation 資産。 この Runbook が Automation アカウントにまだ存在しない場合、最初の実行時に **Enable-MultipleSolution** Runbook によって自動的にインポートされます。
    * *LASolutionSubscriptionId*:Log Analytics ワークスペースが配置されているサブスクリプション ID。
    * *LASolutionWorkspaceId*:Automation アカウントにリンクされている Log Analytics ワークスペースのワークスペース ID。

    これらの変数は、オンボード VM のワークスペースを構成するために使用されます。 これらが指定されていない場合、最初にサブスクリプションによって変更履歴とインベントリにオンボードされている VM、次に Automation アカウントが含まれているサブスクリプション、次にユーザー アカウントがアクセスできる他のすべてのサブスクリプションがスクリプトによって検索されます。 適切に構成されていない場合、マシンがランダムな Log Analytics ワークスペースにオンボードされる可能性があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

1. Azure portal で、 **[Automation アカウント]** に移動します。 **[Automation アカウント]** ページで、一覧からお使いのアカウントを選択します。

1. Automation アカウントで、 **[構成管理]** の **[インベントリ]** または **[変更履歴]** を選択します。

1. Log Analytics ワークスペースを選択し、 **[有効にする]** をクリックします。 インベントリまたは変更履歴を有効にすると、バナーが表示されます。

    ![Change Tracking と Inventory の有効化](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>モジュールのインストールと更新

Runbook を使用する VM の Update Management を正常に有効にするには、最新の Azure モジュールに更新し、[Az.OperationalInsights](/powershell/module/az.operationalinsights) モジュールをインポートする必要があります。

1. Automation アカウントで、 **[共有リソース]** の **[モジュール]** を選択します。

2. **[Azure モジュールの更新]** を選択して、Azure モジュールを最新バージョンに更新します。

3. **[はい]** を選択して、既存の Azure モジュールすべてを最新バージョンに更新します。

    ![モジュールの更新](media/enable-from-runbook/update-modules.png)

4. **[共有リソース]** の下にある **[モジュール]** に戻ります。

5. **[ギャラリーの参照]** を選択して、モジュール ギャラリーを開きます。

6. `Az.OperationalInsights` を検索し、このモジュールを Automation アカウントにインポートします。

    ![OperationalInsights モジュールのインポート](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>管理する Azure VM の選択

変更履歴とインベントリを有効にすると、その機能で管理する Azure VM を追加できます。

1. Automation アカウントから、 **[構成管理]** の **[Change tracking]\(変更履歴\)** または **[インベントリ]** を選択します。

2. **[Azure VM の追加]** をクリックして VM を追加します。

3. 一覧から VM を選択し、 **[有効]** をクリックします。 このアクションにより、VM に対して変更履歴とインベントリが有効になります。

   ![VM の変更履歴とインベントリを有効にする](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > 変更履歴とインベントリの設定が完了する前に別の機能を有効にしようとすると、`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.` というメッセージが表示されます。

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Runbook をインポートして変更履歴とインベントリを有効にする

1. お使いの Automation アカウントから、 **[プロセスの自動化]** の **[Runbook]** を選択します。

2. **[ギャラリーの参照]** を選択します。

3. **更新プログラムと変更履歴** を探します。

4. Runbook を選択し、 **[ソースの表示]** ページの **[インポート]** をクリックします。

5. **[OK]** をクリックして、その Runbook を Automation アカウントにインポートします。

   ![設定用の Runbook のインポート](media/enable-from-runbook/import-from-gallery.png)

6. **[Runbook]** ページで、**Enable-MultipleSolution** Runbook を選択し、 **[編集]** をクリックします。 テキスト エディターで **[発行]** を選択します。

7. 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして Runbook を発行します。

## <a name="start-the-runbook"></a>Runbook の起動

Azure VM でこの Runbook を起動するには、[変更履歴とインベントリ] を有効にしておく必要があります。 ターゲット リソース グループに 1 つ以上の VM を構成するには、この機能が有効な既存の VM とリソース グループが必要です。

1. **Enable-MultipleSolution** Runbook を開きます。

   ![複数のソリューション Runbook](media/enable-from-runbook/runbook-overview.png)

2. [開始] ボタンをクリックし、次のフィールドにパラメーター値を入力します。

   * **VMNAME** - 変更履歴とインベントリに追加する既存の VM の名前。 リソース グループ内のすべての VM を追加するには、このフィールドを空白のままにします。
   * **VMRESOURCEGROUP** - VM を有効にするリソース グループの名前。
   * **SUBSCRIPTIONID** - 有効にする新しい VM のサブスクリプション ID。 ワークスペースのサブスクリプションを使用するには、このフィールドを空白のままにします。 別のサブスクリプション ID を使用する場合は、ご自分の Automation アカウントの実行アカウントをサブスクリプションの共同作成者として追加してください。
   * **ALREADYONBOARDEDVM** - 既に更新が手動で有効にされている VM の名前。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM が属しているリソース グループの名前。
   * **SOLUTIONTYPE** - 「**ChangeTracking**」と入力します。

   ![Enable-MultipleSolution Runbook パラメーター](media/enable-from-runbook/runbook-parameters.png)

3. **[OK]** を選択して、Runbook ジョブを開始します。

4. **[ジョブ]** ページで Runbook ジョブの進行状況とエラーを監視します。

## <a name="next-steps"></a>次のステップ

* Runbook のスケジュールを設定するには、「[Azure Automation のスケジュールを管理する](../shared-resources/schedules.md)」を参照してください。

* この機能の操作の詳細については、[変更履歴の管理](manage-change-tracking.md)と[インベントリの管理](manage-inventory-vms.md)に関するページを参照してください。

* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。


