---
title: Azure App Configuration でのプライベート エンドポイントの使用
description: プライベート エンドポイントを使用した App Configuration ストアのセキュリティ保護
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 6cadadfb3623d05dd3ae3851acd5eaca13860023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929845"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Azure App Configuration でのプライベート エンドポイントの使用

Azure App Configuration の[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のクライアントは[プライベート リンク](../private-link/private-link-overview.md)を介してデータに安全にアクセスできるようになります。 プライベート エンドポイントでは、App Configuration ストアの VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと App Configuration ストアの間のネットワーク トラフィックは、Microsoft バックボーン ネットワーク上のプライベート リンクを使用して VNet を経由することで、パブリック インターネットへの露出を排除します。

App Configuration にプライベート エンドポイントを使用すると、次のことが可能になります。
- パブリック エンドポイント上での App Configuration へのすべての接続をブロックするようにファイアウォールを構成して、アプリケーション構成の詳細をセキュリティで保護します。
- 仮想ネットワーク (VNet) のセキュリティを強化し、データが VNet から抜け出さないようにします。
- [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoutes](../expressroute/expressroute-locations.md) とプライベート ピアリングを使用して VNet に接続するオンプレミス ネットワークから App Configuration ストアに安全に接続します。

## <a name="conceptual-overview"></a>概念の概要

プライベート エンドポイントは、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) 内の Azure サービス用の特別なネットワーク インターフェイスです。 App Config ストアのプライベート エンドポイントを作成すると、VNet 上のクライアントと構成ストア間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。 プライベート エンドポイントと構成ストア間の接続には、セキュリティで保護されたプライベート リンクが使用されます。

VNet 内のアプリケーションは、プライベート エンドポイント経由で構成ストアに接続できます。その際、**使用される接続文字列と承認メカニズムは、それを経由しない場合と同じものになります**。 プライベート エンドポイントは、App Configuration ストアでサポートされているすべてのプロトコルで使用できます。

App Configuration ではサービス エンドポイントはサポートされませんが、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用するサブネットにプライベート エンドポイントを作成できます。 サブネット内のクライアントは、プライベート エンドポイントを使用して 1 つの App Configuration ストアに安全に接続でき、サービス エンドポイントを使用してその他にアクセスできます。  

お使いの VNet でサービス用プライベート エンドポイントを作成すると、承認を得るために同意要求がサービス アカウント オーナーに送信されます。 プライベート エンドポイントの作成を要求しているユーザーがアカウントのオーナーでもある場合、この同意要求は自動的に承認されます。

サービス アカウント オーナーは、[Azure portal](https://portal.azure.com) の構成ストアの [`Private Endpoints`] タブを使用して、同意要求とプライベート エンドポイントを管理できます。

### <a name="private-endpoints-for-app-configuration"></a>App Configuration でのプライベート エンドポイント 

プライベート エンドポイントを作成する場合は、接続先の App Configuration ストアを指定する必要があります。 1 つのアカウント内に複数の App Configuration インスタンスがある場合は、ストアごとに個別のプライベート エンドポイントが必要です。

### <a name="connecting-to-private-endpoints"></a>プライベート エンドポイントへの接続

Azure は、VNet から構成ストアへの接続をプライベート リンク経由でルーティングするために、DNS 解決に依存しています。 App Configuration ストアを選択し、 **[設定]**  >  **[アクセス キー]** を選択すると、Azure portal 内で接続文字列をすばやく見つけることができます。  

> [!IMPORTANT]
> パブリック エンドポイントに使用するのと同じ接続文字列を使用して、プライベート エンドポイントを使用して App Configuration ストアに接続します。 `privatelink` サブドメイン URL を使用してストアに接続しないでください。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更

プライベート エンドポイントを作成すると、構成ストアの DNS CNAME リソース レコードは、プレフィックス `privatelink` を持つサブドメイン内のエイリアスに更新されます。 `privatelink` サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントをホストしている VNet 内からエンドポイント URL を解決すると、ストアのプライベート エンドポイントに解決されます。 VNet の外部から解決すると、エンドポイント URL はパブリック エンドポイントに解決されます。 プライベート エンドポイントを作成すると、パブリック エンドポイントは無効になります。

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントで、サービス エンドポイントの完全修飾ドメイン名 (FQDN) をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して `AppConfigInstanceA.privatelink.azconfig.io` の A レコードを構成します。

> [!TIP]
> カスタムまたはオンプレミスの DNS サーバーを使用している場合は、`privatelink` サブドメインのストア名をプライベート エンドポイントの IP アドレスに解決するように DNS サーバーを構成する必要があります。 これを行うには、VNet のプライベート DNS ゾーンに `privatelink` サブドメインを委任するか、DNS サーバー上で DNS ゾーンを構成し、DNS A レコードを追加します。

## <a name="pricing"></a>価格

プライベート エンドポイントを有効にするには、[Standard レベル](https://azure.microsoft.com/pricing/details/app-configuration/)の App Configuration ストアが必要です。  プライベート リンクの料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="next-steps"></a>次のステップ

App Configuration ストアのプライベート エンドポイントの作成の詳細については、次の記事を参照してください。

- [Azure portal でプライベート リンク センターを使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-portal.md)
- [Azure CLI を使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell を使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-powershell.md)

プライベート エンドポイントを使用して DNS サーバーを構成する方法を学習します。

- [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-overview.md#dns-configuration)