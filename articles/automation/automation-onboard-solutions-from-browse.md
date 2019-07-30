---
title: Azure Automation の Update Management、Change Tracking、および Inventory ソリューションを複数の VM で使用する準備を行う方法について説明します
description: Azure Automation に含まれる Update Management、Change Tracking、および Inventory ソリューションで、Azure Virtual Machine の使用準備を行う方法について説明します
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 11dda62a7d8a92b17eb1d431e61086680f356195
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476616"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Update Management、Change Tracking、および Inventory ソリューションを複数の VM で使用できるようにする

Azure Automation には、オペレーティング システムのセキュリティ更新プログラム、変更の追跡、およびご使用のコンピューターにインストールされている内容のインベントリを管理するソリューションが用意されています。 マシンの使用準備を行う方法は複数あります。[仮想マシンから](automation-onboard-solutions-from-vm.md)、お使いの [Automation アカウントから](automation-onboard-solutions-from-automation-account.md) (仮想マシンを参照する場合) または [Runbook](automation-onboard-solutions.md) によってソリューションの使用準備を行うことができます。 この記事では、Azure の仮想マシンを参照するときにこれらのソリューションの使用準備を行う方法について説明します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[https://portal.azure.com ](https://portal.azure.com) で Azure にサインインします

## <a name="enable-solutions"></a>ソリューションの有効化

Azure Portal で**仮想マシン**に移動します。

チェックボックスを使用して、Change Tracking と Inventory または Update Management を使用準備する仮想マシンを選択します。 使用準備は、同時に最大 3 つの異なるリソース グループに対して行うことができます。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

![VM の一覧](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> フィルター コントロールを使用して仮想マシンの一覧を変更し、一番上のチェックボックスをオンにし、一覧のすべての仮想マシンを選択します。

コマンド バーから **[サービス]** をクリックし、 **[Change Tracking]** 、 **[Inventory]** 、または **[Update Management]** のいずれかを選択します。

> [!NOTE]
> **[変更追跡]** と **[インベントリ]** は、同じソリューションを使用します。一方が有効であれば、もう一方も有効です。

次のイメージは Update Management です。 Change Tracking と Inventory のレイアウトおよび動作は同じです。

仮想マシンの一覧はフィルターされ、同じサブスクリプションおよび場所にある仮想マシンのみが表示されます。 お使いの仮想マシンが 4 つ以上のリソース グループにある場合、最初の 3 つのリソース グループが選択されます。

### <a name="resource-group-limit"></a> オンボードの制限事項

オンボードのために使用できるリソース グループの数は、[Resource Manager のデプロイメントの制限](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md)によって制限されます。 Resource Manager のデプロイメントは、更新プログラムの展開と混同してはならず、デプロイあたり 5 つのリソース グループに制限されています。 オンボードの整合性を確保するため、それらのリソース グループのうちの 2 つは、Log Analytics ワークスペース、Automation アカウント、および関連リソースを構成するために予約されています。 これで、デプロイのために選択するリソース グループは 3 つ残ります。

フィルター コントロールを使用して、異なるサブスクリプション、場所、およびリソース グループから仮想マシンを選択します。

![Update Management ソリューションの使用準備](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Log Analytics ワークスペースと Automation アカウントの選択を確認します。 既定では、既存のワークスペースと Automation アカウントが選択されます。 異なる Log Analytics ワークスペースと Automation アカウントを使用する場合は、 **[CUSTOM] (カスタム)** をクリックして、 **[Custom Configuration] (カスタム構成)** ページから選択します。 Log Analytics ワークスペースを選択すると、それが Automation アカウントにリンクされているかどうかを判断するためのチェックが行われます。 リンクされている Automation アカウントが見つかった場合は、次の画面が表示されます。 完了したら、 **[OK]** をクリックします。

![ワークスペースとアカウントを選択する](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

選択したワークスペースが Automation アカウントにリンクされていない場合は、次の画面が表示されます。 Automation アカウントを選択し、完了したら **[OK]** をクリックします。

![ワークスペースがありません](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> ソリューションを有効にすると、Log Analytics ワークスペースと Automation アカウントをリンクするために特定のリージョンのみがサポートされます。
>
> サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

有効にしない仮想マシンの横のチェックボックスをオフにします。 有効にできない仮想マシンは既に選択解除されています。

**[有効にする]** をクリックしてソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

## <a name="unlink-workspace"></a>ワークスペースのリンクの解除

以下のソリューションは、Log Analytics ワークスペースに依存しています。

* [更新管理](automation-update-management.md)
* [変更の追跡](automation-change-tracking.md)
* [勤務時間外に VM を起動/停止する](automation-solution-vm-management.md)

ご使用の Automation アカウントを Log Analytics ワークスペースと統合する必要がなくなった場合は、Azure portal から直接、そのアカウントのリンクを解除できます。 作業を進める前に、上記で説明したソリューションを削除する必要があります。そうしないと、このプロセスを続行できません。 インポート済みのソリューションに関する記事を確認して、削除に必要な手順を理解してください。

これらのソリューションを削除したら、以下の手順を行うと、Automation アカウントのリンクを解除できます。

> [!NOTE]
> Azure SQL 監視ソリューションの以前のバージョンを含む一部のソリューションでは、Automation アセットを作成している可能性があり、ワークスペースのリンクを解除する前にその削除が必要な場合があります。

1. Azure portal から Automation アカウントを開き、[Automation アカウント] ページで、左側にある **[関連リソース]** セクションで **[リンクされたワークスペース]** を選択します。

2. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックします。

   ![[ワークスペースのリンクを解除] ページ](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png)。

   続行するかどうかを確認するプロンプトが表示されます。

3. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

更新の管理ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* スケジュールの更新 - 各スケジュールには、作成した更新のデプロイに一致する名前が付いています。

* ソリューションに作成されたハイブリッド worker グループ - 各グループの名前は machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8 のようになります。

勤務時間外の VM の開始/停止ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* VM の開始/停止の Runbook スケジュール
* VM の開始/停止の Runbook
* 変数

Automation アカウントの自分のワークスペースを Log Analytics ワークスペースからリンク解除することもできます。 自分のワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。 [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="troubleshooting"></a>トラブルシューティング

複数のマシンを使用準備する場合、 **[有効にできません]** と表示されるマシンが存在する場合があります。 有効にできないマシンが存在する理由はさまざまです。 次の各セクションでは、使用準備しようとするときに VM が**有効にできない**状態になる考え得る理由を示します。

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM reports to a different workspace: '\<workspaceName\>' (VM のレポート先が異なるワークスペース <ワークスペース名> である)。  Change configuration to use it for enabling (これを使用するように構成を変更して有効化できるようにしてください)

**原因**:このエラーは、使用準備しようとしている VM のレポート先が別のワークスペースであることを示しています。

**解決策**: **[構成として使用する]** をクリックして、対象の Automation アカウントおよび Log Analytics ワークスペースを変更します。

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM reports to a workspace that is not available in this subscription (VM のレポート先がこのサブスクリプションで使用できないワークスペースである)

**原因**:仮想マシンのレポート先ワークスペースが次のいずれかです。

* 別のサブスクリプションにある
* 存在しない
* アクセス許可がないリソース グループにある

**解決策**:VM のレポート先のワークスペースに関連付けられた Automation アカウントを探し、スコープ構成を変更して仮想マシンを使用準備します。

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM operating system version or distribution is not supported (VM のオペレーティング システムのバージョンまたはディストリビューションがサポートされていない)

**原因:** このソリューションは、すべての Linux ディストリビューションまたはすべてのバージョンの Windows に対してサポートされているわけではありません。

**解決策:** [サポート対象クライアントの一覧](automation-update-management.md#clients)を参照して解決してください。

### <a name="classic-vms-cannot-be-enabled"></a>Classic VMs cannot be enabled (クラシック VM を有効にできない)

**原因**:クラシック デプロイ モデルを使用する仮想マシンはサポートされていません。

**解決策**:仮想マシンを Resource Manager デプロイ モデルに移行します。 この方法については、「[Migrate classic deployment model resources](../virtual-machines/windows/migration-classic-resource-manager-overview.md)」(クラシック デプロイ モデル リソースの移行) を参照してください。

### <a name="vm-is-stopped-deallocated"></a>VM is stopped. (deallocated) (VM が停止 (割り当て解除) している)

**原因**:仮想マシンが**実行**状態ではありません。

**解決策**:VM がソリューションを使用できるようにするためには VM は実行中である必要があります。 **[VM の起動]** インライン リンクをクリックし、ページから移動せずに VM を起動します。

## <a name="next-steps"></a>次の手順

お使いの仮想マシンでソリューションを使用できるようになりました。Update Management の概要の記事にアクセスし、お使いのマシンに更新の評価を表示する方法を参照してください。

> [!div class="nextstepaction"]
> [Update Management - 更新の評価を表示する](./automation-update-management.md#viewing-update-assessments)

ソリューションおよびその使用方法についてのその他のチュートリアル

* [チュートリアル - VM の更新プログラムの管理](automation-tutorial-update-management.md)

* [チュートリアル - VM 上のソフトウェアの特定](automation-tutorial-installed-software.md)

* [チュートリアル - VM に対する変更のトラブルシューティング](automation-tutorial-troubleshoot-changes.md)
