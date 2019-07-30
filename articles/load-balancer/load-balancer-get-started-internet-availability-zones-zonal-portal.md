---
title: ゾーンのフロントエンドを使用してロード バランサーを作成する - Azure portal
titlesuffix: Azure Load Balancer
description: Azure portal を使用してゾーン フロントエンドで Standard Load Balancer を作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 9a260c267febb3dc851fe0a8ca8a8e351124116b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275150"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Azure portal を使用してゾーン フロントエンドで Standard Load Balancer を作成する

この記事では、ゾーン フロントエンド IP 構成でパブリック [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。 可用性ゾーンと Standard Load Balancer がどのように機能するかを理解するには、「[Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)」を参照してください。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> 可用性ゾーンのサポートは、Azure リソース、リージョン、および VM サイズ ファミリを選択するために使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (https://portal.azure.com ) にログインします。

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>ゾーンのフロントエンド IP アドレスを使用してロード バランサーを作成する

1. ブラウザーから Azure Portal ([https://portal.azure.com](https://portal.azure.com)) に移動し、Azure アカウントでログインします。
2. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Load Balancer]** を選択します。
3. **[ロード バランサーの作成]** ページで、 **[名前]** に「**myLoadBalancer**」と入力します。
4. **[種類]** から **[パブリック]** を選択します。
5. [SKU] から **[Standard]** を選択します。
6. **[パブリック IP アドレス]** 、 **[新規作成]** の順にクリックします。 **[パブリック IP アドレスの作成]** ページの [名前] に「**myPublicIPZonal**」と入力します。SKU の場合は **[Standard]** を選択し、可用性ゾーンの場合は **[1]** を選択します。
    
>[!NOTE] 
> この手順で作成されるパブリック IP は、既定で Standard SKU です。

1. **[リソース グループ]** の場合は、 **[新規作成]** をクリックし、リソース グループの名前として「**myResourceGroupZLB**」と入力します。
1. **[場所]** には **[西ヨーロッパ]** を選択し、 **[OK]** をクリックします。 ロード バランサーのデプロイが開始されます。デプロイが正常に完了するまでに数分かかります。

    ![Azure portal を使用してゾーン冗長 Standard Load Balancer を作成する](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>次の手順
- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)を確認します。



