---
title: Runbook から Azure Automation の Update Management を有効にする
description: この記事では、Runbook から Update Management を有効にする方法について説明します。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: mvc
ms.openlocfilehash: 5377a3ff7ef7033b57f8785baa615a717ef7fa0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575874"
---
# <a name="enable-update-management-from-a-runbook"></a>Runbook から Update Management を有効にする

この記事では、Runbook を使用して、お客様の環境内の VM に対して [Update Management](overview.md) 機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、Update Management を使用して既存の VM を有効にする必要があります。

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

この方法では、次の 2 つの Runbook を使用します。

* **Enable-MultipleSolution** - 構成情報の入力を求め、指定された VM に対してクエリを実行し、その他の検証チェックを実行してから、**Enable-AutomationSolution** Runbook を呼び出して、指定されたリソース グループ内の各 VM に対して Update Management を構成するプライマリ Runbook。
* **Enable-AutomationSolution** - ターゲット リソース グループに指定された 1 つ以上の VM に対して Update Management を有効にします。 前提条件が満たされていることが確認され、Log Analytics VM 拡張機能がインストールされていることが確認され、見つからない場合はインストールされ、Automation アカウントにリンクされている指定された Log Analytics ワークスペースのスコープ構成に VM が追加されます。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* [Log Analytics ワークスペース](../../azure-monitor/logs/design-logs-deployment.md)
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。
* **Enable-AutomationSolution** Runbook によって使用される 2 つの Automation 資産。 この Runbook が Automation アカウントにまだ存在しない場合、最初の実行時に **Enable-MultipleSolution** Runbook によって自動的にインポートされます。
    * *LASolutionSubscriptionId*:Log Analytics ワークスペースが配置されているサブスクリプション ID。
    * *LASolutionWorkspaceId*:Automation アカウントにリンクされている Log Analytics ワークスペースのワークスペース ID。

    これらの変数は、オンボード VM のワークスペースを構成するために使用され、手動で作成する必要があります。 これらが指定されていない場合、最初にサブスクリプションによって Update Management にオンボードされている VM、次に Automation アカウントが含まれているサブスクリプション、次にユーザー アカウントがアクセスできる他のすべてのサブスクリプションがスクリプトによって検索されます。 適切に構成されていない場合、マシンがランダムな Log Analytics ワークスペースにオンボードされる可能性があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-update-management"></a>Update Management の有効化

1. Azure portal で、 **[Automation アカウント]** に移動します。 **[Automation アカウント]** ページで、一覧からお使いのアカウントを選択します。

2. Automation アカウントの **[更新の管理]** で **[Update Management]** を選択します。

3. Log Analytics ワークスペースを選択し、 **[有効にする]** をクリックします。 Update Management が有効化されている間、バナーが表示されます。

    ![Update Management の有効化](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>モジュールのインストールと更新

Runbook を使用する VM の Update Management を正常に有効にするには、最新の Azure モジュールに更新し、[AzureRM.OperationalInsights](/powershell/module/azurerm.operationalinsights) モジュールをインポートする必要があります。

1. Automation アカウントで、 **[共有リソース]** の **[モジュール]** を選択します。

2. **[Azure モジュールの更新]** を選択して、Azure モジュールを最新バージョンに更新します。

3. **[はい]** を選択して、既存の Azure モジュールすべてを最新バージョンに更新します。

    ![モジュールの更新](media/enable-from-runbook/update-modules.png)

4. **[共有リソース]** の下にある **[モジュール]** に戻ります。

5. **[ギャラリーの参照]** を選択して、モジュール ギャラリーを開きます。

6. `AzureRM.OperationalInsights` を検索し、このモジュールを Automation アカウントにインポートします。

    ![OperationalInsights モジュールのインポート](media/enable-from-runbook/import-operational-insights-module-azurerm.png)

## <a name="select-azure-vm-to-manage"></a>管理する Azure VM の選択

Update Management が有効になると、更新プログラムを受け取る Azure VM を追加できます。

1. Automation アカウントから、セクション **[更新の管理]** の **[Update Management]** を選択します。

2. **[Azure VM の追加]** を選択して VM を追加します。

3. 一覧から VM を選択し、 **[有効にする]** をクリックして VM を管理用に構成します。

   ![VM の Update Management の有効化](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Update Management の設定が完了する前に別の機能を有効にしようとすると、`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.` というメッセージが表示されます。

## <a name="import-a-runbook-to-enable-update-management"></a>Update Management を有効にする Runbook のインポート

1. お使いの Automation アカウントから、 **[プロセスの自動化]** の **[Runbook]** を選択します。

2. **[ギャラリーの参照]** を選択します。

3. **更新プログラムと変更履歴** を探します。

4. Runbook を選択し、 **[ソースの表示]** ページの **[インポート]** をクリックします。

5. **[OK]** をクリックして、その Runbook を Automation アカウントにインポートします。

   ![設定用の Runbook のインポート](media/enable-from-runbook/import-from-gallery.png)

6. **[Runbook]** ページで、**Enable-MultipleSolution** Runbook を選択し、 **[編集]** をクリックします。 テキスト エディターで **[発行]** を選択します。

7. 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして Runbook を発行します。

## <a name="start-the-runbook"></a>Runbook の起動

Azure VM でこの Runbook を起動するには、Update Management を有効にしておく必要があります。 ターゲット リソース グループに 1 つ以上の VM を構成するには、この機能が有効な既存の VM とリソース グループが必要です。

1. **Enable-MultipleSolution** Runbook を開きます。

   ![複数のソリューション Runbook](media/enable-from-runbook/runbook-overview.png)

2. [開始] ボタンをクリックし、次のフィールドにパラメーター値を入力します。

   * **VMNAME** - Update Management に追加する既存の VM の名前。 リソース グループ内のすべての VM を追加するには、このフィールドを空白のままにします。
   * **VMRESOURCEGROUP** - VM を有効にするリソース グループの名前。
   * **SUBSCRIPTIONID** - 有効にする新しい VM のサブスクリプション ID。 ワークスペースのサブスクリプションを使用するには、このフィールドを空白のままにします。 別のサブスクリプション ID を使用する場合は、ご自分の Automation アカウントの実行アカウントをサブスクリプションの共同作成者として追加してください。
   * **ALREADYONBOARDEDVM** - 既に更新が手動で有効にされている VM の名前。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM が属しているリソース グループの名前。
   * **SOLUTIONTYPE** - 「**Updates**」と入力します。

   ![Enable-MultipleSolution Runbook パラメーター](media/enable-from-runbook/runbook-parameters.png)

3. **[OK]** を選択して、Runbook ジョブを開始します。

4. **[ジョブ]** ページで Runbook ジョブの進行状況とエラーを監視します。

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[VM の更新プログラムとパッチの管理](manage-updates-for-vm.md)」を参照してください。

* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](../troubleshoot/update-management.md)に関する記事を参照してください。
