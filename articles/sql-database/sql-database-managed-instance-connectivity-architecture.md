---
title: Azure SQL Database マネージド インスタンス用の接続アーキテクチャ | Microsoft Docs
description: Azure SQL Database マネージド インスタンスの通信および接続アーキテクチャと、コンポーネントによるマネージド インスタンスへのトラフィックの誘導方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: f08b22f24dfde41646f56dc1ecd9777f267620ee
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651314"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Azure SQL Database マネージド インスタンス用の接続アーキテクチャ 

この記事では、Azure SQL Database マネージド インスタンスの通信について説明します。 接続アーキテクチャと、コンポーネントによるマネージド インスタンスへのトラフィックの誘導方法についても説明します。  

SQL Database マネージド インスタンスは、マネージド インスタンス専用の Azure 仮想ネットワークとサブネットの内部に配置されます。 このデプロイでは、以下が提供されます。

- セキュリティで保護されたプライベート IP アドレス。
- オンプレミス ネットワークをマネージド インスタンスに接続する機能。
- マネージド インスタンスをリンク サーバーまたは別のオンプレミス データ ストアに接続する機能。
- マネージド インスタンスを Azure リソースに接続する機能。

## <a name="communication-overview"></a>通信の概要

次の図は、マネージド インスタンスに接続するエンティティを示しています。 マネージド インスタンスと通信するために必要なリソースも示されています。 図の一番下にある通信プロセスは、マネージド インスタンスにデータ ソースとして接続するお客様のアプリケーションとツールを表しています。  

![接続アーキテクチャのエンティティ](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

マネージド インスタンスは、PaaS (サービスとしてのプラットフォーム) サービスです。 Microsoft では、自動化されたエージェント (管理、デプロイ、およびメンテナンス) を使用して、テレメトリ データ ストリームに基づいてこのサービスを管理します。 管理責任は Microsoft にあるため、お客様はリモート デスクトップ プロトコル (RDP) を通してマネージド インスタンス仮想クラスター マシンにアクセスすることはできません。

エンド ユーザーまたはアプリケーションによって開始される SQL Server 操作の中には、マネージド インスタンスとプラットフォームとの間の対話を必要とするものがあります。 一例として、マネージド インスタンス データベースの作成があります。 このリソースは、Azure portal、PowerShell、Azure CLI、および REST API を介して公開されます。

マネージド インスタンスは、Azure サービスに依存しています (たとえば、バックアップは Azure Storage、テレメトリは Azure Service Bus、認証は Azure Active Directory、Transparent Data Encryption (TDE) は Azure Key Vault)。 マネージド インスタンスは、これらのサービスへの接続を行います。

すべての通信で、暗号化と署名用の証明書が使用されます。 通信相手の信頼性を確認するために、マネージド インスタンスでは、証明機関と常に接触してこれらの証明書が確認されます。 証明書が失効しているか確認できない場合、マネージド インスタンスではデータを保護するために接続が終了します。

## <a name="high-level-connectivity-architecture"></a>接続アーキテクチャの概要

おおまかに言えば、マネージド インスタンスは、一連のサービス コンポーネントです。 これらのコンポーネントは、お客様の仮想ネットワーク サブネット内で実行される分離された専用の仮想マシン セット上でホストされます。 これらのマシンによって仮想クラスターが形成されます。

1 つの仮想クラスターで、複数のマネージド インスタンスをホストできます。 サブネットにプロビジョニングされるインスタンスの数をお客様が変更した場合、クラスターは、必要に応じて自動的に拡張または縮小されます。

お客様のアプリケーションがマネージド インスタンスに接続し、クエリを実行してデータベースを更新できるのは、そのアプリケーションが、仮想ネットワーク、ピアリングされた仮想ネットワーク、または VPN か Azure ExpressRoute によって接続されたネットワーク内で実行されている場合だけです。 このネットワークでは、エンドポイントとプライベート IP アドレスを使用する必要があります。  

![接続アーキテクチャ図](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft の管理およびデプロイ サービスは、仮想ネットワークの外部で実行されます。 マネージド インスタンスと Microsoft サービスは、パブリック IP アドレスを持つエンドポイント経由で接続されます。 マネージド インスタンスで送信接続を作成する場合、受信側では、ネットワーク アドレス変換 (NAT) によって、このパブリック IP を発信元とする接続であるかのように見えます。

管理トラフィックは、お客様の仮想ネットワーク内を通過します。 つまり、仮想ネットワークのインフラストラクチャの要素は、インスタンスを失敗させて利用不可にすることで管理トラフィックに害を及ぼす可能性があります。

> [!IMPORTANT]
> カスタマー エクスペリエンスとサービスの可用性を高めるために、Microsoft では、Azure Virtual Network インフラストラクチャの要素に対してネットワーク インテント ポリシーを適用しています。 このポリシーによって、マネージド インスタンスの動作に影響が出る可能性があります。 このプラットフォーム メカニズムは、ネットワーク要件をユーザーに透過的に伝達します。 このポリシーの主要目的は、ネットワークの構成ミスを防ぎ、マネージド インスタンスの通常の動作を確保することです。 マネージド インスタンスを削除すると、ネットワーク インテント ポリシーも削除されます。

## <a name="virtual-cluster-connectivity-architecture"></a>仮想クラスターの接続アーキテクチャ

マネージド インスタンスの接続アーキテクチャについて、さらに踏み込んでみましょう。 次の図は、仮想クラスターの概念上のレイアウトを示しています。

![仮想クラスターの接続アーキテクチャ](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

クライアントは、`<mi_name>.<dns_zone>.database.windows.net` 形式のホスト名を使用して、マネージド インスタンスに接続します。 このホスト名は、パブリック ドメイン ネーム システム (DNS) ゾーンに登録されていてパブリックに解決できますが、プライベート IP アドレスに解決されます。 クラスターを作成すると、`zone-id` が自動的に生成されます。 新しく作成されたクラスターで 2 番目のマネージド インスタンスがホストされる場合は、プライマリ クラスターとゾーン ID が共有されます。 詳細については、「[自動フェールオーバー グループを使用して、複数のデータベースの透過的な調整されたフェールオーバーを有効にする](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)」を参照してください。

このプライベート IP アドレスは、マネージド インスタンスの内部ロード バランサーに属しています。 ロード バランサーは、マネージド インスタンスのゲートウェイにトラフィックを送信します。 同じクラスター内で複数のマネージド インスタンスを実行できるため、ゲートウェイでは、マネージド インスタンスのホスト名を使用して、トラフィックを適切な SQL Engine サービスにリダイレクトします。

管理およびデプロイ サービスでは、外部ロード バランサーにマッピングされる[管理エンドポイント](#management-endpoint)を使用して、マネージド インスタンスに接続します。 トラフィックは、マネージド インスタンスの管理コンポーネントだけが使用する定義済みの一連のポートで受信された場合のみ、ノードにルーティングされます。 ノード上の組み込みのファイアウォールは、Microsoft の IP 範囲からのトラフィックのみを許可するように設定されています。 証明書によって、管理コンポーネントと管理プレーンとの間のすべての通信が互いに認証されます。

## <a name="management-endpoint"></a>管理エンドポイント

マネージド インスタンスの管理は、Microsoft が管理エンドポイントを使用して行います。 このエンドポイントは、インスタンスの仮想クラスターの内部にあります。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールによって保護されます。 アプリケーション レベルでは、相互の証明書の検証によって保護されます。 このエンドポイントの IP アドレスを見つけるには、[管理エンドポイントの IP アドレスの確認](sql-database-managed-instance-find-management-endpoint-ip-address.md)に関する記事を参照してください。

マネージド インスタンスの内部から接続が開始される場合 (バックアップと監査ログ)、トラフィックは、管理エンドポイントのパブリック IP アドレスから始まっているように見えます。 マネージド インスタンスの IP アドレスのみを許可するようにファイアウォール規則を設定することで、マネージ ドインスタンスからパブリック サービスへのアクセスを制限できます。 詳細については、[マネージド インスタンスの組み込みのファイアウォールの確認](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)に関する記事を参照してください。

> [!NOTE]
> マネージド インスタンスの内部から開始される接続用のファイアウォールとは異なり、マネージド インスタンスのリージョン内にある Azure サービスには、サービス間のトラフィックを最適化するファイアウォールがあります。

## <a name="network-requirements"></a>ネットワークの要件

マネージド インスタンスは、仮想ネットワーク内の専用サブネットにデプロイします。 サブネットには、次の特性が必要です。

- **専用サブネット:** マネージド インスタンスのサブネットには自身に関連付けられている他のクラウド サービスを含めることはできず、ゲートウェイ サブネットになることもできません。 サブネットには、マネージド インスタンス以外のリソースを含めることはできず、後でサブネットにリソースを追加することもできません。
- **ネットワーク セキュリティ グループ (NSG):** 仮想サブネットに関連付けられている NSG で、他の規則に先立って、[受信セキュリティ規則](#mandatory-inbound-security-rules)と[送信セキュリティ規則](#mandatory-outbound-security-rules)を定義する必要があります。 NSG を使用してポート 1433 のトラフィックをフィルター処理することで、マネージド インスタンスのデータ エンドポイントへのアクセスを制御できます。
- **ユーザー定義ルート (UDR) テーブル:** 仮想ネットワークに関連付けられている UDR テーブルには、特定の[エントリ](#user-defined-routes)が含まれている必要があります。
- **サービス エンドポイントなし**: マネージド インスタンスのサブネットにサービス エンドポイントを関連付けることはできません。 仮想ネットワークの作成時に、サービス エンドポイント オプションが無効になっていることを確認してください。
- **十分な IP アドレス**: マネージド インスタンス サブネットには、少なくとも 16 個の IP アドレスが必要です。 推奨される最小値は、32 個の IP アドレスです。 詳細については、[マネージド インスタンス用のサブネットのサイズの決定](sql-database-managed-instance-determine-size-vnet-subnet.md)に関する記事を参照してください。 [マネージド インスタンスのネットワーク要件](#network-requirements)を満たすように[既存のネットワーク](sql-database-managed-instance-configure-vnet-subnet.md)を構成し、そのネットワークにマネージド インスタンスをデプロイできます。 それ以外の場合は、[新しいネットワークとサブネット](sql-database-managed-instance-create-vnet-subnet.md)を作成します。

> [!IMPORTANT]
> デプロイ先のサブネットにこれらの特性が欠けている場合、新しいマネージド インスタンスをデプロイすることはできません。 マネージド インスタンスを作成すると、ネットワーク設定に対する非準拠の変更を防止するために、ネットワーク インテント ポリシーが適用されます。 最後のインスタンスがサブネットから削除されると、ネットワーク インテント ポリシーも削除されます。

### <a name="mandatory-inbound-security-rules"></a>必須の受信セキュリティ規則

| 名前       |ポート                        |Protocol|ソース           |宛先|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |ALLOW |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |ALLOW |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |ALLOW |

### <a name="mandatory-outbound-security-rules"></a>必須の送信セキュリティ規則

| 名前       |ポート          |Protocol|ソース           |宛先|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80、443、12000|TCP     |任意              |AzureCloud  |ALLOW |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET*  |ALLOW |

> [!IMPORTANT]
> ポート 9000、9003、1438、1440、1452 に対するインバウンド規則が 1 つだけあり、ポート 80、443、12000 に対するアウトバウンド規則が 1 つあることを確認します。 インバウンド規則とアウトバウンド規則がポートごとに別々に構成されていると、ARM デプロイによるマネージド インスタンスのプロビジョニングは失敗します。 これらのポートが別々の規則に含まれている場合、デプロイはエラー コード `VnetSubnetConflictWithIntendedPolicy` で失敗します

\* MI SUBNET は、10.x.x.x/y 形式のサブネットの IP アドレス範囲を参照します。 この情報は、Azure portal のサブネット プロパティで見つけることができます。

> [!IMPORTANT]
> 必須の受信セキュリティ規則では、ポート 9000、9003、1438、1440、および 1452 で "_任意_" のソースからのトラフィックを許可しますが、これらのポートは組み込みのファイアウォールによって保護されています。 詳細については、[管理エンドポイントのアドレスの確認](sql-database-managed-instance-find-management-endpoint-ip-address.md)に関する記事を参照してください。

> [!NOTE]
> マネージド インスタンスでトランザクション レプリケーションを使用しているときに、いずれかのインスタンス データベースをパブリッシャーまたはディストリビューターとして使用した場合、サブネットのセキュリティ規則でポート 445 (TCP 送信) を開きます。 このポートは、Azure ファイル共有へのアクセスを許可します。

### <a name="user-defined-routes"></a>ユーザー定義のルート

|名前|アドレス プレフィックス|次ホップ|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|仮想ネットワーク|
|mi-0-5-next-hop-internet|0.0.0.0/5|インターネット|
|mi-11-8-nexthop-internet|11.0.0.0/8|インターネット|
|mi-12-6-nexthop-internet|12.0.0.0/6|インターネット|
|mi-128-3-nexthop-internet|128.0.0.0/3|インターネット|
|mi-16-4-nexthop-internet|16.0.0.0/4|インターネット|
|mi-160-5-nexthop-internet|160.0.0.0/5|インターネット|
|mi-168-6-nexthop-internet|168.0.0.0/6|インターネット|
|mi-172-12-nexthop-internet|172.0.0.0/12|インターネット|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|インターネット|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|インターネット|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|インターネット|
|mi-173-8-nexthop-internet|173.0.0.0/8|インターネット|
|mi-174-7-nexthop-internet|174.0.0.0/7|インターネット|
|mi-176-4-nexthop-internet|176.0.0.0/4|インターネット|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|インターネット|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|インターネット|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|インターネット|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|インターネット|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|インターネット|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|インターネット|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|インターネット|
|mi-192-9-nexthop-internet|192.0.0.0/9|インターネット|
|mi-193-8-nexthop-internet|193.0.0.0/8|インターネット|
|mi-194-7-nexthop-internet|194.0.0.0/7|インターネット|
|mi-196-6-nexthop-internet|196.0.0.0/6|インターネット|
|mi-200-5-nexthop-internet|200.0.0.0/5|インターネット|
|mi-208-4-nexthop-internet|208.0.0.0/4|インターネット|
|mi-224-3-nexthop-internet|224.0.0.0/3|インターネット|
|mi-32-3-nexthop-internet|32.0.0.0/3|インターネット|
|mi-64-2-nexthop-internet|64.0.0.0/2|インターネット|
|mi-8-7-nexthop-internet|8.0.0.0/7|インターネット|
||||

また、オンプレミスのプライベート IP 範囲が宛先として含まれるトラフィックを、仮想ネットワーク ゲートウェイまたは仮想ネットワーク アプライアンス (NVA) 経由でルーティングするルート テーブルにエントリを追加することもできます。

仮想ネットワークにカスタム DNS が含まれている場合は、Azure の再帰的リゾルバーの IP アドレスのエントリ (168.63.129.16 など) を追加します。 詳細については、[カスタム DNS の設定](sql-database-managed-instance-custom-dns.md)に関する記事を参照してください。 カスタム DNS サーバーは、*microsoft.com*、*windows.net*、*windows.com*、*msocsp.com*、*digicert.com*、*live.com*、*microsoftonline.com*、*microsoftonline-p.com* の各ドメインとそのサブドメインのホスト名を解決できる必要があります。

## <a name="next-steps"></a>次の手順

- 概要については、 [SQL Database の高度なデータ セキュリティ](sql-database-managed-instance.md)に関する記事を参照してください。
- マネージド インスタンスをデプロイできる[新しい Azure 仮想ネットワーク](sql-database-managed-instance-create-vnet-subnet.md)または[既存の Azure 仮想ネットワーク](sql-database-managed-instance-configure-vnet-subnet.md)の設定方法を確認します。
- マネージド インスタンスをデプロイする[サブネットのサイズを計算](sql-database-managed-instance-determine-size-vnet-subnet.md)します。
- マネージド インスタンスの作成方法を確認します。
  - [Azure ポータル](sql-database-managed-instance-get-started.md)から設定。
  - [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) を使用して。
  - [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)を使用して。
  - [Azure Resource Manager テンプレート (Jumpbox と SSMS を含む)](https://portal.azure.com/) を使用して。
