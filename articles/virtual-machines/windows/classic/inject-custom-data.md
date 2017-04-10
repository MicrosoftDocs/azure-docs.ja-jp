---
title: "Azure で Windows VM にデータを挿入する | Microsoft Docs"
description: "このトピックでは、Azure の仮想マシンのインスタンスを作成する際に、仮想マシンにカスタム データを挿入する方法のほか、Windows と Linux の場合について、カスタム データの場所を探して利用する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 48759f76-eaa0-4202-ada0-706d3f9a9467
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d6782c73c5c44ca4863427f490ed50b47ae142e2
ms.lasthandoff: 03/25/2017


---
# <a name="injecting-custom-data-into-an-azure-virtual-machine"></a>Azure の仮想マシンにカスタム データを挿入する
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用したカスタム スクリプト拡張機能の使用方法については、[こちら](../../virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

[!INCLUDE [virtual-machines-common-classic-inject-custom-data](../../../../includes/virtual-machines-common-classic-inject-custom-data.md)]


