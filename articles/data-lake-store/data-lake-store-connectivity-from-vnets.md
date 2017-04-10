---
title: "VNET から Azure Data Lake Store への接続 | Microsoft Docs"
description: "Azure VNET から Azure Data Lake Store への接続"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9bd7a76ef644df3db72ce95a3ef89a04bec1381f


---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Azure VNET 内の VM から Azure Data Lake Store へのアクセス
Azure Data Lake Store は、パブリック インターネット IP アドレスで動作する PaaS サービスです。 パブリック インターネットに接続できるサーバーは、通常、Azure Data Lake Store エンドポイントにも接続できます。 既定では、Azure VNET 内のすべての VM はインターネットにアクセスできるため、Azure Data Lake Store にアクセスできます。 ただし、VNET 内の VM をインターネットにアクセスできないように構成することもできます。 そのような VM では、Azure Data Lake Store へのアクセスも制限されます。 Azure VNET 内の VM のパブリック インターネット アクセスをブロックするには、次のいずれかの方法を使用します。

* ネットワーク セキュリティ グループ (NSG) を構成する
* ユーザー定義ルート (UDR) を構成する
* インターネットへのアクセスをブロックする ExpressRoute の使用時に BGP (業界標準の動的ルーティング プロトコル) 経由でルートを交換する

この記事では、上に示した 3 つの方法のいずれかを使用してリソースへのアクセスが制限されている Azure VM から Azure Data Lake Store へのアクセスを有効にする方法について説明します。

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>接続が制限されている VM から Azure Data Lake Store への接続を有効にする
このような VM から Azure Data Lake Store にアクセスするには、Azure Data Lake Store アカウントが使用可能である IP アドレスにアクセスするように VM を構成する必要があります。 Data Lake Store アカウントの IP アドレスは、アカウントの DNS 名 (`<account>.azuredatalakestore.net`) を解決することによって調べることができます。 そのためには、**nslookup** などのツールを使用できます。 コンピューターでコマンド プロンプトを開き、次のコマンドを実行します。

    nslookup mydatastore.azuredatalakestore.net

出力結果は、以下のようになります。 **Address** プロパティの値が、Data Lake Store アカウントに関連付けられている IP アドレスを示します。

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>NSG を使用して制限されている VM からの接続を有効にする
NSG ルールを使用してインターネットへのアクセスがブロックされている場合は、Data Lake Store の IP アドレスへのアクセスを許可する別の NSG を作成できます。 NSG ルールの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)に関するページを参照してください。 NSG を作成する方法については、「[How to manage NSGs using the Azure portal (Azure Portal を使用して NSG を管理する方法)](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)」を参照してください。

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>UDR または ExpressRoute を使用して制限されている VM からの接続を有効にする
ルート (UDR または BGP 交換ルート) を使用してインターネットへのアクセスがブロックされている場合は、そのようなサブネット内の VM が Data Lake Store エンドポイントにアクセスできるように、特別なルートを構成する必要があります。 詳細については、[ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md)に関するページを参照してください。 UDR の作成方法の詳細については、[Resource Manager での UDR の作成](../virtual-network/virtual-network-create-udr-arm-ps.md)に関するページを参照してください。

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>ExpressRoute を使用して制限されている VM からの接続を有効にする
ExpressRoute 回線を構成すると、オンプレミスのサーバーはパブリック ピアリングを介して Data Lake Store にアクセスできます。 パブリック ピアリングのための ExpressRoute の構成の詳細については、「[ExpressRoute FAQs (ExpressRoute に関する FAQ)](../expressroute/expressroute-faqs.md)」を参照してください。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Azure Data Lake Store に格納されているデータのセキュリティ保護](data-lake-store-security-overview.md)




<!--HONumber=Nov16_HO3-->


