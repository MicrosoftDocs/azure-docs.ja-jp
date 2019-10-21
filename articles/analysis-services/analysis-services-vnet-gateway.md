---
title: Azure Virtual Network データソースに対してオンプレミス データ ゲートウェイを使用する | Microsoft Docs
description: VNet 上のデータソースに対してゲートウェイを使用するようにサーバーを構成する方法を説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301218"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Azure Virtual Network (VNet) 上のデータソースに対してゲートウェイを使用する

この記事では、データソースが [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) 上に存在する場合に使用する **AlwaysUseGateway** サーバー プロパティについて説明します。

## <a name="server-access-to-vnet-data-sources"></a>VNet データソースへのサーバー アクセス

データソースが VNet 経由でアクセスされる場合、Azure Analysis Services サーバーは、それらがオンプレミスであり、独自の環境内に存在しているかのように接続する必要があります。 **AlwaysUseGateway** サーバー プロパティを構成して、サーバーが[オンプレミス ゲートウェイ](analysis-services-gateway.md)経由ですべてのデータ ソースにアクセスするように指定できます。 

Azure SQL Database Managed Instance データ ソースは、プライベート IP アドレスを使用して Azure VNet 内で実行されます。 インスタンスでパブリック エンドポイントが有効な場合、ゲートウェイは必要ありません。 パブリック エンドポイントが有効な場合は、オンプレミス データ ゲートウェイが必要であり、AlwaysUseGateway プロパティを true に設定する必要があります。

> [!NOTE]
> このプロパティは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)がインストールされ、構成されている場合にのみ有効です。 ゲートウェイは、VNet 上に配置できます。

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway プロパティを構成する

1. [SSMS] > サーバー > **[プロパティ]**  >  **[全般]** で、 **[Show Advanced (All) Properties]\(詳細な (すべての) プロパティの表示\)** を選択します。
2. **[ASPaaS\AlwaysUseGateway]** で、 **[true]** を選択します。

    ![AlwaysUseGateway プロパティ](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>関連項目
[オンプレミス データソースへの接続](analysis-services-gateway.md)   
[オンプレミス データ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

