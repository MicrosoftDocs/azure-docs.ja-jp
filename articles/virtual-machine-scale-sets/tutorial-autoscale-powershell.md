---
title: チュートリアル - Azure PowerShell を使用してスケール セットを自動スケーリングする
description: Azure PowerShell を使用して CPU 需要の増減に合わせて仮想マシンのスケール セットを自動的にスケーリングする方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 8ee124f866a5241620671ff84c24f3713f62efe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078471"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して仮想マシン スケール セットを自動的にスケールする

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

スケール セットを作成するときに、実行する VM インスタンスの数を定義します。 アプリケーションの需要の変化に応じて、VM インスタンスの数を自動的に増減することができます。 自動スケールにより、顧客のニーズに対応したり、アプリのライフサイクル全体でアプリケーション パフォーマンスの変化に対応したりできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * スケール セットの自動スケールを使用する
> * 自動スケール ルールを作成および使用する
> * VM インスタンスのストレステストを行い、自動スケール ルールをトリガーする
> * 需要の減少に合わせて元のサイズに自動的にスケーリングする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

Azure PowerShell モジュール バージョン 6.8.1 以降 (現在のバージョンの Azure Cloud Shell を含む) に関係する問題が確認されています。 このチュートリアルには必ず、Azure PowerShell モジュール バージョン 6.0.0 から 6.8.0 を使用してください。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。


## <a name="create-a-scale-set"></a>スケール セットを作成する
自動スケール ルールを作成しやすくするため、使用するスケール セット用にいくつかの変数を定義します。 次の例は、*myResourceGroup* という名前のリソース グループ内と *East US* 領域内の *myScaleSet* という名前のスケール セットの変数を定義します。 サブスクリプション ID は、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を使用して取得します。 複数のサブスクリプションがアカウントに関連付けられている場合は、最初のサブスクリプションだけが返されます。 名前とサブスクリプション ID を次のように調整します。

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

それでは、仮想マシン スケール セットの作成に移りましょう。これには、[New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) を使います。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。

## <a name="create-a-rule-to-autoscale-out"></a>自動スケールアウト ルールの作成
アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 監視するメトリック (CPU やディスクなど)、指定されたしきい値をアプリケーションの負荷が満たす必要がある期間、およびスケール セットに追加する VM インスタンスの数を制御します。

CPU に対する負荷の平均が 5 分間に 70% を上回った場合に、スケール セット内の VM インスタンスの数を増やすルールを [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) を使用して作成してみましょう。 ルールがトリガーされると、VM インスタンスの数が 3 つ増えます。

このルールでは、次のパラメーターが使用されます。

| パラメーター               | 説明                                                                                                         | 値          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | スケール セット アクションを監視して適用するパフォーマンス メトリック。                                                   | Percentage CPU |
| *-TimeGrain*            | 分析のためにメトリックを収集する頻度。                                                                   | 1 分       |
| *-MetricStatistic*      | 分析のために収集したメトリックの集計方法を定義します。                                                | Average        |
| *-TimeWindow*           | メトリックとしきい値を比較する前に監視する時間。                                   | 5 分      |
| *-Operator*             | しきい値に対してメトリック データを比較するために使用する演算子。                                                     | より大きい   |
| *-Threshold*            | 自動スケール ルールがアクションをトリガーする値。                                                      | 70%            |
| *-ScaleActionDirection* | ルールが適用されるときに、スケール セットをスケールアップするかスケールダウンするかを定義します。                                             | Increase (増加)       |
| *-ScaleActionScaleType* | VM インスタンスの数を特定の値で変更することを示します。                                    | 変更数   |
| *-ScaleActionValue*     | ルールがトリガーされたときに VM インスタンスのパーセンテージを変更する必要があります。                                            | 3              |
| *-ScaleActionCooldown*  | 自動スケール アクションを有効にする時間を稼ぐため、ルールを再度適用する前に待機する時間。 | 5 分      |

次の例では、このスケール アップ ルールを保持する *myRuleScaleOut* という名前のオブジェクトを作成します。 *-MetricResourceId* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>自動スケールイン ルールの作成
夜間や週末は、アプリケーションの需要が低下する可能性があります。 この低下した負荷が一定期間持続する場合、スケール セット内の VM インスタンスの数を減らす自動スケール ルールを構成できます。 このスケールイン アクションによって、現在の需要を満たすのに必要な数のインスタンスのみが実行されるようになるため、スケール セットの実行コストが削減されます。

CPU に対する負荷の平均が 5 分間に 30% を下回った場合にスケール セット内の VM インスタンスの数を減らす別のルールを [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) を使用して作成します。 ルールがトリガーされると、VM インスタンスの数が 1 つ減ります。 次の例では、このスケールダウン ルールを保持する *myRuleScaleDown* という名前のオブジェクトを作成します。 *-MetricResourceId* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>自動スケール プロファイルの定義
自動スケール ルールをスケール セットに関連付けるには、プロファイルを作成します。 自動スケール プロファイルは、スケール セット容量の既定、最小、および最大を定義し、自動スケール ルールを関連付けます。 [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) を使用して自動スケール プロファイルを作成します。 次の例では、既定および最小の容量として *2* つの VM インスタンスを設定し、最大の容量として *10* を定義しています。 上記の手順で作成されたスケールアウトおよびスケールイン ルールは次のように接続されます。

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>スケール セットへの自動スケーリング プロファイルの適用
最後の手順では、自動スケールのプロファイルをスケール セットに適用します。 これでアプリケーションの需要に基づいて、スケール セットを自動的にスケールインまたはスケールアウトすることができます。 次のように [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) を使用して自動スケール プロファイルを適用します。

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>スケール セットに対する CPU 負荷の生成
自動スケール ルールをテストするには、スケール セットの VM インスタンスに対する CPU 負荷を生成します。 このシミュレートされた CPU 負荷を適用すると、自動スケール ルールによってスケールアウトされ、VM インスタンスの数が増えます。 次に、シミュレートされた CPU 負荷を小さくすると、自動スケール ルールによってスケールインされ、VM インスタンスの数が減ります。

スケール セット内の VM インスタンスに接続するための NAT ポートを一覧表示するには、まず、[Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer) を使用して、ロード バランサー オブジェクトを取得します。 次に、[Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) を使用して、受信 NAT 規則を表示します。

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

次の出力例には、インスタンス名、ロード バランサーのパブリック IP アドレス、および NAT 規則によってトラフィックが転送される先のポート番号が示されています。

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

ルールの "*名前*" は、前の [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) コマンドで示された VM インスタンスの名前と一致します。 たとえば、VM インスタンス *0* に接続するには、*myRDPRule.0* を使用し、ポート *50001* に接続します。 VM インスタンス *1* に接続するには、*myRDPRule.1* の値を使用し、ポート *50002* に接続します。

[Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) を使用して、ロード バランサーのパブリック IP アドレスを表示します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

出力例:

```powershell
IpAddress
---------
52.168.121.216
```

最初の VM インスタンスへのリモート接続を作成します。 必要な VM インスタンスの独自のパブリック IP アドレスとポート番号を、前のコマンドで示されたとおりに指定します。 メッセージが表示されたら、スケール セットを作成するときに使用した資格情報 (サンプル コマンドでは、既定の *azureuser* と *P\@ssw0rd!* ) を入力します。 Azure Cloud Shell を使用する場合は、ローカルの PowerShell プロンプトまたはリモート デスクトップ クライアントからこの手順を実行します。 次の例では、VM インスタンス *0* に接続します。

```powershell
mstsc /v 52.168.121.216:50001
```

ログインしたら、タスク バーから Internet Explorer を開きます。

- **[OK]** を選択して、 *[お勧めのセキュリティ、プライバシー、互換性の設定を使う]* のプロンプトを受け入れます
- アドレス バーに「 *http://download.sysinternals.com/files/CPUSTRES.zip* 」と入力します。
- Internet Explorer の強化されたセキュリティ構成が有効になっているので、 **[追加]** を選択し、 *http://download.sysinternals.com* ドメインを信頼できるサイトの一覧に追加します。
- ファイルのダウンロードのプロンプトが表示されたら、 **[開く]** を選択し、*CPUSTRES.EXE* ツールを選択して **実行** します。

ある程度の CPU 負荷を生成するために、2 つのスレッドの **[Active]\(アクティブ\)** チェック ボックスをオンにします。 両方のスレッドの **[Activity]\(アクティビティ\)** ドロップダウン メニューから *[Maximum]\(最大\)* を選択します。 タスク マネージャーを開き、VM の CPU 負荷が 100% になっていることを確認できます。

![CPU Stress ユーティリティによって VM インスタンスへの負荷が生成される](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

リモート デスクトップ接続セッションを開いたまま、別のリモート デスクトップ接続セッションを開きます。 前の [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) コマンドレットによって一覧表示されたポート番号を使用して、2 番目の VM インスタンスに接続します。

```powershell
mstsc /v 52.168.121.216:50002
```

2 番目の VM インスタンスにログインしたら、前の手順を繰り返して *CPUSTRES.EXE* をダウンロードして実行します。 再び、2 つの **アクティブ** スレッドを起動し、アクティビティを *[Maximum]\(最大\)* に設定します。

**CPU Stress** ツールが実行し続けるように、両方のリモート デスクトップ接続セッションを開いたままにします。


## <a name="monitor-the-active-autoscale-rules"></a>アクティブな自動スケール ルールの監視
スケール セット内の VM インスタンスの数を監視するには、**while** を使用します。 各 VM インスタンスで **CPUStress** によって生成された CPU 負荷に対するスケールアウト プロセスが自動スケーリングによって開始されるまでに 5 分かかります。

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

CPU しきい値に達すると、自動スケール ルールによってスケール セット内の VM インスタンスの数が増えます。 次の出力には、スケール セットの自動スケールアウトによって作成された 3 つの VM が示されています。

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

各 VM インスタンスへのリモート デスクトップ接続セッションで、**CPU Stress** ツールを閉じます。 スケール セット全体の平均 CPU 負荷が正常に戻ります。 その 5 分後、自動スケール ルールによって VM インスタンスの数がスケールインされます。 スケールイン アクションでは、ID が最も大きな VM インスタンスが最初に削除されます。 スケール セットが可用性セットまたは可用性ゾーンを使用する場合、スケールイン アクションはこれらの VM インスタンスに均等に分散されます。 次の出力例には、スケール セットの自動スケールインによって削除された 1 つの VM インスタンスが示されています。

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

`Ctrl-c` キーを押して *while* を終了します。 スケール セットは、引き続き 5 分ごとにスケールインされ、その都度 VM インスタンスが 1 つ削除されます。この操作は、VM インスタンスの数が最小数の 2 になるまで繰り返されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットと追加のリソースを削除するには、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure PowerShell を使用してスケール セットを自動的にスケールインまたはスケールアウトする方法について学習しました。

> [!div class="checklist"]
> * スケール セットの自動スケールを使用する
> * 自動スケール ルールを作成および使用する
> * VM インスタンスのストレステストを行い、自動スケール ルールをトリガーする
> * 需要の減少に合わせて元のサイズに自動的にスケーリングする
