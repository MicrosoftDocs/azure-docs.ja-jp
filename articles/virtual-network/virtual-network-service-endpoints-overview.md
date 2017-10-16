---
title: "Azure 仮想ネットワークのサービス エンドポイント | Microsoft Docs"
description: "サービス エンドポイントを使って仮想ネットワークから Azure リソースへの直接アクセスを有効にする方法を説明します。"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: e2359bc6002bd5c823467a33a4660ebccd116374
ms.contentlocale: ja-jp
ms.lasthandoff: 09/26/2017

---

# <a name="virtual-network-service-endpoints-preview"></a>仮想ネットワークのサービス エンドポイント (プレビュー)

仮想ネットワーク (VNet) のサービス エンドポイントは、直接接続によって仮想ネットワークのプライベート アドレス空間を拡張し、VNet の ID を Azure サービスに提供します。 エンドポイントを使用するとこで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。

この機能はプレビュー段階で、次の Azure サービスとリージョンで提供されています。

- **Azure Storage**: WestCentralUS、WestUS2、EastUS、WestUS、AustraliaEast、AustraliaSouthEast。
- **Azure SQL Database**: WestCentralUS、WestUS2、EastUS。

このプレビュー機能に関する最新情報については、[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)ページをご覧ください。

>[!NOTE]
> プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="key-benefits"></a>主な利点

サービス エンドポイントには次の利点があります。

- **Azure サービス リソースのセキュリティ向上**: サービス エンドポイントを使用すると、Azure サービス リソースへのアクセスを仮想ネットワークに限定することができます。 サービス リソースを仮想ネットワークに限定することで、リソースへのパブリック インターネット アクセスを完全に排除し、仮想ネットワークからのトラフィックのみを許可することにより、セキュリティが向上します。
- **仮想ネットワークからの Azure サービス トラフィックのルーティングを最適化**: 現在は、仮想ネットワーク内のルートでオンプレミスまたは仮想アプライアンス経由のインターネット トラフィックが強制されている場合 (強制トンネリング)、Azure サービス トラフィックにもインターネット トラフィックと同じルートが強制されます。 サービス エンドポイントを使用することで、Azure トラフィックのルーティングを最適化することができます。 

  エンドポイントは常に、仮想ネットワークから Microsoft Azure のバックボーン ネットワーク上のサービスへのサービス トラフィックを直接受け取ります。 Azure のバックボーン ネットワーク上でトラフィックを維持することで、サービス トラフィックに影響を与えることなく、強制トンネリングを通じて、仮想ネットワークからの送信インターネット トラフィックの監査と監視を続けることができます。 詳細については、[ユーザー定義のルートと強制トンネリング](virtual-networks-udr-overview.md)に関するページをご覧ください。
- **管理の手間がかからないシンプルな設定**: 仮想ネットワークで予約済みのパブリック IP アドレスを使用することなく、IP ファイアウォールを通じて Azure リソースにアクセスできるようになりました。 サービス エンドポイントの設定に NAT やゲートウェイ用のデバイスは必要ありません。 サービス エンドポイントは、サブネット上での単純なクリック操作で構成されます。 エンドポイントを維持するために余分なコストはかかりません。

## <a name="limitations"></a>制限事項

- この機能は、Azure Resource Manager デプロイメント モデルを使ってデプロイされた仮想ネットワークでのみ使用できます。
- エンドポイントは、Azure 仮想ネットワークに構成されたサブネット上で有効になります。 エンドポイントは、オンプレミスから Azure サービスへのトラフィックに対しては使用できません。 詳細については、「[オンプレミスから Azure サービスへのアクセスの保護](#securing-azure-services-to-virtual-networks)」をご覧ください。
- サービス エンドポイントは、仮想ネットワークのリージョン内の Azure サービス トラフィックにのみ適用されます。 Azure Storage で RA-GRS および GRS のトラフィックをサポートするには、仮想ネットワークのデプロイ先のリージョンとペアになっているリージョンを含めるようにエンドポイントも拡張します。 [Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)の詳細をご覧ください。

## <a name="securing-azure-services-to-virtual-networks"></a>Azure サービスへのアクセスを仮想ネットワークに限定する

- 仮想ネットワークのサービス エンドポイントは、仮想ネットワークの ID を Azure サービスに提供します。 仮想ネットワークでサービス エンドポイントが有効になったら、Azure サービス リソースに仮想ネットワーク ルールを追加することで、このリソースへのアクセスを仮想ネットワークに限定することができます。
- 現在、仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして使用します。 サービス エンドポイントを使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを接続元 IP アドレスとして使用するよう切り替えます。 この切り替えにより、IP ファイアウォールで使用される予約済みのパブリック IP アドレスを使用することなく、サービスにアクセスすることができます。
- オンプレミスから Azure サービスへのアクセスの保護: 既定では、仮想ネットワークからのアクセスに限定された Azure サービス リソースは、オンプレミスのネットワークからはアクセスできません。 オンプレミスからのトラフィックを許可する場合は、オンプレミスまたは ExpressRoute 回線からの NAT IP アドレスを許可する必要があります。 NAT IP アドレスは、Azure サービス リソースの IP ファイアウォールの構成を通じて追加できます。
- ExpressRoute: オンプレミスから [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用している場合、各 ExpressRoute 回線は 2 つの NAT IP アドレスを使用します。これは、トラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに Azure サービス トラフィックに適用されます。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこの 2 つの IP アドレスを許可する必要があります。 ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。

![Azure サービスへのアクセスを仮想ネットワークに限定する](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>構成

- サービス エンドポイントは仮想ネットワーク内のサブネット上で構成されます。 エンドポイントは、そのサブネット内で実行されているどの種類のコンピューティング インスタンスでも動作します。
- 1 つのサブネットの特定のサービスに対して有効にできるサービス エンドポイントは 1 つだけです。 サブネット上のサポートされているすべての Azure サービス (Azure Storage や Azure SQL Database など) に対して複数のサービス エンドポイントを構成できます。
- 仮想ネットワークは、Azure サービス リソースと同じリージョンにある必要があります。 GRS および RA-GRS Azure Storage アカウントを使用している場合、プライマリ アカウントは仮想ネットワークと同じリージョンにある必要があります。
- エンドポイントが構成されている仮想ネットワークのサブスクリプションは、Azure サービス リソースのサブスクリプションと同じでも違っていてもかまいません。 エンドポイントを設定して Azure サービスのセキュリティを保護するために必要なアクセス許可の詳細については、[プロビジョニング](#Provisioning)に関するページを参照してください。
- サポートされているサービスについては、サービス エンドポイントを使って新規または既存のリソースへのアクセスを仮想ネットワークに限定することができます。

### <a name="considerations"></a>考慮事項

- サービス エンドポイントを有効にすると、サブネット内の仮想マシンの接続元 IP アドレスは、そのサブネットがサービスと通信するときに、パブリック IPv4 アドレスからプライベート IPv4 アドレスを使用するように切り替わります。 この切り替え中に、サービスに接続中の既存の TCP 接続はすべて閉じられます。 サービスに接続するサブネットのサービス エンドポイントを有効または無効にするときは、重要なタスクが実行されていないことを確認してください。 また、この IP アドレスの切り替えの後に、アプリケーションが自動的に Azure サービスに接続できることを確認してください。

  IP アドレスの切り替えが影響するのは、仮想ネットワークからのサービス トラフィックのみです。 アドレス指定されるその他のトラフィックへの影響や、仮想マシンに割り当てられたパブリック IPv4 アドレスからの影響はありません。 Azure サービスについては、Azure のパブリック IP アドレスを使用する既存のファイアウォール ルールがある場合、これらのルールは仮想ネットワークのプライベート アドレスへの切り替え時に停止します。
- サービス エンドポイントを使用しても、Azure サービスの DNS エントリは変わらず、引き続き Azure サービスに割り当てられているパブリック IP アドレスに解決されます。
- ネットワーク セキュリティ グループでのサービス エンドポイントの使用:
  - インターネットへの送信インターネット トラフィックを許可します。そのため、仮想ネットワークから Azure サービスのパブリック IP アドレスへのトラフィックも許可します。
  - ネットワーク セキュリティ グループで[サービス タグ](security-overview.md#service-tags)を使用することで、Azure サービスのアドレスを除くパブリック IP アドレスへのトラフィックを拒否することができます。 ネットワーク セキュリティ グループの規則でサポートされている Azure サービスを送信先として指定できます。 各タグの基になる IP アドレスのメンテナンスは、Azure によって提供されます。

### <a name="scenarios"></a>シナリオ

- **ピアリングされた仮想ネットワーク、接続された仮想ネットワーク、または複数の仮想ネットワーク**: Azure サービスへのアクセスを 1 つの仮想ネットワーク内または複数の仮想ネットワークにまたがる複数のサブネットに限定するには、サブネットごとに個別にサービス エンドポイントを有効にして、Azure サービス リソースへのアクセスをこれらのサブネットに限定します。
- **仮想ネットワークから Azure サービスへの送信トラフィックのフィルタリング**: 仮想ネットワークから Azure サービスへのトラフィックを検査またはフィルター処理する場合は、仮想ネットワーク内にネットワーク仮想アプライアンスをデプロイします。 その後、ネットワーク仮想アプライアンスがデプロイされているサブネットにサービス エンドポイントを適用し、このサブネットのみに Azure サービス リソースへのアクセスを限定します。 このシナリオは、ネットワーク仮想アプライアンス フィルタリングを使用して、仮想ネットワークから Azure サービスへのアクセスを特定の Azure リソースにのみ制限する場合に役立ちます。 詳細については、[ネットワーク仮想アプライアンスを持つエグレス](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。
- **Azure リソースへのアクセスを仮想ネットワークに直接デプロイされたサービスに限定する**: さまざまな Azure サービスを仮想ネットワーク内の特定のサブネットに直接デプロイできます。 管理されたサービスのサブネット上にサービス エンドポイントを設定することで、Azure サービス リソースへのアクセスを[管理されたサービス](virtual-network-for-azure-services.md)のサブネットに限定することができます。

### <a name="logging-and-troubleshooting"></a>ロギングおよびトラブルシューティング

特定のサービスに対してサービス エンドポイントを構成したら、そのサービス エンドポイントのルートが有効であることを次の方法で検証します。 

- サービスの診断で任意のサービス要求の発信元 IP アドレスを検証します。 サービス エンドポイントを使った新しい要求では、仮想ネットワークから要求を行うクライアントに割り当てられている、その要求の接続元 IP アドレスが仮想ネットワークのプライベート IP アドレスとして表示されます。 エンドポイントを使用しない場合、このアドレスは Azure のパブリック IP アドレスになります。
- サブネット内の任意のネットワーク インターフェイス上に有効なルートを表示します。 サービスへのルートは次のことを行います。
  - 各サービスのアドレス プレフィックス範囲へのより詳細な既定のルートを示します。
  - *VirtualNetworkServiceEndpoint* の nextHopType を持ちます。
  - 強制トンネリングのルートと比べて、サービスへのより直接的な接続が有効であることを示します。

>[!NOTE]
> サービス エンドポイントのルートは、アドレス プレフィックスが Azure サービスのものと一致するすべての BGP または UDR のルートをオーバーライドします。 [有効なルートを使用したトラブルシューティング](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow)の詳細をご覧ください。

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。 Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの *Microsoft.Network/JoinServicetoaSubnet* へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)と、特定のアクセス許可を[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法の詳細をご覧ください。

仮想ネットワークと Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 仮想ネットワークと Azure サービス リソースのサブスクリプションが異なる場合、プレビュー期間中、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 

## <a name="pricing-and-limits"></a>料金と制限

サービス エンドポイントの使用に追加料金はかかりません。 現時点では、Azure サービス (Azure Storage、Azure SQL Database) の現在の価格設定モデルが適用されます。

仮想ネットワーク内のサービス エンドポイントの合計数に制限はありません。

Azure サービス リソース (Azure Storage アカウントなど) の場合、リソースへのアクセスに使用されるサブネットの数がサービスによって制限される場合があります。 詳細については、「[次のステップ](#next-steps)」の各種サービスに関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワークのサービス エンドポイントを構成する](virtual-network-service-endpoints-configure.md)方法を学習する
- [Azure Storage アカウントを仮想ネットワークに限定する](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法を学習する
- [Azure SQL Database を仮想ネットワークに限定する](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法を学習する
- [仮想ネットワーク内の Azure サービス統合](virtual-network-for-azure-services.md)について学習する


