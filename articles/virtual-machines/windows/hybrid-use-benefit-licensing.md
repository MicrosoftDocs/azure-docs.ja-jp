---
title: Windows Server 向け Azure Hybrid Benefit | Microsoft Docs
description: Windows ソフトウェア アシュアランスの特典を最大限利用してオンプレミスのライセンスを Azure で使用する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: 0a0b2a38cb01a5cd551d07da89a42dd837264aae
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875068"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure Hybrid Benefit
ソフトウェア アシュアランスを取得したお客様は、Windows Server 向け Azure Hybrid Benefit により、オンプレミスの Windows Server ライセンスを使用し、Azure で Windows 仮想マシンを低コストで実行することができます。 Windows Server 向け Azure ハイブリッド特典を使用して、Windows OS 搭載の新しい仮想マシンをデプロイすることができます。 この記事では、Windows Server 向け Azure ハイブリッド特典での新しい VM のデプロイ方法と、既存の稼働中 VM を更新する方法について説明します。 Windows Server 向け Azure Hybrid Benefit のライセンスとコスト削減について詳しくは、[Windows Server 向け Azure Hybrid Benefit のライセンス ページ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)をご覧ください。

> [!Important]
> 各 2 プロセッサ ライセンスまたは 16 コア ライセンスの各セットでは、最大 8 コアのインスタンスを 2 つ、または最大 16 コアのインスタンスを 1 つ利用できます。 Standard Edition のハイブリッド特典ライセンスは、オンプレミスまたは Azure 内のどちらかで 1 度のみ使用できます。 Datacenter Edition エディションの特典は、オンプレミスと Azure 内の両方で同時に使用することができます。
>

> [!Important]
> SQL Server やサード パーティ製マーケットプレース ソフトウェアなどの追加のソフトウェアを搭載した VM を含め、Windows Server OS を実行するすべてのリージョンの任意の VM で Windows Server 向け Azure ハイブリッド特典の使用がサポートされるようになりました。 
>

> [!NOTE]
> クラシック VM では、オンプレミスのカスタム イメージからの新規 VM のデプロイのみがサポートされています。 この記事でサポートされる機能を利用するには、最初にクラシック VM を Resource Manager モデルに移行する必要があります。
>

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure Hybrid Benefit の使用方法
Azure Hybrid Benefit で Windows 仮想マシンを使用する方法はいくつかあります。

1. Azure Marketplace で提供されている Windows Server イメージの 1 つから VM をデプロイできます
2. カスタム VM をアップロードし、Resource Manager テンプレートまたは Azure PowerShell を使ってデプロイできます
3. 既存の VM については、Azure ハイブリッド特典で実行するか、Windows Server のオンデマンド料金を支払うかの間で切り替えおよび変換することができます
4. Windows Server 向け Azure ハイブリッド特典を仮想マシン スケール セットに適用することもできます


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure ハイブリッド特典で VM を作成する
Windows Server 向け Azure ハイブリッド特典では、Windows Server OS ベースのすべてのイメージがサポートされます。 Azure プラットフォーム サポートのイメージを使用したり、独自のカスタム Windows Server イメージをアップロードしたりできます。 

### <a name="portal"></a>ポータル
Windows Server 向け Azure ハイブリッド特典で VM を作成するには、[Save money]\(コストの削減) セクションの切り替えを使用します。

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Template
Resource Manager テンプレート内に、追加パラメーター `licenseType` を指定する必要があります。 [Azure Resource Manager テンプレートの作成](../../resource-group-authoring-templates.md)で詳細を確認できます
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>既存の VM を Windows Server 向け Azure ハイブリッド特典を使用するように変換する
Windows Server 向け Azure ハイブリッド特典を利用するように変換したい既存の VM がある場合は、以下の手順に従って、VM のライセンスの種類を更新できます。

> [!NOTE]
> VM 上のライセンスの種類を変更しても、システムが再起動したり、サービスが中断したりすることはありません。  メタデータ フラグが更新されるだけです。
> 

### <a name="portal"></a>ポータル
ポータル VM ブレードから、"構成" オプションを選択して Azure ハイブリッド特典を使用するように VM を更新し、"Azure ハイブリッド特典" オプションに切り替えることができます。

### <a name="powershell"></a>PowerShell
- 既存の Windows Server VM を Windows Server 向け Azure ハイブリッド特典に変換する

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- 特典付きの Windows Server VM を従量課金制に戻す

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- 既存の Windows Server VM を Windows Server 向け Azure ハイブリッド特典に変換する

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM がライセンスの特典を利用していることを確認する方法
VM を PowerShell、Resource Manager テンプレート、ポータルのいずれかの方法でデプロイした後、次の方法で設定を確認できます。

### <a name="portal"></a>ポータル
ポータルの VM ブレードから、[Configuration] (構成) タブを選択して Windows Server 向け Azure ハイブリッド特典の切り替えを表示できます。

### <a name="powershell"></a>PowerShell
次の例は、1 つの VM のライセンスの種類を示しています
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

出力:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

この出力を、Windows Server 向け Azure Hybrid Benefit のライセンスなしでデプロイされた次の VM と比べてください。
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> VM 上のライセンスの種類を変更しても、システムが再起動したり、サービスが中断したりすることはありません。 これはメタデータのライセンス フラグのみです。
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>サブスクリプションのすべての Windows Server 向け Azure ハイブリッド特典付き VM の一覧表示
Windows Server 向け Azure Hybrid Benefit でデプロイされたすべての仮想マシンを参照し、カウントするには、自分のサブスクリプションから次のコマンドを実行します。

### <a name="portal"></a>ポータル
仮想マシンまたは仮想マシン スケール セットのリソース ブレードから、[Azure Hybrid Benefit]\(Azure ハイブリッド特典) を含むようテーブル列を構成することで、すべての VM と ライセンスの種類の一覧を表示することができます。 VM の設定は、[Enabled]\(有効)、[Not enabled]\(無効)、または [Not supported]\(未サポート) の状態にできます。

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure ハイブリッド特典で仮想マシン スケール セットをデプロイする
仮想マシン スケール セットの Resource Manager テンプレート内の VirtualMachineProfile プロパティ内に、追加パラメーター `licenseType` を指定する必要があります。 これは、ARM テンプレート、PowerShell、Azure CLI、または REST を使用してご自分のスケール セットの作成時または更新時に行うことができます。

次の例では、Windows Server 2016 Datacenter イメージで ARM テンプレートを作成します。
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
[仮想マシン スケール セットの変更](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md)方法の詳細を確認し、スケール セットの更新方法をさらに調べることもできます。

## <a name="next-steps"></a>次の手順
- [Azure ハイブリッド特典でコストを削減する方法](https://azure.microsoft.com/pricing/hybrid-use-benefit/)について詳しく読みます
- [Azure ハイブリッド特典についてよく寄せられる質問](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)について詳しく読みます
- [Windows Server 向け Azure ハイブリッド特典のライセンスの詳しいガイダンス](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)を確認します。
- [Windows Server 向け Azure ハイブリッド特典と Azure Site Recovery によって、Azure へのアプリケーションの移行のコスト効率を高める方法](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)の詳細を確認します
- [マルチテナント ホスティング権限を使用した Azure 上の Windows 10](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) について確認します
- [Resource Manager テンプレートの使用方法](../../azure-resource-manager/resource-group-overview.md)の詳細を確認します
