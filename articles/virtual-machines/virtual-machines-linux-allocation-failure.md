---
title: "Linux VM の割り当てエラーのトラブルシューティング | Microsoft Docs"
description: "Azure で Linux VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d4520768a13de519fcc3f8778d2b87caa667b654


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Azure で Linux VM を作成、再起動、またはサイズ変更するときの割り当てエラーのトラブルシューティング
VM を作成するとき、停止した (割り当てを解除した) VM を再起動するとき、または VM のサイズを変更するとき、Microsoft Azure はコンピューティング リソースをサブスクリプションに割り当てます。 これらの操作をしているときに、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。 この記事では、いくつかの一般的な割り当てエラーの原因を説明し、可能な改善方法を提案します。 この情報は、サービスのデプロイを計画する場合にも役立ちます。 [Azure で Windows VM を作成、再起動、またはサイズ変更するときの割り当てエラーをトラブルシューティング](virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することもできます。

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]




<!--HONumber=Nov16_HO3-->


