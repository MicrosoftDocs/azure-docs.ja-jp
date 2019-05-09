---
title: Azure Database for PostgreSQL サーバーの vnet サービス エンドポイントの概要
description: Azure Database for PostgreSQL サーバーで VNET サービス エンドポイントがどのように機能するかについて説明します。
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/20/2018
ms.openlocfilehash: c6549ad170a0fc3b4387d5bc5163ca0548b92119
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009193"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の Virtual Network のサービス エンドポイントとルールを使用する

*仮想ネットワーク ルール*は、Azure Database for PostgreSQL サーバーが仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御する 1 つのファイアウォール セキュリティ機能です。 この記事では、仮想ネットワーク ルール機能が、場合によっては Azure Database for PostgreSQL サーバーへの通信を安全に許可するための最善の選択になる理由を説明します。

仮想ネットワーク ルールを作成するには、まず、[仮想ネットワーク][vm-virtual-network-overview] (VNet) と参照するルールの[仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]が必要です。 次の図は、Virtual Network のサービス エンドポイントが Azure Database for PostgreSQL でどのように動作するかを示しています。

![VNet サービス エンドポイントの動作例](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> この機能は、Azure Database for PostgreSQL が汎用サーバーとメモリ最適化サーバー用にデプロイされている Azure パブリック クラウドのすべてのリージョンで利用できます。
> VNet ピアリングでは、トラフィックがサービス エンドポイントを含む共通 VNet Gateway を通過して、ピアにフローするようになっている場合は、ACL/VNet ルールを作成して、Gateway VNet 内の Azure Virtual Machines が Azure Database for PostgreSQL サーバーにアクセスできるようにしてください。

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>用語と説明

**仮想ネットワーク:** ご自分の Azure サブスクリプションに仮想ネットワークを関連付けることができます。

**サブネット:** 仮想ネットワークには**サブネット**が含まれます。 保持している任意の Azure 仮想マシン (VM) がサブネットに割り当てられます。 1 つのサブネットには、複数の VM や他のコンピューティング ノードが含まれる場合があります。 お使いの仮想ネットワークの外部にあるコンピューティング ノードは、アクセスを許可するようにセキュリティを構成しない限り、お使いの仮想ネットワークにはアクセスできません。

**Virtual Network サービス エンドポイント:**[Virtual Network サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 この記事では、"SQL Database" という名前の Azure サービスを参照する **Microsoft.Sql** という種類名に注目します。 このサービス タグは、Azure Database for PostgreSQL サービスと MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネットの Azure SQL Database、Azure Database for PostgreSQL、および Azure Database for MySQL のすべてのサーバーに対してサービス エンドポイント トラフィックが構成されることに注意することが重要です。 

**仮想ネットワーク ルール:** Azure Database for PostgreSQL サーバーの仮想ネットワーク ルールは、Azure Database for PostgreSQL サーバーのアクセス制御リスト (ACL) に記載されているサブネットです。 Azure Database for PostgreSQL の ACL 内に記載するためには、サブネットに **Microsoft.Sql** という種類名が含まれている必要があります。

仮想ネットワーク ルールは、サブネット上にあるどのノードからの通信も許可するように、Azure Database for PostgreSQL サーバーに指示します。







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>仮想ネットワーク規則の利点

操作を実行するまで、サブネット上の VM は Azure Database for PostgreSQL と通信できません。 通信を確立するアクションの 1 つは、仮想ネットワーク ルールの作成です。 VNet ルールの方法を選択する根拠については、ファイアウォールで提供される競合するセキュリティ オプションと比較対照して考察する必要があります。

### <a name="a-allow-access-to-azure-services"></a>A. Azure サービスへのアクセス許可

接続のセキュリティ ペインには、**[Azure サービスへのアクセスを許可]** とラベル付けされた **[オン/オフ]** ボタンがあります。 **[オン]** 設定は、すべての Azure IP アドレスと Azure サブネットからの通信を許可します。 これらの Azure IP またはサブネットは、ユーザーが所有していない場合もあります。 この **[オン]** 設定は、おそらくは Azure Database for PostgreSQL Database に期待する範囲を超えて開かれています。 仮想ネットワーク規則機能によって、さらにきめ細かい制御が提供されます。

### <a name="b-ip-rules"></a>B. IP 規則

Azure Database for PostgreSQL のファイアウォールでは、Azure Database for PostgreSQL Database への通信が許可される IP アドレス範囲を指定できます。 この方法は、Azure プライベート ネットワークの外部にある安定した IP アドレスに適しています。 しかし、Azure プライベート ネットワーク内にある多数のノードは、*動的* IP アドレスで構成されています。 動的 IP アドレスは、VM が再起動されたときなどに変更される場合があります。 運用環境では、ファイアウォール規則に動的 IP アドレスを指定することは、賢明ではありません。

お使いの VM 用に*静的* IP アドレスを取得することで、IP のオプションを復旧することができます。 詳細については、「[Azure Portal を使用して仮想マシンのプライベート IP アドレスを構成する][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]」をご覧ください。

ただし、静的 IP の方法は管理が困難になる場合があり、まとめて実行すると負荷がかかります。 仮想ネットワーク規則を確立して管理するほうが簡単です。

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. サービス エンドポイントを定義せずにサブネット上に Azure Database for PostgreSQL を保持することは、まだできません

**Microsoft.Sql** サーバーが仮想ネットワーク内のサブネット上のノードであった場合、仮想ネットワーク内のすべてのノードは Azure Database for PostgreSQL サーバーと通信できました。 この場合、仮想ネットワーク ルールや IP ルールがなくても、VM は Azure Database for PostgreSQL と通信できました。

しかし、2018 年 8 月時点ではまだ、Azure Database for PostgreSQL サービスは、サブネットに直接割り当てることができるサービスではありません。

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>仮想ネットワーク規則の詳細

このセクションでは、仮想ネットワーク規則に関する詳細について、いくつか説明します。

### <a name="only-one-geographic-region"></a>geography 型の 1 つのリージョンのみ

各 Virtual Network サービス エンドポイントは、1 つの Azure リージョンだけに適用されます。 エンドポイントが他のリージョンを有効にして、サブネットからの通信を許可することはありません。

どの仮想ネットワーク規則も、その基本となるエンドポイントが適用先のリージョンに制限されます。

### <a name="server-level-not-database-level"></a>データベース レベルではなく、サーバー レベル

各仮想ネットワーク ルールは、サーバー上の 1 つの特定のデータベースだけではなく、Azure Database for PostgreSQL サーバー全体に適用されます。 言い換えると、仮想ネットワーク規則はデータベース レベルではなく、サーバー レベルに適用されます。

#### <a name="security-administration-roles"></a>セキュリティ管理ロール

Virtual Network サービス エンドポイントの管理では、セキュリティ ロールが分離されています。 以下の各ロールでは、次の操作が必要です。

- **ネットワーク管理者:**&nbsp; エンドポイントを有効にします。
- **データベース管理者:**&nbsp; アクセス制御リスト (ACL) を更新して、指定されたサブネットを Azure Database for PostgreSQL サーバーに追加します。

*RBAC による代替:*

ネットワーク管理およびデータベース管理のロールには、仮想ネットワーク規則の管理に必要とされる機能以外もあります。 それらの機能のうち 1 つのサブネットだけが必要になります。

必要な機能のサブネットのみを保持する単一のカスタム ロールを作成するために、Azure には[ロールベースのアクセス制御 (RBAC)][rbac-what-is-813s] を使用するオプションがあります。 ネットワーク管理またはデータベース管理に関連付ける代わりに、カスタム ロールを使用できます。カスタム ロールにユーザーを追加する場合と、他の 2 つの主要な管理者ロールにユーザーを追加する場合では、前者の方がセキュリティ脅威にさらされる領域が少なくなります。

> [!NOTE]
> Azure Database for PostgreSQL と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションが同じ Azure Active Directory テナントに存在する必要がある。
> - ユーザーに操作 (サービス エンドポイントの有効化や、特定のサーバーへの VNet サブネットの追加など) を開始するために必要な権限がある。

## <a name="limitations"></a>制限事項

Azure Database for PostgreSQL の場合、仮想ネットワーク ルール機能には以下のような制限事項があります。

- Web アプリは、VNet/サブネット内のプライベート IP にマップできます。 サービス エンドポイントが特定の VNet/サブネットから有効化されている場合でも、Web アプリからサーバーへの接続には、VNet/サブネットのソースではなく、Azure のパブリック IP ソースが使用されます。 サーバーに VNet ファイアウォール規則がある場合、Web アプリからそのサーバーへの接続を有効にするには、サーバーで Azure サービスにサーバーへのアクセスを許可する必要があります。

- Azure Database for PostgreSQL のファイアウォールでは、各仮想ネットワーク ルールはサブネットを参照します。 これらの参照されるサブネットはすべて、Azure Database for PostgreSQL がホストされているのと同じ地理的リージョンでホストされている必要があります。

- 各 Azure Database for PostgreSQL サーバーは、指定された仮想ネットワークに対して最大 128 個までの ACL エントリを保持できます。

- 仮想ネットワーク規則は[クラシック デプロイ モデル][arm-deployment-model-568f] ネットワークではなく、Azure Resource Manager の仮想ネットワークのみに適用されます。

- **Microsoft.Sql**  サービス タグを使用して Azure Database for PostgreSQL への仮想ネットワーク サービス エンドポイントをオンにすると、次のすべての Azure Database サービスのエンドポイントも有効になります:Azure Database for MySQL、Azure Database for PostgreSQL、Azure SQL Database、Azure SQL Data Warehouse。

- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

- ファイアウォールでは、IP アドレスは以下のネットワーク項目に適用されますが、仮想ネットワーク規則は適用されません。
    - [サイト間 (S2S) 仮想プライベート ネットワーク (VPN)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v] 経由のオンプレミス

## <a name="expressroute"></a>ExpressRoute

ネットワークが [ExpressRoute][expressroute-indexmd-744v] を使用して Azure ネットワークに接続されている場合、各回線は、Microsoft Edge で 2 つのパブリック IP アドレスを使用して構成されています。 2 つの IP アドレスは、Azure パブリック ピアリングを使用して、Azure Storage などの Microsoft サービスへの接続に使用されます。

回線から Azure Database for PostgreSQL への通信を許可するには、回線のパブリック IP アドレスに対する IP ネットワーク ルールを作成する必要があります。 ExpressRoute 回線のパブリック IP アドレスを見つけるには、Azure Portal を使用して ExpressRoute のサポート チケットを開きます。

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET サービス エンドポイントをオンにすることなく VNET ファイアウォール規則をサーバーに追加する

単にファイアウォール規則を設定するだけでは、VNet へのサーバーのセキュリティ保護には役立ちません。 セキュリティを有効にするには、VNet サービス エンドポイントを**オン**にする必要もあります。 サービス エンドポイントを**オン**にする場合、**オフ**から**オン**への切り替えが完了するまで VNet サブネットでダウンタイムが発生します。 これは、大規模 VNet のコンテキストに特に当てはまります。 **IgnoreMissingServiceEndpoint** フラグを使用すると、切り替え中のダウンタイムを軽減または除去できます。

**IgnoreMissingServiceEndpoint** フラグは、Azure CLI またはポータルを使用して設定できます。

## <a name="related-articles"></a>関連記事
- [Azure 仮想ネットワーク][vm-virtual-network-overview]
- [Azure 仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>次の手順
VNet ルールの作成については、以下の記事を参照してください。
- [Azure portal を使用した Azure Database for PostgreSQL VNet ルールの作成と管理](howto-manage-vnet-using-portal.md)
- [Azure CLI を使用した Azure Database for PostgreSQL VNet ルールの作成と管理](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml
