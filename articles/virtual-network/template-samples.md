---
title: 仮想ネットワーク用の Azure Resource Manager テンプレート サンプル | Microsoft Docs
description: Azure Virtual Network のデプロイに使用できるさまざまな Azure Resource Manager テンプレートについて説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/13/2018
ms.author: jdial
ms.openlocfilehash: a16feffea6ac64423398b995499f0e6a60182636
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039590"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>仮想ネットワーク用の Azure Resource Manager テンプレート サンプル

次の表は、Azure Resource Manager テンプレート サンプルのリンク一覧です。 テンプレートのデプロイには、Azure [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json) のいずれかを使用できます。 独自のテンプレートを作成する方法については、[初めてのテンプレートの作成](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページおよび「[Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

テンプレートで使用する JSON 構文とプロパティについては、「[Microsoft.Network resource types (Microsoft.Network のリソースの種類)](/azure/templates/microsoft.network/allversions)」を参照してください。


| | |
|----|----|
|[2 つのサブネットから成る仮想ネットワークの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| 2 つのサブネットから成る仮想ネットワークを作成します。|
|[ネットワーク仮想アプライアンス経由のトラフィックのルーティング](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| 3 つのサブネットから成る仮想ネットワークを作成します。 それぞれのサブネットに仮想マシンをデプロイします。 3 つ目のサブネット内の仮想マシンを介して一方のサブネットからもう一方のサブネットへトラフィックを誘導するルートを含んだルート テーブルを作成します。 このルート テーブルをいずれかのサブネットに関連付けます。|
|[Azure Storage の仮想ネットワーク サービス エンドポイントの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|それぞれネットワーク インターフェイスを 1 つ含んだ 2 つのサブネットから成る新しい仮想ネットワークを作成します。 いずれか 1 つのサブネットについて、Azure Storage へのサービス エンドポイントを有効にし、そのサブネットに新しいストレージ アカウントを結び付けます。|
|[2 つの仮想ネットワークの接続](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| 2 つの仮想ネットワークを作成し、両者の間に仮想ネットワーク ピアリングを作成します。|
|[複数の IP アドレスを持つ仮想マシンの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| 複数の IP アドレスを持つ Windows VM または Linux VM の作成|
