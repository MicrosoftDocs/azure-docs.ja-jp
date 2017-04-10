---
title: "Windows の Azure CLI サンプル | Microsoft Docs"
description: "Windows の Azure CLI サンプル"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: cde57732f16d9571af8cc4ae5df3c012579ce831
ms.openlocfilehash: 29e8a19d4c05ac3c01ecbe43650c6607234c0546
ms.lasthandoff: 03/01/2017


---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Windows 仮想マシン用の Azure CLI サンプル

次の表には、Windows 仮想マシンをデプロイしている Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|---|---|
|**仮想マシンの作成**||
| [仮想マシンの作成](./scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 最小の構成で Windows 仮想マシンを作成します。 |
| [完全に構成された仮想マシンの作成](./scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [可用性が高い仮想マシンの作成](./scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。 |
| [VM の作成と構成スクリプトの実行](./scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して IIS をインストールします。 |
| [VM の作成と DSC 構成の実行](./scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Azure Desired State Configuration (DSC) の拡張機能を使用して IIS をインストールします。 |
|**ネットワークの仮想マシン**||
| [仮想マシン間のネットワーク トラフィックのセキュリティ保護](./scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 2 つの仮想マシン、すべての関連リソース、および内部と外部のネットワーク セキュリティ グループ (NSG) を作成します。 |
|**仮想マシンの監視**||
| [Operations Management Suite による VM の監視](./scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Operations Management Suite エージェントをインストールし、OMS ワークスペースに VM を登録します。  |
| | |

