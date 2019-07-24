---
title: パブリック ピアリングを Microsoft ピアリングに移行する - Azure ExpressRoute | Microsoft Docs
description: この記事では、ExpressRoute でパブリック ピアリングを Microsoft ピアリングに移行する手順を示します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6b2bce6b488698db0a72c9a17f67c2555c6afa5b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100023"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>パブリック ピアリングを Microsoft ピアリングに移行する

この記事は、パブリック ピアリング構成をダウンタイムなしで Microsoft ピアリングに移行するために役立ちます。 ExpressRoute は、Azure Storage や Azure SQL Database などの Azure PaaS サービスのために Microsoft ピアリングとルート フィルターの併用をサポートしています。 Microsoft PaaS および SaaS サービスへのアクセスに必要なルーティング ドメインは 1 つだけになりました。 消費する Azure リージョンの PaaS サービス プレフィックスを選択的にアドバタイズするには、ルート フィルターを使用できます。 ルーティング ドメインとピアリングの詳細については、「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。

## <a name="before"></a>開始する前に

* Microsoft ピアリングに接続するには、NAT を設定および管理する必要があります。 接続プロバイダーが、管理されたサービスとして NAT を設定および管理する可能性があります。 Microsoft ピアリングで Azure PaaS および Azure SaaS サービスにアクセスする予定がある場合は、NAT IP プールを正しくサイズ設定することが重要です。 ExpressRoute の NAT の詳細については、「[Microsoft ピアリングの NAT 要件](expressroute-nat.md#nat-requirements-for-microsoft-peering)」を参照してください。

* パブリック ピアリングを使用していて、[Azure Storage](../storage/common/storage-network-security.md) または [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) へのアクセスに使用されるパブリック IP アドレスの IP ネットワーク ルールが現在ある場合は、Azure Storage アカウントまたは Azure SQL アカウントのパブリック IP アドレスの一覧に、Microsoft ピアリングを使用して構成された NAT IP プールを含める必要があります。

* ダウンタイムなしで Microsoft ピアリングに移行するには、この記事の手順をそれらが示されている順序で実行します。

## <a name="create"></a>1.Microsoft ピアリングを作成する

Microsoft ピアリングがまだ作成されていない場合は、次のいずれかの記事を使用して Microsoft ピアリングを作成します。 接続プロバイダーが管理対象レイヤー 3 サービスを提供している場合は、使用している回線の Microsoft ピアリングを有効にするように接続プロバイダーに依頼できます。

  * [Create Microsoft peering using Azure portal (Azure Portal を使用して Microsoft ピアリングを作成する)](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Create Microsoft peering using Azure Powershell (Azure Powershell を使用して Microsoft ピアリングを作成する)](expressroute-howto-routing-arm.md#msft)
  * [Create Microsoft peering using Azure CLI (Azure CLI を使用して Microsoft ピアリングを作成する)](howto-routing-cli.md#msft)

## <a name="validate"></a>2.Microsoft ピアリングが有効になっていることを検証する

Microsoft ピアリングが有効になっており、アドバタイズされたパブリック プレフィックスが構成された状態にあることを確認します。

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3.ルート フィルターを構成して回線に接続する

既定では、ルート フィルターが回線に接続されるまで、新しい Microsoft ピアリングはどのプレフィックスもアドバタイズしません。 ルート フィルターの規則を作成する場合は、次のスクリーンショットに示すように、Azure PaaS サービスのために消費する Azure リージョンのサービス コミュニティの一覧を指定できます。

![パブリック ピアリングをマージする](./media/how-to-move-peering/public.png)

次のいずれかの記事を使用して、ルート フィルターを構成します。

  * [Configure route filters for Microsoft peering using Azure portal (Azure Portal を使用して Microsoft ピアリングのルート フィルターを構成する)](how-to-routefilter-portal.md)
  * [Configure route filters for Microsoft peering using Azure PowerShell (Azure PowerShell を使用して Microsoft ピアリングのルート フィルターを構成する)](how-to-routefilter-powershell.md)
  * [Configure route filters for Microsoft peering using Azure CLI (Azure CLI を使用して Microsoft ピアリングのルート フィルターを構成する)](how-to-routefilter-cli.md)

## <a name="delete"></a>4.パブリック ピアリングを削除する

Microsoft ピアリングが構成されており、消費するプレフィックスが Microsoft ピアリングで正しくアドバタイズされることを確認したら、パブリック ピアリングを削除できます。 パブリック ピアリングを削除するには、次のいずれかの記事を使用します。

  * [Delete Azure public peering using Azure portal (Azure Portal を使用して Azure パブリック ピアリングを削除する)](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Delete Azure public peering using Azure PowerShell (Azure PowerShell を使用して Azure パブリック ピアリングを削除する)](expressroute-howto-routing-arm.md#deletepublic)
  * [Delete Azure public peering using CLI (CLI を使用して Azure パブリック ピアリングを削除する)](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5.ピアリングの表示
  
Azure Portal で、すべての ExpressRoute 回線とピアリングの一覧を表示できます。 詳細については、[Microsoft ピアリングの詳細表示](expressroute-howto-routing-portal-resource-manager.md#getmsft)に関するセクションをご覧ください。

## <a name="next-steps"></a>次の手順

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
