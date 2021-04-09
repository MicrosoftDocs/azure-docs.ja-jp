---
title: チュートリアル - Azure で Windows 仮想マシンを監視する
description: このチュートリアルでは、Windows 仮想マシンのパフォーマンスと、そこで検出された実行中のアプリケーション コンポーネントを監視する方法について説明します。
author: mgoedtel
manager: carmonm
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 8c4345d16efe296413ea9d995c2307bba62d33f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560703"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>チュートリアル:Azure で Windows 仮想マシンを監視する

Azure Monitoring は、エージェントを使用して Azure VM からブート データとパフォーマンス データを収集します。そのデータは、Azure Storage に格納され、ポータルや Azure PowerShell モジュール、Azure CLI からアクセスできるようになります。 Azure Monitor for VMs でパフォーマンス メトリックを収集し、VM にインストールされているアプリケーション コンポーネントを検出すると共に、パフォーマンス グラフと依存関係マップを追加することによって、先進の監視機能が実現されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * VM ホストのメトリックを表示する
> * Azure Monitor for VMs の有効化
> * VM のパフォーマンス メトリックを表示する
> * アラートを作成する

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

このチュートリアルで Azure の監視と更新管理を構成するには、Azure 内に Windows VM が必要です。 まず、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

次に、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。 次の例では、場所 *EastUS* に *myVM* という名前の VM を作成します。 これらが存在しない場合は、リソース グループ *myResourceGroupMonitorMonitor* と関連ネットワーク リソースが作成されます。

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

[Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata) コマンドを使用してブートの診断データを取得できます。 次の例では、ブート診断は *c:\* ドライブのルートにダウンロードされています。

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Windows VM と連動する専用のホスト VM があります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に表示されます。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。
2. ホスト VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、 **[利用可能なメトリック]** からいずれかのホスト メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>高度な監視を有効にする

Azure Monitor for VMs を使用した Azure VM の監視を有効にするには、次の手順に従います。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。

2. [VM] ページの **[監視]** セクションで、 **[Insights (プレビュー)]** を選択します。

3. **[Insights (プレビュー)]** ページで、 **[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  

    この一覧では、サブスクリプションで VM がデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成するには、[Log Analytics ワークスペースの作成](../../azure-monitor/logs/quick-create-workspace.md)に関するページを参照してください。 ワークスペースは、[サポートされているリージョン](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)のいずれかに属している必要があります。

監視を有効にした後、VM のパフォーマンス メトリックが表示されるまでに数分かかることがあります。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VM のパフォーマンス メトリックを表示する

VM 用 Azure Monitor には、仮想マシンがどの程度効果的に実行されているかを判定するのに役立ついくつかの主要業績評価指標 (KPI) を対象とする一連のパフォーマンス グラフが含まれています。 VM からアクセスするには、次の手順を行います。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。

2. [VM] ページの **[監視]** セクションで、 **[Insights (プレビュー)]** を選択します。

3. **[パフォーマンス]** タブを選択します。

このページには、パフォーマンス使用率グラフだけでなく、検出された各論理ディスクについて、その容量、使用率、およびメジャーごとの全体平均も表示されます。

## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。

2. VM ブレードの **[アラート ルール]** をクリックし、アラート ブレード上部にある **[メトリック アラートの追加]** をクリックします。

3. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。

4. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。

5. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。

6. **[OK]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM のパフォーマンスを構成して確認しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * リソース グループと VM を作成する
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * Azure Monitor for VMs の有効化
> * VM のメトリックを表示する
> * アラートを作成する

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](../../security/fundamentals/overview.md)
