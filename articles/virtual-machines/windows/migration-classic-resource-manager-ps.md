---
title: PowerShell を使用した Resource Manager への移行 | Microsoft Docs
description: この記事では、Azure PowerShell コマンドを使用して、プラットフォームでサポートされているクラシックから Azure Resource Manager (ARM) へ仮想マシン (VM)、仮想ネットワーク (VNET)、ストレージ アカウントなどの IaaS リソースを移行する方法について説明します
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: bf964f23b6c38444fb15b61161cb7ed5a2b15e00
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102649"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する
以下の手順では、Azure PowerShell コマンドを使用して、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに移行する方法を説明します。

必要に応じて、 [Azure コマンド ライン インターフェイス (Azure CLI)](../linux/migration-classic-resource-manager-cli.md)を使用してリソースを移行することもできます。

* サポートされる移行シナリオの背景については、「 [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md)」を参照してください。
* 詳細なガイダンスと移行のチュートリアルについては、「 [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md)」を参照してください。
* [Review most common migration errors](migration-classic-resource-manager-errors.md) (移行の一般的なエラーを確認する)

<br>
移行プロセス中に実行する必要のある手順を順番に示すフローチャートを以下に示します。

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-plan-for-migration"></a>手順 1:移行の計画
ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

* [機能と構成のうちでサポートされるものとサポートされないもの](migration-classic-resource-manager-overview.md)に関するページをご覧ください。 サポートされていない構成または機能を使用する仮想マシンがある場合、構成または機能のサポートが発表されるまで待つことをお勧めします。 または、必要に応じて、その機能を削除するか、その構成を外して、移行を有効にします。
* インフラストラクチャとアプリケーションをデプロイする自動化スクリプトを今お持ちの場合は、これらのスクリプトを移行に使用して、同様のテスト設定を作成してみてください。 または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。

> [!IMPORTANT]
> 現在、Application Gateway は、クラシックから Resource Manager への移行には対応していません。 Application Gateway が設定された従来の仮想ネットワークを移行するには、準備操作を実行してネットワークを移動する前に、まずそのゲートウェイを削除する必要があります。 移行が完了した後、Azure Resource Manager でゲートウェイに再接続します。
>
>別のサブスクリプションの ExpressRoute 回線に接続する ExpressRoute ゲートウェイは自動移行できません。 そのような場合は、ExpressRoute ゲートウェイを削除し、仮想ネットワークを移行してゲートウェイを再作成します。 詳細については、「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](../../expressroute/expressroute-migration-classic-resource-manager.md)」を参照してください。

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>手順 2:Azure PowerShell の最新バージョンのインストール
Azure PowerShell をインストールするための主なオプションは 2 つあります:[PowerShell ギャラリー](https://www.powershellgallery.com/profiles/azure-sdk/)または [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps)。 WebPI は月次の更新プログラムを受け取ります。 PowerShell ギャラリーは、継続的に更新プログラムを受け取ります。 この記事は、Azure PowerShell バージョン 2.1.0 に基づいています。

インストール指示については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>手順 3:Azure portal でサブスクリプションの管理者であることを確認する
この移行を実行には、[Azure ポータル](https://portal.azure.com)で自分をサブスクリプションの共同管理者として追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで、 **[サブスクリプション]** を選択します。 表示されない場合は、 **[すべてのサービス]** を選択します。
3. 適切なサブスクリプションのエントリを検索し、を見て、 **[MY ROLE]** (自分のロール) フィールドを確認します。 共同管理者の場合は、この値は _[アカウント管理者]_ です。

共同管理者を追加できない場合は、サービス管理者またはサブスクリプションの共同管理者に連絡して、追加してもらってください。   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>手順 4:サブスクリプションを設定し、移行するためにサインアップする
まず PowerShell プロンプトを開始します。 移行の場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。

Resource Manager モデルの自分のアカウントにサインインします。

```powershell
    Connect-AzAccount
```

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

現在のセッション用の Azure サブスクリプションを設定します。 この例では、既定のサブスクリプション名を **My Azure Subscription** に設定します。 例のサブスクリプション名を対象のサブスクリプションの名前に置き換えてください。

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> 登録は 1 回限りの手順ですが、移行を試みる前に実行する必要があります。 登録を行わないと、次のエラー メッセージが表示されます。
>
> *BadRequest: 移行の対象サブスクリプションが登録されていません。*

次のコマンドを使用して、移行リソース プロバイダーに登録します。

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

登録が完了するまで 5 分間お待ちください。 承認の状態は、次のコマンドで確認できます。

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

RegistrationState が `Registered` であることを確認してから続行してください。

ここで、クラシック モデルの自分のアカウントにサインインします。

```powershell
    Add-AzureAccount
```

次のコマンドを使用して、利用可能なサブスクリプションを取得します。

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

現在のセッション用の Azure サブスクリプションを設定します。 この例では、既定のサブスクリプションを **My Azure Subscription** に設定します。 例のサブスクリプション名を対象のサブスクリプションの名前に置き換えてください。

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>手順 5:現在のデプロイまたは VNET の Azure リージョンで Azure Resource Manager 仮想マシンの vCPU 数が十分にあることを確認する
Azure Resource Manager での現在の vCPU 数は、次の PowerShell コマンドを使用して確認できます。 vCPU クォータの詳細については、「[制限と Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)」を参照してください。

この例は、**米国西部**リージョンでの可用性をチェックします。 例のリージョン名を対象のリージョン名に置き換えてください。

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>手順 6:IaaS リソースを移行するコマンドを実行する
* [クラウド サービス内 (仮想ネットワーク外) で VM を移行する](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [仮想ネットワーク内で VM を移行する](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [ストレージ アカウントを移行する](#step-62-migrate-a-storage-account)

> [!NOTE]
> ここで説明するすべての操作がべき等です。 サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。 これによりプラットフォームでアクションが再試行されます。


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>手順 6.1:オプション 1 - クラウド サービス内 (仮想ネットワーク外) で仮想マシンを移行する
次のコマンドを使用してクラウド サービスの一覧を取得し、移行するクラウド サービスを選択します。 クラウド サービスの VM が仮想ネットワーク内にある場合、または Web ロールか worker ロールを持つ場合は、コマンドではエラー メッセージが返されます。

```powershell
    Get-AzureService | ft Servicename
```

クラウド サービスのデプロイ名を取得します。 この例では、**My Service** というサービス名を使用しています。 例のサービス名を対象のサービスの名前に置き換えてください。

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

移行に合わせてクラウド サービスの仮想マシンを準備します。 2 つのオプションから選択できます。

* **オプション 1: プラットフォームで作成した仮想ネットワークに VM を移行する**

    最初に、次のコマンドを使用して、クラウド サービスを移行できるかどうかを検証します。

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    次のコマンドによって、移行をブロックするすべての警告とエラーが表示されます。 検証が成功した場合は、**準備**手順に進むことができます。

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **オプション 2: Resource Manager デプロイ モデルの既存の仮想ネットワークに移行する**

    この例では、リソース グループ名を **myResourceGroup**、仮想ネットワーク名を **myVirtualNetwork**、サブネット名を **mySubNet** に設定します。 例の名前を対象のリソースの名前に置き換えてください。

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    最初に、次のコマンドを使用して、仮想ネットワークを移行できるかどうかを検証します。

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    次のコマンドによって、移行をブロックするすべての警告とエラーが表示されます。 検証が成功した場合は、次の準備手順に進むことができます。

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

上記のいずれかのオプションで Prepare 操作が完了したら、VM の移行状態を照会します。 VM の状態が `Prepared` であることを確認してください。

この例では、VM 名を **myVM** に設定します。 例の名前を対象の VM の名前に置き換えてください。

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

PowerShell または Azure ポータルを使用して、準備したリソースの構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>手順 6.1:オプション 2 - 仮想ネットワーク内の仮想マシンを移行する

仮想ネットワーク内の仮想マシンを移行するには、その仮想ネットワークを移行します。 仮想マシンは、仮想ネットワークとともに自動的に移行されます。 移行する仮想ネットワークを選択します。
> [!NOTE]
> [単一の従来の仮想マシンを移行](migrate-single-classic-to-resource-manager.md)するには、仮想マシンの VHD (OS とデータ) ファイルを使用して Managed Disks を備えた新しい Resource Manager 仮想マシンを作成します。
<br>

> [!NOTE]
> 仮想ネットワーク名は、新しいポータルに表示される名前と異なる可能性があります。 新しい Azure Portal には、`[vnet-name]` という名前が表示されますが、実際の仮想ネットワーク名は `Group [resource-group-name] [vnet-name]` 型の名前です。 移行する前に、コマンド `Get-AzureVnetSite | Select -Property Name` を使用して実際の仮想ネットワーク名を検索するか、または以前の Azure Portal で表示します。 

この例では、仮想ネットワーク名を **myVnet** に設定します。 例の仮想ネットワーク名を対象の仮想ネットワークの名前に置き換えてください。

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 仮想ネットワークに Web ロールか worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合は、検証エラー メッセージが返されます。

最初に、次のコマンドを使用して、仮想ネットワークを移行できるかどうかを検証します。

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

次のコマンドによって、移行をブロックするすべての警告とエラーが表示されます。 検証が成功した場合は、次の準備手順に進むことができます。

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell または Azure Portal のどちらかを使用して、準備した仮想マシンの構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>手順 6.2: ストレージ アカウントを移行する
仮想マシンの移行が完了したら、ストレージ アカウントを移行する前に、次の前提条件チェックを実行することをお勧めします。

> [!NOTE]
> ストレージ アカウントに関連付けられたディスクまたは VM データがない場合は、「**ストレージ アカウントを検証して移行を開始する**」セクションに進むことができます。

* **VM を移行したか、ストレージ アカウントにディスク リソースがあるかどうかの前提条件チェック**
    * **ディスクがストレージ アカウントに格納されている従来の仮想マシンを移行する**

        次のコマンドは、ストレージ アカウント内のすべての クラシック VM ディスクの RoleName および DiskName プロパティを返します。 RoleName はディスクが接続される仮想マシンの名前です。 このコマンドでディスクが返された場合、これらのディスクが接続される仮想マシンは、ストレージ アカウントを移行する前に移行されています。
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **ストレージ アカウントに格納されている接続されていないクラシック VM ディスクを削除する**

        次のコマンドを使用して、ストレージ アカウントの接続されていないクラシック VM ディスクを見つけます。

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        上のコマンドでディスクが返された場合、次のコマンドを使用してこれらのディスクを削除します。

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **ストレージ アカウントに格納されている VM イメージを削除する**

        次のコマンドは、ストレージ アカウントに格納されたすべての VM イメージと OS ディスクを返します。
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         次のコマンドは、ストレージ アカウントに格納されたすべての VM イメージとデータ ディスクを返します。
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        次のコマンドを使用して、上のコマンドによって返されるすべての VM イメージを削除します。
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **ストレージ アカウントを検証して移行を開始する**

    次のコマンドを使用して、各ストレージ アカウントの移行の検証をします。 この例では、**myStorageAccount** というストレージ アカウント名を使用しています。 例の名前を対象のストレージ アカウントの名前に置き換えてください。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    次の手順は、ストレージ アカウントの移行の準備です

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Azure PowerShell または Azure Portal のどちらかを使用して、準備したストレージ アカウントの構成を確認します。 移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    準備した構成が正しい場合は、次に進み、以下のコマンドを使用してリソースをコミットできます。

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>次の手順
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
