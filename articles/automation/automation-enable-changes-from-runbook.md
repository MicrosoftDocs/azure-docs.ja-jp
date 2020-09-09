---
title: Runbook から Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Runbook で変更履歴とインベントリを有効にする方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 92149289c6c422179ddb3562274020acf779f10b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186284"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Runbook で変更履歴とインベントリを有効にする

この記事では、Runbook を使用して、お客様の環境内の VM に対して[変更履歴とインベントリ](change-tracking.md)機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。 

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](./index.yml)。
* [仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化 

1. Azure portal で **[Automation アカウント]** を選択し、一覧からご自身の Automation アカウントを選択します。
1. **[構成管理]** で **[Inventory]** を選択します。
1. 既存の Log Analytics ワークスペースを選択するか、新規作成します。 
1. **[有効化]** をクリックします。

    ![Change Tracking と Inventory の有効化](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>管理する Azure VM の選択

変更履歴とインベントリを有効にすると、その機能で管理する Azure VM を追加できます。

1. Automation アカウントから、 **[構成管理]** の **[Change tracking]\(変更履歴\)** または **[インベントリ]** を選択します。

2. **[Azure VM の追加]** をクリックして VM を追加します。

3. 一覧から VM を選択し、 **[有効]** をクリックします。 このアクションにより、VM に対して変更履歴とインベントリが有効になります。

   ![VM の変更履歴とインベントリを有効にする](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > 変更履歴とインベントリの設定が完了する前に別の機能を有効にしようとすると、`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.` というメッセージが表示されます。

## <a name="install-and-update-modules"></a>モジュールのインストールと更新

VM の変更履歴とインベントリを適切に有効化するには、最新の Azure モジュールに更新し、[Az.OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) モジュールをインポートする必要があります。

1. Automation アカウントで、 **[共有リソース]** の **[モジュール]** を選択します。 
2. **[Azure モジュールの更新]** を選択して、Azure モジュールを最新バージョンに更新します。 
3. **[はい]** を選択して、既存の Azure モジュールすべてを最新バージョンに更新します。

    ![モジュールの更新](media/automation-enable-changes-from-runbook/update-modules.png)

4. **[共有リソース]** の下にある **[モジュール]** に戻ります。 
5. **[ギャラリーを参照]** を選択して、モジュール ギャラリーを開きます。 
6. Az.OperationalInsights を検索し、そのモジュールを Automation アカウントにインポートします。

    ![OperationalInsights モジュールのインポート](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Runbook をインポートして変更履歴とインベントリを有効にする

1. お使いの Automation アカウントから、 **[プロセスの自動化]** の **[Runbook]** を選択します。
2. **[ギャラリーの参照]** を選択します。
3. `update and change tracking` を検索します。
4. Runbook を選択し、[ソースの表示] ページの **[インポート]** をクリックします。 
5. **[OK]** をクリックして、その Runbook を Automation アカウントにインポートします。

   ![設定用の Runbook のインポート](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. [Runbook] ページで、 **[編集]** をクリックし、 **[発行]** を選択します。 
7. [Runbook の発行] ペインで **[はい]** をクリックして Runbook を発行します。

## <a name="start-the-runbook"></a>Runbook の起動

Azure VM でこの Runbook を起動するには、[変更履歴とインベントリ] を有効にしておく必要があります。 対応する機能がパラメーターで有効にされている既存の VM とリソース グループが必要です。

1. **Enable-MultipleSolution** Runbook を開きます。

   ![複数のソリューション Runbook](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. [開始] ボタンをクリックし、次のフィールドにパラメーター値を入力します。

   * **VMNAME** - 変更履歴とインベントリに追加する既存の VM の名前。 リソース グループ内のすべての VM を追加するには、このフィールドを空白のままにします。
   * **VMRESOURCEGROUP** - VM を有効にするリソース グループの名前。
   * **SUBSCRIPTIONID** - 有効にする新しい VM のサブスクリプション ID。 ワークスペースのサブスクリプションを使用するには、このフィールドを空白のままにします。 別のサブスクリプション ID を使用する場合は、ご自分の Automation アカウントの実行アカウントをサブスクリプションの共同作成者として追加してください。
   * **ALREADYONBOARDEDVM** - 既に変更用に手動で有効にされている VM の名前。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM が属しているリソース グループの名前。
   * **SOLUTIONTYPE** - 「**ChangeTracking**」と入力します。

   ![Enable-MultipleSolution Runbook パラメーター](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. **[OK]** を選択して、Runbook ジョブを開始します。
1. Runbook ジョブ ページで進行状況とエラーを監視します。

## <a name="next-steps"></a>次のステップ

* Runbook のスケジュールを設定するには、「[Azure Automation のスケジュールを管理する](shared-resources/schedules.md)」を参照してください。
* この機能の操作の詳細については、[変更履歴とインベントリの管理](change-tracking-file-contents.md)に関するページをご覧ください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](troubleshoot/change-tracking.md)」を参照してください。
