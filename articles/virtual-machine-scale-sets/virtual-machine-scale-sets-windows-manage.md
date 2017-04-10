---
title: "仮想マシン スケール セットの VM を管理する | Microsoft Docs"
description: "Azure PowerShell を利用し、仮想マシン スケール セットで仮想マシンを管理する"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 96560927bd9dd93c9313be7392e0cea427a50980


---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セットで仮想マシンを管理する
この記事では、仮想マシン スケール セットで仮想マシンを管理する方法を紹介します。

スケール セットで仮想マシンを管理するためのほとんどの作業で、管理するマシンのインスタンス ID が必要になります。 [Azure リソース エクスプローラー](https://resources.azure.com) を利用し、スケール セットの仮想マシンのインスタンス ID を検索できます。 リソース エクスプローラーを利用し、完了した作業の状態を確認することもできます。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="display-information-about-a-scale-set"></a>スケール セットに関する情報を表示する
スケール セットに関する全般情報を取得できます。これは、インスタンス ビューとも呼ばれています。 あるいは、スケール セットのリソースに関する情報など、より具体的な情報を取得できます。

引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えてから、コマンドを実行します。

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

次のような結果が返されます。

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えます。 *#* を情報の取得元となる仮想マシンのインスタンス ID に置き換えてから、コマンドを実行します。

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

次のような結果が返されます。

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>スケール セットで仮想マシンを起動する
引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えます。 *#* を起動する仮想マシンの ID に置き換えてから、コマンドを実行します。

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

リソース エクスプローラーで、インスタンスの状態が **running**(実行中) であることがわかります。

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

-InstanceId パラメーターを使用しなければ、スケール セット内のすべての仮想マシンを開始できます。

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>スケール セットで仮想マシンを停止する
引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えます。 *#* を停止する仮想マシンの ID に置き換えてから、コマンドを実行します。

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

リソース エクスプローラーで、インスタンスの状態が **deallocated**(割り当て解除) であることがわかります。

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

仮想マシンを停止して割当てを解除しない場合、-StayProvisioned パラメーターを使用します。 -InstanceId パラメーターを使用しなければ、セット内のすべての仮想マシンを停止できます。

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>スケール セットで仮想マシンを再起動する
引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えます。 *#* を再起動する仮想マシンの ID に置き換えてから、コマンドを実行します。

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

-InstanceId パラメーターを使用しなければ、セット内のすべての仮想マシンを再起動できます。

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>スケール セットから仮想マシンを削除する
引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えます。 *#* を削除する仮想マシンの ID に置き換えてから、コマンドを実行します。  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

-InstanceId パラメーターを使用しなければ、すべての仮想マシンをスケール セットから削除できます。

## <a name="change-the-capacity-of-a-scale-set"></a>スケール セットの容量を変更する
セットの容量を変更することで、仮想マシンを追加または削除できます。 変更するスケール セットを取得し、必要な容量を設定してから、新しい容量でスケール セットを更新します。 次のコマンドでは、引用符で囲まれた値をリソース グループおよびスケール セットの名前に置き換えます。

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

スケール セットから仮想マシンを削除する場合は、ID が最も大きい仮想マシンから順に削除されます。




<!--HONumber=Dec16_HO2-->


