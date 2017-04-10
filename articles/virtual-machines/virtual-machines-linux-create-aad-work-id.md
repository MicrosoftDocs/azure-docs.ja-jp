---
title: "Linux の AAD で職場または学校の ID を作成する | Microsoft Docs"
description: "Linux 仮想マシンで使用する Azure Active Directory の職場または学校の ID を作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: b0f86d77-c669-4aa1-a095-c2aa4d9105fe
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 1d35114800ba024d0c201840722df2c6a74d1d61
ms.openlocfilehash: 0eaa83fc19316708685eed0ca994112bc09153e5


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Linux VM と共に使用する Azure Active Directory での職場または学校の ID を作成する
MSDN Azure クレジットを活用するために、個人の Azure アカウントを作成した場合や、個人の MSDN サブスクリプションがあり Azure アカウントを作成した場合、*Microsoft アカウント* ID を使って作成しています。 Azure の優れた機能の中でも[リソース グループ テンプレート](../azure-resource-manager/resource-group-overview.md)はその一例ですが、作業にあたって職場または学校のアカウント (Azure Active Directory で管理される ID) が必要です。 下の指示に従って、新しい職場または学校のアカウントを作成できます。個人の Azure アカウントを持つことで得られるメリットの&1; つは、既定の Azure Active Directory ドメインが付属することです。それを使用して職場または学校のアカウントを新規に作成し、アカウントが必要な Azure 機能で利用できます。

ただし、最近の変更によって、[ここ](../xplat-cli-connect.md)で説明されている `azure login` 対話型ログイン方法を使用するすべての種類の Azure アカウントでサブスクリプションを管理できるようになっています。 そのメカニズムを使用することも、次の指示に従うこともできます。 [Windows VM と共に使用する Azure Active Directory での職場または学校の ID を作成する](virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]




<!--HONumber=Jan17_HO4-->


