---
title: Azure ネットワーク リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure ネットワーク リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 2d6814653967fa76ad22bcefcc94df42d60d67b1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496225"
---
# <a name="migrate-network-resources-to-global-azure"></a>ネットワーク リソースをグローバル Azure に移行する

ほとんどのネットワーク サービスは、Azure Germany からグローバル Azure への移行をサポートしていません。 ただし、サイト間 VPN を使用して、両方のクラウド環境内のネットワークを接続できます。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

クラウドの間でサイト間 VPN を設定するために実行する手順は、オンプレミスのネットワークと Azure の間でサイト間 VPN をデプロイするために実行する手順に似ています。 両方のクラウドでゲートウェイを定義した後、互いに通信する方法を VPN に指示します。 「[Azure Portal でサイト間接続を作成する](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)」では、サイト間 VPN をデプロイするために完了する手順について説明しています。 手順の概要を次に示します。

1. 仮想ネットワークを定義します。
1. アドレス空間を定義します。
1. サブネットを定義します。
1. ゲートウェイ サブネットを定義します。
1. 仮想ネットワークのゲートウェイを定義します。
1. ローカル ネットワークのゲートウェイを定義します (ローカル VPN デバイス)。
1. ローカル VPN デバイスを構成します。
1. 接続を構築します。

グローバル Azure と Azure Germany の間の仮想ネットワークを接続するには:

1. グローバル Azure で、前の手順にある手順 1. ～ 5. を完了します。
1. Azure Germany で、手順 1. ～ 5. を完了します。
1. グローバル Azure で、手順 6. を完了します。
   - Azure Germany で、VPN ゲートウェイのパブリック IP アドレスを入力します。
1. Azure Germany で、手順 6. を完了します。
   - グローバル Azure で、VPN ゲートウェイのパブリック IP アドレスを入力します。
1. 手順 7. をスキップします。
1. 手順 8. を完了します。

## <a name="virtual-networks"></a>仮想ネットワーク

現時点では、仮想ネットワークの Azure Germany からグローバル Azure への移行はサポートされていません。 ターゲット リージョンで新しい仮想ネットワークを作成し、これらの仮想ネットワークにリソースを移行することをお勧めします。

詳細:

- [Azure Virtual Network のチュートリアル](https://docs.microsoft.com/azure/virtual-network/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)を確認してください。
- [仮想ネットワークを計画する](../virtual-network/virtual-network-vnet-plan-design-arm.md)方法を学習してください。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

現時点では、ネットワーク セキュリティ グループの Azure Germany からグローバル Azure への移行はサポートされていません。 ターゲット リージョンで新しいネットワーク セキュリティ グループを作成し、ネットワーク セキュリティ グループのルールを新しいアプリケーション環境に適用することをお勧めします。

ネットワーク セキュリティ グループの現在の構成はポータルから、または次の PowerShell コマンドを実行することによって取得します。

```powershell
$nsg=Get-AzNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```

詳細:

- [ネットワーク セキュリティ グループに関する知識](../virtual-network/security-overview.md#network-security-groups)を更新してください。
- [ネットワーク セキュリティの概要](../virtual-network/security-overview.md)を確認してください。
- [ネットワーク セキュリティ グループを管理する](../virtual-network/manage-network-security-group.md)方法を学習してください。

## <a name="expressroute"></a>ExpressRoute

現時点では、Azure ExpressRoute インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しい ExpressRoute 回線と新しい ExpressRoute ゲートウェイを作成することをお勧めします。

詳細:

- [ExpressRoute のチュートリアル](https://docs.microsoft.com/azure/expressroute/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [新しい ExpressRoute ゲートウェイを作成する](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)方法を学習してください。
- [ExpressRoute の場所およびサービス プロバイダー](../expressroute/expressroute-locations.md)について学習してください。
- [ExpressRoute 用の仮想ネットワーク ゲートウェイ](../expressroute/expressroute-about-virtual-network-gateways.md)に関するページを参照してください。

## <a name="vpn-gateway"></a>VPN Gateway

現時点では、Azure VPN Gateway インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、VPN Gateway の新しいインスタンスを作成および構成することをお勧めします。

現在の VPN Gateway 構成に関する情報は、ポータルまたは PowerShell を使用して収集できます。 PowerShell で、`Get-AzVirtualNetworkGateway*` で始まる一連のコマンドレットを使用します。

オンプレミスの構成を確実に更新するようにしてください。 また、Azure ネットワーク環境を更新した後、古い IP アドレス範囲の既存のルールもすべて削除してください。

詳細:

- [VPN Gateway のチュートリアル](https://docs.microsoft.com/azure/vpn-gateway/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [サイト間接続を作成する](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)方法を学習してください。
- [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell コマンドレットを確認してください。
- [サイト間接続の作成](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/)に関するブログの投稿を参照してください。
 
## <a name="application-gateway"></a>Application Gateway

現時点では、Azure Application Gateway インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しいゲートウェイを作成および構成することをお勧めします。

現在のゲートウェイ構成に関する情報は、ポータルまたは PowerShell を使用して収集できます。 PowerShell で、`Get-AzApplicationGateway*` で始まる一連のコマンドレットを使用します。

詳細:

- [Application Gateway のチュートリアル](https://docs.microsoft.com/azure/application-gateway/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [アプリケーション ゲートウェイを作成する](../application-gateway/quick-create-portal.md)方法を学習してください。
- [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell コマンドレットを確認してください。

## <a name="dns"></a>DNS

Azure DNS の構成を Azure Germany からグローバル Azure に移行するには、DNS ゾーン ファイルをエクスポートした後、新しいサブスクリプションのもとでそれをインポートします。 現在、ゾーン ファイルをエクスポートする唯一の方法は Azure CLI の使用です。

Azure Germany でソース サブスクリプションにサインインした後、Azure Resource Manager モードを使用するように Azure CLI を構成します。 次のコマンドを実行してゾーンをエクスポートします。

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

例:

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

このコマンドは、Azure DNS サービスを呼び出して、リソース グループ `myresourcegroup` 内のゾーン `contoso.com` をエクスポートします。 出力は、BIND と互換性のあるゾーン ファイルとして現在のフォルダー内の contoso.com.txt に格納されます。

エクスポートが完了したら、ゾーン ファイルから NS レコードを削除します。 新しいリージョンとサブスクリプションのために新しい NS レコードが作成されます。

次に、ターゲット環境にサインインし、新しいリソース グループを作成 (または既存のリソース グループを選択) してから、ゾーン ファイルをインポートします。

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

ゾーンがインポートされたら、次のコマンドを実行してゾーンを検証する必要があります。

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

検証が完了したら、ドメイン レジストラーに連絡し、NS レコードを再委任します。 NS レコードの情報を取得するには、次のコマンドを実行します。

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

詳細:

- [Azure DNS のチュートリアル](https://docs.microsoft.com/azure/dns/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [Azure DNS の概要](../dns/dns-overview.md)を確認してください。
- [Azure DNS のインポートとエクスポート](../dns/dns-import-export.md)の詳細を学習してください。

## <a name="network-watcher"></a>Network Watcher

現時点では、Azure Network Watcher インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しい Network Watcher インスタンスを作成および構成することをお勧めします。 その後、古い環境と新しい環境の間で結果を比較します。

詳細:

- [Network Watcher のチュートリアル](https://docs.microsoft.com/azure/network-watcher/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Network Watcher の概要](../network-watcher/network-watcher-monitoring-overview.md)を確認してください。
- [ネットワーク セキュリティ グループのフロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md)の詳細を学習してください。
- [接続モニター](../network-watcher/connection-monitor.md)に関するページを参照してください。

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager は、スムーズな移行を完了するために役立ちます。 ただし、Azure Germany で作成した Traffic Manager プロファイルをグローバル Azure に移行することはできません  (移行中に Traffic Manager エンドポイントをターゲット環境に移行するため、いずれにしても Traffic Manager プロファイルを更新する必要があります)。

ソース環境で引き続き実行されている Traffic Manager を使用して、ターゲット環境で追加のエンドポイントを定義できます。 Traffic Manager が新しい環境で実行されている場合も引き続き、ソース環境でまだ移行されていないエンドポイントを定義できます。 このシナリオは、[ブルーグリーン シナリオ](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)と呼ばれます。 このシナリオには、次の手順が必要です。

1. グローバル Azure で、新しい Traffic Manager プロファイルを作成します。
1. Azure Germany でエンドポイントを定義します。
1. ご利用の DNS CNAME レコードを新しい Traffic Manager プロファイルに変更します。
1. 古い Traffic Manager プロファイルを無効にします。
1. エンドポイントを移行して構成します。 Azure Germany 内のエンドポイントごとに、次の操作を行います。
   1. エンドポイントをグローバル Azure に移行します。
   1. 新しいエンドポイントを使用するように Traffic Manager プロファイルを変更します。

詳細:

- [Traffic Manager のチュートリアル](https://docs.microsoft.com/azure/traffic-manager/#step-by-step-tutorials)を完了して、新しい知識を得ましょう。
- [Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md)を確認してください。
- [Traffic Manager プロファイルの作成](../traffic-manager/traffic-manager-create-profile.md)方法を確認します。

## <a name="load-balancer"></a>Load Balancer

現時点では、Azure Load Balancer インスタンスの Azure Germany からグローバル Azure への移行はサポートされていません。 グローバル Azure で、新しいロード バランサーを作成および構成することをお勧めします。

詳細:

- [Load Balancer のチュートリアル](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)を完了することによって知識を更新してください。
- [Load Balancer の概要](../load-balancer/load-balancer-overview.md)を確認してください。
- [新しいロード バランサーを作成する](../load-balancer/quickstart-load-balancer-standard-public-portal.md)方法を学習してください。

## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
