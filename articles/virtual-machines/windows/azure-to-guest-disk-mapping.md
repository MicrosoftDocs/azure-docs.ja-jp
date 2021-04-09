---
title: Azure ディスクを Windows VM のゲスト ディスクにマップする方法
description: Windows VM のゲスト ディスクの土台となる Azure ディスクを特定する方法を説明します。
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560992"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Azure ディスクを Windows VM のゲスト ディスクにマップする方法

VM のゲスト ディスクをバックアップする Azure ディスクの特定が必要になる場合があります。 場合によっては、ディスクまたはボリュームのサイズを、接続されている Azure ディスクのサイズと比較することができます。 同じサイズの複数の Azure ディスクが VM に接続されているシナリオでは、データ ディスクの論理ユニット番号 (LUN) を使用する必要があります。 

## <a name="what-is-a-lun"></a>LUN について

論理ユニット番号 (LUN) は、特定のストレージ デバイスを識別するために使用される番号です。 各ストレージ デバイスには、0 から始まる一意の数値識別子が割り当てられます。 デバイスの完全なパスは、バス番号、ターゲット ID 番号、および論理ユニット番号 (LUN) で表されます。 

次に例を示します。***バス番号 0、ターゲット ID 0、LUN 3***

この演習では、LUN を使用するだけで十分です。

## <a name="finding-the-lun"></a>LUN の検索

LUN を検索する方法は 2 つあります。[記憶域スペース](/windows-server/storage/storage-spaces/overview)を使用しているかどうかによって、どちらの方法を選択するかが異なります。

### <a name="disk-management"></a>ディスクの管理

記憶域プールを使用していない場合は、[[ディスクの管理]](/windows-server/storage/disk-management/overview-of-disk-management) を使用して LUN を見つけることができます。

1. VM に接続し、[ディスクの管理] を開きます (a.)。 [スタート] ボタンを右クリックし、[ディスクの管理] を選択します (a.)。 [検索の開始] ボックスに `diskmgmt.msc` を入力することもできます。
1. 下のウィンドウでいずれかのディスクを右クリックし、[プロパティ] を選択します。
1. LUN は [全般] タブの "場所" プロパティに一覧表示されます。

### <a name="storage-pools"></a>ストレージ プール

1. VM に接続し、サーバー マネージャーを開きます。
1. [ファイル サービスと記憶域サービス]、[ボリューム]、[記憶域プール] を選択します。
1. サーバー マネージャーの右下隅に、[物理ディスク] セクションがあります。 記憶域プールを構成するディスクと各ディスクの LUN がここに一覧表示されます。

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure ディスクの LUN の検索

Azure portal、Azure CLI、または Azure PowerShell を使用して、Azure ディスクの LUN を見つけることができます。

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure portal での Azure ディスクの LUN の検索

1. Azure portal で [仮想マシン] を選択して、仮想マシンの一覧を表示します。
1. 仮想マシンを選択します
1. [ディスク] を選択します。
1. 接続されているディスクの一覧からデータ ディスクを選択します。
1. ディスクの LUN がディスクの詳細ウィンドウに表示されます。 ここに表示される LUN は、デバイス マネージャーまたはサーバー マネージャーを使用してゲストで検索された Lun に関連付けられます。

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Azure CLI または Azure PowerShell を使用した Azure ディスクの LUN の検索

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---