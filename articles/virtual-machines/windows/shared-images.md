---
title: Azure PowerShell で共有 VM イメージを作成する | Microsoft Docs
description: Azure PowerShell を使用して Azure に共有仮想マシン イメージを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/11/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 07912369179a1d1226c750a8e86837fdc6887922
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984173"
---
# <a name="preview-create-a-shared-image-gallery-with-azure-powershell"></a>更新:Azure PowerShell を使用して共有イメージ ギャラリーを作成する 

[共有イメージ ギャラリー](shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、デプロイ タスク (アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成など) のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、組織内、リージョン内またはリージョン間、AAD テナント内で、他のユーザーとご自身のカスタム VM イメージを共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 複数のギャラリーを作成することで、共有イメージを論理的にグループ化できます。 

ギャラリーは最上位リソースで、完全なロールベースのアクセス制御 (RBAC) が可能です。 イメージのバージョン管理もできるため、Azure リージョンの別のセットに各イメージのバージョンをレプリケートできます。 ギャラリーは、マネージド イメージでのみ機能します。

共有イメージ ギャラリー機能には、リソースの種類が複数あります。 それらを、この記事の中で使用または作成していきます。

| リソース | 説明|
|----------|------------|
| **マネージド イメージ** | これは、単独で使用することも、イメージ ギャラリーに**イメージ バージョン**を作成するために使用することもできる基本的なイメージです。 マネージド イメージは、一般化された VM から作成されます。 マネージド イメージは、複数の VM を作成する際に使用できる特別な種類の VHD で、共有イメージ バージョンを作成する際にも使用できるようになりました。 |
| **イメージ ギャラリー** | Azure Marketplace などの **イメージ ギャラリー**は、イメージを管理して共有するためのリポジトリです。ただし、アクセス権の所有者を制御します。 |
| **イメージ定義** | イメージはギャラリー内で定義され、内部で使用するためにイメージと要件に関する情報を伝達します。 この情報には、イメージが Windows または Linux のどちらか、リリース ノート、および最小と最大のメモリ要件が含まれます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン**は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン**を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="before-you-begin"></a>開始する前に

この記事の例を実際に行うには、既存のマネージド イメージが必要です。 お持ちでない場合は、[Azure PowerShell で Azure VM のカスタム イメージを作成する方法に関するチュートリアル](tutorial-custom-images.md)に従って作成してください。 この記事の作業に出現するリソース グループと VM の名前は適宜置き換えてください。

[!INCLUDE [virtual-machines-common-shared-images-ps](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>イメージから VM を作成する

イメージ バージョンが完成したら、新しい VM を作成することができます。 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) コマンドレットには、単純化したパラメーター セットを使用します。独自に指定する必要があるのは、イメージ バージョンのイメージ ID だけです。 

この例では、"*米国東部*" のデータセンターにある *myResourceGroup* に、*myVMfromImage* という名前の VM を作成します。

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $imageVersion.Id `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myGalleryRG
```

## <a name="next-steps"></a>次の手順

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [イメージ バージョンから VM を作成する](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

共有イメージ ギャラリーの詳細については、[概要](shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[共有イメージ ギャラリーのトラブルシューティング](troubleshooting-shared-images.md)」を参照してください。

