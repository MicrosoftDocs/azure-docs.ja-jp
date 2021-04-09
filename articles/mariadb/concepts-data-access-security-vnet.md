---
title: VNet サービス エンドポイント - Azure Database for MariaDB
description: VNet サービス エンドポイントが Azure Database for MariaDB サーバーに対してどのように機能するかについて説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/17/2020
ms.openlocfilehash: bd7d08e4f65612b9a76b63e8153603d043209ad3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662680"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>仮想ネットワーク サービス エンドポイントと規則を Azure Database for MariaDB に対して使用する

*仮想ネットワーク規則* は、Azure Database for MariaDB サーバーが仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御する 1 つのファイアウォール セキュリティ機能です。 この記事では、仮想ネットワーク規則機能が、場合によっては Azure Database for MariaDB サーバーへの通信を安全に許可するための最善の選択になる理由を説明します。

仮想ネットワーク規則を作成するには、まず、[仮想ネットワーク][vm-virtual-network-overview] (VNet) と参照する規則の[仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]が必要です。 次の図は、仮想ネットワーク サービス エンドポイントが Azure Database for MariaDB でどのように動作するかを示しています。

![VNet サービス エンドポイントの動作例](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> この機能は、Azure Database for MariaDB が汎用サーバーとメモリ最適化サーバー用にデプロイされている Azure のすべてのリージョンで利用できます。

また、接続に [Private Link](concepts-data-access-security-private-link.md) の使用を検討することもできます。 Private Link を使用すると、VNet 内のプライベート IP アドレスが Azure Database for MariaDB サーバーに提供されます。

<a name="anch-terminology-and-description-82f"></a>

## <a name="terminology-and-description"></a>用語と説明

**仮想ネットワーク:** ご自分の Azure サブスクリプションに仮想ネットワークを関連付けることができます。

**サブネット:** 仮想ネットワークには **サブネット** が含まれます。 保持している任意の Azure 仮想マシン (VM) がサブネットに割り当てられます。 1 つのサブネットには、複数の VM や他のコンピューティング ノードが含まれる場合があります。 お使いの仮想ネットワークの外部にあるコンピューティング ノードは、アクセスを許可するようにセキュリティを構成しない限り、お使いの仮想ネットワークにはアクセスできません。

**仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 この記事では、"SQL Database" という名前の Azure サービスを参照する **Microsoft.Sql** という種類名に注目します。 このサービス タグは、Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL の各サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、Azure SQL Database、Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL のすべてのサーバーに対してサービス エンドポイント トラフィックがサブネット上に構成されることに注意することが重要です。

**仮想ネットワーク規則:** Azure Database for MariaDB サーバーの仮想ネットワーク規則は、Azure Database for MariaDB サーバーのアクセス制御リスト (ACL) に記載されているサブネットです。 Azure Database for MariaDB サーバーの ACL 内に記載するためには、サブネットに **Microsoft.Sql** という種類名が含まれている必要があります。

仮想ネットワーク規則は、サブネット上にあるどのノードからの通信も許可するように、お使いの Azure Database for MariaDB サーバーに指示します。







<a name="anch-benefits-of-a-vnet-rule-68b"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>仮想ネットワーク規則の利点

操作を実行するまで、サブネット上の VM は Azure Database for MariaDB サーバーと通信できません。 通信を確立するアクションの 1 つは、仮想ネットワーク規則の作成です。 VNet ルールの方法を選択する根拠については、ファイアウォールで提供される競合するセキュリティ オプションと比較対照して考察する必要があります。

### <a name="a-allow-access-to-azure-services"></a>A. Azure サービスへのアクセス許可

接続のセキュリティ ペインには、 **[Azure サービスへのアクセスを許可]** とラベル付けされた **[オン/オフ]** ボタンがあります。 **[オン]** 設定は、すべての Azure IP アドレスと Azure サブネットからの通信を許可します。 これらの Azure IP またはサブネットは、ユーザーが所有していない場合もあります。 この **[オン]** 設定は、おそらく Azure Database for MariaDB Database に期待する範囲を超えて開かれています。 仮想ネットワーク規則機能によって、さらにきめ細かい制御が提供されます。

### <a name="b-ip-rules"></a>B. IP 規則

Azure Database for MariaDB のファイアウォールでは、Azure Database for MariaDB サーバーへの通信が許可される IP アドレス範囲を指定できます。 この方法は、Azure プライベート ネットワークの外部にある安定した IP アドレスに適しています。 しかし、Azure プライベート ネットワーク内にある多数のノードは、*動的* IP アドレスで構成されています。 動的 IP アドレスは、VM が再起動されたときなどに変更される場合があります。 運用環境では、ファイアウォール規則に動的 IP アドレスを指定することは、賢明ではありません。

お使いの VM 用に *静的* IP アドレスを取得することで、IP のオプションを復旧することができます。 詳細については、「[Azure portal を使用して仮想マシンのプライベート IP アドレスを構成する][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]」をご覧ください。

ただし、静的 IP の方法は管理が困難になる場合があり、まとめて実行すると負荷がかかります。 仮想ネットワーク規則を確立して管理するほうが簡単です。


<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>仮想ネットワーク規則の詳細

このセクションでは、仮想ネットワーク規則に関する詳細について、いくつか説明します。

### <a name="only-one-geographic-region"></a>geography 型の 1 つのリージョンのみ

各仮想ネットワーク サービス エンドポイントは、1 つの Azure リージョンだけに適用されます。 エンドポイントが他のリージョンを有効にして、サブネットからの通信を許可することはありません。

どの仮想ネットワーク規則も、その基本となるエンドポイントが適用先のリージョンに制限されます。

### <a name="server-level-not-database-level"></a>データベース レベルではなく、サーバー レベル

各仮想ネットワーク規則は、サーバー上の 1 つの特定のデータベースだけではなく、お使いの Azure Database for MariaDB サーバー全体に適用されます。 言い換えると、仮想ネットワーク規則はデータベース レベルではなく、サーバー レベルに適用されます。

### <a name="security-administration-roles"></a>セキュリティ管理ロール

仮想ネットワーク サービス エンドポイントの管理では、セキュリティ ロールが分離されています。 以下の各ロールでは、次の操作が必要です。

- **ネットワーク管理者:** &nbsp; エンドポイントを有効にします。
- **データベース管理者:** &nbsp; アクセス制御リスト (ACL) を更新して Azure Database for MariaDB サーバーに特定のサブネットを追加します。

"*Azure RBAC による代替:* "

ネットワーク管理およびデータベース管理のロールには、仮想ネットワーク規則の管理に必要とされる機能以外もあります。 それらの機能のうち 1 つのサブネットだけが必要になります。

必要な機能のサブセットのみを保持する単一のカスタム ロールを作成するために、Azure には [Azure ロールベースのアクセス制御 (Azure RBAC)][rbac-what-is-813s] を使用するオプションがあります。 ネットワーク管理またはデータベース管理に関連付ける代わりに、カスタム ロールを使用できます。カスタム ロールにユーザーを追加する場合と、他の 2 つの主要な管理者ロールにユーザーを追加する場合では、前者の方がセキュリティ脅威にさらされる領域が少なくなります。

> [!NOTE]
> Azure Database for MariaDB と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションが同じ Azure Active Directory テナントに存在する必要がある。
> - ユーザーに操作 (サービス エンドポイントの有効化や、特定のサーバーへの VNet サブネットの追加など) を開始するために必要な権限がある。
> - 両方のサブスクリプションに **Microsoft.Sql** および **Microsoft.DBforMariaDB** リソース プロバイダーが確実に登録されている。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

## <a name="limitations"></a>制限事項

Azure Database for MariaDB の場合、仮想ネットワーク規則機能には以下のような制限事項があります。

- Web アプリは、VNet/サブネット内のプライベート IP にマップできます。 サービス エンドポイントが特定の VNet/サブネットから有効化されている場合でも、Web アプリからサーバーへの接続には、VNet/サブネットのソースではなく、Azure のパブリック IP ソースが使用されます。 サーバーに VNet ファイアウォール規則がある場合、Web アプリからそのサーバーへの接続を有効にするには、サーバーで Azure サービスにサーバーへのアクセスを許可する必要があります。

- Azure Database for MariaDB のファイアウォールでは、各仮想ネットワーク規則はサブネットを参照します。 これらの参照されるサブネットはすべて、Azure Database for MariaDB がホストされているのと同じ geography 型のリージョンでホストされている必要があります。

- 各 Azure Database for MariaDB サーバーは、指定された仮想ネットワークに対して最大 128 個までの ACL エントリを保持できます。

- 仮想ネットワーク規則は[クラシック デプロイ モデル][resource-manager-deployment-model-568f] ネットワークではなく、Azure Resource Manager の仮想ネットワークのみに適用されます。

- **Microsoft.Sql** サービス タグを使用して Azure Database for MariaDB への仮想ネットワーク サービス エンドポイントを有効にすると、次のすべての Azure データベース サービスのエンドポイントも有効になります。Azure Database for MariaDB、Azure Database for MySQL、Azure Database for PostgreSQL、Azure SQL Database、および Azure Synapse Analytics。

- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

- サブネットで **Microsoft.Sql** が有効になっている場合は、VNet 規則のみを使用して接続することを指定します。 このサブネット内のリソースの [VNet 以外のファイアウォール規則](concepts-firewall-rules.md)は機能しません。

- ファイアウォールでは、IP アドレスは以下のネットワーク項目に適用されますが、仮想ネットワーク規則は適用されません。
    - [サイト間 (S2S) 仮想プライベート ネットワーク (VPN)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v] 経由のオンプレミス

## <a name="expressroute"></a>ExpressRoute

ネットワークが [ExpressRoute][expressroute-indexmd-744v] を使用して Azure ネットワークに接続されている場合、各回線は、Microsoft Edge で 2 つのパブリック IP アドレスを使用して構成されています。 2 つの IP アドレスは、Azure パブリック ピアリングを使用して、Azure Storage などの Microsoft サービスへの接続に使用されます。

回線から Azure Database for MariaDB への通信を許可するには、回線のパブリック IP アドレスに対する IP ネットワーク規則を作成する必要があります。 ExpressRoute 回線のパブリック IP アドレスを見つけるには、Azure Portal を使用して ExpressRoute のサポート チケットを開きます。

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET サービス エンドポイントをオンにすることなく VNET ファイアウォール規則をサーバーに追加する

単に VNet ファイアウォール規則を設定するだけでは、VNet へのサーバーのセキュリティ保護には役立ちません。 セキュリティを有効にするには、VNet サービス エンドポイントを **オン** にする必要もあります。 サービス エンドポイントを **オン** にする場合、**オフ** から **オン** への切り替えが完了するまで VNet サブネットでダウンタイムが発生します。 これは、大規模 VNet のコンテキストに特に当てはまります。 **IgnoreMissingServiceEndpoint** フラグを使用すると、切り替え中のダウンタイムを軽減または除去できます。

**IgnoreMissingServiceEndpoint** フラグは、Azure CLI またはポータルを使用して設定できます。

## <a name="related-articles"></a>関連記事
- [Azure 仮想ネットワーク][vm-virtual-network-overview]
- [Azure 仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>次のステップ
VNet ルールの作成については、以下の記事を参照してください。
- [Azure portal を使用した Azure Database for MariaDB VNet 規則の作成と管理](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md