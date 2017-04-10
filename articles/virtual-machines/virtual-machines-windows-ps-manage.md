---
title: "PowerShell を使用した Azure VM の管理 | Microsoft Docs"
description: "PowerShell を使用して Azure Virtual Machines を管理します。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 4a78ea285b844f13f0c8e28029835f123f589c12
ms.lasthandoff: 03/08/2017


---
# <a name="manage-azure-virtual-machines-using-powershell"></a>PowerShell を使用した Azure Virtual Machines の管理

この記事では、Azure 仮想マシンで実行される一般的な管理タスクについて説明します。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

> [!NOTE]
> この記事では、機能を使用するために Azure PowerShell の再インストールが必要になる場合があります。 Managed Disks 機能は、バージョン 3.5 以降でご利用いただけます。
> 
> 

コマンドの実行を容易にするために次の変数を作成し、環境に合わせて値を変更します。
    
```powershell
$myResourceGroup = "myResourceGroup"
$myVM = "myVM"
$location = "centralus"
```

## <a name="display-information-about-a-virtual-machine"></a>仮想マシンに関する情報の表示

仮想マシンに関する情報を取得します。

```powershell
Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM -DisplayHint Expand
```

次のような結果が返されます。

    ResourceGroupName             : myResourceGroup
    Id                            : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
    VmId                          : #########-####-####-####-############
    Name                          : myVM
    Type                          : Microsoft.Compute/virtualMachines
    Location                      : centralus
    Tags                          : {}
    DiagnosticsProfile            :
      BootDiagnostics             :
      Enabled                     : True
      StorageUri                  : https://mystorage1.blob.core.windows.net/
    AvailabilitySetReference      : 
      Id                          : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAV1
    Extensions[0]                 :
      Id                          : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/
                                    virtualMachines/myVM/extensions/BGInfo
      Name                        : BGInfo
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Compute
      VirtualMachineExtensionType : BGInfo
      TypeHandlerVersion          : 2.1
      AutoUpgradeMinorVersion     : True
      ProvisioningState           : Succeeded
    HardwareProfile               : 
      VmSize                      : Standard_DS1_v2
    NetworkProfile          
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
    OSProfile                     : 
      ComputerName                : myVM
      AdminUsername               : myaccount1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
         EnableAutomaticUpdates   : True
    ProvisioningState             : Succeeded
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : latest   
      OsDisk                      :
        OsType                    : Windows
        Name                      : myosdisk
        Vhd                       : 
          Uri                     : http://mystore1.blob.core.windows.net/vhds/myosdisk.vhd
        Caching                   : ReadWrite
        CreateOption              : FromImage
    NetworkInterfaceIDs[0]        : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC

## <a name="stop-a-virtual-machine"></a>仮想マシンの停止

仮想マシンを停止するには、2 つの方法があります。 仮想マシンを停止してそのすべての設定を保持する (引き続き課金されます)、または仮想マシンを停止して割り当てを解除します。 仮想マシンの割り当てを解除すると、それに関連付けられているすべてのリソースの割り当ても解除され、仮想マシンに対する課金が終了します。

### <a name="stop-and-deallocate"></a>停止と割り当て解除
    
```powershell
Stop-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

次のように確認が求められます。

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):
 
    Enter **Y** to stop the virtual machine.

数分後、次のような結果が返されます。

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:11:57 PM
    EndTime     : 9/13/2016 12:14:40 PM
    Error       :

### <a name="stop-and-stay-provisioned"></a>停止およびプロビジョニングされた状態を維持

```powershell
Stop-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM -StayProvisioned
```

次のように確認が求められます。

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):
 
仮想マシンを停止するには、「 **Y** 」と入力します。

数分後、次のような結果が返されます。

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:11:57 PM
    EndTime     : 9/13/2016 12:14:40 PM
    Error       :

## <a name="start-a-virtual-machine"></a>仮想マシンの起動
 
停止している場合は、仮想マシンを起動します。

```powershell
Start-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

数分後、次のような結果が返されます。

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:32:55 PM
    EndTime     : 9/13/2016 12:35:09 PM
    Error       :

既に実行されている仮想マシンを再起動するには、次に説明するように **Restart-AzureRmVM** を使用します。

## <a name="restart-a-virtual-machine"></a>仮想マシンの再起動

実行中の仮想マシンを再起動します。

```powershell
Restart-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

次のような結果が返されます。

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:54:40 PM
    EndTime     : 9/13/2016 12:55:54 PM
    Error       :    

## <a name="add-a-data-disk-to-a-virtual-machine"></a>データ ディスクを仮想マシンに追加する
    
### <a name="managed-data-disk"></a>管理対象データ ディスク

```powershell
$diskConfig = New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk = New-AzureRmDisk -DiskName "myDataDisk1" -Disk $diskConfig -ResourceGroupName $myResourceGroup
$vm = Get-AzureRmVM -Name $myVM -ResourceGroupName $myResourceGroup
Add-AzureRmVMDataDisk -VM $vm -Name "myDataDisk1" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

Add-AzureRmVMDataDisk を実行した後、次のような結果が表示されます。

    ResourceGroupName        : myResourceGroup
    Id                       : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Compute/virtualMachines/myVM
    VmId                     : ########-####-####-####-############
    Name                     : myVM
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {Id}
    DiagnosticsProfile       : {BootDiagnostics}
    HardwareProfile          : {VmSize}
    NetworkProfile           : {NetworkInterfaces}
    OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState        : Succeeded
    StorageProfile           : {ImageReference, OsDisk, DataDisks}
    DataDiskNames            : {myDataDisk1}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Network/networkInterfaces/myNIC}

Update-AzureRmVM を実行した後、次のような結果が表示されます。

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK

### <a name="unmanaged-data-disk"></a>非管理対象データ ディスク

```powershell
$vm = Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
Add-AzureRmVMDataDisk -VM $vm -Name "myDataDisk1" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

Add-AzureRmVMDataDisk を実行した後、次のような結果が表示されます。

    ResourceGroupName        : myResourceGroup
    Id                       : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Compute/virtualMachines/myVM
    VmId                     : ########-####-####-####-############
    Name                     : myVM
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {Id}
    DiagnosticsProfile       : {BootDiagnostics}
    HardwareProfile          : {VmSize}
    NetworkProfile           : {NetworkInterfaces}
    OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState        : Succeeded
    StorageProfile           : {ImageReference, OsDisk, DataDisks}
    DataDiskNames            : {myDataDisk1}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Network/networkInterfaces/myNIC}

Update-AzureRmVM を実行した後、次のような結果が表示されます。

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK

## <a name="update-a-virtual-machine"></a>仮想マシンの更新

この例では、仮想マシンのサイズを更新する方法を示します。

```powershell
$vm = Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
$vm.HardwareProfile.vmSize = "Standard_DS2_v2"
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

仮想マシンに使用できるサイズの一覧は、「 [Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」をご覧ください。

## <a name="delete-a-virtual-machine"></a>仮想マシンの削除

仮想マシンを削除します。  

```powershell
Remove-AzureRmVM -ResourceGroupName $myResourceGroup –Name $myVM
```

> [!NOTE]
> **-Force** パラメーターを使用して確認プロンプトをスキップできます。
> 
> 

-Force パラメーターを使用していない場合は、次のように確認が求められます。

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):

次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="next-steps"></a>次のステップ

- デプロイに問題がある場合は、「[Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)」(Azure Resource Manager を使用した Azure のデプロイで発生する一般的なエラーのトラブルシューティング) を参照してください。
- Azure PowerShell を使用して仮想マシンを作成する方法について、「[Resource Manager と PowerShell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
- テンプレートを使用して仮想マシンを作成する方法については、「 [Resource Manager テンプレートで Windows 仮想マシンを作成する](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

