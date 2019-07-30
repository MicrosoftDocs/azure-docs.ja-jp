---
title: Azure で管理イメージから VM を作成する | Microsoft Docs
description: Resource Manager デプロイ モデルで Azure PowerShell または Azure Portal を使って、一般化した管理イメージから仮想マシンを作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 3ae730389b54fc1034bfac3ffdc7e56a2dc5f3fc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718969"
---
# <a name="create-a-vm-from-a-managed-image"></a>管理イメージから VM を作成する

Azure portal または PowerShell を使用して、Azure 管理 VM イメージから複数の仮想マシン (VM) を作成できます。 管理 VM イメージには、OS や データ ディスクなど、VM の作成に必要な情報が含まれています。 OS ディスクやデータ ディスクなど、イメージを構成する仮想ハード ディスク (VHD) は、マネージド ディスクとして保管されます。 

新しい VM を作成する前に、ソース イメージとして使用する[マネージド VM イメージを作成](capture-image-resource.md)し、そのイメージにアクセスする必要があるすべてのユーザーに、イメージに対する読み取りアクセスを許可する必要があります。 


## <a name="use-the-portal"></a>ポータルの使用

1. [Azure Portal](https://portal.azure.com)を開きます。
2. 左側のメニューで、 **[すべてのリソース]** を選択します。 **[種類]** でリソースを並べ替えて、イメージを簡単に見つけることができます。
3. 一覧から使うイメージを選びます。 イメージの **[概要]** ページが開きます。
4. メニューから **[VM の作成]** を選択します。
5. 仮想マシンの情報を入力します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 完了したら、 **[OK]** を選択します。 既存のリソース グループに新しい VM を作成するか、 **[新規作成]** を選択して VM を格納する新しいリソース グループを作成することができます。
6. VM のサイズを選択します。 その他のサイズも表示するには、 **[すべて表示]** を選択するか、 **[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 
7. **[設定]** で必要な変更を行い、 **[OK]** を選択します。 
8. 概要ページでは、イメージ名が **[プライベート イメージ]** の一覧に表示されます。 **[OK]** を選択して、仮想マシンのデプロイを開始します。


## <a name="use-powershell"></a>PowerShell の使用

PowerShell を使用すると、[New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) コマンドレットに対して設定された簡略化されたパラメーターを使用して、イメージから VM を作成できます。 このイメージは、VM を作成する同じリソース グループに存在する必要があります。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) の簡易なパラメーター セットでは、イメージから VM を作成するための名前、リソース グループ、およびイメージ名を指定する必要があります。 New-AzVm には、自動的に作成するすべてのリソースの名前として **-Name** パラメーターの値が使用されます。 この例では、各リソースのより詳細な名前を指定しますが、それらがコマンドレットで自動的に作成されるようにしてください。 また、仮想ネットワークなどのリソースを事前に作成し、そのリソース名をコマンドレットに渡すこともできます。 New-AzVm では、一致する名前の既存のリソースが見つかった場合は、それが使用されます。

次の例では、*myImage* という名前のイメージから *myVMFromImage* という名前の VM を (*myResourceGroup* リソース グループ内に) 作成します。 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>次の手順
[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

