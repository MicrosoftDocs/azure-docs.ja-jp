---
title: "Azure で Linux VM にデータを挿入する | Microsoft Docs"
description: "このトピックでは、Azure の仮想マシンのインスタンスを作成する際に、仮想マシンにカスタム データを挿入する方法のほか、Windows と Linux の場合について、カスタム データの場所を探して利用する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: d376093c-e01d-4ee3-a826-2b5a35caaa7e
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5b67033f98777ae5a43887f3428ce5f185e7712b
ms.lasthandoff: 03/27/2017


---
# <a name="injecting-custom-data-into-an-azure-virtual-machine"></a>Azure の仮想マシンにカスタム データを挿入する
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用したカスタム スクリプト拡張機能の使用方法については、[こちら](../../virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

[!INCLUDE [virtual-machines-common-classic-inject-custom-data](../../../../includes/virtual-machines-common-classic-inject-custom-data.md)]


