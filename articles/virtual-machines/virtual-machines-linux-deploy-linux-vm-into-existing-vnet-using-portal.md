---
title: "既存のネットワークに Linux VM をデプロイする - Azure Portal | Microsoft Docs"
description: "ポータルを使用して、既存の Azure Virtual Network に Linux VM をデプロイします。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 8e75aa3b38df512dc93031d5a0e9047febe7ab74


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>ポータルを使用して既存の VNet および NSG に Linux VM をデプロイする

この記事では、既存の仮想ネットワーク (VNet) に VM をデプロイする方法について説明します。  VNet や NSG (ネットワーク セキュリティ グループ) などの Azure 資産は、ほとんどデプロイされることのない有効期間の長い静的リソースにすることをお勧めします。  VNet をデプロイしたら、インフラストラクチャに悪影響を及ぼすことなく、継続的なデプロイで再利用できます。  VNet を従来のハードウェア ネットワーク スイッチと考えると、デプロイごとにまったく新しいハードウェア スイッチを構成する必要はありません。  

適切に構成された VNet があれば、新しいサーバーをその VNet に何度もデプロイできます。VNet の有効期間中に必要な変更は、あるとしてもごくわずかです。

## <a name="create-the-resource-group"></a>リソース グループの作成

まず、このチュートリアルで作成するすべてのものを整理するリソース グループをデプロイします。  Azure リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

![createResourceGroup](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>VNet の作成

まず、VM を起動する VNet を作成します。  この VNet にはサブネットが&1; つ含まれます。後の手順で、このサブネットに NSG を関連付けます。

![createVNet](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>サブネットへの VNic の追加

仮想ネットワーク カード (VNic) は、さまざまな VM に接続できるので重要です。VM は一時的なものでもかまいませんが、VNic は静的リソースとして保持します。 VNic を作成し、前の手順で作成したサブネットに関連付けます。

![createVNic](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>NSG の作成

Azure NSG はネットワーク層のファイアウォールに相当します。 Azure NSG の詳細については、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

![createNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>受信 SSH 許可ルールの追加

Linux VM はインターネットからのアクセスを必要とするため、受信ポート 22 のトラッフィクを、Linux VM のポート 22 にネットワーク経由で渡すことを許可するルールを作成します。

![createInboundSSH](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>サブネットへの NSG の関連付け

VNet とサブネットが作成されたので、サブネットに NSG を関連付けます。  NSG はサブネット全体に関連付けることも、個々の VNic に関連付けることもできます。  NSG を&1; つの VNic だけに関連付けると、1 つの VM だけが保護されるのに対し、サブネット レベルでトラフィックをフィルター処理するファイアウォールを使用すると、サブネット内のすべての VNic と VM が NSG によって保護されます。

![associateNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet と NSG への VM のデプロイ

Azure Portal を使用して、既存の Azure リソース グループ、VNet、サブネット、VNic に Linux VM をデプロイします。

![createVM](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

ポータルを使用して既存のリソースを選択することで、既存のネットワーク内に VM をデプロイするよう Azure に指示します。  繰り返しますが、VNet とサブネットをデプロイしたら、Azure リージョン内でこれらを静的または永続的なリソースにしておくことができます。  

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Jan17_HO4-->


