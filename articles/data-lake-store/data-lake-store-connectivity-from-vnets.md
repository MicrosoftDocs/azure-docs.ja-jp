---
title: VNET から Azure Data Lake Storage Gen1 への接続 | Microsoft Docs
description: リソースへのアクセスが制限されている Azure 仮想マシンから Azure Data Lake Storage Gen1 へのアクセスを有効にする方法について説明します。
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: e319cf9dfc01546607e20572c5bf4930fd974c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104038"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Azure VNET 内の VM から Azure Data Lake Storage Gen1 へのアクセス
Azure Data Lake Storage Gen1 は、パブリック インターネット IP アドレスで動作する PaaS サービスです。 パブリック インターネットに接続できるサーバーは、通常、Azure Data Lake Storage Gen1 エンドポイントにも接続できます。 既定では、Azure VNET 内のすべての VM はインターネットにアクセスできるため、Azure Data Lake Storage Gen1 にアクセスできます。 ただし、VNET 内の VM をインターネットにアクセスできないように構成することもできます。 そのような VM では、Azure Data Lake Storage Gen1 へのアクセスも制限されます。 Azure VNET 内の VM のパブリック インターネット アクセスをブロックするには、次のいずれかの方法を使用します。

* ネットワーク セキュリティ グループ (NSG) を構成する
* ユーザー定義ルート (UDR) を構成する
* インターネットへのアクセスをブロックする ExpressRoute の使用時に BGP (業界標準の動的ルーティング プロトコル) 経由でルートを交換する

この記事では、上に示した 3 つの方法のいずれかを使用してリソースへのアクセスが制限されている Azure VM から Azure Data Lake Storage Gen1 へのアクセスを有効にする方法について説明します。

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>接続が制限されている VM から Azure Data Lake Storage Gen1 への接続を有効にする
このような VM から Azure Data Lake Storage Gen1 にアクセスするには、Azure Data Lake Storage Gen1 アカウントが使用可能なリージョンの IP アドレスにアクセスするように、VM を構成する必要があります。 Data Lake Storage Gen1 アカウントのリージョンの IP アドレスは、アカウントの DNS 名 (`<account>.azuredatalakestore.net`) を解決することによって確認できます。 アカウントの DNS 名を解決するには、**nslookup** などのツールを使用できます。 コンピューターでコマンド プロンプトを開き、次のコマンドを実行します。

```console
nslookup mydatastore.azuredatalakestore.net
```

出力結果は、以下のようになります。 **Address** プロパティの値が、Data Lake Storage Gen1 アカウントに関連付けられている IP アドレスを示します。

```output
Non-authoritative answer:
Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
Address:  104.44.88.112
Aliases:  mydatastore.azuredatalakestore.net
```


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>NSG を使用して制限されている VM からの接続を有効にする
NSG ルールを使用してインターネットへのアクセスがブロックされている場合は、Data Lake Storage Gen1 の IP アドレスへのアクセスを許可する別の NSG を作成できます。 NSG ルールの詳細については、[ネットワーク セキュリティ グループの概要](../virtual-network/network-security-groups-overview.md)に関するページを参照してください。 NSG を作成する方法については、[ネットワーク セキュリティ グループを作成する方法](../virtual-network/tutorial-filter-network-traffic.md)に関するページを参照してください。

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>UDR または ExpressRoute を使用して制限されている VM からの接続を有効にする
ルート (UDR または BGP 交換ルート) を使用してインターネットへのアクセスがブロックされている場合は、そのようなサブネット内の VM が Data Lake Storage Gen1 エンドポイントにアクセスできるように、特別なルートを構成する必要があります。 詳細については、[ユーザー定義のルートの概要](../virtual-network/virtual-networks-udr-overview.md)に関するページを参照してください。 UDR の作成方法の詳細については、[Resource Manager での UDR の作成](../virtual-network/tutorial-create-route-table-powershell.md)に関するページを参照してください。

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>ExpressRoute を使用して制限されている VM からの接続を有効にする
ExpressRoute 回線を構成すると、オンプレミスのサーバーはパブリック ピアリングを介して Data Lake Storage Gen1 にアクセスできます。 パブリック ピアリングのための ExpressRoute の構成の詳細については、「[ExpressRoute FAQs (ExpressRoute に関する FAQ)](../expressroute/expressroute-faqs.md)」を参照してください。

## <a name="see-also"></a>参照
* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
* [Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護](data-lake-store-security-overview.md)