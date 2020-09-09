---
title: Azure 共有ディスクを使用して FCI を作成する (プレビュー)
description: Azure 共有ディスクを使用して、Azure Virtual Machines 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: e1a4a366b3e4fa045df69683d6e72b157ccf0a1f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87003629"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Azure 共有ディスクを使用して FCI を作成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 共有ディスクを使用して、Azure Virtual Machines (VM) 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 


## <a name="prerequisites"></a>前提条件 

この記事に記載されている手順を完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料](https://azure.microsoft.com/free/)で開始しましょう。 
- 同じ[可用性セット](../../../virtual-machines/linux/tutorial-availability-sets.md)と[近接配置グループ](../../../virtual-machines/windows/co-location.md#proximity-placement-groups)に含まれる、[2 つ以上の米国中西部の準備済み Windows Azure 仮想マシン](failover-cluster-instance-prepare-vm.md)。可用性セットは、障害ドメインと更新ドメインを **1** に設定して作成する必要があります。 
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)。 


## <a name="add-azure-shared-disk"></a>Azure 共有ディスクを追加する
共有ディスク機能を有効にしてマネージド Premium SSD ディスクをデプロイします。 `maxShares` を **2** に設定して、両方の FCI ノード間でディスクを共有できるようにします。 

次の手順に従って、Azure 共有ディスクを追加します。 


1. 次のスクリプトを *SharedDiskConfig.json* として保存します。 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. PowerShell を使用して *SharedDiskConfig.json* を実行します。 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. 次のコマンドを実行して、VM ごとに、接続された共有ディスクを GUID パーティション テーブル (GPT) として初期化し、New Technology File System (NTFS) としてフォーマットします。 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>フェールオーバー クラスターを作成する

フェールオーバー クラスターを作成するには、以下が必要です。

- クラスター ノードになる仮想マシンの名前。
- フェールオーバー クラスターの名前。
- フェールオーバー クラスターの IP アドレス。 クラスター ノードと同じ Azure 仮想ネットワークおよびサブネットでは使用されていない IP アドレスを使用することができます。


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

次の PowerShell スクリプトを実行すると、フェールオーバー クラスターが作成されます。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

次の PowerShell スクリプトを実行すると、フェールオーバー クラスターが作成されます。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

詳細については、「[フェールオーバー クラスター:クラスター ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」に関するセクションを確認してください。

---


## <a name="configure-quorum"></a>クォーラムを構成する

ビジネス ニーズに最も適したクォーラム ソリューションを構成します。 [ディスク監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness)、または[ファイル共有監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)を構成できます。 詳細については、[SQL Server VM でのクォーラム](hadr-cluster-best-practices.md#quorum)に関する記事をご覧ください。 

## <a name="validate-cluster"></a>クラスターの検証
UI または PowerShell を使用して、クラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンで次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 
1. **[次へ]** を選択します。
1. **[テストの選択]** の下で、 **[記憶域]** を*除く*すべてのテストを選択します

## <a name="test-cluster-failover"></a>クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 **[フェールオーバー クラスター マネージャー]** で、クラスターを右クリックし、 **[その他の操作]**  >  **[Move Core Cluster Resource]\(コア クラスター リソースの移動\)**  >  **[ノードの選択]** の順に選択した後、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 クラスターを各ノードに正常に移動できる場合は、SQL Server をインストールする準備ができています。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::

## <a name="create-sql-server-fci"></a>SQL Server FCI を作成する

フェールオーバー クラスターと、ストレージを含むすべてのクラスター コンポーネントを構成したら、SQL Server FCI を作成できます。

1. リモート デスクトップ プロトコル (RDP) を使用して、最初の仮想マシンに接続します。

1. **[フェールオーバー クラスター マネージャー]** で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 

1. **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択します。 ウィザードの指示に従って、SQL Server FCI をインストールします。

FCI データ ディレクトリは、Azure 共有ディスク上に存在する必要があります。 

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. セットアップで FCI が最初のノードにインストールされたら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開き、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** を選択します。 ウィザードの指示に従って SQL Server をインストールし、このサーバーを FCI に追加します。

   >[!NOTE]
   >SQL Server を含む Azure Marketplace ギャラリー イメージを使用した場合、SQL Server のツールはイメージに含まれています。 これらのいずれかのイメージを使用しなかった場合、SQL Server のツールは別途インストールしてください。 詳細については、「 [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)」 (SQL Server Management Studio (SSMS) のダウンロード) を参照してください。
   >

## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP への登録

ポータルから SQL Server VM を管理するには、それを[軽量管理モード](sql-vm-resource-provider-register.md#lightweight-management-mode)で SQL VM リソース プロバイダー (RP) に登録します。現時点では、FCI と Azure VM 上の SQL Server でサポートされている唯一のモードです。 


PowerShell を使用して軽量モードで SQL Server VM を登録します。  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>接続の構成 

現在のプライマリ ノードに適切にトラフィックをルーティングするには、お使いの環境に適した接続オプションを構成します。 [Azure ロード バランサー](hadr-vnn-azure-load-balancer-configure.md)を作成することができます。または、SQL Server 2019 と Windows Server 2019 を使用している場合は、代わりに[分散ネットワーク名](hadr-distributed-network-name-dnn-configure.md)機能をプレビューすることもできます。 

## <a name="limitations"></a>制限事項

- Windows Server 2019 上の SQL Server 2019 のみがサポートされています。 
- [軽量管理モード](sql-vm-resource-provider-register.md#management-modes)での SQL VM リソース プロバイダーへの登録のみがサポートされています。

## <a name="next-steps"></a>次のステップ

[仮想ネットワーク名と Azure ロード バランサー](hadr-vnn-azure-load-balancer-configure.md)または[分散ネットワーク名 (DNN)](hadr-distributed-network-name-dnn-configure.md) を使用した FCI への接続をまだ構成していない場合は、構成してください。 

Azure 共有ディスクがお客様に適した FCI 記憶域ソリューションでない場合は、代わりに [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)または[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)を使用して FCI を作成することを検討してください。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスター構成のベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。

詳細情報 
- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
