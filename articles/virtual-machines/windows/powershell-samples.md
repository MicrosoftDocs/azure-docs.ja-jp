---
title: "Azure 仮想マシンの PowerShell のサンプル | Microsoft Docs"
description: "Azure 仮想マシンの PowerShell のサンプル"
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
ms.date: 05/05/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ff8b741b04ff37fa1a5fbaddabdd588ab43f19c5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 仮想マシンの PowerShell のサンプル

次の表には、Windows 仮想マシンを作成および管理する PowerShell サンプル スクリプトへのリンクが掲載されています。

| | |
|---|---|
|**仮想マシンの作成**||
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [可用性が高い仮想マシンの作成](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。|
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して IIS をインストールします。 |
| [VM の作成と DSC 構成の実行](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 仮想マシンを作成し、Azure Desired State Configuration (DSC) の拡張機能を使用して IIS をインストールします。 |
| [VHD をアップロードして VM を作成する](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | ローカル VHD ファイルを Azure にアップロードします。その VHD からイメージを作成し、そのイメージから VM を作成します。 |
| [管理 OS ディスクから VM を作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 既存の管理ディスクを OS ディスクとして接続することで仮想マシンを作成します。 |
| [スナップショットから VM を作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | スナップショットから管理ディスクを作成し、その新しい管理ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 |
|**ストレージの管理**||
| [同じまたは別のサブスクリプションの VHD から管理ディスクを作成する](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 同じまたは別のサブスクリプションで、OS ディスクとして特殊化した VHD から、またはデータ ディスクとしてデータ VHD から、管理ディスクを作成します。  |
| [スナップショットから管理ディスクを作成する](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | スナップショットから管理ディスクを作成します。 |
| [同じまたは別のサブスクリプションに管理ディスクをコピーする](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 同じサブスクリプションまたは親管理ディスクと同じリージョン内の別のサブスクリプションに、管理ディスクをコピーします。 
| [ストレージ アカウントに VHD としてスナップショットをエクスポートする](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 別のリージョンのストレージ アカウントに、VHD として管理スナップショットをエクスポートします。 |
| [VHD からスナップショットを作成する](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | VHD からスナップショットを作成して、スナップショットから複数の同じ管理ディスクを短時間で作成します。  |
| [同じまたは別のサブスクリプションにスナップショットをコピーする](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 同じサブスクリプションまたは親スナップショットと同じリージョン内の別のサブスクリプションに、スナップショットをコピーします。 |
|**仮想マシンのセキュリティ保護**||
| [VM とデータ ディスクを暗号化する](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Azure Key Vault、暗号化キー、およびサービス プリンシパルを作成し、VM を暗号化します。 |
|**仮想マシンの監視**||
| [Operations Management Suite による VM の監視](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 仮想マシンを作成し、Operations Management Suite エージェントをインストールし、OMS ワークスペースに VM を登録します。  |
| | |


