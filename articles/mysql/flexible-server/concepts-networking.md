---
title: ネットワークの概要 - Azure Database for MySQL フレキシブル サーバー
description: Azure Database for MySQL のフレキシブル サーバー デプロイ オプションの接続およびネットワークのオプションについて説明します
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: a8e2d77ff3c7cb2e4352b21cd87d630331e28660
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906150"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL の接続およびネットワークの概念 - フレキシブル サーバー (プレビュー)

この記事では、サーバーのパブリックおよびプライベートの接続オプションについて説明します。 Azure でサーバーを安全に作成するため、Azure Database for MySQL フレキシブル サーバーのネットワークの概念について詳しく説明します。

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーはプレビュー段階です。

## <a name="choosing-a-networking-option"></a>ネットワーク オプションの選択
Azure Database for MySQL フレキシブル サーバーのネットワーク オプションは 2 つあります。 **プライベート アクセス (VNet 統合)** オプションと **パブリック アクセス (許可された IP アドレス)** オプションです。 サーバーの作成時に 1 つのオプションを選択する必要があります。 

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

プライベート アクセス オプションとパブリック アクセス オプションのどちらの使用を選ぶかに応じて以下の特性が当てはまります。
* 許可された IP アドレスからの接続では、有効な資格情報を使用して MySQL サーバーに対する認証を行う必要がある
* ネットワーク トラフィックに対して[接続の暗号化](#tls-and-ssl)を使用できる
* サーバーには完全修飾ドメイン名 (fqdn) があります。 接続文字列の hostname プロパティには、IP アドレスではなく fqdn を使用することをお勧めします。
* どちらのオプションでも、データベース レベルやテーブル レベルではなく、サーバー レベルでアクセスが制御されます。 MySQL のロールのプロパティを使用して、データベース、テーブル、およびその他のオブジェクトのアクセスを制御します。


## <a name="private-access-vnet-integration"></a>プライベート アクセス (VNet 統合)
仮想ネットワーク (vnet) 統合を使用したプライベート アクセスでは、MySQL フレキシブル サーバーに対する非公開の、セキュリティで保護された通信が提供されます。

### <a name="virtual-network-concepts"></a>仮想ネットワークの概念
以下に、MySQL フレキシブル サーバーと共に仮想ネットワークを使用する際に知っておく必要があるいくつかの概念を示します。

* **仮想ネットワーク** - Azure Virtual Network (VNet) には、実際の使用のために構成されたプライベート IP アドレス空間が含まれます。 Azure の仮想ネットワークの詳細については、[Azure Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

    ご利用の仮想ネットワークは、フレキシブル サーバーと同じ Azure リージョンに存在する必要があります。


* **委任されたサブネット** - 仮想ネットワークには、サブネット (サブネットワーク) が含まれています。 サブネットを使用すると、仮想ネットワークをより小さなアドレス空間に分割できます。 Azure リソースは、仮想ネットワーク内の特定のサブネットにデプロイされます。 

   MySQL フレキシブル サーバーは、MySQL フレキシブル サーバーでの使用のためにのみ **委任された** サブネット内に存在する必要があります。 この委任は、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。 その委任プロパティを Microsoft.DBforMySQL/flexibleServers として割り当てて、サブネットを委任します。

* **ネットワーク セキュリティ グループ (NSG)** ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 詳細については、[ネットワーク セキュリティ グループの概要](../../virtual-network/network-security-groups-overview.md)に関するページを参照してください。


### <a name="unsupported-virtual-network-scenarios"></a>サポートされない仮想ネットワークのシナリオ
* パブリック エンドポイント (またはパブリック IP あるいは DNS) - 仮想ネットワークにデプロイされたフレキシブル サーバーに、パブリック エンドポイントを設けることはできません
* フレキシブル サーバーを仮想ネットワークおよびサブネットにデプロイした後、それを別の仮想ネットワークまたはサブネットに移動することはできません。 仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。
* サブネットにリソースを配置すると、そのサブネットのサイズ (アドレス空間) を増やすことはできません
* 複数のリージョンにまたがる Vnet のピアリングはサポートされていません

[Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) を使用してプライベート アクセス (VNet 統合) を有効にする方法を確認します。

> [!NOTE]
> カスタム DNS サーバーを使用する場合は、Azure Database for MySQL - フレキシブル サーバーの FQDN を解決する DNS フォワーダーを使用する必要があります。 「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。

## <a name="public-access-allowed-ip-addresses"></a>パブリック アクセス (許可された IP アドレス)
パブリック アクセスの方法には、以下のような特性があります。
* 許可する IP アドレスだけが、MySQL フレキシブル サーバーにアクセスするためのアクセス許可を持ちます。 既定では、許可される IP アドレスはありません。 IP アドレスは、サーバーの作成時に追加すること、または後から追加することができます。
* MySQL サーバーにはパブリックに解決できる DNS 名がある
* どの Azure 仮想ネットワークにもフレキシブル サーバーが存在しない
* サーバーとの間のネットワーク トラフィックは、プライベート ネットワークを経由しません。 このトラフィックでは、一般的なインターネット経路が使用されます。

### <a name="firewall-rules"></a>ファイアウォール規則
IP アドレスへのアクセス許可を付与することは、ファイアウォール規則と呼ばれます。 許可されていない IP アドレスから接続が試みられた場合、発信元のクライアントにエラーが表示されます。


### <a name="allowing-all-azure-ip-addresses"></a>Azure のすべての IP アドレスの許可
ご使用の Azure サービスに対して固定の発信 IP アドレスが使用できない場合は、すべての Azure データセンターの IP アドレスからの接続を有効にすることを検討できます。

> [!IMPORTANT]
> **[Allow public access from Azure services and resources within Azure]\(Azure サービスおよび Azure 内のリソースからのパブリック アクセスを許可\)** オプションを選択すると、他の顧客のサブスクリプションからの接続を含め、Azure からのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

[Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) を使用して、パブリック アクセス (許可される IP アドレス) を有効にし、管理する方法を確認します。


### <a name="troubleshooting-public-access-issues"></a>パブリック アクセスに関する問題をトラブルシューティングする
Microsoft Azure Database for MySQL サーバー サービスに対するアクセスが予期したように動作しない場合は、以下の点について考えてください。

* **許可一覧に変更が反映されない:** Azure Database for MySQL サーバーのファイアウォール構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

* **認証に失敗した:** その Azure Database for MySQL サーバーで、ユーザーがアクセス許可を持っていないか、使用したパスワードが正しくない場合、Azure Database for MySQL サーバーへの接続は拒否されます。 ファイアウォール設定の作成によってクライアントに提供されるのは、サーバーへの接続を試行する機会のみです。 各クライアントは、必要なセキュリティ資格情報を提供する必要があることに変わりはありません。

* **動的なクライアント IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

   * Azure Database for MySQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲については、お使いのインターネット サービス プロバイダー (ISP) に問い合わせてください。その後、その IP アドレス範囲をファイアウォール規則として追加します。
   * 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。
  
* **ファイアウォール規則が IPv6 形式で使用できない:** ファイアウォール規則は IPv4 形式である必要があります。 IPv6 形式でファイアウォール規則を指定すると、検証エラーが表示されます。


## <a name="hostname"></a>Hostname (ホスト名)
選択するネットワーク オプションに関係なく、フレキシブル サーバーに接続する場合は常に、ホスト名として完全修飾ドメイン名 (FQDN) を使用することをお勧めします。 サーバーの IP アドレスが静的のままであることは保証されていません。 FQDN を使用すると、接続文字列を変更せずに済みます。 

例
* 推奨 `hostname = servername.mysql.database.azure.com`
* 可能な場合は `hostname = 10.0.0.4` (プライベート アドレス) や `hostname = 40.2.45.67` (パブリック IP) の使用を避ける



## <a name="tls-and-ssl"></a>TLS と SSL
Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS) を使用する、MySQL サービスへのクライアント アプリケーションの接続がサポートされます。 TLS は、データベース サーバーとクライアント アプリケーションの間で、暗号化されたネットワーク接続を保証する業界標準のプロトコルです。 TLS は、Secure Sockets Layer (SSL) が更新されたプロトコルです。

Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS 1.2) を使用して暗号化された接続のみがサポートされます。 TLS 1.0 および TLS 1.1 を使用する着信接続はすべて拒否されます。 Azure Database for MySQL フレキシブル サーバーへの接続のための TLS バージョンは、無効にしたり変更したりすることはできません。


## <a name="next-steps"></a>次のステップ
* [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) を使用してプライベート アクセス (VNet 統合) を有効にする方法を確認します
* [Azure portal](how-to-manage-firewall-portal.md) または [Azure CLI](how-to-manage-firewall-cli.md) を使用してパブリック アクセス (許可される IP アドレス) を有効にする方法を確認します
* [TLS を使用する](how-to-connect-tls-ssl.md)方法を確認します
