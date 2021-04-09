---
title: ネットワークの概要 - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure Database for PostgreSQL のフレキシブル サーバー デプロイ オプションの接続およびネットワークのオプションについて説明します
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/21/2021
ms.openlocfilehash: a6f049670a6860bbc195b92458945d1a53029b4f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732804"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>ネットワークの概要 - Azure Database for PostgreSQL - フレキシブル サーバー

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

この記事では、Azure Database for PostgreSQL - フレキシブル サーバーの接続とネットワークの概念について説明します。 

## <a name="choosing-a-networking-option"></a>ネットワーク オプションの選択
Azure Database for PostgreSQL - フレキシブル サーバーのネットワーク オプションは 2 つあります。 これらのオプションは、**プライベート アクセス (VNet 統合)** と **パブリック アクセス (許可されている IP アドレス)** です。 サーバーの作成時に 1 つのオプションを選択する必要があります。 

> [!NOTE]
> サーバーを作成した後にネットワーク オプションを変更することはできません。 

* **プライベート アクセス (VNet 統合)** – お使いの [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) 内にフレキシブル サーバーをデプロイできます。 Azure の仮想ネットワークでは、非公開の、セキュリティで保護されたネットワーク通信が提供されます。 仮想ネットワーク内のリソースでは、プライベート IP アドレスを通した通信が可能です。

   以下の機能が必要な場合は、VNet 統合オプションを選択します。
   * プライベート IP アドレスを使用して、同じ仮想ネットワーク内の Azure リソースからフレキシブル サーバーに接続する
   * VPN または ExpressRoute を使用して Azure 以外のリソースからフレキシブル サーバーに接続する
   * フレキシブル サーバーにパブリック エンドポイントがない

* **パブリック アクセス (許可されている IP アドレス)** - フレキシブル サーバーには、パブリック エンドポイントを介してアクセスされます。 パブリック エンドポイントは、パブリックに解決できる DNS アドレスです。 "許可された IP アドレス" という表現は、サーバーに対するアクセス許可を付与することを選択する、ある範囲の IP を指しています。 これらのアクセス許可は、**ファイアウォール規則** と呼ばれます。 

   次の機能が必要な場合は、パブリック アクセスの方法を選択します。
   * 仮想ネットワークをサポートしていない Azure リソースから接続する
   * VPN または ExpressRoute によって接続されていない Azure 外部のリソースから接続する 
   * フレキシブル サーバーにパブリック エンドポイントがある

プライベート アクセスまたはパブリック アクセスのどちらのオプションを使用するかに関係なく、次の特性が適用されます。
* 許可されている IP アドレスからの接続では、有効な資格情報を使用して、PostgreSQL サーバーに対して認証を行う必要がある
* ネットワーク トラフィックに対して[接続の暗号化](#tls-and-ssl)を使用できる
* サーバーには完全修飾ドメイン名 (fqdn) があります。 接続文字列の hostname プロパティには、IP アドレスではなく fqdn を使用することをお勧めします。
* どちらのオプションでも、データベース レベルやテーブル レベルではなく、サーバー レベルでアクセスが制御されます。 PostgreSQL のロールのプロパティを使用して、データベース、テーブル、その他のオブジェクトのアクセスを制御します。


## <a name="private-access-vnet-integration"></a>プライベート アクセス (VNet 統合)
仮想ネットワーク (vnet) 統合を使用したプライベート アクセスでは、PostgreSQL フレキシブル サーバーに対する非公開で、セキュリティで保護された通信が提供されます。

### <a name="virtual-network-concepts"></a>仮想ネットワークの概念
PostgreSQL フレキシブル サーバーで仮想ネットワークを使用する場合、次の概念について十分に理解しておく必要があります。

* **仮想ネットワーク** - Azure Virtual Network (VNet) には、自分用に構成されたプライベート IP アドレス空間が含まれています。 Azure の仮想ネットワークの詳細については、[Azure Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

    ご利用の仮想ネットワークは、フレキシブル サーバーと同じ Azure リージョンに存在する必要があります。


* **委任されたサブネット** - 仮想ネットワークには、サブネット (サブネットワーク) が含まれています。 サブネットを使用すると、仮想ネットワークをより小さなアドレス空間に分割できます。 Azure リソースは、仮想ネットワーク内の特定のサブネットにデプロイされます。 

   PostgreSQL フレキシブル サーバーは、PostgreSQL フレキシブル サーバー専用に **委任された** サブネット内に存在する必要があります。 この委任は、Azure Database for PostgreSQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。 サブネットを委任するには、その委任プロパティを Microsoft.DBforPostgreSQL/flexibleServers として割り当てます。

   フレキシブル サーバーに委任されたサブネット用に、`Microsoft.Storage` をサービス エンドポイントに追加します。 

* **ネットワーク セキュリティ グループ (NSG)** ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 詳細については、[ネットワーク セキュリティ グループの概要](../../virtual-network/network-security-groups-overview.md)に関するページを参照してください。


### <a name="unsupported-virtual-network-scenarios"></a>サポートされない仮想ネットワークのシナリオ
* パブリック エンドポイント (またはパブリック IP あるいは DNS) - 仮想ネットワークにデプロイされたフレキシブル サーバーに、パブリック エンドポイントを設けることはできません
* フレキシブル サーバーを仮想ネットワークおよびサブネットにデプロイした後、それを別の仮想ネットワークまたはサブネットに移動することはできません。 仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。
* サブネットにリソースを配置すると、そのサブネットのサイズ (アドレス空間) を増やすことはできません
* 複数のリージョンにまたがる Vnet のピアリングはサポートされていません

プライベート アクセス (VNet 統合) を使用するフレキシブル サーバーを [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) で作成する方法を参照してください。

> [!NOTE]
> カスタム DNS サーバーを使用する場合は、Azure Database for PostgreSQL - フレキシブル サーバーの FQDN を解決する DNS フォワーダーを使用する必要があります。 「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。

## <a name="public-access-allowed-ip-addresses"></a>パブリック アクセス (許可された IP アドレス)
パブリック アクセスの方法には、次のような特性があります。
* PostgreSQL フレキシブル サーバーにアクセスするためのアクセス許可があるのは、許可される IP アドレスだけです。 既定では、許可される IP アドレスはありません。 IP アドレスは、サーバーの作成時に追加するか、後から追加することもできます。
* PostgreSQL サーバーには、パブリックに解決できる DNS 名があります
* フレキシブル サーバーは、Azure 仮想ネットワークのいずれにも存在しません
* サーバーとの間のネットワーク トラフィックは、プライベート ネットワークを経由しません。 このトラフィックでは、一般的なインターネット経路が使用されます。

### <a name="firewall-rules"></a>ファイアウォール規則
IP アドレスへのアクセス許可を付与することは、ファイアウォール規則と呼ばれます。 許可されていない IP アドレスから接続が試みられた場合、発信元のクライアントにエラーが表示されます。

パブリック アクセス (許可されている IP アドレス) を使用するフレキシブル サーバーを [Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) で作成する方法を参照してください。


### <a name="allowing-all-azure-ip-addresses"></a>すべての Azure IP アドレスを許可する
ご使用の Azure サービスに対して固定の発信 IP アドレスが使用できない場合は、すべての Azure データセンターの IP アドレスからの接続を有効にすることを検討できます。

> [!IMPORTANT]
> **[Allow public access from Azure services and resources within Azure]\(Azure サービスおよび Azure 内のリソースからのパブリック アクセスを許可\)** オプションを選択すると、他の顧客のサブスクリプションからの接続を含め、Azure からのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

### <a name="troubleshooting-public-access-issues"></a>パブリック アクセスに関する問題のトラブルシューティング
Microsoft Azure Database for PostgreSQL サーバー サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

* **許可一覧に変更が反映されない:** Azure Database for PostgreSQL サーバーのファイアウォール構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

* **認証に失敗した:** ユーザーが Azure Database for PostgreSQL サーバーに対するアクセス許可を持っていないか、使用されたパスワードが正しくない場合、Azure Database for PostgreSQL サーバーへの接続は拒否されます。 ファイアウォール設定を作成しても、クライアントはサーバーへの接続を試行できるようになるだけです。 各クライアントは、必要なセキュリティ資格情報を提供する必要があることに変わりはありません。

* **動的なクライアント IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

   * Azure Database for PostgreSQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として、その IP アドレス範囲を追加してください。
   * 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。

* **ファイアウォール規則が IPv6 形式で使用できない:** ファイアウォール規則は IPv4 形式である必要があります。 IPv6 形式でファイアウォール規則を指定すると、検証エラーが表示されます。

## <a name="hostname"></a>Hostname (ホスト名)
選択するネットワーク オプションに関係なく、フレキシブル サーバーに接続する場合は常に、ホスト名として完全修飾ドメイン名 (FQDN) を使用することをお勧めします。 サーバーの IP アドレスが静的のままであることは保証されていません。 FQDN を使用すると、接続文字列を変更せずに済みます。 

例
* 推奨 `hostname = servername.postgres.database.azure.com`
* 可能であれば、`hostname = 10.0.0.4` (プライベート アドレス) または `hostname = 40.2.45.67` (パブリック アドレス) の使用は避けてください


## <a name="tls-and-ssl"></a>TLS と SSL
Azure Database for PostgreSQL - フレキシブル サーバーでは、トランスポート層セキュリティ (TLS) を使用して、クライアント アプリケーションを PostgreSQL サービスに接続できます。 TLS は、データベース サーバーとクライアント アプリケーションの間で、暗号化されたネットワーク接続を保証する業界標準のプロトコルです。 TLS は、SSL (Secure Sockets Layer) を更新したプロトコルです。

Azure Database for PostgreSQL - フレキシブル サーバーでは、トランスポート層セキュリティを使用して暗号化された接続のみがサポートされます。 TLS 1.0 および TLS 1.1 を使用する着信接続はすべて拒否されます。 

## <a name="next-steps"></a>次のステップ
* **プライベート アクセス (VNet 統合)** を使用するフレキシブル サーバーを [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) で作成する方法を参照してください。
* **パブリック アクセス (許可されている IP アドレス)** を使用するフレキシブル サーバーを [Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) で作成する方法を参照してください。
