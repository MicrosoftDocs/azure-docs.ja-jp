---
title: "複数の NIC を持つ VM (クラシック) を作成する - Azure PowerShell | Microsoft Docs"
description: "PowerShell を使用して複数の NIC を持つ VM (クラシック) を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 6e2bb0e228aa28c79969cba07352061abbb47951
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>PowerShell を使用して複数の NIC を持つ VM (クラシック) を作成する

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Azure に仮想マシン (VM) を作成し、複数のネットワーク インターフェイス (NIC) を各 VM にアタッチできます。 複数 NIC では、複数の NIC 全体にトラフィック タイプを分散できます。 たとえば、インターネットと通信する NIC もあれば、インターネットに接続されていない内部リソースとのみ通信する NIC もあるとします。 アプリケーションの配布や WAN の最適化ソリューションなど、多くのネットワーク仮想アプライアンスでは、複数の NIC 間にネットワーク トラフィックを分散できる必要があります。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager デプロイ モデル](virtual-network-deploy-multinic-arm-ps.md)を使用してこれらの手順を実行する方法について説明します。

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

次の手順では、*IaaSStory* という名前のリソース グループを WEB サーバーに、*IaaSStory-BackEnd* という名前のリソース グループを DB サーバーに使用します。

## <a name="prerequisites"></a>前提条件

DB サーバーを作成する前に、このシナリオで必要なすべてのリソースを備えた *IaaSStory* リソース グループを作成する必要があります。 これらのリソースを作成するには、次の手順に従います。 VNet を作成するには、「[仮想ネットワークを作成する](virtual-networks-create-vnet-classic-netcfg-ps.md)」の記事の手順に従います。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>バックエンド VM を作成する
バックエンド VM は、次のリソースの作成に依存します。

* **バックエンド サブネット**。 データベース サーバーは、トラフィックを分離するために、別のサブネットに含まれます。 次のスクリプトでは、このサブネットが *WTestVnet*という名前の Vnet に存在する必要があります。
* **データ ディスクのストレージ アカウント**。 パフォーマンスを高めるために、データベース サーバー上のデータ ディスクはソリッド ステート ドライブ (SSD) テクノロジーを使用します。これには、Premium Storage アカウントが必要です。 デプロイする Azure の場所が Premium Storage をサポートすることを確認してください。
* **可用性セット**。 メンテナンス中に少なくとも 1 つの VM が稼働し、実行されているようにするためには、すべてのデータベース サーバーを単一の可用性セットに追加します。

### <a name="step-1---start-your-script"></a>手順 1 - スクリプトの開始
使用するすべての PowerShell スクリプトは、 [ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)からダウンロードできます。 以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

1. 上記の [前提条件](#Prerequisites)でデプロイした既存のリソース グループに基づいて、以下の変数の値を変更します。

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. バックエンド デプロイメントに使用する値に基づいて、以下の変数の値を変更します。

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>手順 2 - VM 用に必要なリソースの作成
新しいクラウド サービスとすべての VM に対してデータ ディスクのストレージ アカウントを作成する必要があります。 また、イメージと、VM のローカル管理者アカウントを指定する必要があります。 これらのリソースを作成するには、次の手順に従います。

1. 新しいクラウド サービスを作成します。

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Premium Storage アカウントを作成します。

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. 上で作成したストレージ アカウントを、サブスクリプションの現在のストレージ アカウントとして設定します。

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. VM のイメージを選択します。

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. ローカル管理者アカウントの資格情報を設定します。

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>手順 3 - VM の作成
ループを使用して必要な数の VM を作成し、必要な NIC と VM をループ内に作成します。 NIC と VM を作成するには、次の手順を実行します。

1. `$numberOfVMs` 変数の値に基づいて、`for` ループを開始して、1 つの VM と 2 つの NIC を作成するコマンドを必要な回数繰り返します。

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. VM のイメージ、サイズ、可用性セットを指定する `VMConfig` オブジェクトを作成します。

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Windows VM として VM をプロビジョニングします。

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.Password
    ```

4. 既定の NIC 設定し、静的 IP アドレスを割り当てます。

    ```powershell
    Set-AzureSubnet            -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP     -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. 各 VM の 2 つめの NIC を追加します。

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```

6. 各 VM のデータ ディスクを作成します。

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. 各 VM を作成し、ループを終了します。

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>手順 4 - スクリプトの実行
ニーズに合わせてスクリプトをダウンロードおよび変更し、スクリプトを実行して複数の NIC を持つバックエンド データベース VM を作成しました。

1. スクリプトを保存し、それを **PowerShell** コマンド プロンプトまたは **PowerShell ISE** から実行します。 次のように、最初の出力が表示されます。

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. 資格情報プロンプトに必要な情報を入力して、 **[OK]**をクリックします。 次に示す出力が返されます。

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

