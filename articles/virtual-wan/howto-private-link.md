---
title: Virtual WAN 間でプライベート リンク サービスを共有する
titleSuffix: Azure Virtual WAN
description: Virtual WAN で Private Link を構成する手順
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913757"
---
# <a name="use-private-link-in-virtual-wan"></a>Virtual WAN で Private Link を使用する

[Azure Private Link](../private-link/private-link-overview.md) は、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を公開することで、プライベート IP アドレスの接続を使用して Azure のサービスとしてのプラットフォームのオファリングを接続できるテクノロジです。 Azure Virtual WAN では、任意の仮想ハブに接続された仮想ネットワークの 1 つにプライベート エンドポイントをデプロイできます。 これにより、同じ Virtual WAN に接続されている他の仮想ネットワークまたはブランチに接続できます。

## <a name="before-you-begin"></a>始める前に

この記事の手順では、1 つ以上のハブがある Virtual WAN が既にデプロイされ、Virtual WAN に少なくとも 2 つの仮想ネットワークが接続されていること想定しています。

新しい仮想 WAN と新しいハブを作成するには、次の記事の手順に従います。

* [仮想 WAN を作成する](virtual-wan-site-to-site-portal.md#openvwan)
* [ハブを作成する](virtual-wan-site-to-site-portal.md#hub)
* [VNet をハブに接続する](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>プライベート リンク エンドポイントを作成する

プライベート リンク エンドポイントは、さまざまなサービスに対して作成できます。 この例では、Azure SQL Database を使用します。 Azure SQL Database のプライベート エンドポイントを作成する方法の詳細については、「[クイックスタート:Azure portal を使用してプライベート エンドポイントを作成する](../private-link/create-private-endpoint-portal.md)」を参照してください。 次の図は、Azure SQL Database のネットワーク構成を示しています。

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="プライベート リンクの作成" lightbox="./media/howto-private-link/create-private-link.png":::

Azure SQL Database を作成した後、プライベート エンドポイントを参照してプライベート エンドポイントの IP アドレスを確認することができます。

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="プライベート エンドポイント" lightbox="./media/howto-private-link/endpoints.png":::

作成したプライベート エンドポイントをクリックすると、そのプライベート IP アドレスだけでなく、その完全修飾ドメイン名 (FQDN) も表示されます。 プライベート エンドポイントの IP アドレスが、デプロイされている VNet の範囲内 (10.1.3.0/24) にあることに注意してください。

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL エンドポイント" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>同じ VNet からの接続を確認する

この例では、MS SQL ツールがインストールされている Ubuntu 仮想マシンから Azure SQL Database への接続性を検証します。 最初の手順では、DNS 解決が機能することと、Azure SQL Database の完全修飾ドメイン名が、プライベート エンドポイントのデプロイ先と同じ VNet 内のプライベート IP アドレス (10.1.3.0/24) に解決されていることを確認します。

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

前の出力でわかるように、FQDN `wantest.database.windows.net` は `wantest.privatelink.database.windows.net` にマップされ、プライベート エンドポイントと共に作成されたプライベート DNS ゾーンは、プライベート IP アドレス `10.1.3.228` に解決されます。 プライベート DNS ゾーンを見ていくと、プライベート IP アドレスにマップされたプライベート エンドポイントに A レコードがあることが確認できます。

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS ゾーン" lightbox="./media/howto-private-link/dns-zone.png":::

正しい DNS 解決を確認した後、データベースへの接続を試みることができます。

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

ご覧のとおり、ここでは SQL Server によってクライアントから認識される発信元 IP アドレスを返す特別な SQL クエリを使用しています。 この場合、サーバーでは、クライアントはそのプライベート IP (`10.1.3.75`) で認識されます。つまり、トラフィックは VNet からプライベート エンドポイントに直行します。

このガイドの例を使用するには、Azure SQL Database で定義されている資格情報と一致するように `username` と `password` 変数を設定する必要があることに注意してください。

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>別の VNet からの接続

Azure Virtual WAN 内の 1 つの VNet がプライベート エンドポイントに接続できるようになったので、Virtual WAN に接続されている他のすべての Vnet とブランチも、それにアクセスできるようになりました。 Azure Virtual WAN でサポートされているモデルのいずれかを介して接続性を提供する必要があります。例としては、[Any-to-any シナリオ](scenario-any-to-any.md)や [Shared Services VNet シナリオ](scenario-shared-services-vnet.md)などの 2 つがあります。

VNet またはブランチからプライベート エンドポイントがデプロイされている VNet への接続が完了したら、DNS 解決を構成する必要があります。

* VNet からプライベート エンドポイントに接続する場合は、Azure SQL Database で作成したプライベート ゾーンと同じものを使用できます。
* ブランチ (サイト間 VPN、ポイント対サイト VPN、または ExpressRoute) からプライベート エンドポイントに接続する場合は、オンプレミスの DNS 解決を使用する必要があります。

この例では、別の VNet から接続します。そのため、まず、プライベート DNS ゾーンを新しい VNet に接続して、そのワークロードが Azure SQL Database の完全修飾ドメイン名をプライベート IP アドレスに解決できるようにします。 これを行うには、次のようにプライベート DNS ゾーンを新しい VNet にリンクします。

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS リンク" lightbox="./media/howto-private-link/dns-link.png":::

これで、接続された VNet 内の仮想マシンは、Azure SQL Database の FQDN をプライベート リンクのプライベート IP アドレスに正しく解決されるはずです。

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

この VNet (10.1.1.0/24) が、プライベート エンドポイントが構成されている元の VNet (10.1.3.0/24) に接続していることを再確認するには、VNet 内の任意の仮想マシンで有効なルート テーブルを確認します。

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="有効なルート" lightbox="./media/howto-private-link/effective-routes.png":::

ご覧のように、Azure Virtual WAN の Virtual Network ゲートウェイによって挿入された VNet 10.1.3.0/24 をポイントするルートがあります。 これで、ようやくデータベースへの接続をテストできるようになりました。

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

この例では、Virtual WAN に接続されているいずれかの Vnet にプライベート エンドポイントを作成することで、Virtual WAN 内の残りの Vnet とブランチに接続できることを見てきました。

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
