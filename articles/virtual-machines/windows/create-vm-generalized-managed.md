---
title: Azure で管理イメージから VM を作成する
description: Azure PowerShell またはポータルを使用して、一般化されたマネージド イメージから Windows 仮想マシンを作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: bc87b0ec9cf41b2ddc5746a9734463f544b0cc2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550690"
---
# <a name="create-a-vm-from-a-managed-image"></a>管理イメージから VM を作成する

Azure portal または PowerShell を使用して、Azure 管理 VM イメージから複数の仮想マシン (VM) を作成できます。 管理 VM イメージには、OS や データ ディスクなど、VM の作成に必要な情報が含まれています。 OS ディスクやデータ ディスクなど、イメージを構成する仮想ハード ディスク (VHD) は、マネージド ディスクとして保管されます。 

新しい VM を作成する前に、ソース イメージとして使用する[マネージド VM イメージを作成](capture-image-resource.md)し、そのイメージにアクセスする必要があるすべてのユーザーに、イメージに対する読み取りアクセスを許可する必要があります。 

1 つのマネージド イメージは、最大 20 個の同時デプロイをサポートします。 同じマネージド イメージから 20 個を超える VM を同時に作成しようとすると、1 つの VHD におけるストレージ パフォーマンスの制限によって、プロビジョニングのタイムアウトが発生する可能性があります。 20 個を超える VM を同時に作成するには、20 個の同時実行 VM デプロイごとに 1 つのレプリカで構成された[共有イメージ ギャラリー](../shared-image-galleries.md) イメージを使用します。

## <a name="use-the-portal"></a>ポータルの使用

1. [Azure portal](https://portal.azure.com) にアクセスしてマネージド イメージを検索します。 **[イメージ]** を検索して選択します。
3. 一覧から使うイメージを選びます。 イメージの **[概要]** ページが開きます。
4. メニューから **[VM の作成]** を選択します。
5. 仮想マシンの情報を入力します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 完了したら、 **[OK]** を選択します。 既存のリソース グループに新しい VM を作成するか、 **[新規作成]** を選択して VM を格納する新しいリソース グループを作成することができます。
6. VM のサイズを選択します。 その他のサイズも表示するには、 **[すべて表示]** を選択するか、 **[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 
7. **[設定]** で必要な変更を行い、 **[OK]** を選択します。 
8. 概要ページでは、イメージ名が **[プライベート イメージ]** の一覧に表示されます。 **[OK]** を選択して、仮想マシンのデプロイを開始します。


## <a name="use-powershell"></a>PowerShell の使用

PowerShell を使用すると、[New-AzVm](/powershell/module/az.compute/new-azvm) コマンドレットに対して設定された簡略化されたパラメーターを使用して、イメージから VM を作成できます。 このイメージは、VM を作成する同じリソース グループに存在する必要があります。

 

[New-AzVm](/powershell/module/az.compute/new-azvm) の簡易なパラメーター セットでは、イメージから VM を作成するための名前、リソース グループ、およびイメージ名を指定する必要があります。 New-AzVm には、自動的に作成するすべてのリソースの名前として **-Name** パラメーターの値が使用されます。 この例では、各リソースのより詳細な名前を指定しますが、それらがコマンドレットで自動的に作成されるようにしてください。 また、仮想ネットワークなどのリソースを事前に作成し、そのリソース名をコマンドレットに渡すこともできます。 New-AzVm では、一致する名前の既存のリソースが見つかった場合は、それが使用されます。

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



## <a name="next-steps"></a>次のステップ
[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md)