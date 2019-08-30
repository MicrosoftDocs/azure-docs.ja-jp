---
title: Azure PowerShell のサンプル スクリプト - IIS | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - IIS
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a72584cfe2b113fdf9eef9323dae6b0096e3f192
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090606"
---
# <a name="create-an-iis-vm-with-powershell"></a>PowerShell を使用して IIS VM を作成する

このスクリプトでは、Windows Server 2016 を実行する Azure 仮想マシンを作成してから、Azure 仮想マシンのカスタム スクリプト拡張機能を使用して IIS をインストールします。 このスクリプトを実行すると、仮想マシンのパブリック IP アドレス上にあるの既定 IIS Web サイトにアクセスできるようになります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 また、このコマンドでは、ポート 80 を開いたり、管理者の資格情報を設定したりします。 |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | 仮想マシンに VM 拡張機能を追加します。 このサンプルでは、カスタム スクリプト拡張機能を使用して IIS をインストールします。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
