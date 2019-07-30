---
title: Azure 仮想マシンの PowerShell のサンプル | Microsoft Docs
description: Azure 仮想マシンの PowerShell のサンプル
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9aea63b8366bf974fd89c32105bea707ad72c8a5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719984"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 仮想マシンの PowerShell のサンプル

次の表では、Windows 仮想マシン (VM) を作成および管理する PowerShell サンプル スクリプトへのリンクを示します。

| | |
|---|---|
|**仮想マシンの作成**||
| [仮想マシンの短時間での作成](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを、最小限のプロンプトで作成します。|
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [可用性が高い仮想マシンの作成](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。|
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して IIS をインストールします。 |
| [VM の作成と DSC 構成の実行](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Azure Desired State Configuration (DSC) の拡張機能を使用して IIS をインストールします。 |
| [VHD をアップロードして VM を作成する](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | ローカル VHD ファイルを Azure にアップロードします。その VHD からイメージを作成し、そのイメージから VM を作成します。 |
| [管理 OS ディスクから VM を作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 既存の管理ディスクを OS ディスクとして接続することで仮想マシンを作成します。 |
| [スナップショットから VM を作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 |
|**ストレージの管理**||
| [同じまたは別のサブスクリプションの VHD からマネージド ディスクを作成する](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 同じまたは別のサブスクリプションで、OS ディスクとして特殊化した VHD から、またはデータ ディスクとしてデータ VHD から、マネージド ディスクを作成します。  |
| [スナップショットからマネージド ディスクを作成する](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | スナップショットからマネージド ディスクを作成します。 |
| [同じサブスクリプションまたは別のサブスクリプションにマネージド ディスクをコピーする](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 同じサブスクリプションまたは親マネージド ディスクと同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。
| [ストレージ アカウントに VHD としてスナップショットをエクスポートする](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 管理スナップショットを VHD として別のリージョンのストレージ アカウントにエクスポートします。 |
| [ストレージ アカウントにマネージド ディスクの VHD をエクスポートする](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 別のリージョンのストレージ アカウントに、マネージド ディスクの基盤となる VHD をエクスポートします。 |
| [VHD からスナップショットを作成する](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | VHD からスナップショットを作成した後、そのスナップショットを使用して複数の同じマネージド ディスクを短時間で作成します。  |
| [同じサブスクリプションまたは別のサブスクリプションにスナップショットをコピーする](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 同じサブスクリプションまたは親スナップショットと同じリージョン内の別のサブスクリプションに、スナップショットをコピーします。 |
|**セキュリティ保護された仮想マシン**||
| [VM とそのデータ ディスクを暗号化する](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Azure キー コンテナー、暗号化キー、およびサービス プリンシパルを作成し、VM を暗号化します。 |
|**仮想マシンの監視**||
| [Azure Monitor を使用して VM を監視する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Azure Log Analytics エージェントをインストールして、VM を Log Analytics ワークスペースに登録します。  |
| [PowerShell を使用してサブスクリプション内のすべての VM に関する詳細情報を収集する](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 指定されたサブスクリプション内の VM の VM 名、リソース グループ名、リージョン、仮想ネットワーク、サブネット、プライベート IP アドレス、OS の種類、およびパブリック IP アドレスを含む csv を作成します。
| | |
