---
title: "バックアップ用に Azure Managed Disk をコピーする | Microsoft Docs"
description: "バックアップまたはディスクの問題のトラブルシューティングに使うために、Azure Managed Disk のコピーを作成する方法について説明します。"
documentationcenter: 
author: cwatson-cat
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
ms.openlocfilehash: c19b5156bc23b06de48bc7a6dc786f576f78127d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>管理スナップショットを使って Azure Managed Disk として保存された VHD のコピーを作成する
バックアップのために Managed Disk のスナップショットを作成するか、スナップショットから Managed Disk を作成してトラブルシューティングのためにテスト仮想マシンに接続します。 管理スナップショットは、VM の Managed Disk の完全なポイントインタイム コピーです。 VHD の読み取り専用のコピーを作成し、既定では Standard Managed Disk として保存します。 Managed Disks の詳細については、「[Azure Managed Disks overview (Azure Managed Disks の概要)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

価格の詳細については、「[Azure Storage の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」をご覧ください。 

## <a name="before-you-begin"></a>開始する前に
PowerShell を使用する場合は、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](/powershell/azure/overview)をご覧ください。

## <a name="copy-the-vhd-with-a-snapshot"></a>スナップショットを使用して VHD をコピーする
Managed Disk のスナップショットを作成するには、Azure Portal またはPowerShell を使います。

### <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal を使ってスナップショットを作成する 

1. [Azure Portal](https://portal.azure.com)にサインインします。
2. 左上の **[新規]** をクリックし、**[スナップショット]** を探します。
3. [スナップショット] ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの場所を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成する Managed Disk を選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_LRS]** をお勧めします。
9. **Create** をクリックしてください。

### <a name="use-powershell-to-take-a-snapshot"></a>PowerShell を使用し、スナップショットを作成する
次の手順では、コピーする VHD ディスクの取得方法、スナップショット構成の作成方法、AzureRmSnapshot コマンドレット<!--Add link to cmdlet when available-->を用いたディスクのスナップショットの取得方法を示します。 

1. パラメーターを設定する。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  パラメーターの値を次のように置き換えます。
  -  "myResourceGroup"は仮想マシンのリソース グループにします。
  -  "southeastasia"は管理スナップショットを保存する地域です。 <!---How do you look these up? -->
  -  "ContosoMD_datadisk1"はコピーする VHD ディスクの名前です。
  -  "ContosoMD_datadisk1_snapshot1"は新規スナップショットに使用する名前です。

2. コピーされる VHD ディスクを取得する。

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. スナップショットの構成を作成する。 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. スナップショットを取得する。

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
スナップショットを使って Managed Disk を作成し、高パフォーマンスが必要な VM に接続する計画がある場合は、New-AzureRmSnapshot コマンドで `-AccountType Premium_LRS` パラメーターを使います。 スナップショットが作成され、Premium Managed Disk として保存されます。 Premium Managed Disks は、Standard Managed Disks よりも高価格です。 なので、このパラメーターを使用する前に Premium Managed Disks が本当に必要なことを確認してください。


