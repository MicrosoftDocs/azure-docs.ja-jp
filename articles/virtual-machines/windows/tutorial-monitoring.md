---
title: チュートリアル - Azure で Windows 仮想マシンの監視と更新を行う | Microsoft Docs
description: このチュートリアルでは、Windows 仮想マシンを対象に、ブート診断とパフォーマンス メトリックを監視する方法と、パッケージの更新を管理する方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 16edb1ac764df6533b426652f9caf18a2f549f8c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101670"
---
# <a name="tutorial-monitor-and-update-a-windows-virtual-machine-in-azure"></a>チュートリアル:Azure で Windows 仮想マシンの監視と更新を行う

Azure Monitoring では、エージェントを使用して Azure VM からブートとパフォーマンス データを収集し、Azure Storage にこのデータを格納し、ポータル、Azure PowerShell モジュールと Azure CLI でアクセスできるようにします。 更新管理では、Azure Windows VM の更新プログラムとパッチを管理できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * VM ホストのメトリックを表示する
> * 診断拡張機能をインストールする
> * VM のメトリックを表示する
> * アラートを作成する
> * Windows 更新プログラムを管理する
> * 変更とインベントリを監視する
> * 高度な監視をセットアップする

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

このチュートリアルで Azure の監視と更新管理を構成するには、Azure 内に Windows VM が必要です。 まず、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

次に、[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) を使用して VM を作成します。 次の例では、場所 *EastUS* に *myVM* という名前の VM を作成します。 これらが存在しない場合は、リソース グループ *myResourceGroupMonitorMonitor* と関連ネットワーク リソースが作成されます。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

リソースと VM が作成されるまで数分かかります。

## <a name="view-boot-diagnostics"></a>ブート診断を表示する

Windows 仮想マシンが起動すると、ブート診断エージェントは画面出力をキャプチャします。これをトラブルシューティングに使用することができます。 この機能は既定で有効になっています。 キャプチャしたスクリーンショットは、既定で作成される Azure Storage アカウントに格納されます。

[Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata) コマンドを使用してブートの診断データを取得できます。 次の例では、ブート診断は *c:\* ドライブのルートにダウンロードされています。

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Windows VM と連動する専用のホスト VM があります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に表示されます。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。
2. ホスト VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、 **[利用可能なメトリック]** からいずれかのホスト メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>診断拡張機能をインストールする

基本的なホスト メトリックは利用できますが、さらに粒度の細かい VM 固有のメトリックを表示するためには、Azure Diagnostics 拡張機能を VM にインストールする必要があります。 Azure Diagnostics 拡張機能を通じて、より詳しい監視データと診断データを VM から取得することができます。 これらのパフォーマンス メトリックを確認したり、VM のパフォーマンスに基づくアラートを作成したりすることができます。 診断拡張機能は、次のように Azure Portal からインストールします。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。
2. **[診断の設定]** をクリックします。 *[ブート診断]* は、前のセクションで既に有効にしたので、そのように表示されています。 *[基本メトリック]* のチェック ボックスをオンにします。
3. **[ゲスト レベルの監視を有効にする]** ボタンをクリックします。

    ![診断メトリックを表示する](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>VM のメトリックを表示する

VM のメトリックは、ホスト VM のメトリックと同じ方法で表示できます。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、 **[利用可能なメトリック]** からいずれかの診断メトリックを選択します。

    ![VM のメトリックを表示する](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM ブレードの **[アラート ルール]** をクリックし、アラート ブレード上部にある **[メトリック アラートの追加]** をクリックします。
3. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。
4. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。
5. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。
6. **[OK]** ボタンをクリックします。

## <a name="manage-windows-updates"></a>Windows 更新プログラムを管理する

更新管理では、Azure Windows VM の更新プログラムとパッチを管理できます。
VM から直接、利用可能な更新プログラムのステータスを迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

### <a name="enable-update-management"></a>Update Management の有効化

お使いの VM で Update Management を有効にする

1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. [VM] 画面の **[操作]** セクションで、 **[更新の管理]** をクリックします。 **[更新管理の有効化]** 画面が開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。
この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../../log-analytics/log-analytics-overview.md) ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。
ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。
更新を必要とする VM で追加のアクションを実行する場合、Azure Automation を使用すると、VM に対して Runbook を実行して、更新プログラムをダウンロードして適用するなどの操作を行うことができます。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation ハイブリッド Runbook worker でプロビジョニングされているかどうかが確認されます。
このエージェントは VM との通信に使用され、更新ステータスに関する情報を取得します。

Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

オンボード中に次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../../log-analytics/log-analytics-overview.md) ワークスペース
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) が VM で有効になっている

**[更新の管理]** 画面が開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[有効にする]** をクリックします。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

![Update Management ソリューションを有効にする](./media/tutorial-monitoring/manageupdates-update-enable.png)

ソリューションを有効にするには最大 15 分かかります。 この処理中はブラウザーのウィンドウは閉じないでください。 ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Azure Monitor ログに送られます。 データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

### <a name="view-update-assessment"></a>更新の評価を確認する

**更新管理**が有効になると、 **[更新管理]** 画面が表示されます。 更新の評価が完了すると、 **[不足している更新プログラム]** タブに、不足している更新プログラムの一覧が表示されます。

 ![更新ステータスを確認する](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

**[更新の管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** をクリックして、VM の新しい更新プログラムのデプロイをスケジュールします。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

新しい更新プログラムのデプロイを作成するには、 **[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** ページが開きます。 次の表で説明されているプロパティの値を入力し、 **[作成]** をクリックします。

| プロパティ | Description |
| --- | --- |
| Name |更新プログラムの展開を識別する一意の名前。 |
|オペレーティング システム| Linux または Windows|
| 更新するグループ |Azure マシンの場合、サブスクリプション、リソース グループ、場所、およびタグの組み合わせに基づいてクエリを定義し、デプロイに含める Azure VM の動的グループを構築します。 </br></br>Azure 以外のマシンの場合、既存の保存された検索を選択して、デプロイに含める Azure 以外のマシンのグループを選択します。 </br></br>詳しくは、[動的グループ](../../automation/automation-update-management.md#using-dynamic-groups)に関するページをご覧ください。|
| 更新するマシン |保存した検索条件、インポートしたグループを選択するか、ドロップダウンから [マシン] を選択し、個別のマシンを選択します。 **[マシン]** を選択すると、マシンの準備状況が **[エージェントの更新の準備]** 列に示されます。</br> Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../../azure-monitor/platform/computer-groups.md)に関するページを参照してください |
|更新プログラムの分類|必要な更新プログラムの分類すべてを選択します|
|更新プログラムの包含/除外|**[包含/除外]** ページが開きます。 含めるまたは除外する更新プログラムは別のタブに表示されます。 包含を処理する方法について詳しくは、[包含の動作](../../automation/automation-update-management.md#inclusion-behavior)に関するページをご覧ください。 |
|スケジュール設定|開始する時刻を選択し、繰り返しの設定として、[1 回] または [定期的] のいずれかを選択します|
| 事前スクリプトと事後スクリプト|デプロイの前後に実行するスクリプトを選択します|
| メンテナンス期間 |更新プログラムに対して設定された分数です。 30 分未満の値を指定することはできません。また、6 時間を超えることはできません |
| 再起動制御| 再起動の処理方法を決定します。 使用できるオプションは次のとおりです。</br>必要に応じて再起動 (既定値)</br>常に再起動</br>再起動しない</br>Only reboot - will not install updates (再起動のみ - 更新プログラムをインストールしない)|

更新プログラムのデプロイはプログラムで作成することもできます。 REST API を使用して更新プログラムのデプロイを作成する方法については、「[Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create)」(ソフトウェア更新プログラムの構成 - 作成) をご覧ください。 週単位の更新プログラムのデプロイを作成するために使用できるサンプル Runbook もあります。 この Runbook について詳しくは、「[Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)」(リソース グループ内の VM に対して週単位の更新プログラムのデプロイを作成する) をご覧ください。

スケジュールの構成が完了したら、 **[作成]** ボタンをクリックして、状態ダッシュボードに戻ります。
**スケジュール済み**の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

### <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされた展開の開始後、 **[更新管理]** 画面の **[更新プログラムの展開]** タブに、展開の状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。 正常に完了すると、状態は **[成功]** に変わります。
デプロイ時に 1 つ以上の更新プログラムでエラーが発生した場合、ステータスは **[部分的に失敗]** になります。
完了した更新プログラムのデプロイをクリックし、その更新プログラムのデプロイのダッシュボードを確認します。

![特定のデプロイに関する更新プログラムのデプロイ ステータスのダッシュボード](./media/tutorial-monitoring/manageupdates-view-results.png)

**[新プログラムを実行した結果]** のタイルに表示されるのは、VM 上の更新プログラムの合計数とデプロイ結果の概要です。
右側の表に、各更新プログラムとインストールの結果の詳細が示されます。これは、次の値のいずれかになります。

* **試行されていません**: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **成功**: 更新できました。
* **失敗**: 更新できませんでした。

**[すべてのログ]** をクリックし、デプロイで作成されたすべてのログ エントリを確認します。

**[出力]** タイルをクリックし、ターゲット VM での更新プログラムのデプロイを管理する runbook のジョブ ストリームを確認します。

デプロイで発生したエラーの詳細情報を確認するには、 **[エラー]** をクリックします。

## <a name="monitor-changes-and-inventory"></a>変更とインベントリを監視する

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

### <a name="enable-change-and-inventory-management"></a>変更とインベントリの管理を有効にする

使用している VM の変更とインベントリの管理を有効にします。

1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. VM 画面で、 **[操作]** セクションの **[インベントリ]** または **[変更の追跡]** をクリックします。 **[Enable Change Tracking and Inventory]\(変更の追跡とインベントリの有効化\)** 画面が開きます。

使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[有効にする]** をクリックします。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。 メニューではソリューションは別々に表示されますが、同じソリューションです。 一方を有効にすると、使用している VM に対して両方が有効になります。

![変更とインベントリの追跡を有効にする](./media/tutorial-monitoring/manage-inventory-enable.png)

ソリューションを有効にした後、VM でインベントリが収集され、データが表示されるまでに時間がかかる場合があります。

### <a name="track-changes"></a>変更を追跡する

使用している VM で、 **[操作]** の **[変更の追跡]** を選択します。 **[設定の編集]** をクリックすると、 **[変更の追跡]** ページが表示されます。 追跡する設定の種類を選択し、 **[+ 追加]** をクリックして、設定を構成します。 Windows 用の使用できるオプションは次のとおりです。

* Windows レジストリ
* Windows ファイル

変更の追跡の詳細については、[VM での変更のトラブルシューティング](../../automation/automation-tutorial-troubleshoot-changes.md)に関するページをご覧ください

### <a name="view-inventory"></a>インベントリを表示する

使用している VM で、 **[操作]** の **[インベントリ]** を選択します。 **[ソフトウェア]** タブには、検出されたソフトウェアの一覧が表示されます。 各ソフトウェア レコードの詳細情報を表で確認できます。 これらの詳細には、ソフトウェアの名前、バージョン、発行元、最終更新時刻などが含まれます。

![インベントリを表示する](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>アクティビティ ログおよび変更を監視する

VM の **[変更履歴]** ページから **[アクティビティ ログ接続の管理]** を選択します。 このタスクで、 **[Azure アクティビティ ログ]** ページが開きます。 **[接続]** を選択して、変更履歴を VM の Azure アクティビティ ログに接続します。

この設定を有効にして、VM の **[概要]** ページに移動し、 **[停止]** を選択して VM を停止します。 メッセージが表示されたら、 **[はい]** を選択して VM を停止します。 割り当てが解除されたら、 **[スタート]** を選択して VM を再起動します。

VM の起動時と停止時には、イベントがアクティビティ ログに記録されます。 **[変更履歴]** ページに戻ります。 ページの下部にある **[イベント]** タブを選択します。 しばらくすると、グラフと表にイベントが表示されます。 各イベントを選択して、そのイベントの詳細情報を表示できます。

![アクティビティ ログで変更を表示する](./media/tutorial-monitoring/manage-activitylog-view-results.png)

グラフには、時間の経過とともに発生した変更が表示されます。 アクティビティ ログ接続を追加すると、上部の折れ線グラフに Azure アクティビティ ログ イベントが表示されます。 棒グラフの各行は、さまざまな追跡可能な変更の種類を表します。 具体的には、Linux デーモン、ファイル、Windows レジストリ キー、ソフトウェア、および Windows サービスです。 [変更] タブには、変更が発生した時刻の降順 (最新のものから順に) で、変更の詳細が図示されます。

## <a name="advanced-monitoring"></a>高度な監視

使用している VM をさらに詳しく監視するには、[Azure Automation](../../automation/automation-intro.md) で提供される変更やインベントリ、Update Management などのソリューションを使用します。

Log Analytics ワークスペースにアクセスして、ワークスペース キーとワークスペース識別子を確認するには、 **[設定]** の **[詳細設定]** を選択します。 [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) コマンドを使用して、Microsoft Monitoring Agent 拡張機能を VM に追加します。 以下のサンプルの変数値を更新して、お使いの Log Analytics ワークスペース キーとワークスペース ID を反映させます。

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzVMExtension -ResourceGroupName "myResourceGroupMonitor" `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName "myVM" `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location "East US"
```

しばらくすると、Log Analytics ワークスペースに新しい VM が表示されます。

![Log Analytics ワークスペース ブレード](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Security Center で VM を構成して確認しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * リソース グループと VM を作成する
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * 診断拡張機能をインストールする
> * VM のメトリックを表示する
> * アラートを作成する
> * Windows 更新プログラムを管理する
> * 変更とインベントリを監視する
> * 高度な監視をセットアップする

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](../../security/fundamentals/overview.md)
