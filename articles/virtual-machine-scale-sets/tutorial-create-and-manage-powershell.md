---
title: チュートリアル:Azure VM スケール セットの作成と管理 - Azure PowerShell
description: Azure PowerShell を使用して仮想マシン スケール セットを作成するための方法と一般的な管理タスク (インスタンスの起動と停止、スケール セット容量の変更の方法など) について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/18/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 102ab619a3f341658383dc4997f9fbfc2cd12a43
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519182"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用した仮想マシン スケール セットの作成および管理

仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 仮想マシン スケール セットのライフサイクルを通して、1 つ以上の管理タスクを実行することが必要になる場合があります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想マシン スケール セットを作成して接続する
> * VM イメージを選択して使用する
> * 特定の VM インスタンス サイズを確認して使用する
> * スケール セットを手動でスケーリングする
> * スケール セットの一般的な管理タスクを実行する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>リソース グループを作成する
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシン スケール セットの前にリソース グループを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *EastUS* リージョンに作成されます。 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
このチュートリアルでスケール セットを作成または変更するときにこのリソース グループ名を指定します。


## <a name="create-a-scale-set"></a>スケール セットを作成する
まず、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM インスタンスの管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

次に、[New-AzVmss](/powershell/module/az.compute/new-azvmss) を使用して仮想マシン スケール セットを作成します。 個々の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 ロード バランサーには、TCP ポート 80 上のトラフィックを分散するルールだけでなく、TCP ポート 3389 上のリモート デスクトップ トラフィックと TCP ポート 5985 上の PowerShell リモート処理を許可するルールも含まれています。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかります。

> [!IMPORTANT]
> スケール セットに接続できないときは、 *[-SecurityGroupName "mySecurityGroup"](/powershell/module/az.compute/new-azvmss)* パラメーターを追加して、ネットワーク セキュリティ グループを作成することが必要になる場合があります。


## <a name="view-the-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを表示する
スケール セット内の VM インスタンスの一覧を表示するには、次のように [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) を使用します。

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

次の出力例には、スケール セット内の 2 つの VM インスタンスが示されています。

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

特定の VM インスタンスに関する追加情報を表示するには、[Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) に `-InstanceId` パラメーターを追加します。 次の例では、VM インスタンス *1* に関する情報を表示しています。

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>接続情報を一覧表示する
トラフィックを個々の VM インスタンスにルーティングするロード バランサーに、パブリック IP アドレスが割り当てられます。 既定では、リモート接続トラフィックを特定のポート上の各 VM に転送する Azure ロード バランサーにネットワーク アドレス変換 (NAT) 規則が追加されます。 スケール セット内の VM インスタンスに接続するには、割り当てられたパブリック IP アドレスとポート番号へのリモート接続を作成します。

スケール セット内の VM インスタンスに接続するための NAT ポートを一覧表示するには、まず、[Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer) を使用して、ロード バランサー オブジェクトを取得します。 次に、[Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig) を使用して、インバウンド NAT 規則を表示します。


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

次の出力例には、インスタンス名、ロード バランサーのパブリック IP アドレス、および NAT 規則によってトラフィックが転送される先のポート番号が示されています。

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

ルールの "*名前*" は、前の [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) コマンドで示された VM インスタンスの名前と一致します。 たとえば、VM インスタンス *0* に接続するには、*myScaleSet3389.0* を使用し、ポート *50001* に接続します。 VM インスタンス *1* に接続するには、*myScaleSet3389.1* の値を使用し、ポート *50002* に接続します。 PowerShell リモート処理を使用するには、*TCP* ポート *5985* の適切な VM インスタンス ルールに接続します。

[Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) を使用して、ロード バランサーのパブリック IP アドレスを表示します。


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

出力例:

```powershell
IpAddress
---------
52.168.121.216
```

最初の VM インスタンスへのリモート接続を作成します。 必要な VM インスタンスのパブリック IP アドレスとポート番号を、前のコマンドで示されたとおりに指定します。 メッセージが表示されたら、スケール セットを作成するときに使用した資格情報 (サンプル コマンドでは、既定の *azureuser* と *P\@ssw0rd!* ) を入力します。 Azure Cloud Shell を使用する場合は、ローカルの PowerShell プロンプトまたはリモート デスクトップ クライアントからこの手順を実行します。 次の例では、VM インスタンス *1* に接続します。

```powershell
mstsc /v 52.168.121.216:50001
```

VM インスタンスにログインすると、必要に応じて手動で構成を変更することができます。 ここで、リモート接続を閉じます。


## <a name="understand-vm-instance-images"></a>VM インスタンス イメージについて
Azure Marketplace には、VM インスタンスの作成に使用できる多くのイメージが用意されています。 使用可能な発行元の一覧を表示するには、[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) コマンドを使用します。

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

特定の発行元のイメージの一覧を表示するには、[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) を使用します。 また、`-PublisherName` か `-Offer` を使用してリストをフィルタリングすることも可能です。 次の例では、発行元名が *MicrosoftWindowsServer* で、*WindowsServer* に一致するプランがあるすべてのイメージを表示するように、リストをフィルター処理します。

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

次の出力例は、使用可能なすべての Windows Server イメージを示しています。

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

チュートリアルの開始時にスケール セットを作成したとき、VM インスタンスに対して既定の VM イメージである *Windows Server 2016 DataCenter* が提供されました。 [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) の出力に基づいて、異なる VM イメージを指定できます。 次の例では、`-ImageName` パラメーターに VM イメージ *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest* を指定して、スケール セットを作成します。 すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかるため、次のスケール セットをデプロイする必要はありません。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```

> [!IMPORTANT]
> "*最新*" のイメージ バージョンを使用することをお勧めします。 デプロイ時に使用可能な最新バージョンのイメージを使用するには、'latest' を指定します。 "latest" を使用する場合でも、デプロイ後に新しいバージョンが使用可能になったとしても、VM イメージが自動的に更新されないことに注意してください。

## <a name="understand-vm-instance-sizes"></a>VM インスタンス サイズについて
VM インスタンス サイズ (または *SKU*) により、CPU、GPU、メモリなど、VM インスタンスで利用できるコンピューティング リソースの量が決定されます。 スケール セット内の VM インスタンスのサイズは、予定のワーク ロードに合ったものにする必要があります。

### <a name="vm-instance-sizes"></a>VM インスタンス サイズ
次の表は、ユース ケース別に一般的な VM サイズを分類したものです。

| Type                     | 一般的なサイズ           |    説明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](../virtual-machines/sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0 - 7| CPU とメモリのバランスがとれています。 開発/テスト環境や、小中規模のアプリケーションとデータ ソリューションに最適です。  |
| [コンピューティングの最適化](../virtual-machines/sizes-compute.md)   | Fs、F             | メモリに対する CPU の比が大きくなっています。 トラフィックが中程度のアプリケーション、ネットワーク アプライアンス、バッチ処理に適しています。        |
| [メモリの最適化](../virtual-machines/sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | コアに対するメモリの比が大きくなっています。 リレーショナル データベース、中から大規模のキャッシュ、およびインメモリ分析に適しています。                 |
| [ストレージの最適化](../virtual-machines/sizes-storage.md)      | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV、NC            | 負荷の高いグラフィック レンダリングやビデオ編集に特化した VM です。       |
| [高性能](../virtual-machines/sizes-hpc.md) | H、A8 ～ 11          | オプションで高スループットのネットワーク インターフェイス (RDMA) を備えた、最も強力な CPU VM です。 

### <a name="find-available-vm-instance-sizes"></a>利用可能な VM インスタンス サイズを確認する
特定のリージョンで利用可能な VM インスタンス サイズのリストを確認するには、[Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) コマンドを使用します。 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

出力は次の縮約された例のようになります。ここには、各 VM サイズに割り当てられたリソースが示されています。

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

チュートリアルの開始時にスケール セットを作成したとき、VM インスタンスに対して既定の VM SKU である *Standard_DS1_v2* が提供されました。 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) の出力に基づいて、異なる VM インスタンス サイズを指定できます。 次の例では、`-VmSize` パラメーターに VM インスタンス サイズ *Standard_F1* を指定して、スケール セットを作成します。 すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかるため、次のスケール セットをデプロイする必要はありません。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>スケール セットの容量を変更する
スケール セットを作成したときは、2 つの VM インスタンスを要求しました。 スケール セット内の VM インスタンスの数を増やすか、または減らすために、手動でその容量を変更できます。 スケール セットにより、必要な数の VM インスタンスが作成または削除され、その後、トラフィックを分散するようにロード バランサーが構成されます。

最初に、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) を使用してスケール セット オブジェクトを作成し、次に `sku.capacity` に新しい値を指定します。 容量の変更を適用するには、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用します。 次の例では、スケール セット内の VM インスタンスの数を *3* に設定します。

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

スケール セットの容量を更新するには数分かかります。 スケール セットに存在するインスタンスの数を確認するには、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) を使用します。

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

次の出力例は、スケール セットの容量が *3* になったことを示しています。

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>一般的な管理タスク
これで、スケール セットを作成し、接続情報を一覧表示し、VM インスタンスに接続することができます。 VM インスタンスに異なる OS イメージを使用する方法、別の VM サイズを選択する方法、インスタンス数を手動でスケーリングする方法についても学習しました。 日常の管理の一環として、スケール セット内の VM インスタンスの停止、起動、または再起動が必要になる場合があります。

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを停止および割り当て解除する
スケール セット内の 1 つ以上の VM を停止するには、[Stop-AzVmss](/powershell/module/az.compute/stop-azvmss) を使用します。 `-InstanceId` パラメーターでは、停止する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が停止されます。 次の例では、インスタンス *1* を停止します。

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

既定では、停止された VM は割り当て解除されるため、コンピューティング料金は発生しません。 停止されたときに VM をプロビジョニングされた状態のままにする場合は、前のコマンドに `-StayProvisioned` パラメーターを追加します。 停止された VM がプロビジョニングされたままになる場合は、通常のコンピューティング料金が発生します。

### <a name="start-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを起動する
スケール セット内の 1 つ以上の VM を起動するには、[Start-AzVmss](/powershell/module/az.compute/start-azvmss) を使用します。 `-InstanceId` パラメーターでは、起動する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が起動されます。 次の例では、インスタンス *1* を起動します。

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>スケール セット内の VM インスタンスを再起動する
スケール セット内の 1 つ以上の VM を再起動するには、[Restart-AzVmss](/powershell/module/az.compute/restart-azvmss) を使用します。 `-InstanceId` パラメーターでは、再起動する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が再起動されます。 次の例では、インスタンス *1* を再起動します。

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする
リソース グループを削除すると、グループに含まれているリソース (VM インスタンス、仮想ネットワーク、ディスクなど) もすべて削除されます。 `-Force` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。 `-AsJob` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure PowerShell を使用していくつかの基本的なスケール セットの作成および管理タスクを実行する方法について学習しました。

> [!div class="checklist"]
> * 仮想マシン スケール セットを作成して接続する
> * VM イメージを選択して使用する
> * 特定の VM サイズを確認して使用する
> * スケール セットを手動でスケーリングする
> * スケール セットの一般的な管理タスクを実行する

次のチュートリアルに進み、スケール セット ディスクについて確認してください。

> [!div class="nextstepaction"]
> [スケール セットでデータ ディスクを使用する](tutorial-use-disks-powershell.md)
