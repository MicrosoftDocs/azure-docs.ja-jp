---
title: "Azure Portal を使用したゾーニングされたパブリック IP アドレスの作成 | Microsoft Docs"
description: "Azure Portal を使用して、可用性ゾーン内にパブリック IP アドレスを作成します。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2fcbed2f83d66a0b4336cd1c464bb02eff3ef229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Azure Portal を使用して、可用性ゾーン内にパブリック IP アドレスを作成する

Azure 可用性ゾーン (プレビュー) 内にパブリック IP アドレスをデプロイできます。 可用性ゾーンとは、Azure リージョン内の物理的に分離されたゾーンのことです。 以下の項目について説明します。

> * 可用性ゾーン内にパブリック IP アドレスを作成する
> * 可用性ゾーン内に作成された関連リソースを識別する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> 可用性ゾーンはプレビュー段階にあり、開発とテストのシナリオのために準備されています。 選択された Azure リソース、リージョン、および VM サイズ ファミリに対するサポートが使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。

## <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal (https://portal.azure.com) にログインします。 

## <a name="create-a-zonal-public-ip-address"></a>ゾーンのパブリック IP アドレスを作成する

1. Azure Portal の左上にある **[新規]** ボタンをクリックします。
2. **[ネットワーク]** を選択してから、**[パブリック IP アドレス]** を選択します。
3. 次の設定の値を入力または選択して、サブスクリプションを選択し、残りの設定の既定値を受け入れてから、**[作成]** をクリックします。

    |設定|値|
    |---|---|
    |SKU| **[Basic]**: 静的または動的な割り当て方法で割り当てられます。 ネットワーク インターフェイス、VPN Gateway、Application Gateway、インターネットに接続するロード バランサーなどの、パブリック IP アドレスを割り当てることができる任意の Azure リソースに割り当てることができます。 このパブリック IP アドレスは、**[Availability zone] (可用性ゾーン)** 設定で特定のゾーンに割り当てることができます。 ゾーン冗長ではありません。 可用性ゾーンの詳細については、「[可用性ゾーンの概要](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 **[Standard]**: 静的な割り当て方法でのみ割り当てられます。 ネットワーク インターフェイス、またはインターネットに接続する標準のロード バランサーに割り当てることができます。 このパブリック IP アドレスをインターネットに接続する標準のロード バランサーに割り当てる場合は、[Standard] を選択する必要があります。 Azure Load Balancer SKU の詳細については、「[Azure Load Balancer の Standard SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)」を参照してください。 既定では、ゾーン冗長です。 ゾーンのパブリック IP アドレスとして作成でき、特定の可用性ゾーンで保証できます。 Standard SKU は、プレビュー リリース段階にあります。 Standard SKU のパブリック IP アドレスを作成する前に、まずプレビューに登録する必要があります。 プレビューに登録するには、「[Standard SKU プレビューへの登録](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up)」を参照してください。 Standard SKU は、サポートされている場所でのみ作成できます。  サポートされている場所 (リージョン) の一覧については、[[利用可能なリージョン]](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) を参照し、追加のリージョンのサポートについて [[Azure Virtual Network updates] (Azure Virtual Network の更新)](https://azure.microsoft.com/updates/?product=virtual-network) ページを監視してください。|   
    |名前|選択したリソース グループ内で一意となる名前を使用してください。|
    |リソース グループ|[新規作成] をクリックし、「myResourceGroup」と入力します。|
    |場所|西ヨーロッパ|
    |可用性ゾーン|**Standard** SKU を選択した場合、その IP アドレスのゾーンにまたがる復元性を高くしようとするときは *[ゾーン冗長]* を選択できます。 **Basic** SKU を選択した場合、その IP アドレスのゾーンにまたがる復元性は高くありません。 選択した SKU には関係なく、このアドレスを特定のゾーンに割り当てることができます (選択した場合)。 |

    次の図に示すように、設定がポータルに表示されます。

    ![ゾーンのパブリック IP アドレスを作成する](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Standard SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てる場合は、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)で目的のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。

## <a name="next-steps"></a>次のステップ

- [可用性ゾーン](https://docs.microsoft.com/azure/availability-zones/az-overview)の詳細を学習する
- [パブリック IP アドレス](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細を学習する