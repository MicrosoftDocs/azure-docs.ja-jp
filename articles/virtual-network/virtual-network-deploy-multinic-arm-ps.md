---
title: "複数の NIC を持つ VM の作成 - Azure PowerShell | Microsoft Docs"
description: "PowerShell を使用して、複数の NIC を持つ VM を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88880483-8f9e-4eeb-b783-64b8613407d9
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: ae6c92d4f005b389de2d65c30113580190914b42
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-multiple-nics-using-powershell"></a>PowerShell を使用して複数の NIC を持つ VM を作成する

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-multinic-arm-cli.md)
> * [テンプレート](virtual-network-deploy-multinic-arm-template.md)
> * [PowerShell (クラシック)](virtual-network-deploy-multinic-classic-ps.md)
> * [Azure CLI (クラシック)](virtual-network-deploy-multinic-classic-cli.md)

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](virtual-network-deploy-multinic-classic-ps.md)ではなくこのモデルをお勧めします。
>

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

次の手順では、*IaaSStory* という名前のリソース グループを WEB サーバーに、*IaaSStory-BackEnd* という名前のリソース グループを DB サーバーに使用します。

## <a name="prerequisites"></a>前提条件
DB サーバーを作成する前に、このシナリオで必要なすべてのリソースを備えた *IaaSStory* リソース グループを作成する必要があります。 これらのリソースを作成するには、次の手順に従います。

1. [テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)に移動します。
2. テンプレート ページの **[親リソース グループ]** の右側にある **[Azure へのデプロイ]** をクリックします。
3. 必要に応じて、パラメーター値を変更し、Azure プレビュー ポータル内の手順に従ってリソース グループをデプロイします。

> [!IMPORTANT]
> ストレージ アカウント名が一意であることを確認してください。 Azure では重複するストレージ アカウント名を使用できません。
> 

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>バックエンド VM を作成する
バックエンド VM は、次のリソースの作成に依存します。

* **データ ディスクのストレージ アカウント**。 パフォーマンスを高めるために、データベース サーバー上のデータ ディスクはソリッド ステート ドライブ (SSD) テクノロジーを使用します。これには、Premium Storage アカウントが必要です。 デプロイする Azure の場所が Premium Storage をサポートすることを確認してください。
* **NIC**。 各 VM には&2; つの NIC があり、1 つはデータベース アクセス用で、もう&1; つは管理用です。
* **可用性セット**。 メンテナンス中に少なくとも&1; つの VM が稼働し、実行されているようにするためには、すべてのデータベース サーバーを単一の可用性セットに追加します。  

### <a name="step-1---start-your-script"></a>手順 1 - スクリプトの開始
使用するすべての PowerShell スクリプトは、 [ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1)からダウンロードできます。 以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

1. 上記の [前提条件](#Prerequisites)でデプロイした既存のリソース グループに基づいて、以下の変数の値を変更します。

    ```powershell
    $existingRGName        = "IaaSStory"
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    $remoteAccessNSGName   = "NSG-RemoteAccess"
    $stdStorageAccountName = "wtestvnetstoragestd"
    ```

2. バックエンド デプロイメントに使用する値に基づいて、以下の変数の値を変更します。

    ```powershell
    $backendRGName         = "IaaSStory-Backend"
    $prmStorageAccountName = "wtestvnetstorageprm"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $publisher             = "MicrosoftSQLServer"
    $offer                 = "SQL2014SP1-WS2012R2"
    $sku                   = "Standard"
    $version               = "latest"
    $vmNamePrefix          = "DB"
    $osDiskPrefix          = "osdiskdb"
    $dataDiskPrefix        = "datadisk"
    $diskSize               = "120"    
    $nicNamePrefix         = "NICDB"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```
3. デプロイメントに必要な既存のリソースを取得します。

    ```powershell
    $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
    $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
    $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
    $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>手順 2 - VM 用に必要なリソースの作成
新しいリソース グループ、データ ディスク用のストレージ アカウント、およびすべての VM 用の可用性セットを作成する必要があります。 VM ごとに、ローカル管理者アカウントの資格情報も必要です。 これらのリソースを作成するには、次の手順を実行します。

1. 新しいリソース グループを作成します。

    ```powershell
    New-AzureRmResourceGroup -Name $backendRGName -Location $location
    ```
2. 上記で作成したリソース グループ内に、新しい Premium Storage アカウントを作成します。

    ```powershell
    $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
    -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location
    ```
3. 新しい可用性セットを作成します。

    ```powershell
    $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location
    ```
4. VM ごとに、使用するローカル管理者アカウントの資格情報を取得します。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

### <a name="step-3---create-the-nics-and-back-end-vms"></a>手順 3 - NIC とバックエンド VM の作成
ループを使用して必要な数の VM を作成し、必要な NIC と VM をループ内に作成します。 NIC と VM を作成するには、次の手順を実行します。

1. `$numberOfVMs` 変数の値に基づいて、`for` ループを開始して、1 つの VM と&2; つの NIC を作成するコマンドを必要な回数だけ繰り返します。
   
    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. データベース アクセスに使用する NIC を作成します。

    ```powershell
    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
    -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1
    ```

3. リモート アクセスに使用する NIC を作成します。 この NIC がどのようにして NSG を自身に関連付けるかに注意してください。

    ```powershell
    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
    -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
    -NetworkSecurityGroupId $remoteAccessNSG.Id
    ```

4. `vmConfig` オブジェクトを作成します。

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id
    ```

5. VM あたり&2; つのデータ ディスクを作成します。 データ ディスクが、前に作成した、Premium Storage アカウント内にあることに注意してください。

    ```powershell
    $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"
    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
    -VhdUri $data1VhdUri -CreateOption empty -Lun 0

    $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"
    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
    -VhdUri $data2VhdUri -CreateOption empty -Lun 1
    ```

6. VM で使用するオペレーティング システムとイメージを構成します。

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version
    ```

7. 上記で作成した&2; つの NIC を `vmConfig` オブジェクトに追加します。

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id
    ```

8. OS ディスクを作成し、VM を作成します。 `}` で `for` ループが終了することに注意してください。

    ```powershell
    $osDiskName = $vmName + "-" + $osDiskSuffix
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
    }
    ```

### <a name="step-4---run-the-script"></a>手順 4 - スクリプトの実行
ニーズに合わせてスクリプトをダウンロードおよび変更し、スクリプトを実行して複数の NIC を持つバックエンド データベース VM を作成しました。

1. スクリプトを保存し、それを **PowerShell** コマンド プロンプトまたは **PowerShell ISE** から実行します。 次のように、最初の出力が表示されます。

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
            Actions  NotActions
            =======  ==========
                *                  

        ResourceId        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory-Backend

2. 数分後、資格情報プロンプトに入力して、 **[OK]**をクリックします。 次に示す出力は、1 つの VM を表しています。 プロセス全体を完了するには、8 分間かかりました。

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText :     {
                                     "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Compute/availabilitySets/ASDB"
                                    }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                        "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0
        EndTime             : [Date] [Time]
        Error               :
        Output              :
        StartTime           : [Date] [Time]
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK

