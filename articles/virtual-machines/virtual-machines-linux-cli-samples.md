---
title: "Azure CLI のサンプル | Microsoft Docs"
description: "Azure CLI のサンプル"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 59dfe13e70a5a20da4965f8a698380dc78b21028
ms.lasthandoff: 03/21/2017


---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Linux 仮想マシン用の Azure CLI サンプル

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|---|---|
|**仮想マシンの作成**||
| [仮想マシンの作成](./scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 最小の構成で Linux 仮想マシンを作成します。 |
| [完全に構成された仮想マシンの作成](./scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [可用性が高い仮想マシンの作成](./scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。 |
| [Docker が有効になっている VM の作成](./scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Docker ホストとしてこの VM を構成し、NGINX コンテナーを実行します。 |
| [VM の作成と構成スクリプトの実行](./scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して NGINX をインストールします。 |
| [WordPress がインストールされている VM の作成](./scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して WordPress をインストールします。 |
|**ネットワークの仮想マシン**||
| [仮想マシン間のネットワーク トラフィックのセキュリティ保護](./scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 2 つの仮想マシン、すべての関連リソース、および内部と外部のネットワーク セキュリティ グループ (NSG) を作成します。 |
|**仮想マシンの監視**||
| [Operations Management Suite による VM の監視](./scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | 仮想マシンを作成し、Operations Management Suite エージェントをインストールし、OMS ワークスペースに VM を登録します。  |
|**仮想マシンのトラブルシューティング**||
| [VM オペレーティング システム ディスクのトラブルシューティング](./scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 1 つ目の VM からオペレーティング システム ディスクを&2; つ目の VM のデータ ディスクとしてマウントします。 |
| | |

