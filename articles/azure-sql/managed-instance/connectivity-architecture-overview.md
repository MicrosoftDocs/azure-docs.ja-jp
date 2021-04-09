---
title: 接続のアーキテクチャ
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance の通信および接続アーキテクチャと、コンポーネントによるマネージド インスタンスへのトラフィックの誘導方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: 58563629b30e7be764732a9810162e1a0b1931e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725838"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance の接続アーキテクチャ
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、Azure SQL Managed Instance の通信について説明します。 また、接続アーキテクチャと、コンポーネントによるマネージド インスタンスへのトラフィックの誘導方法についても説明します。  

SQL Managed Instance は、マネージド インスタンス専用の Azure 仮想ネットワークとサブネットの内部に配置されます。 このデプロイでは、以下が提供されます。

- セキュリティで保護されたプライベート IP アドレス。
- オンプレミス ネットワークを SQL Managed Instance に接続する機能。
- SQL Managed Instance をリンク サーバーまたは別のオンプレミス データ ストアに接続する機能。
- SQL Managed Instance を Azure リソースに接続する機能。

## <a name="communication-overview"></a>通信の概要

次の図は、SQL Managed Instance に接続するエンティティを示しています。 マネージド インスタンスと通信するために必要なリソースも示されています。 図の一番下にある通信プロセスは、SQL Managed Instance にデータ ソースとして接続するお客様のアプリケーションとツールを表しています。  

![接続アーキテクチャのエンティティ](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance は、PaaS (サービスとしてのプラットフォーム) サービスです。 Azure では、自動化されたエージェント (管理、デプロイ、メンテナンス) を使用されて、テレメトリ データ ストリームに基づいてこのサービスが管理されます。 管理責任は Azure にあるため、お客様はリモート デスクトップ プロトコル (RDP) を通して SQL Managed Instance 仮想クラスター マシンにアクセスすることはできません。

エンド ユーザーまたはアプリケーションによって開始される操作の中には、SQL Managed Instance とプラットフォームとの間のやりとりを必要とするものがあります。 1 つのケースが、SQL Managed Instance データベースの作成です。 このリソースは、Azure portal、PowerShell、Azure CLI、および REST API を介して公開されます。

SQL Managed Instance は、Azure サービスに依存しています。たとえば、バックアップには Azure Storage、テレメトリには Azure Event Hubs、認証には Azure Active Directory (Azure AD)、Transparent Data Encryption (TDE) には Azure Key Vault、そして、セキュリティとサポート機能が提供されるいくつかの Azure プラットフォーム サービスがあります。 SQL Managed Instance では、これらのサービスへの接続が行われます。

通信はすべて暗号化され、証明書を使って署名されます。 通信相手の信頼性を確認するために、SQL Managed Instance では、証明書失効一覧を介してこれらの証明書が確認されます。 証明書が失効している場合、SQL Managed Instance ではデータを保護するために接続が終了されます。

## <a name="high-level-connectivity-architecture"></a>接続アーキテクチャの概要

おおまかに言えば、SQL Managed Instance は、一連のサービス コンポーネントです。 これらのコンポーネントは、お客様の仮想ネットワーク サブネット内で実行される分離された専用の仮想マシン セット上でホストされます。 これらのマシンによって仮想クラスターが形成されます。

1 つの仮想クラスターで、複数のマネージド インスタンスをホストできます。 サブネットにプロビジョニングされるインスタンスの数をお客様が変更した場合、クラスターは、必要に応じて自動的に拡張または縮小されます。

お客様のアプリケーションは、SQL Managed Instance に接続できます。また、クエリを実行して、仮想ネットワーク、ピアリングされた仮想ネットワーク、または VPN か Azure ExpressRoute によって接続されたネットワーク内のデータベースを更新できます。 このネットワークでは、エンドポイントとプライベート IP アドレスを使用する必要があります。  

![接続アーキテクチャ図](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure の管理およびデプロイ サービスは、仮想ネットワークの外部で実行されます。 SQL Managed Instance と Azure サービスは、パブリック IP アドレスを持つエンドポイント経由で接続されます。 SQL Managed Instance によって送信接続が作成される場合、受信側では、ネットワーク アドレス変換 (NAT) によってこのパブリック IP アドレスを発信元とする接続のように見えます。

管理トラフィックは、お客様の仮想ネットワーク内を通過します。 つまり、仮想ネットワークのインフラストラクチャの要素は、インスタンスを失敗させて利用不可にすることで管理トラフィックに害を及ぼす可能性があります。

> [!IMPORTANT]
> カスタマー エクスペリエンスとサービスの可用性を高めるために、Azure では、Azure Virtual Network インフラストラクチャの要素に対してネットワーク インテント ポリシーを適用しています。 このポリシーによって、SQL Managed Instance の動作に影響が出る可能性があります。 このプラットフォーム メカニズムは、ネットワーク要件をユーザーに透過的に伝達します。 このポリシーの主要な目的は、ネットワークの誤った構成を防ぎ、SQL Managed Instance の通常の動作を確保することです。 マネージド インスタンスを削除すると、ネットワーク インテント ポリシーも削除されます。

## <a name="virtual-cluster-connectivity-architecture"></a>仮想クラスターの接続アーキテクチャ

SQL Managed Instance の接続アーキテクチャについて、さらに踏み込んで見ていきましょう。 次の図は、仮想クラスターの概念上のレイアウトを示しています。

![仮想クラスターの接続アーキテクチャ](./media/connectivity-architecture-overview/connectivityarch003.png)

クライアントは、`<mi_name>.<dns_zone>.database.windows.net` 形式のホスト名を使用して、SQL Managed Instance に接続します。 このホスト名は、パブリック ドメイン ネーム システム (DNS) ゾーンに登録されていてパブリックに解決できますが、プライベート IP アドレスに解決されます。 クラスターを作成すると、`zone-id` が自動的に生成されます。 新しく作成されたクラスターで 2 番目のマネージド インスタンスがホストされる場合は、プライマリ クラスターとゾーン ID が共有されます。 詳細については、「[自動フェールオーバー グループを使用して、複数のデータベースの透過的な調整されたフェールオーバーを有効にする](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)」を参照してください。

このプライベート IP アドレスは、SQL Managed Instance の内部ロード バランサーに属しています。 ロード バランサーは、SQL Managed Instance のゲートウェイにトラフィックを送信します。 同じクラスター内で複数のマネージド インスタンスを実行できるため、ゲートウェイでは、SQL Managed Instance のホスト名を使用して、トラフィックを適切な SQL Engine サービスにリダイレクトします。

管理およびデプロイ サービスでは、SQL Managed Instance への接続に、外部ロード バランサーにマッピングされる[管理エンドポイント](#management-endpoint)が使用されます。 トラフィックは、SQL Managed Instance の管理コンポーネントだけに使用される定義済みの一連のポートで受信された場合のみ、ノードにルーティングされます。 ノード上の組み込みのファイアウォールは、Microsoft の IP 範囲からのトラフィックのみを許可するように設定されています。 証明書によって、管理コンポーネントと管理プレーンとの間のすべての通信が互いに認証されます。

## <a name="management-endpoint"></a>管理エンドポイント

Azure では、管理エンドポイントを使用して SQL Managed Instance が管理されます。 このエンドポイントは、インスタンスの仮想クラスターの内部にあります。 管理エンドポイントは、ネットワーク レベルでは組み込みのファイアウォールによって保護されます。 アプリケーション レベルでは、相互の証明書の検証によって保護されます。 このエンドポイントの IP アドレスを見つけるには、[管理エンドポイントの IP アドレスの確認](management-endpoint-find-ip-address.md)に関する記事を参照してください。

SQL Managed Instance の内部から接続が開始される場合 (バックアップおよび監査ログのように)、トラフィックは、管理エンドポイントのパブリック IP アドレスから始まっているように見えます。 SQL Managed Instance の IP アドレスのみを許可するようにファイアウォール規則を設定することで、SQL Managed Instance からパブリック サービスへのアクセスを制限することができます。 詳細については、[SQL Managed Instance の組み込みのファイアウォールの確認](management-endpoint-verify-built-in-firewall.md)に関する記事を参照してください。

> [!NOTE]
> SQL Managed Instance のリージョン内にある Azure サービスに送信されたトラフィックは最適化されるため、管理エンドポイントのパブリック IP アドレスに変換される NAT 処理が行われません。 そのため、IP ベースのファイアウォール規則を使用する必要がある場合 (最も一般的な用途はストレージ)、サービスが SQL Managed Instance とは異なるリージョンに存在している必要があります。

## <a name="service-aided-subnet-configuration"></a>サービス支援サブネット構成

お客様のセキュリティと管理の要件に対処するため、SQL Managed Instance は、手動からサービス支援サブネット構成に切り替え中です。

サービス支援サブネット構成では、ユーザーがデータ (TDS) トラフィックを完全に制御しますが、SQL Managed Instance は、SLA を満たすために、管理トラフィックのフローが中断されないことを保証する役割を担います。

サービス支援サブネット構成は、仮想ネットワーク [サブネット委任](../../virtual-network/subnet-delegation-overview.md)機能の上に構築されます。これにより、ネットワーク構成の自動管理を提供し、サービス エンドポイントを有効にすることができます。 

サービス エンドポイントを使用することで、バックアップおよび監査ログを保持するストレージ アカウントの仮想ネットワーク ファイアウォール規則を構成することができます。 サービス エンドポイントが有効になっている場合でも、サービス エンドポイント経由でセキュリティを追加する[プライベート リンク](../../private-link/private-link-overview.md)を使用することをお客様にお勧めしています。

> [!IMPORTANT]
> コントロール プレーン構成の特性に起因し、サービス支援サブネット構成では、国内のクラウドでサービス エンドポイントが有効になりません。 

### <a name="network-requirements"></a>ネットワークの要件

SQL Managed Instance を、仮想ネットワーク内の専用サブネットにデプロイします。 サブネットには、次の特性が必要です。

- **専用サブネット:** SQL Managed Instance のサブネットには、それ自体に関連付けられている他のクラウド サービスを含められず、それをゲートウェイ サブネットにすることもできません。 サブネットには、SQL Managed Instance 以外のリソースを含めることはできず、後でサブネットに他の種類のリソースを追加することもできません。
- **[サブネットの委任]:** SQL Managed Instance のサブネットは `Microsoft.Sql/managedInstances` リソース プロバイダーに委任する必要があります。
- **ネットワーク セキュリティ グループ (NSG):** NSG は、SQL Managed Instance サブネットに関連付けられている必要があります。 SQL Managed Instance がリダイレクト接続用に構成されている場合は、NSG を使用してポート 1433 およびポート 11000 から 11999 上のトラフィックをフィルター処理することで、SQL Managed Instance のデータ エンドポイントへのアクセスを制御できます。 サービスによって、管理トラフィックのフローが中断されないようにするために必要な[規則](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)が自動的にプロビジョニングされ、最新に維持されます。
- **ユーザー定義ルート (UDR) テーブル:** UDR テーブルは、SQL Managed Instance サブネットに関連付けられている必要があります。 オンプレミスのプライベート IP 範囲が宛先として含まれるトラフィックを、仮想ネットワーク ゲートウェイまたは仮想ネットワーク アプライアンス (NVA) 経由でルーティングするルート テーブルにエントリを追加することもできます。 サービスは、管理トラフィックが中断されないようにするために必要な[エントリ](#user-defined-routes-with-service-aided-subnet-configuration)を自動的にプロビジョニングし、最新に維持します。
- **十分な IP アドレス**: SQL Managed Instance サブネットには、少なくとも 32 個の IP アドレスが必要です。 詳細については、[SQL Managed Instance 用のサブネットのサイズの決定](vnet-subnet-determine-size.md)に関する記事を参照してください。 [SQL Managed Instance のネットワーク要件](#network-requirements)を満たすように[既存のネットワーク](vnet-existing-add-subnet.md)を構成した後、そのネットワークにマネージド インスタンスをデプロイできます。 それ以外の場合は、[新しいネットワークとサブネット](virtual-network-subnet-create-arm-template.md)を作成します。

> [!IMPORTANT]
> マネージド インスタンスを作成すると、ネットワーク設定に対する非準拠の変更を防止するために、ネットワーク インテント ポリシーが適用されます。 最後のインスタンスがサブネットから削除されると、ネットワーク インテント ポリシーも削除されます。 下のルールは情報提供のみを目的としています。ARM テンプレート、PowerShell、または CLI を使用してこれらをデプロイしないでください。 最新の公式テンプレートを使用する場合は、常に[ポータルから取得](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)できます。

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>サービス支援サブネット構成を使用した必須の受信セキュリティ規則

| 名前       |Port                        |Protocol|source           |到着地|アクション|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000、9003、1438、1440、1452|TCP     |SqlManagement    |MI SUBNET  |Allow |
|            |9000、9003                  |TCP     |CorpNetSaw       |MI SUBNET  |Allow |
|            |9000、9003                  |TCP     |CorpnetPublic    |MI SUBNET  |Allow |
|mi_subnet   |Any                         |Any     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>サービス支援サブネット構成を使用した必須の送信セキュリティ規則

| 名前       |Port          |Protocol|source           |到着地|アクション|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443、12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI SUBNET        |MI SUBNET  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>サービス支援サブネット構成を使用したユーザー定義ルート

|名前|アドレス プレフィックス|次のホップ|
|----|--------------|-------|
|subnet-to-vnetlocal|MI SUBNET|仮想ネットワーク|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|インターネット|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|インターネット|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|インターネット|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|インターネット|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|インターネット|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|インターネット|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|インターネット|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|インターネット|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|インターネット|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|インターネット|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|インターネット|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|インターネット|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|インターネット|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|インターネット|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|インターネット|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|インターネット|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|インターネット|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|インターネット|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|インターネット|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|インターネット|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|インターネット|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|インターネット|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|インターネット|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|インターネット|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|インターネット|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|インターネット|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|インターネット|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|インターネット|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|インターネット|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|インターネット|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|インターネット|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|インターネット|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|インターネット|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|インターネット|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|インターネット|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|インターネット|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|インターネット|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|インターネット|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|インターネット|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|インターネット|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|インターネット|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|インターネット|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|インターネット|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|インターネット|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|インターネット|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|インターネット|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|インターネット|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|インターネット|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|インターネット|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|インターネット|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|インターネット|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|インターネット|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|インターネット|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|インターネット|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|インターネット|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|インターネット|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|インターネット|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|インターネット|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|インターネット|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|インターネット|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|インターネット|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|インターネット|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|インターネット|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|インターネット|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|インターネット|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|インターネット|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|インターネット|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|インターネット|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|インターネット|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|インターネット|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|インターネット|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|インターネット|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|インターネット|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|インターネット|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|インターネット|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|インターネット|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|インターネット|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|インターネット|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|インターネット|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|インターネット|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|インターネット|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|インターネット|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|インターネット|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|インターネット|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|インターネット|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|インターネット|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|インターネット|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|インターネット|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|インターネット|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|インターネット|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|インターネット|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|インターネット|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|インターネット|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|インターネット|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|インターネット|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|インターネット|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|インターネット|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|インターネット|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|インターネット|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|インターネット|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|インターネット|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|インターネット|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|インターネット|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|インターネット|
||||

\* MI SUBNET は、x.x.x.x/y 形式のサブネットの IP アドレス範囲を参照します。 この情報は、Azure portal のサブネット プロパティで見つけることができます。

\** 宛先アドレスが Azure のサービスのいずれかのアドレスの場合、トラフィックはインターネットにルーティングされるのではなく、Azure のバックボーン ネットワーク経由でそのサービスに直接ルーティングされます。 仮想ネットワークが存在する Azure リージョン、または Azure サービスのインタンスがデプロイされている Azure リージョンに関係なく、Azure サービス間のトラフィックはインターネットを経由しません。 詳細については、[UDR ドキュメントのページ](../../virtual-network/virtual-networks-udr-overview.md)を確認してください。

また、オンプレミスのプライベート IP 範囲が宛先として含まれるトラフィックを、仮想ネットワーク ゲートウェイまたは仮想ネットワーク アプライアンス (NVA) 経由でルーティングするルート テーブルにエントリを追加することもできます。

仮想ネットワークにカスタム DNS が含まれる場合、カスタム DNS サーバーはパブリック DNS レコードを解決できる必要があります。 Azure AD Authentication などの追加機能を使用するには、追加の FQDN 解決が必要になる可能性があります。 詳細については、[カスタム DNS の設定](custom-dns-configure.md)に関する記事を参照してください。

### <a name="networking-constraints"></a>ネットワークの制約

**TLS 1.2 は送信接続に適用されます**:2020 年 1 月、Microsoft はすべての Azure サービスのサービス内トラフィックに TLS 1.2 を適用しました。 その結果、Azure SQL Managed Instance については、SQL Server へのレプリケーションとリンク サーバー接続に使用される送信接続に対して、TLS 1.2 が適用されることになりました。 SQL Managed Instance で 2016 より前のバージョンの SQL Server を使用している場合は、[TLS 1.2 固有の更新プログラム](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) が確実に適用されているようにしてください。

現在、SQL Managed Instance では、次の仮想ネットワーク機能は "*サポートされていません*"。

- **Microsoft ピアリング**:SQL Managed Instance が存在する仮想ネットワークと直接または推移的にピアリングされた ExpressRoute 回線で [Microsoft ピアリング](../../expressroute/expressroute-faqs.md#microsoft-peering)を有効にすると、仮想ネットワーク内の SQL Managed Instance コンポーネント間のトラフィック フローと、それに依存するサービスに影響が及び、可用性の問題が発生します。 Microsoft ピアリングが既に有効になっている仮想ネットワークへの SQL Managed Instance デプロイは、失敗することが予想されます。
- **グローバル仮想ネットワーク ピアリング**:Azure リージョン間での [仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)接続は、2020 年 9 月 22 日より前に作成されてサブネットに配置された SQL マネージド インスタンスでは機能しません。
- **AzurePlatformDNS**:AzurePlatformDNS [サービス タグ](../../virtual-network/service-tags-overview.md)を使用してプラットフォーム DNS 解決をブロックすると、SQL Managed Instance をレンダリングできなくなります。 SQL Managed Instance では、エンジン内部の DNS 解決にお客様による定義の DNS がサポートされていますが、プラットフォームの操作については、プラットフォーム DNS への依存性があります。
- **NAT Gateway**:[Azure Virtual Network NAT](../../virtual-network/nat-overview.md) を使用して、特定のパブリック IP アドレスでの送信接続を制御すると、SQL Managed Instance をレンダリングできなくなります。 現時点では、SQL Managed Instance サービスは基本的なロード バランサーの使用に制限されており、Virtual Network NAT を使用した受信フローと送信フローを同時に実行することができません。
- **Azure Virtual Network の IPv6**:SQL Managed Instance は [デュアル スタックの IPv4 または IPv6 仮想ネットワーク](../../virtual-network/ipv6-overview.md)にデプロイできないことが予想されています。 ネットワーク セキュリティ グループ (NSG) または IPv6 アドレス プレフィックスを含むルート テーブル (UDR) を SQL Managed Instance サブネットに関連付けるか、Managed Instance サブネットに既に関連付けられている NSG または UDR に IPv6 アドレス プレフィックスを追加すると、SQL Managed Instance が利用できなくなります。 既に IPv6 プレフィックスが与えられている NSG と UDR を含むサブネットに SQL Managed Instance はデプロイできないことが想定されています。

## <a name="next-steps"></a>次のステップ

- 概要については、「 [Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
- SQL Managed Instance をデプロイできる[新しい Azure 仮想ネットワーク](virtual-network-subnet-create-arm-template.md)または[既存の Azure 仮想ネットワーク](vnet-existing-add-subnet.md)の設定方法を確認します。
- SQL Managed Instance をデプロイする[サブネットのサイズを計算](vnet-subnet-determine-size.md)します。
- マネージド インスタンスの作成方法を確認します。
  - [Azure ポータル](instance-create-quickstart.md)から設定。
  - [PowerShell](scripts/create-configure-managed-instance-powershell.md) を使用して。
  - [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)を使用して。
  - [Azure Resource Manager テンプレート (Jumpbox と SSMS を含む)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/) を使用して。