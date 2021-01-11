---
title: Private Link - Azure Database for MariaDB
description: Azure Database for MariaDB で Private link がどのように機能するかについて説明します。
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 20add4859b272b6d121666cde9c56296119d41e4
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836532"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Azure Database for MariaDB 用の Private Link

Private Link を使用すると、Azure Database for MariaDB のプライベート エンドポイントを作成できるため、プライベート仮想ネットワーク (VNet) 内で Azure サービスを利用できます。 プライベート エンドポイントでは、VNet 内の他のリソースと同様に、Azure Database for MariaDB データベース サーバーへの接続に使用できるプライベート IP が公開されます。

Private Link 機能をサポートしている PaaS サービスの一覧については、Private Link の[ドキュメント](https://docs.microsoft.com/azure/private-link/index)を参照してください。 プライベート エンドポイントは、特定の [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) およびサブネット内のプライベート IP アドレスです。

> [!NOTE]
> プライベート リンク機能は、General Purpose または Memory Optimized の価格レベルの Azure Database for MariaDB サーバーにのみ使用可能です。 データベース サーバーがこれらの価格レベルのいずれであるかを確認します。

## <a name="data-exfiltration-prevention"></a>データの流出防止

Azure Database for MariaDB におけるデータの流出とは、データベース管理者などの承認されたユーザーが、あるシステムからデータを抽出して、組織外の別の場所またはシステムに移動できる場合です。 たとえば、ユーザーがサードパーティによって所有されているストレージ アカウントにデータを移動します。

Azure Database for MariaDB インスタンスに接続している Azure VM 内で MariaDB ワークベンチを実行するユーザーのシナリオを考えてみます。 この MariaDB インスタンスは、米国西部のデータ センターにあります。 次の例では、ネットワーク アクセス制御を使用して Azure Database for MariaDB のパブリック エンドポイントでアクセスを制限する方法を示します。

* [Allow Azure Services]\(Azure サービスを許可する\) を OFF に設定して、パブリック エンドポイント経由の Azure Database for MariaDB へのすべての Azure サービス トラフィックを無効にします。 IP アドレスまたは範囲が、[ファイアウォール規則](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)または[仮想ネットワーク サービス エンドポイント](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)経由のサーバーへのアクセスを許可されていないことを確認してください。

* VM のプライベート IP アドレスを使用する Azure Database for MariaDB へのトラフィックのみを許可します。 詳細については、[サービス エンドポイント](concepts-data-access-security-vnet.md)と [VNet ファイアウォール規則](howto-manage-vnet-portal.md)に関する記事を参照してください。

* Azure VM で、次のようにネットワーク セキュリティ グループ (NSG) とサービス タグを使用して、送信接続の範囲を絞り込みます。

    * 米国西部にある Azure Database for MariaDB への接続のみを許可するように、サービス タグ = SQL.WestUs のトラフィックを許可する NSG ルールを指定します
    * すべてのリージョンで MariaDB Database への接続を拒否するように、サービス タグへのトラフィックを拒否する NSG ルールを (高い優先度で) 指定します (Service Tag = SQL)</br></br>

この設定が終了すると、Azure VM は米国西部リージョンにある Azure Database for MariaDB にのみ接続できます。 ただし、接続は 1 つの Azure Database for MariaDB に限定されません。 この VM は、サブスクリプションに含まれていないデータベースも含め、米国西部リージョン内の任意の Azure Database for MariaDB に引き続き接続できます。 上記のシナリオでは、データの流出が特定のリージョンに限定されていますが、完全には除外されていません。</br>

Private Link を使用することで、NSG のようなネットワーク アクセス制御を設定してプライベート エンドポイントへのアクセスを制限できるようになりました。 その後、個々の Azure PaaS リソースが特定のプライベート エンドポイントにマップされます。 悪意のある内部関係者は、マップされた PaaS リソース (Azure Database for MariaDB など) にしかアクセスできず、その他のリソースにはアクセスできません。

## <a name="on-premises-connectivity-over-private-peering"></a>プライベート ピアリングを介したオンプレミス接続

オンプレミスのマシンからパブリック エンドポイントに接続する場合、サーバーレベルのファイアウォール規則を使用して、ご自分の IP アドレスを IP ベースのファイアウォールに追加する必要があります。 このモデルは、開発またはテストのワークロード用に個々のコンピューターへのアクセスを許可する場合には適していますが、運用環境で管理するのは困難です。

Private Link を使用すると、[Express Route](https://azure.microsoft.com/services/expressroute/) (ER)、プライベート ピアリング、または [VPN トンネル](https://docs.microsoft.com/azure/vpn-gateway/)を使用して、プライベート エンドポイントへのクロスプレミス アクセスを有効にすることができます。 その後、パブリック エンドポイント経由のすべてのアクセスを無効にして、IP ベースのファイアウォールを使用しないようにすることができます。

> [!NOTE]
> Azure Database for MariaDB と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションに **Microsoft.DBforMariaDB** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Azure Database for MariaDB 用に Private Link を構成する

### <a name="creation-process"></a>作成プロセス

Private Link を有効にするには、プライベート エンドポイントが必要です。 これは、次の操作方法ガイドを使用して行えます。

* [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>承認プロセス

ネットワーク管理者がプライベート エンドポイント (PE) を作成すると、管理者は Azure Database for MariaDB へのプライベート エンドポイント接続 (PEC) を管理できます。 ネットワーク管理者と DBA の職掌分散は、Azure Database for MariaDB 接続の管理に役立ちます。 

* Azure portal で Azure Database for MariaDB サーバー リソースに移動します。 
    * 左側のウィンドウで、プライベート エンドポイント接続を選択します
    * すべてのプライベート エンドポイント接続 (PEC) の一覧が表示されます
    * 対応するプライベート エンドポイント (PE) が作成されます

![プライベート エンドポイントの選択のポータル](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* リストから個々の PEC を選択します。

![承認保留中のプライベート エンドポイントの選択](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MariaDB サーバー管理者は、PEC の承認または拒否を選択できます。また、必要に応じて、短いテキスト応答を追加することもできます。

![プライベート エンドポイントの選択のメッセージ](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 承認または拒否すると、一覧には応答テキストと共に適切な状態が反映されます

![プライベート エンドポイントの選択の最終状態](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Azure Database for MariaDB 用の Private Link のユースケース

クライアントは、同じ VNet または同じリージョン内でピアリングされた VNet から、またはリージョン間の VNet 間接続を介して、プライベート エンドポイントに接続できます。 さらに、クライアントは、ExpressRoute、プライベート ピアリング、または VPN トンネリングを使用して、オンプレミスから接続できます。 一般的なユース ケースを示す簡略化された図を以下に示します。

![プライベート エンドポイントの選択の概要](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>ピアリングされた Virtual Network (VNet) での Azure VM からの接続
[VNet ピアリング](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell)を構成して、ピアリングされた VNet 内の Azure VM から Azure Database for MariaDB への接続を確立します。

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet 間環境での Azure VM からの接続
[VNet 間 VPN ゲートウェイ接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)を構成して、別のリージョンまたはサブスクリプションの Azure VM から Azure Database for MariaDB への接続を確立します。

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>オンプレミス環境から VPN 経由の接続
オンプレミス環境から Azure Database for MariaDB への接続を確立するには、次のいずれかのオプションを選択して実装します。

* [ポイント対サイト接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [サイト間 VPN 接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute 回線](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Private Link とファイアウォール規則の組み合わせ

Private Link とファイアウォール規則を組み合わせて使用すると、次のような状況と結果になる可能性があります。

* ファイアウォール規則を構成していない場合、既定では、いずれのトラフィックも Azure Database for MariaDB にアクセスできません。

* パブリック トラフィックまたはサービス エンドポイントを構成し、プライベート エンドポイントを作成する場合、さまざまな種類の受信トラフィックが、対応する種類のファイアウォール規則によって承認されます。

* パブリック トラフィックまたはサービス エンドポイントを構成せずにプライベート エンドポイントを作成する場合、Azure Database for MariaDB にはプライベート エンドポイントからのみアクセスできます。 パブリック トラフィックまたはサービス エンドポイントを構成しない場合、すべての承認済みプライベート エンドポイントが拒否または削除されると、いずれのトラフィックも Azure Database for MariaDB にアクセスできません。

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Azure Database for MariaDB のパブリック アクセスの拒否

Azure Database for MariaDB にアクセスする方法をプライベート エンドポイントのみに完全に依存する場合、データベース サーバー上で **[パブリック ネットワーク アクセスの拒否]** 構成を設定し、すべてのパブリック エンドポイント設定 ([ファイアウォール規則](concepts-firewall-rules.md)や [VNet サービス エンドポイント](concepts-data-access-security-vnet.md)) を無効にできます。 

この設定が *[はい]* に設定されている場合、Azure Database for MariaDB にはプライベート エンドポイント経由の接続のみが許可されます。 この設定が *[いいえ]* に設定されている場合、ファイアウォール設定または VNet サービス エンドポイント設定に基づいてクライアントは Azure Database for MariaDB に接続できます。 さらに、プライベート ネットワーク アクセスの値が設定されると、お客様は既存の 'ファイアウォール規則' や 'VNet サービス エンドポイント規則' の追加も更新もできなくなります。

> [!Note]
> この機能は、Azure Database for PostgreSQL 単一サーバーで "汎用" および "メモリ最適化" の価格レベルがサポートされているすべての Azure リージョンで利用できます。
>
> この設定は、Azure Database for MariaDB の SSL 構成と TLS 構成に何の影響も与えません。

Azure portal から Azure Database for MariaDB の **[パブリック ネットワーク アクセスの拒否]** を設定する方法については、[パブリック ネットワーク アクセスの拒否を構成する](howto-deny-public-network-access.md)方法に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Database for MariaDB のセキュリティ機能の詳細については、次の記事を参照してください。

* Azure Database for MariaDB 用のファイアウォールを構成する場合は、[ファイアウォールのサポート](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)に関する記事を参照してください。

* Azure Database for MariaDB の仮想ネットワーク サービス エンドポイントを構成する方法については、[仮想ネットワークからのアクセスの構成](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)に関する記事を参照してください。

* Azure Database for MariaDB 接続の概要については、[Azure Database for MySQL の接続アーキテクチャ](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)に関する記事を参照してください

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md