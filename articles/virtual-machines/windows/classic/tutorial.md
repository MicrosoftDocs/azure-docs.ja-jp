---
title: "VM を Azure Portal で作成する | Microsoft Docs"
description: "Azure ポータルで Windows 仮想マシンを作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 6259c2d163cc2036964d119eb0b54ba108c5cd0a
ms.lasthandoff: 03/27/2017


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Windows を実行する仮想マシンを Azure ポータルで作成する
> [!div class="op_single_selector"]
> * [Azure クラシック ポータル](tutorial.md)
> * [PowerShell: クラシック デプロイ](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 **Azure Portal** を使って [Resource Manager デプロイメント モデルを使用してこれらの手順を実行する](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について理解してください。

このチュートリアルでは、Windows を実行する Azure 仮想マシン (VM) を Azure Portal で作成する方法について説明します。 例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。 イメージの選択肢は、サブスクリプションによって異なります。 たとえば、Windows デスクトップ イメージは MSDN サブスクライバーが使用できます。

このセクションでは、Azure Portal の **ダッシュボード** を使用して仮想マシンを選択して作成する方法について説明します。

また、 [独自のイメージ](createupload-vhd.md)を使用して VM を作成することもできます。 この方法や他の方法の詳細については、「 [Windows 仮想マシンを作成するさまざまな方法](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

<!-- 02/27/2017 Video removed as it was based on the classic portal. -->

## <a id="createvirtualmachine"> </a>仮想マシンの作成
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>次のステップ
* Azure Portal で [Resource Manager デプロイメント モデルを使用して VM を作成する](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法を理解します。
* 仮想マシンにログオンします。 手順については、 [Windows Server が実行されている仮想マシンへのログオン](connect-logon.md)に関する記事をご覧ください。
* データを格納するディスクを接続します。 空のディスクと、データが含まれているディスクのどちらも接続できます。 手順については、「 [クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする](attach-disk.md)」をご覧ください。

