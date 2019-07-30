---
title: Azure 仮想マシンの PowerShell のサンプル | Microsoft Docs
description: Azure 仮想マシンの PowerShell のサンプル
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 2d954bc068693a34ef1d69e4296e972979d4f61b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671026"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 仮想マシンの PowerShell のサンプル

次の表には、Linux 仮想マシンを作成および管理する PowerShell サンプル スクリプトへのリンクが掲載されています。

| | |
|---|---|
|**仮想マシンの作成**||
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [Docker が有効になっている VM の作成](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Docker ホストとしてこの VM を構成し、NGINX コンテナーを実行します。 |
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して NGINX をインストールします。 |
| [WordPress がインストールされている VM の作成](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して WordPress をインストールします。 |
| [管理 OS ディスクから VM を作成する](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 既存の管理ディスクを OS ディスクとして接続することで仮想マシンを作成します。 |
| [スナップショットから VM を作成する](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 |
|**ストレージの管理**||
| [同じまたは別のサブスクリプションの VHD からマネージド ディスクを作成する](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 同じまたは別のサブスクリプションで、OS ディスクとして特殊化した VHD から、またはデータ ディスクとしてデータ VHD から、マネージド ディスクを作成します。  |
| [スナップショットからマネージド ディスクを作成する](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | スナップショットからマネージド ディスクを作成します。 |
| [ストレージ アカウントに VHD としてスナップショットをエクスポートする](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 管理スナップショットを VHD として別のリージョンのストレージ アカウントにエクスポートします。 |
| [ストレージ アカウントにマネージド ディスクの VHD をエクスポートする](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 別のリージョンのストレージ アカウントに、マネージド ディスクの基盤となる VHD をエクスポートします。 |
| [VHD からスナップショットを作成する](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | VHD からスナップショットを作成した後、そのスナップショットを使用して複数の同じマネージド ディスクを短時間で作成します。  |
| [同じサブスクリプションまたは別のサブスクリプションにスナップショットをコピーする](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 同じサブスクリプションまたは親スナップショットと同じリージョン内の別のサブスクリプションに、スナップショットをコピーします。 |
|**仮想マシンの監視**||
| [Azure Monitor ログを使用して VM を監視する](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Log Analytics エージェントをインストールして、VM を Log Analytics ワークスペースに登録します。  |
| [同じサブスクリプションまたは別のサブスクリプションにマネージド ディスクをコピーする](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 同じサブスクリプションまたは親マネージド ディスクと同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。
| [PowerShell を使用してサブスクリプション内のすべての VM に関する詳細情報を収集する](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 指定されたサブスクリプション内の VM の VM 名、リソース グループ名、リージョン、仮想ネットワーク、サブネット、プライベート IP アドレス、OS の種類、およびパブリック IP アドレスを含む csv を作成します。
| | |
