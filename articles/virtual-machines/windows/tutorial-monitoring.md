---
title: "Azure で Windows Virtual Machines を監視し、更新する | Microsoft Docs"
description: "チュートリアル - Azure PowerShell を使用した Windows Virtual Machine の監視と更新"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Azure PowerShell を使用した Windows Virtual Machine の監視と更新

Azure Monitoring では、エージェントを使用して Azure VM からブートとパフォーマンス データを収集し、Azure Storage にこのデータを格納し、ポータル、Azure PowerShell モジュールと Azure CLI でアクセスできるようにします。 更新管理では、Azure Windows VM の更新プログラムとパッチを管理できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * VM ホストのメトリックを表示する
> * 診断拡張機能をインストールする
> * VM のメトリックを表示する
> * アラートを作成する
> * Windows 更新プログラムを管理する
> * 高度な監視をセットアップする

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、この[サンプル スクリプト](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)で仮想マシンを作成できます。 このチュートリアルを実行するときは、リソース グループ、VM の名前、場所を適宜置き換えてください。

## <a name="view-boot-diagnostics"></a>ブート診断を表示する

Windows 仮想マシンが起動すると、ブート診断エージェントは画面出力をキャプチャします。これをトラブルシューティングに使用することができます。 この機能は既定で有効になっています。 キャプチャしたスクリーン ショットは、既定で作成される Azure Storage アカウントに格納されます。 

[Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) コマンドを使用してブートの診断データを取得できます。 次の例では、ブート診断は *c:\* ドライブのルートにダウンロードされています。 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Windows VM と連動する専用のホスト VM があります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に表示されます。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. ホスト VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、**[利用可能なメトリック]** からいずれかのホスト メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>診断拡張機能をインストールする

基本的なホスト メトリックは利用できますが、さらに粒度の細かい VM 固有のメトリックを表示するためには、Azure 診断拡張機能を VM にインストールする必要があります。 Azure 診断拡張機能を通じて、より詳しい監視データと診断データを VM から取得することができます。 これらのパフォーマンス メトリックを確認したり、VM のパフォーマンスに基づくアラートを作成したりすることができます。 診断拡張機能は、次のように Azure Portal からインストールします。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. **[診断の設定]** をクリックします。 *[ブート診断]* は、前のセクションで既に有効にしたので、そのように表示されています。 *[基本メトリック]* のチェック ボックスをオンにします。
3. **[ゲスト レベルの監視を有効にする]** ボタンをクリックします。

    ![診断メトリックを表示する](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>VM のメトリックを表示する

VM のメトリックは、ホスト VM のメトリックと同じ方法で表示できます。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、**[利用可能なメトリック]** からいずれかの診断メトリックを選択します。

    ![VM のメトリックを表示する](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM ブレードの **[アラート ルール]** をクリックし、アラート ブレード上部にある **[メトリック アラートの追加]** をクリックします。
4. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。
5. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。
6. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。
7. **[OK]** ボタンをクリックします。

## <a name="manage-windows-updates"></a>Windows 更新プログラムを管理する

更新管理では、Azure Windows VM の更新プログラムとパッチを管理できます。
VM から直接、利用可能な更新プログラムのステータスを迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

### <a name="enable-update-management"></a>Update Management の有効化

お使いの VM で Update Management を有効にする
 
1. 画面左側の **[仮想マシン]** を選択します。
2. 一覧から VM を選択します。
3. [VM] 画面の **[操作]** セクションで、**[更新の管理]** をクリックします。 **[更新管理の有効化]** 画面が開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。 この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

Log Analytics ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。 更新を必要とする VM で追加のアクションを実行する場合、Azure Automation を使用すると、VM に対してスクリプトを実行して、更新プログラムをダウンロードして適用するなどの操作を行うことができます。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) とハイブリッド worker でプロビジョニングされているかどうかが確認されます。 このエージェントは VM との通信に使用され、更新ステータスに関する情報を取得します。 

これらの前提条件が満たされていない場合、バナーが表示され、ソリューションを有効にするオプションを選択できます。

![Update Management のオンボード構成バナー](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

バナーをクリックすると、ソリューションが有効になります。 検証後、次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../../log-analytics/log-analytics-overview.md) ワークスペース
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) が VM で有効になっている

**[更新管理の有効化]** 画面が開きます。 設定を構成し、**[有効]** をクリックします。

![Update Management ソリューションを有効にする](./media/tutorial-monitoring/manageupdates-update-enable.png)

ソリューションを有効にするは最大 15 分かかります。このとき、ブラウザー ウィンドウを閉じないでください。 ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Log Analytics に送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

### <a name="view-update-assessment"></a>更新の評価を確認する

**更新管理**が有効になると、**[更新管理]** 画面が表示されます。 **[不足している更新プログラム]** タブに、不足している更新プログラムのリストが表示されます。

 ![更新ステータスを確認する](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。
デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

**[更新の管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** をクリックして、VM の新しい更新プログラムのデプロイをスケジュールします。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

* **名前**: 更新プログラムのデプロイを識別する一意の名前を指定します。
* **更新プログラムの分類** - 更新プログラムのデプロイによってデプロイに追加されたソフトウェアの種類を選択します。 分類の種類は次のとおりです。
  * 緊急更新プログラム
  * セキュリティ更新プログラム
  * 更新プログラムのロールアップ
  * Feature Pack
  * Service Pack
  * 定義の更新
  * ツール
  * 更新プログラム

* **スケジュール設定**: 既定の日時 (現在の時刻から 30 分後) を使用するか、別の日時を指定します。
  デプロイを 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。 定期的なスケジュールを設定するには、[繰り返し] の下の [繰り返し] オプションをクリックします。

  ![更新プログラムのスケジュール設定画面](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **メンテナンス期間 (分)**: 更新プログラムをデプロイする期間を指定します。  これにより、定義したサービス期間内に変更が確実に実行されます。

スケジュールの構成が完了したら、**[作成]** ボタンをクリックして、ステータス ダッシュボードに戻ります。
**スケジュール済み**の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

> [!WARNING]
> 更新に再起動が必要な場合、VM は自動的に再起動されます。

### <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を確認する

スケジュールされたデプロイの開始後、**[更新管理]** 画面の **[更新プログラムの展開]** タブに、デプロイの状態が表示されます。
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

デプロイで発生したエラーの詳細情報を確認するには、**[エラー]** をクリックします。

## <a name="advanced-monitoring"></a>高度な監視 

[Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) を使用すると、VM に対してさらに高度な監視を行うことができます。 まだサインアップしていない場合は、Operations Management Suite の[無料試用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)にサインアップしてください。

OMS ポータルにアクセスすると、[設定] ブレードにワークスペース キーとワークスペース識別子が表示されます。 [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) コマンドを使用し、OMS 拡張機能を VM に追加します。 以下のサンプルの変数値を更新して、お使いの OMS ワークスペース キーとワークスペース ID を反映させます。  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

しばらくすると、OMS ワークスペースに新しい VM が表示されます。 

![OMS ブレード](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>次のステップ
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
> * 高度な監視をセットアップする

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](./tutorial-azure-security.md)