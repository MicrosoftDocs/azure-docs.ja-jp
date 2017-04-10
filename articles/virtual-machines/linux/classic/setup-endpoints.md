---
title: "クラシック Linux VM でのエンドポイントのセットアップ | Microsoft Docs"
description: "Azure 上で Linux 仮想マシンとの通信を許可するように Azure クラシック ポータルで Linux VM のエンドポイントをセットアップする方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 53f18d08b2356863496d651b1a7c138e1afad38e
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Azure 上で Linux のクラシック仮想マシンにエンドポイントをセットアップする方法
クラシック デプロイメント モデルを使用して Azure で作成したすべての Linux 仮想マシンが、プライベート ネットワーク チャネルを介して、同じクラウド サービスまたは仮想ネットワーク内の他の仮想マシンと自動的に通信できます。 ただし、インターネットまたは他の仮想ネットワークにあるコンピューターと通信するには、仮想マシンへの着信ネットワーク トラフィックを転送するエンドポイントが必要になります。 この記事は、 [Windows 仮想マシン](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)にも利用できます。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

**Resource Manager** デプロイ モデルでは、エンドポイントは**ネットワーク セキュリティ グループ (NSG)** を使用して構成します。 詳細については、「[ポートおよびエンドポイントの開放](../../virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

Azure クラシック ポータルで Linux 仮想マシンを作成すると、通常は Secure Shell (SSH) 用のエンドポイントが自動的に作成されます。 必要に応じて、仮想マシンの作成中や作成後に、追加のエンドポイントを構成できます。

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>次のステップ
* VM エンドポイントは、 [Azure コマンド ライン インターフェイス](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)を使用して作成することもできます。 **azure vm endpoint create** コマンドを実行します。
* Resource Manager デプロイメント モデルで仮想マシンを作成した場合は、Resource Manager モードで Azure CLI を使用して、VM へのトラフィックを制御する [ネットワーク セキュリティ グループを作成](../../../virtual-network/virtual-networks-create-nsg-arm-cli.md) できます。


