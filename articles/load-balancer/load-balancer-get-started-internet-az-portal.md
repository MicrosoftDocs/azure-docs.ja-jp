---
title: ゾーン冗長フロントエンドを使用して Load Balancer を作成する - Azure portal
titlesuffix: Azure Load Balancer
description: Azure portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Standard ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: a875fc3446944b8a39f43b29440ab1d7179b847d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275075"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Azure portal を使用してゾーン冗長フロントエンドで Standard ロード バランサーを作成する

この記事では、パブリック IP Standard アドレスでゾーン冗長フロントエンドを使用してパブリック [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。 Standard Load Balancer 上の単一のフロントエンド IP アドレスは、既定でゾーン冗長になります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
>  可用性ゾーンのサポートは、Azure リソース、リージョン、および VM サイズ ファミリを選択するために使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (https://portal.azure.com ) にログインします。

## <a name="create-a-zone-redundant-load-balancer"></a>ゾーン冗長ロード バランサーの作成

1. ブラウザーから Azure Portal ([https://portal.azure.com](https://portal.azure.com)) に移動し、Azure アカウントでログインします。
2. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Load Balancer]** を選択します。
3. **[ロード バランサーの作成]** ページで、 **[名前]** に「**myLoadBalancer**」と入力します。
4. **[種類]** から **[パブリック]** を選択します。
5. [SKU] から **[Standard]** を選択します。
6. **[パブリック IP アドレス]** をクリックし、 **[新規作成]** を作成し、 **[パブリック IP アドレスの作成]** ページの [名前] に「**myPublicIPStandard**」と入力します。
    >[!NOTE] 
    > この手順で作成されるパブリック IP は既定で Standard SKU であり、ゾーン冗長です。 
8. **[場所]** から **[米国東部 2]** を選択し、 **[OK]** をクリックします。 ロード バランサーのデプロイが開始されます。デプロイが正常に完了するまでに数分かかります。

## <a name="next-steps"></a>次の手順
- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)を確認します。



