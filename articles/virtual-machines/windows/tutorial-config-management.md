---
title: チュートリアル - Windows 仮想マシンの構成を Azure で管理する
description: このチュートリアルでは、Windows 仮想マシン上の変更を特定したりパッケージの更新プログラムを管理したりする方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: db7c0f2dbc547d78e3caa9ab37450c86278929bd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555943"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>チュートリアル:Azure で変更を監視し、Windows 仮想マシンを更新する

Azure [Change Tracking](../../automation/change-tracking/overview.md) と [Update Management](../../automation/update-management/overview.md) を使用すると、Azure の Windows 仮想マシンの変更を簡単に識別し、それらの VM のオペレーティング システムの更新を管理できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Windows 更新プログラムを管理する。
> * 変更とインベントリを監視する。

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell を開く

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、Azure アカウントで使用できるように構成されています。

Cloud Shell でコード ブロックを開くには、そのコード ブロックの右上隅にある **[使ってみる]** を選択します。

[https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコード ブロックをコピーし、[Cloud Shell] タブに貼り付けて、Enter キーを押してコードを実行します。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

このチュートリアルで Azure の監視と更新管理を構成するには、Azure 内に Windows VM が必要です。

まず、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

次に、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。 次の例では、`myVM` という名前の VM を `East US` の場所に作成します。 これらが存在しない場合は、リソース グループ `myResourceGroupMonitor` と関連ネットワーク リソースが作成されます。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

リソースと VM が作成されるまで数分かかります。

## <a name="manage-windows-updates"></a>Windows 更新プログラムを管理する

Update Management は、Azure Windows VM の更新プログラムとパッチの管理に役立ちます。 次のことを VM から直接すばやく実行できます。

- 利用可能な更新プログラムの状態を評価します。
- 必要な更新プログラムのインストールをスケジュールします。
- 展開の結果を確認して、VM に更新プログラムが正常に適用されたことを確認します。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページを参照してください。

### <a name="enable-update-management"></a>Update Management の有効化

お使いの VM で Update Management を有効にするには:

1. ウィンドウの左端の **[仮想マシン]** を選択します。
1. 一覧から VM を選択します。
1. VM ウィンドウの **[操作]** ペインで、 **[更新プログラムの管理]** を選択します。
1. **[更新管理の有効化]** ウィンドウが開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。 この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../../azure-monitor/logs/log-query-overview.md) ワークスペースを使用して、Update Management のような機能およびサービスによって生成されるデータを収集します。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

更新を必要とする VM で追加のアクションを実行する場合、Azure Automation を使用して、VM に対して Runbook を実行することができます。 このような操作には、更新プログラムのダウンロードや適用が含まれます。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation Hybrid Runbook Worker でプロビジョニングされているかどうかが確認されます。 このエージェントは、VM と通信し、更新ステータスに関する情報を取得するために使用されます。

**[更新管理の有効化]** ウィンドウで、Log Analytics ワークスペースと Automation アカウントを選択し、 **[有効にする]** を選択します。 ソリューションが有効になるまでに最大 15 分かかります。

オンボード中に次の前提条件のいずれかを満たしていない場合、これらが自動的に追加されます。

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) ワークスペース
* [Automation](../../automation/index.yml)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) が VM で有効になっている

ソリューションが有効になると、 **[更新の管理]** ウィンドウが開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[有効にする]** を選択します。 これらのオプションが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、そのソリューションのワークスペースと Automation アカウントを使用する必要があることを示します。

![Update Management ソリューションを有効にする](./media/tutorial-monitoring/manageupdates-update-enable.png)

Update Management ソリューションは、有効になるまでに最大15分かかる場合があります。 この処理中はブラウザーのウィンドウを閉じないでください。 ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Azure Monitor ログに送られます。 データが分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

### <a name="view-an-update-assessment"></a>更新の評価を表示する

Update Management が有効になると、 **[更新の管理]** ウィンドウが表示されます。 更新の評価が完了すると、 **[不足している更新プログラム]** タブに、不足している更新プログラムの一覧が表示されます。

 ![更新ステータスを確認する](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。 展開に含める更新の種類を選択します。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

VM の新しい更新プログラムの展開をスケジュールするには、 **[更新の管理]** ウィンドウの上部にある **[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** ウィンドウで、次の情報を指定します。

| オプション | 説明 |
| --- | --- |
| **名前** |更新プログラムの展開を識別する一意の名前を入力します。 |
|**オペレーティング システム**| **Linux** か **Windows** を選択します。|
| **更新するグループ** |Azure でホストされている VM の場合は、サブスクリプション、リソース グループ、場所、およびタグの組み合わせに基づいてクエリを定義します。 このクエリは、展開に含めるための、Azure でホストされている VM の動的なグループを構築します。 </br></br>Azure でホストされていない VM の場合は、既存の保存された検索条件を選択します。 この検索では、展開に含めるこれらの VM のグループを選択できます。 </br></br> 詳しくは、[動的グループ](../../automation/update-management/configure-groups.md)に関するページをご覧ください。|
| **更新するマシン** |**[保存した検索条件]** 、 **[Imported group]\(インポートしたグループ\)** 、または **[マシン]** を選択します。<br/><br/>**[マシン]** を選択した場合は、ドロップダウン リストから個々のマシンを選択できます。 各マシンの準備状況は、表の **[エージェントの更新の準備]** 列に示されます。</br></br> Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../../azure-monitor/logs/computer-groups.md)に関するページを参照してください |
|**更新プログラムの分類**|必要なすべての更新プログラムの分類を選択します。|
|**更新プログラムの包含/除外**|このオプションを選択すると、 **[包含/除外]** ペインが開きます。 含める更新プログラムおよび除外する更新プログラムは別のタブに表示されます。 包含を処理する方法について詳しくは、「[更新プログラムのデプロイをスケジュールする](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment)」を参照してください。 |
|**スケジュール設定**|開始する時刻を選択し、 **[1 回]** または **[定期的]** のいずれかを選択します。|
| **事前スクリプトと事後スクリプト**|展開の前後に実行するスクリプトを選択します。|
| **メンテナンス期間** | 更新プログラムに対して設定された分数を入力します。 有効な値の範囲は 30 から 360 分です。 |
| **Reboot control (再起動制御)**| 再起動の処理方法を選択します。 選択できる項目は次のとおりです。<ul><li>**必要に応じて再起動**</li><li>**常に再起動**</li><li>**再起動しない**</li><li>**再起動のみ**</li></ul>**[必要に応じて再起動]** が既定で選択されます。 **[再起動のみ]** を選択した場合、更新プログラムはインストールされません。|

スケジュールの構成が完了したら、 **[作成]** をクリックして、状態ダッシュボードに戻ります。 **[スケジュール済み]** の表に、作成した展開スケジュールが表示されます。

更新プログラムの展開はプログラムで作成することもできます。 REST API を使用して更新プログラムの展開を作成する方法については、「[ソフトウェア更新プログラムの構成 - 作成](/rest/api/automation/softwareupdateconfigurations/create)」を参照してください。 週単位の更新プログラムの展開を作成するために使用できるサンプル Runbook もあります。 この Runbook について詳しくは、「[Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)」(リソース グループ内の VM に対して週単位の更新プログラムのデプロイを作成する) をご覧ください。

### <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を確認する

スケジュールされた展開の開始後、 **[更新の管理]** ウィンドウの **[更新プログラムの展開]** タブに、展開の状態が表示されます。

展開を現在実行中の場合、状態は [処理中] と表示されます。 正常に完了すると、状態は [成功] に変わります。 ただし、展開において、いずれかの更新プログラムが失敗した場合、状態は [部分的に失敗] になります。

完了した更新プログラムの展開を選択すると、その展開用のダッシュボードが表示されます。

![特定のデプロイに関する更新プログラムのデプロイ ステータスのダッシュボード](./media/tutorial-monitoring/manageupdates-view-results.png)

**[更新プログラムを実行した結果]** のタイルに表示されるのは、VM 上の更新プログラムの合計数と展開結果の概要です。 右側の表には、各更新プログラムとインストールの結果の詳細が示されます。 それぞれの結果には次のいずれかの値が示されます。

* **試行されていません**:更新プログラムがインストールされていません。 定義されたメンテナンス期間に基づいて、十分な時間を確保できませんでした。
* **[成功]** : 更新できました。
* **[失敗]** : 更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、 **[すべてのログ]** を選択します。

ターゲット VM での更新プログラムの展開を管理する Runbook のジョブ ストリームを確認するには、 **[出力]** タイルを選択します。

展開で発生したエラーの詳細情報を確認するには、 **[エラー]** を選択します。

## <a name="monitor-changes-and-inventory"></a>変更とインベントリを監視する

コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

### <a name="enable-change-and-inventory-management"></a>変更とインベントリの管理を有効にする

使用している VM の変更とインベントリの管理を有効にするには、次の手順に従います。

1. ウィンドウの左端の **[仮想マシン]** を選択します。
1. 一覧から VM を選択します。
1. VM ウィンドウの **[操作]** で、 **[インベントリ]** または **[変更の追跡]** を選択します。
1. **[変更履歴とインベントリを有効化]** ペインが開きます。

使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[有効にする]** を選択します。 オプションが淡色表示になっている場合は、VM の Automation ソリューションが既に有効になっています。 この場合、既に有効化されているワークスペースおよび Automation アカウントを使用する必要があります。

メニューではソリューションは別々に表示されますが、同じソリューションです。 一方を有効にすると、使用している VM に対して両方が有効になります。

![変更とインベントリの追跡を有効にする](./media/tutorial-monitoring/manage-inventory-enable.png)

ソリューションを有効にした後、データが表示される前に、VM でインベントリが収集されるのに時間がかかる場合があります。

### <a name="track-changes"></a>変更を追跡する

VM の **[操作]** で、 **[変更の追跡]** を選択し、 **[設定の編集]** を選択します。 **[変更の追跡]** ペインが表示されます。 追跡する設定の種類を選択し、 **[+ 追加]** を選択して、設定を構成します。

Windows 用の使用できる設定オプションは次のとおりです。

* Windows レジストリ
* Windows ファイル

変更の追跡の詳細については、[VM に対する変更のトラブルシューティング](../../automation/automation-tutorial-troubleshoot-changes.md)に関するページをご覧ください。

### <a name="view-inventory"></a>インベントリを表示する

使用している VM で、 **[操作]** の **[インベントリ]** を選択します。 **[ソフトウェア]** タブには、検出されたソフトウェアの一覧を示す表があります。 各ソフトウェア レコードの詳細情報が表に表示されます。 これらの詳細には、ソフトウェアの名前、バージョン、発行元、最終更新時刻などが含まれます。

![インベントリを表示する](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>アクティビティ ログおよび変更を監視する

VM の **[変更の追跡]** ウィンドウから **[アクティビティ ログ接続の管理]** を選択して **[Azure アクティビティ ログ]** ペインを開きます。 **[接続]** を選択して、変更履歴を VM の Azure アクティビティ ログに接続します。

変更履歴を有効にして、VM の **[概要]** ペインに移動し、 **[停止]** を選択して VM を停止します。 メッセージが表示されたら、 **[はい]** を選択して VM を停止します。 VM の割り当てが解除されたら、 **[スタート]** を選択して VM を再起動します。

VM を停止して再起動すると、イベントがアクティビティ ログに記録されます。 **[変更の追跡]** ペインに戻り、ペインの下部にある **[イベント]** タブを選択します。 しばらくすると、グラフと表にイベントが表示されます。 各イベントを選択すると、そのイベントの詳細情報を表示できます。

![アクティビティ ログで変更を表示する](./media/tutorial-monitoring/manage-activitylog-view-results.png)

前のグラフには、時間の経過とともに発生した変更が表示されています。 Azure アクティビティ ログ接続を追加すると、上部の折れ線グラフに Azure アクティビティ ログ イベントが表示されます。

棒グラフの各行は、さまざまな追跡可能な変更の種類を表します。 具体的には、Linux デーモン、ファイル、Windows レジストリ キー、ソフトウェア、および Windows サービスです。 **[変更]** タブに変更の詳細が表示されます。 変更は発生した順に表示され、最新の変更が最初に表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM の Change Tracking と Update Management を構成して確認しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * リソース グループと VM を作成する。
> * Windows 更新プログラムを管理する。
> * 変更とインベントリを監視する。

次のチュートリアルに進み、VM の監視について学習してください。

> [!div class="nextstepaction"]
> [仮想マシンの監視](tutorial-monitor.md)
