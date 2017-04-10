---
title: "クラシック Windows VM でのエンドポイントのセットアップ | Microsoft Docs"
description: "Azure 上で Windows 仮想マシンとの通信を許可するように Azure クラシック ポータルで Windows VM のエンドポイントをセットアップする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: dcafe045203d77b6169f2e7a5d7a0c42de37f685
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Azure 上でクラシック Windows 仮想マシンにエンドポイントをセットアップする方法
クラシック デプロイメント モデルを使用して Azure で作成したすべての Windows 仮想マシンが、プライベート ネットワーク チャネルを介して、同じクラウド サービスまたは仮想ネットワーク内の他の仮想マシンと自動的に通信できます。 ただし、インターネットまたは他の仮想ネットワークにあるコンピューターと通信するには、仮想マシンへの着信ネットワーク トラフィックを転送するエンドポイントが必要になります。 この記事は、 [Linux 仮想マシン](../../linux/classic/setup-endpoints.md)にも利用できます。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

**Resource Manager** デプロイ モデルでは、エンドポイントは**ネットワーク セキュリティ グループ (NSG)** を使用して構成します。 詳しくは、[Azure Portal を使用して VM への外部アクセスを許可する方法](../../virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

Azure クラシック ポータルで Windows 仮想マシンを作成すると、共通エンドポイント (リモート デスクトップや Windows PowerShell リモート処理用のエンドポイントなど) が通常は自動的に作成されます。 必要に応じて、仮想マシンの作成中や作成後に、追加のエンドポイントを構成できます。

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>次のステップ
* Azure PowerShell コマンドレットを使用して VM エンドポイントをセットアップするには、「 [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)」をご覧ください。
* Azure PowerShell コマンドレットを使用してエンドポイントの ACL を管理するには、「 [PowerShell を使用したエンドポイントのアクセス制御リスト (ACL) の管理](../../../virtual-network/virtual-networks-acl-powershell.md)」をご覧ください。
* Resource Manager デプロイメント モデルで仮想マシンを作成した場合は、Azure PowerShell を使用して、VM へのトラフィックを制御する [ネットワーク セキュリティ グループを作成](../../../virtual-network/virtual-networks-create-nsg-arm-ps.md) できます。


