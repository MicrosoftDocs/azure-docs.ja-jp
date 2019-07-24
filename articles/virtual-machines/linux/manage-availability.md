---
title: Azure での Linux VM の可用性管理 | Microsoft Docs
description: 複数の仮想マシンを使って Azure の Linux アプリケーションの高い可用性を確保する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecddbb54137c018c1acc202e4056672eb626f87d
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888942"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Linux 仮想マシンの可用性管理

複数の仮想マシンを設定および管理して、Azure の Linux アプリケーションの高い可用性を確保する方法について説明します。 [Windows 仮想マシンの可用性を管理する](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。

Resource Manager デプロイ モデルで CLI を使用して可用性セットを作成する方法の詳細については、「[Azure 可用性セット: 可用性セットを管理するコマンド](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)」を参照してください。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>次の手順
仮想マシンの負荷分散の詳細については、 [仮想マシンの負荷分散](../virtual-machines-linux-load-balance.md)に関するページをご覧ください。

