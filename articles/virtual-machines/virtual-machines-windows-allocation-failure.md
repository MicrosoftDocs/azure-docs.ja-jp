---
title: "Windows VM の割り当てエラーのトラブルシューティング |Microsoft Docs"
description: "Azure で Windows VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7c83071775cd4c237dfcdb77dc89a6d5c4df4aac


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Azure で Windows VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング
VM を作成するとき、停止した (割り当て解除した) VM を再起動するとき、または VM のサイズを変更するとき、Microsoft Azure はコンピューティング リソースをサブスクリプションに割り当てます。 これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。 この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。 この情報は、サービスのデプロイを計画する場合にも役立ちます。 [Azure で Linux VM を作成、再起動、またはサイズ変更するときの割り当てエラーをトラブルシューティング](virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)することもできます。

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]




<!--HONumber=Nov16_HO3-->


