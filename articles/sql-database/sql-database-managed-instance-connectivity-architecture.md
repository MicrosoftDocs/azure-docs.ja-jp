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
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: aac328806e2570bd124626e916c250d481a11311
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567593"
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

マネージド インスタンスは、Azure サービスに依存しています。たとえば、バックアップは Azure Storage、テレメトリは Azure Event Hubs、認証は Azure Active Directory、Transparent Data Encryption (TDE) は Azure Key Vault、セキュリティとサポート機能を提供するいくつかの Azure プラットフォーム サービスです。 マネージド インスタンスは、これらのサービスへの接続を行います。

通信はすべて暗号化され、証明書を使って署名されます。 通信相手の信頼性を確認するために、マネージド インスタンスでは、証明書失効一覧を介してこれらの証明書が確認されます。 証明書が失効している場合、マネージド インスタンスではデータを保護するために接続が終了します。

## <a name="high-level-connectivity-architecture"></a>接続アーキテクチャの概要

おおまかに言えば、マネージド インスタンスは、一連のサービス コンポーネントです。 これらのコンポーネントは、お客様の仮想ネットワーク サブネット内で実行される分離された専用の仮想マシン セット上でホストされます。 これらのマシンによって仮想クラスターが形成されます。

1 つの仮想クラスターで、複数のマネージド インスタンスをホストできます。 サブネットにプロビジョニングされるインスタンスの数をお客様が変更した場合、クラスターは、必要に応じて自動的に拡張または縮小されます。

お客様のアプリケーションは、マネージド インスタンスに接続できます。また、クエリを実行して、仮想ネットワーク、ピアリングされた仮想ネットワーク、または VPN か Azure ExpressRoute によって接続されたネットワーク内のデータベースを更新できます。 このネットワークでは、エンドポイントとプライベート IP アドレスを使用する必要があります。  

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
> マネージド インスタンスのリージョン内にある Azure サービスに送信されるトラフィックは最適化されるため、マネージド インスタンス管理エンドポイントのパブリック IP アドレスに対しては NAT 処理が行われません。 そのため、IP ベースのファイアウォール規則を使用する必要がある場合 (最も一般的な用途はストレージ)、サービスがマネージド インスタンスとは異なるリージョンに存在する必要があります。

## <a name="network-requirements"></a>ネットワークの要件

マネージド インスタンスは、仮想ネットワーク内の専用サブネットにデプロイします。 サブネットには、次の特性が必要です。

- **専用サブネット:** マネージド インスタンスのサブネットには自身に関連付けられている他のクラウド サービスを含めることはできず、ゲートウェイ サブネットになることもできません。 サブネットには、マネージド インスタンス以外のリソースを含めることはできず、後でサブネットに他の種類のリソースを追加することもできません。
- **ネットワーク セキュリティ グループ (NSG):** 仮想サブネットに関連付けられている NSG で、他の規則に先立って、[受信セキュリティ規則](#mandatory-inbound-security-rules)と[送信セキュリティ規則](#mandatory-outbound-security-rules)を定義する必要があります。 マネージド インスタンスがリダイレクト接続用に構成されている場合は、NSG を使用してポート 1433 およびポート 11000-11999 上のトラフィックをフィルター処理することで、マネージド インスタンスのデータ エンドポイントへのアクセスを制御できます。
- **ユーザー定義ルート (UDR) テーブル:** 仮想ネットワークに関連付けられている UDR テーブルには、特定の[エントリ](#user-defined-routes)が含まれている必要があります。
- **サービス エンドポイントなし**: マネージド インスタンスのサブネットにサービス エンドポイントを関連付けることはできません。 仮想ネットワークの作成時に、サービス エンドポイント オプションが無効になっていることを確認してください。
- **十分な IP アドレス**: マネージド インスタンス サブネットには、少なくとも 16 個の IP アドレスが必要です。 推奨される最小値は、32 個の IP アドレスです。 詳細については、[マネージド インスタンス用のサブネットのサイズの決定](sql-database-managed-instance-determine-size-vnet-subnet.md)に関する記事を参照してください。 [マネージド インスタンスのネットワーク要件](#network-requirements)を満たすように[既存のネットワーク](sql-database-managed-instance-configure-vnet-subnet.md)を構成し、そのネットワークにマネージド インスタンスをデプロイできます。 それ以外の場合は、[新しいネットワークとサブネット](sql-database-managed-instance-create-vnet-subnet.md)を作成します。

> [!IMPORTANT]
> デプロイ先のサブネットにこれらの特性が欠けている場合、新しいマネージド インスタンスをデプロイすることはできません。 マネージド インスタンスを作成すると、ネットワーク設定に対する非準拠の変更を防止するために、ネットワーク インテント ポリシーが適用されます。 最後のインスタンスがサブネットから削除されると、ネットワーク インテント ポリシーも削除されます。

### <a name="mandatory-inbound-security-rules"></a>必須の受信セキュリティ規則

| 名前       |Port                        |Protocol|source           |宛先|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000、9003、1438、1440、1452|TCP     |Any              |MI SUBNET  |Allow |
|mi_subnet   |Any                         |Any     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules"></a>必須の送信セキュリティ規則

| 名前       |Port          |Protocol|source           |宛先|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80、443、12000|TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI SUBNET        |MI SUBNET  |Allow |

> [!IMPORTANT]
> ポート 9000、9003、1438、1440、1452 に対するインバウンド規則が 1 つだけあり、ポート 80、443、12000 に対するアウトバウンド規則が 1 つあることを確認します。 インバウンド規則とアウトバウンド規則がポートごとに別々に構成されていると、Azure Resource Manager デプロイによる Managed Instance のプロビジョニングは失敗します。 これらのポートが別々の規則に含まれている場合、デプロイはエラー コード `VnetSubnetConflictWithIntendedPolicy` で失敗します

\* MI SUBNET は、10.x.x.x/y 形式のサブネットの IP アドレス範囲を参照します。 この情報は、Azure portal のサブネット プロパティで見つけることができます。

> [!IMPORTANT]
> 必須の受信セキュリティ規則では、ポート 9000、9003、1438、1440、および 1452 で "_任意_" のソースからのトラフィックを許可しますが、これらのポートは組み込みのファイアウォールによって保護されています。 詳細については、[管理エンドポイントのアドレスの確認](sql-database-managed-instance-find-management-endpoint-ip-address.md)に関する記事を参照してください。
> [!NOTE]
> マネージド インスタンスでトランザクション レプリケーションを使用しているときに、いずれかのインスタンス データベースをパブリッシャーまたはディストリビューターとして使用した場合、サブネットのセキュリティ規則でポート 445 (TCP 送信) を開きます。 このポートは、Azure ファイル共有へのアクセスを許可します。

### <a name="user-defined-routes"></a>ユーザー定義のルート

|名前|アドレス プレフィックス|次ホップ|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|仮想ネットワーク|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|インターネット|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|インターネット|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|インターネット|
|mi-20-8-nexthop-internet|20.0.0.0/8|インターネット|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|インターネット|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|インターネット|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|インターネット|
|mi-51-8-nexthop-internet|51.0.0.0/8|インターネット|
|mi-52-8-nexthop-internet|52.0.0.0/8|インターネット|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|インターネット|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|インターネット|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|インターネット|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|インターネット|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|インターネット|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|インターネット|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|インターネット|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|インターネット|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|インターネット|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|インターネット|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|インターネット|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|インターネット|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|インターネット|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|インターネット|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|インターネット|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|インターネット|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|インターネット|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|インターネット|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|インターネット|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|インターネット|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|インターネット|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|インターネット|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|インターネット|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|インターネット|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|インターネット|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|インターネット|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|インターネット|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|インターネット|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|インターネット|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|インターネット|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|インターネット|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|インターネット|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|インターネット|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|インターネット|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|インターネット|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|インターネット|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|インターネット|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|インターネット|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|インターネット|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|インターネット|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|インターネット|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|インターネット|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|インターネット|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|インターネット|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|インターネット|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|インターネット|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|インターネット|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|インターネット|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|インターネット|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|インターネット|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|インターネット|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|インターネット|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|インターネット|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|インターネット|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|インターネット|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|インターネット|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|インターネット|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|インターネット|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|インターネット|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|インターネット|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|インターネット|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|インターネット|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|インターネット|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|インターネット|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|インターネット|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|インターネット|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|インターネット|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|インターネット|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|インターネット|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|インターネット|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|インターネット|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|インターネット|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|インターネット|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|インターネット|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|インターネット|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|インターネット|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|インターネット|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|インターネット|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|インターネット|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|インターネット|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|インターネット|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|インターネット|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|インターネット|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|インターネット|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|インターネット|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|インターネット|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|インターネット|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|インターネット|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|インターネット|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|インターネット|
||||

また、オンプレミスのプライベート IP 範囲が宛先として含まれるトラフィックを、仮想ネットワーク ゲートウェイまたは仮想ネットワーク アプライアンス (NVA) 経由でルーティングするルート テーブルにエントリを追加することもできます。

仮想ネットワークにカスタム DNS が含まれる場合、カスタム DNS サーバーはパブリック DNS レコードを解決できる必要があります。 Azure AD Authentication などの追加機能を使用するには、追加の FQDN 解決が必要になる可能性があります。 詳細については、[カスタム DNS の設定](sql-database-managed-instance-custom-dns.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- 概要については、 [SQL Database の高度なデータ セキュリティ](sql-database-managed-instance.md)に関する記事を参照してください。
- マネージド インスタンスをデプロイできる[新しい Azure 仮想ネットワーク](sql-database-managed-instance-create-vnet-subnet.md)または[既存の Azure 仮想ネットワーク](sql-database-managed-instance-configure-vnet-subnet.md)の設定方法を確認します。
- マネージド インスタンスをデプロイする[サブネットのサイズを計算](sql-database-managed-instance-determine-size-vnet-subnet.md)します。
- マネージド インスタンスの作成方法を確認します。
  - [Azure ポータル](sql-database-managed-instance-get-started.md)から設定。
  - [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) を使用して。
  - [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)を使用して。
  - [Azure Resource Manager テンプレート (Jumpbox と SSMS を含む)](https://portal.azure.com/) を使用して。 
