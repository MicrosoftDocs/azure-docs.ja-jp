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
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592129"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>パブリック ピアリングを Microsoft ピアリングに移行する

この記事は、パブリック ピアリング構成をダウンタイムなしで Microsoft ピアリングに移行するために役立ちます。 ExpressRoute は、Azure Storage や Azure SQL Database などの Azure PaaS サービスのために Microsoft ピアリングとルート フィルターの併用をサポートしています。 Microsoft PaaS および SaaS サービスへのアクセスに必要なルーティング ドメインは 1 つだけになりました。 消費する Azure リージョンの PaaS サービス プレフィックスを選択的にアドバタイズするには、ルート フィルターを使用できます。

Azure パブリック ピアリングでは、各 BGP セッションに 1 つの NAT IP アドレスが関連付けられます。 Microsoft ピアリングを使用すると、独自の NAT 割り当てを構成したり、選択的なプレフィックス アドバタイズのルート フィルターを使用したりできます。 パブリック ピアリングは一方向サービスであり、それを使用して WAN から Microsoft Azure サービスへの接続が常に開始されます。 このルーティング ドメインを経由して Microsoft Azure サービスからお客様のネットワークに接続を開始することはできません。

パブリック ピアリングが有効になると、すべての Azure サービスに接続できます。 ルートをアドバタイズするサービスを選択することはできません。 一方、Microsoft ピアリングは双方向接続であり、WAN と共に Microsoft Azure サービスから接続を開始できます。 ルーティング ドメインとピアリングの詳細については、「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。

## <a name="before"></a>開始する前に

Microsoft ピアリングに接続するには、NAT を設定および管理する必要があります。 接続プロバイダーが、管理されたサービスとして NAT を設定および管理する可能性があります。 Microsoft ピアリングで Azure PaaS および Azure SaaS サービスにアクセスする予定がある場合は、NAT IP プールを正しくサイズ設定することが重要です。 ExpressRoute の NAT の詳細については、「[Microsoft ピアリングの NAT 要件](expressroute-nat.md#nat-requirements-for-microsoft-peering)」を参照してください。 Azure ExpressRoute (Microsoft ピアリング) を介して Microsoft に接続する際は、Microsoft への複数のリンクがあります。 1 つは既存のインターネット接続で、もう 1 つは ExpressRoute によるものです。 Microsoft に向かうトラフィックの一部がインターネットを経由して送信され、ExpressRoute を経由して戻ってくることがあります (その逆もあります)。

![双方向接続](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Microsoft にアドバタイズされる NAT IP プールはインターネットにアドバタイズしないでください。 他の Microsoft サービスへの接続が切断されます。

Microsoft ピアリングを構成する前に、「[複数のネットワーク パスを使用した非対称ルーティング](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)」で、非対称ルーティングの注意事項を参照してください。

* パブリック ピアリングを使用していて、[Azure Storage](../storage/common/storage-network-security.md) または [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) へのアクセスに使用されるパブリック IP アドレスの IP ネットワーク ルールが現在ある場合は、Azure Storage アカウントまたは Azure SQL アカウントのパブリック IP アドレスの一覧に、Microsoft ピアリングを使用して構成された NAT IP プールを含める必要があります。<br>
* ダウンタイムなしで Microsoft ピアリングに移行するには、この記事の手順をそれらが示されている順序で実行します。

## <a name="create"></a>1.Microsoft ピアリングを作成する

Microsoft ピアリングがまだ作成されていない場合は、次のいずれかの記事を使用して Microsoft ピアリングを作成します。 接続プロバイダーが管理対象レイヤー 3 サービスを提供している場合は、使用している回線の Microsoft ピアリングを有効にするように接続プロバイダーに依頼できます。

レイヤー 3 を自分で管理している場合は、先に進む前に次の情報が必要です。

* プライマリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。<br>
* セカンダリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。<br>
* このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。 プライマリとセカンダリの両方のリンクに対し、同じ VLAN ID を使用する必要があります。<br>
* ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。<br>
* アドバタイズするプレフィックス:BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定する必要があります。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。<br>
* ルーティング レジストリ名: AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。

* **省略可能** - 顧客 ASN:ピアリング AS 番号に登録されていないプレフィックスをアドバタイズする場合は、そのプレフィックスを登録する AS 番号を指定できます。<br>
* **省略可能** - 使用する場合は MD5 ハッシュ。

Microsoft ピアリングを有効にするための詳細な手順については、次の記事をご覧ください。

* [Create Microsoft peering using Azure portal (Azure Portal を使用して Microsoft ピアリングを作成する)](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Create Microsoft peering using Azure Powershell (Azure Powershell を使用して Microsoft ピアリングを作成する)](expressroute-howto-routing-arm.md#msft)<br>
* [Create Microsoft peering using Azure CLI (Azure CLI を使用して Microsoft ピアリングを作成する)](howto-routing-cli.md#msft)

## <a name="validate"></a>2.Microsoft ピアリングが有効になっていることを検証する

Microsoft ピアリングが有効になっており、アドバタイズされたパブリック プレフィックスが構成された状態にあることを確認します。

* [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3.ルート フィルターを構成して回線に接続する

既定では、ルート フィルターが回線に接続されるまで、新しい Microsoft ピアリングではどのプレフィックスもアドバタイズされません。 ルート フィルター規則を作成する場合は、Azure PaaS サービスのために消費する Azure リージョンのサービス コミュニティの一覧を指定できます。 これにより、次のスクリーンショットに示すように、要件に応じてルートをフィルター処理する柔軟性が提供されます。

![パブリック ピアリングをマージする](./media/how-to-move-peering/routefilter.jpg)

次のいずれかの記事を使用して、ルート フィルターを構成します。

* [Configure route filters for Microsoft peering using Azure portal (Azure Portal を使用して Microsoft ピアリングのルート フィルターを構成する)](how-to-routefilter-portal.md)<br>
* [Configure route filters for Microsoft peering using Azure PowerShell (Azure PowerShell を使用して Microsoft ピアリングのルート フィルターを構成する)](how-to-routefilter-powershell.md)<br>
* [Configure route filters for Microsoft peering using Azure CLI (Azure CLI を使用して Microsoft ピアリングのルート フィルターを構成する)](how-to-routefilter-cli.md)

## <a name="delete"></a>4.パブリック ピアリングを削除する

Microsoft ピアリングが構成されており、消費するプレフィックスが Microsoft ピアリングで正しくアドバタイズされることを確認したら、パブリック ピアリングを削除できます。 パブリック ピアリングを削除するには、次のいずれかの記事を使用します。

* [Delete Azure public peering using Azure portal (Azure Portal を使用して Azure パブリック ピアリングを削除する)](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Delete Azure public peering using Azure PowerShell (Azure PowerShell を使用して Azure パブリック ピアリングを削除する)](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Delete Azure public peering using CLI (CLI を使用して Azure パブリック ピアリングを削除する)](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5.ピアリングの表示
  
Azure Portal で、すべての ExpressRoute 回線とピアリングの一覧を表示できます。 詳細については、[Microsoft ピアリングの詳細表示](expressroute-howto-routing-portal-resource-manager.md#getmsft)に関するセクションをご覧ください。

## <a name="next-steps"></a>次の手順

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
