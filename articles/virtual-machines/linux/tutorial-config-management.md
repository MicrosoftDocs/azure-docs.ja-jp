---
title: チュートリアル - Linux 仮想マシンの構成を Azure で管理する
description: このチュートリアルでは、Linux 仮想マシン上の変更を特定したりパッケージの更新プログラムを管理したりする方法について説明します。
services: virtual-machines
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 979cac8fd3d2c09443d52c9142a5e7c44127713a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552531"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>チュートリアル:Azure で変更を監視し、Linux 仮想マシンを更新する

Azure Linux VM では、Azure [Change Tracking](../../automation/change-tracking/overview.md) を使用して変更を容易に特定したり、[Update Management](../../automation/update-management/overview.md) を使用してオペレーティング システムの更新プログラムを管理したりすることができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Linux の更新プログラムを管理する
> * 変更とインベントリを監視する

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.30 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-vm"></a>VM を作成する

診断とメトリックの動作を確認するには、VM が必要です。 最初に、[az group create](/cli/azure/group#az-group-create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroupMonitor* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

ここで [az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成し、SSH キーを生成します ( *~/.ssh/* にまだ存在していない場合)。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>ソフトウェア更新プログラムを管理する

更新管理では、Azure Linux VM の更新プログラムとパッチを管理できます。
VM から直接、利用可能な更新プログラムのステータスを迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

### <a name="enable-update-management"></a>Update Management の有効化

お使いの VM で Update Management を有効にする

1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. [VM] 画面の **[操作]** セクションで、 **[更新の管理]** を選択します。 **[更新管理の有効化]** 画面が開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。
この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../../azure-monitor/logs/log-query-overview.md) ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。
ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。
更新を必要とする VM で追加のアクションを実行する場合、Azure Automation を使用すると、VM に対して Runbook を実行して、更新プログラムをダウンロードして適用するなどの操作を行うことができます。

また、検証プロセスでは、VM に Log Analytics エージェントと Automation ハイブリッド runbook worker がプロビジョニングされているかどうかも確認されます。 このエージェントは VM との通信に使用され、更新ステータスに関する情報を取得します。

Log Analytics ワークスペースと Automation アカウントを選択し、 **[有効にする]** を選択して、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

オンボード中に次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) ワークスペース
* [Automation アカウント](../../automation/index.yml)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) が VM で有効になっている

**[更新の管理]** 画面が開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[有効にする]** を選択します。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

![Update Management ソリューションを有効にする](./media/tutorial-monitoring/manage-updates-update-enable.png)

ソリューションを有効にするには最大 15 分かかります。 この処理中はブラウザーのウィンドウは閉じないでください。 ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Azure Monitor ログに送られます。 データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

### <a name="view-update-assessment"></a>更新の評価を確認する

**更新管理** が有効になると、 **[更新管理]** 画面が表示されます。 更新の評価が完了すると、 **[不足している更新プログラム]** タブに、不足している更新プログラムの一覧が表示されます。

 ![更新ステータスを確認する](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

**[更新の管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** をクリックして、VM の新しい更新プログラムのデプロイをスケジュールします。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

新しい更新プログラムのデプロイを作成するには、 **[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** ページが開きます。 次の表で説明されているプロパティの値を入力し、 **[作成]** をクリックします。

| プロパティ | 説明 |
| --- | --- |
| 名前 |更新プログラムの展開を識別する一意の名前。 |
|オペレーティング システム| Linux または Windows|
| 更新するグループ |Azure マシンの場合、サブスクリプション、リソース グループ、場所、およびタグの組み合わせに基づいてクエリを定義し、デプロイに含める Azure VM の動的グループを構築します。 </br></br>Azure 以外のマシンの場合、既存の保存された検索を選択して、デプロイに含める Azure 以外のマシンのグループを選択します。 </br></br>詳しくは、[動的グループ](../../automation/update-management/configure-groups.md)に関するページをご覧ください。|
| 更新するマシン |保存した検索条件、インポートしたグループを選択するか、ドロップダウンから [マシン] を選択し、個別のマシンを選択します。 **[マシン]** を選択すると、マシンの準備状況が **[エージェントの更新の準備]** 列に示されます。</br> Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../../azure-monitor/logs/computer-groups.md)に関するページを参照してください |
|更新プログラムの分類|必要な更新プログラムの分類すべてを選択します|
|更新プログラムの包含/除外|**[包含/除外]** ページが開きます。 含めるまたは除外する更新プログラムは別のタブに表示されます。 包含を処理する方法について詳しくは、「[更新プログラムのデプロイをスケジュールする](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment)」を参照してください。 |
|スケジュール設定|開始する時刻を選択し、繰り返しの設定として、[1 回] または [定期的] のいずれかを選択します|
| 事前スクリプトと事後スクリプト|デプロイの前後に実行するスクリプトを選択します|
| メンテナンス期間 |更新プログラムに対して設定された分数です。 30 分未満の値を指定することはできません。また、6 時間を超えることはできません |
| 再起動制御| 再起動の処理方法を決定します。 使用できるオプションは次のとおりです。</br>必要に応じて再起動 (既定値)</br>常に再起動</br>再起動しない</br>Only reboot - will not install updates (再起動のみ - 更新プログラムをインストールしない)|

更新プログラムのデプロイはプログラムで作成することもできます。 REST API を使用して更新プログラムのデプロイを作成する方法については、「[Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create)」(ソフトウェア更新プログラムの構成 - 作成) をご覧ください。 週単位の更新プログラムのデプロイを作成するために使用できるサンプル Runbook もあります。 この Runbook について詳しくは、「[Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)」(リソース グループ内の VM に対して週単位の更新プログラムのデプロイを作成する) をご覧ください。

スケジュールの構成が完了したら、 **[作成]** ボタンをクリックして、状態ダッシュボードに戻ります。
**スケジュール済み** の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

### <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を確認する

スケジュールされた展開の開始後、 **[更新管理]** 画面の **[更新プログラムの展開]** タブに、展開の状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。 正常に完了すると、状態は **[成功]** に変わります。
デプロイ時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。
完了した更新プログラムのデプロイを選択すると、その更新プログラムのデプロイ用のダッシュボードが表示されます。

![特定のデプロイに関する更新プログラムのデプロイ ステータスのダッシュボード](./media/tutorial-monitoring/manage-updates-view-results.png)

**[新プログラムを実行した結果]** のタイルに表示されるのは、VM 上の更新プログラムの合計数とデプロイ結果の概要です。
右側の表に、各更新プログラムとインストールの結果の詳細が示されます。これは、次の値のいずれかになります。

* **試行されていません**: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **成功**: 更新できました。
* **失敗**: 更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、 **[すべてのログ]** を選択します。

ターゲット VM での更新プログラムのデプロイを管理する Runbook のジョブ ストリームを確認するには、 **[出力]** タイルを選択します。

展開で発生したエラーの詳細情報を確認するには、 **[エラー]** を選択します。

## <a name="monitor-changes-and-inventory"></a>変更とインベントリを監視する

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

### <a name="enable-change-and-inventory-management"></a>変更とインベントリの管理を有効にする

使用している VM の変更とインベントリの管理を有効にします。

1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. VM 画面で、 **[操作]** セクションの **[インベントリ]** または **[変更の追跡]** を選択します。 **[Enable Change Tracking and Inventory]\(変更の追跡とインベントリの有効化\)** 画面が開きます。

使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[有効にする]** を選択します。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。 メニューではソリューションは別々に表示されますが、同じソリューションです。 一方を有効にすると、使用している VM に対して両方が有効になります。

![変更とインベントリの追跡を有効にする](./media/tutorial-monitoring/manage-inventory-enable.png)

ソリューションを有効にした後、VM でインベントリが収集され、データが表示されるまでに時間がかかる場合があります。

### <a name="track-changes"></a>変更を追跡する

使用している VM で、 **[操作]** の **[変更の追跡]** を選択します。 **[設定の編集]** を選択すると、 **[変更の追跡]** ページが表示されます。 追跡する設定の種類を選択し、 **[+ 追加]** を選択して、設定を構成します。 Linux の場合は、 **[Linux ファイル]** を選択できます。

変更の追跡の詳細については、[VM での変更のトラブルシューティング](../../automation/automation-tutorial-troubleshoot-changes.md)に関するページをご覧ください

### <a name="view-inventory"></a>インベントリを表示する

使用している VM で、 **[操作]** の **[インベントリ]** を選択します。 **[ソフトウェア]** タブには、検出されたソフトウェアの一覧が表示されます。 各ソフトウェア レコードの詳細情報を表で確認できます。 これらの詳細には、ソフトウェアの名前、バージョン、発行元、最終更新時刻などが含まれます。

![インベントリを表示する](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>アクティビティ ログおよび変更を監視する

VM の **[変更履歴]** ページから **[アクティビティ ログ接続の管理]** を選択します。 このタスクで、 **[Azure アクティビティ ログ]** ページが開きます。 **[接続]** を選択して、変更履歴を VM の Azure アクティビティ ログに接続します。

この設定を有効にして、VM の **[概要]** ページに移動し、 **[停止]** を選択して VM を停止します。 メッセージが表示されたら、 **[はい]** を選択して VM を停止します。 割り当てが解除されたら、 **[スタート]** を選択して VM を再起動します。

VM の起動時と停止時には、イベントがアクティビティ ログに記録されます。 **[変更履歴]** ページに戻ります。 ページの下部にある **[イベント]** タブを選択します。 しばらくすると、グラフと表にイベントが表示されます。 各イベントを選択して、そのイベントの詳細情報を表示できます。

![アクティビティ ログで変更を表示する](./media/tutorial-monitoring/manage-activitylog-view-results.png)

グラフには、時間の経過とともに発生した変更が表示されます。 アクティビティ ログ接続を追加すると、上部の折れ線グラフに Azure アクティビティ ログ イベントが表示されます。 棒グラフの各行は、さまざまな追跡可能な変更の種類を表します。 具体的な種類としては、Linux のデーモン、ファイル、ソフトウェアがあります。 [変更] タブには、変更が発生した時刻の降順 (最新のものから順に) で、変更の詳細が図示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM の Change Tracking と Update Management を構成して確認しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * リソース グループと VM を作成する
> * Linux の更新プログラムを管理する
> * 変更とインベントリを監視する

次のチュートリアルに進み、VM の監視について学習してください。

> [!div class="nextstepaction"]
> [仮想マシンの監視](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)