---
title: Azure Private Link とは
description: Azure Private Link の機能、アーキテクチャ、実装の概要。 Azure プライベート エンドポイントおよび Azure Private Link サービスのしくみとその使用方法について説明します。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 06/18/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 908d9386f08c5de863d1d435c54d65cb85feef7a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181665"
---
# <a name="what-is-azure-private-link"></a>Azure Private Link とは 
Azure Private Link を使用すると、お使いの仮想ネットワーク内の[プライベート エンドポイント](private-endpoint-overview.md)経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできます。

仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。 パブリック インターネットにサービスを公開する必要はありません。 お使いの仮想ネットワークに独自の[プライベート リンク サービス](private-link-service-overview.md)を作成して顧客に提供することができます。 Azure Private Link を使用した設定と消費は、Azure PaaS サービス、顧客所有サービス、共有パートナー サービス間で一貫しています。

> [!IMPORTANT]
> Azure Private Link は、現在、一般提供されています。 プライベート エンドポイントと Private Link サービス (Standard ロード バランサーの背後にあるサービス) の両方が一般提供されています。 さまざまな Azure PaaS が異なるスケジュールで Azure Private Link にオンボードされます。 Private Link 上の Azure PaaS の正確な状態については、以下の「[利用可能なサービス](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)」セクションを確認してください。 既知の制約については、[プライベート エンドポイント](private-endpoint-overview.md#limitations)と [Private Link サービス](private-link-service-overview.md#limitations)の説明を参照してください。 

![プライベート エンドポイントの概要](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>主な利点
Azure Private Link には次のような利点があります。  
- **Azure プラットフォーム上のサービスへのプライベート アクセス**: 接続元または接続先にパブリック IP アドレスを使用せずに、Azure 内のサービスに仮想ネットワークを接続できます。 サービス プロバイダーは独自のローカル仮想ネットワークでサービスを提供することができ、また、コンシューマーは自身のローカル仮想ネットワークでサービスにアクセスすることができます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 
 
- **オンプレミスおよびピアリングされたネットワーク**: Azure で実行されているサービスに対し、ExpressRoute プライベート ピアリングや VPN トンネル経由でオンプレミスから、さらには、ピアリングされた仮想ネットワークから、プライベート エンドポイントを使用してアクセスすることができます。 パブリック ピアリングを設定したり、インターネットを経由してサービスに接続したりする必要はありません。 Private Link を使用すれば、Azure にワークロードを安全に移行することができます。
 
- **データの漏えいに対する保護**: プライベート エンドポイントは、サービス全体にではなく、PaaS リソースの特定のインスタンスにマップされます。 コンシューマーが接続できるのは、その特定のリソースだけです。 サービス内の他のリソースへのアクセスはブロックされます。 このメカニズムにより、データの漏えいリスクを防ぐことができます。 
 
- **グローバルな展開**: 他のリージョンで実行中のサービスにプライベートに接続します。 リージョン A にあるコンシューマーの仮想ネットワークから、リージョン B の、Private Link の背後にあるサービスに接続することができます。  
 
- **独自のサービスへの拡張**: Azure 上のコンシューマーに対し、同じエクスペリエンスと機能でプライベートにサービスを提供できます。 Standard Azure Load Balancer の内側にサービスを配置することで、サービスを Private Link 対応にすることができます。 そうすれば、コンシューマーは自身の仮想ネットワーク内のプライベート エンドポイントを使用して直接そのサービスに接続できます。 接続要求は、承認呼び出しフローを使用して管理できます。 Azure Private Link は、異なる Azure Active Directory テナントに属するコンシューマーとサービスに対しても機能します。 

## <a name="availability"></a>利用可能なサービス 
 次の表に、Private Link サービスと、それらを使用可能なリージョンの一覧を示します。 

|サポートされているサービス  |対応リージョン | その他の注意点 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Standard Azure Load Balancer の背後にある Private Link サービス | すべてのパブリック リージョン<br/> すべての Government リージョン  | Standard Load Balancer でサポートされます | GA <br/> [詳細情報](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
| Azure Storage       |  すべてのパブリック リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [詳細情報](/azure/storage/common/storage-private-endpoints)  |
| Azure Data Lake Storage Gen2        |  すべてのパブリック リージョン      |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [詳細情報](/azure/storage/common/storage-private-endpoints)  |
|  Azure SQL データベース         | すべてのパブリック リージョン <br/> すべての Government リージョン      |  プロキシの[接続ポリシー](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy)についてサポートされます | GA <br/> [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|Azure Synapse Analytics (SQL Data Warehouse)| すべてのパブリック リージョン <br/> すべての Government リージョン |  プロキシの[接続ポリシー](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy)についてサポートされます |GA <br/> [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  すべてのパブリック リージョン<br/> すべての Government リージョン | |GA <br/> [詳細情報](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  Azure Database for PostgreSQL - 単一サーバー         | すべてのパブリック リージョン <br/> すべての Government リージョン     | General Purpose とメモリ最適化の価格レベルでサポートされます | GA <br/> [詳細情報](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Azure Database for MySQL         | すべてのパブリック リージョン<br/> すべての Government リージョン      |  | GA <br/> [詳細情報](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure Database for MariaDB         | すべてのパブリック リージョン<br/> すべての Government リージョン     |  | GA <br/> [詳細情報](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | すべてのパブリック リージョン<br/> すべての Government リージョン      |  | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Azure Kubernetes Service - Kubernetes API | すべてのパブリック リージョン      |  | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | すべてのパブリック リージョン <br/> すべての Government リージョン | プライベート モードのサービスでサポートされます | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/search/search-security-overview#endpoint-access)    |
|Azure Container Registry | すべてのパブリック リージョン<br/> すべての Government リージョン    | コンテナー レジストリの Premium レベルでサポートされます。 [レベルについてはここをクリック](https://docs.microsoft.com/azure/container-registry/container-registry-skus)| GA   <br/> [詳細情報](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Azure App Configuration | すべてのパブリック リージョン      |  | プレビュー   |
|Azure Backup | すべてのパブリック リージョン<br/> すべての Government リージョン   |  | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Azure Event Hub | すべてのパブリック リージョン<br/>すべての Government リージョン      |   | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Azure Service Bus | すべてのパブリック リージョン<br/>すべての Government リージョン  | Azure Service Bus の Premium レベルでサポートされます。 [レベルについてはここをクリック](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-premium-messaging) | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Azure Relay | すべてのパブリック リージョン      |  | プレビュー <br/> [詳細情報](https://docs.microsoft.com/azure/service-bus-relay/private-link-service)  |
|Azure Event Grid| すべてのパブリック リージョン<br/> すべての Government リージョン       |  | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/event-grid/network-security) |
|Azure Web Apps | すべてのパブリック リージョン      | PremiumV2 Windows および Linux とエラスティック Premium 機能でサポートされています  | プレビュー   <br/> [詳細情報](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|Azure Machine Learning | 米国東部、米国中南部、米国西部 2      |  | プレビュー   <br/> [詳細情報](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Azure Automation  | すべてのパブリック リージョン |  | プレビュー | |
| Azure IoT Hub | すべてのパブリック リージョン    |  | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | 米国東部、米国西部 2、米国中南部      |  | プレビュー   <br/> [詳細情報](https://aka.ms/asrs/privatelink)   |
| Azure Monitor <br/>(ログ分析と Application Insights) | すべてのパブリック リージョン      |  | GA   <br/> [詳細情報](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   | 
| Azure File Sync | すべてのパブリック リージョン      | |   GA   <br/> [詳細情報](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints?tabs=azure-portal)   |
| Azure Batch | 米国東部、米国西部 2、米国中南部、米国中西部、フランス中部、東アジア、英国南部、US Gov バージニア、US Gov アリゾナ  | | GA <br/> [詳細情報](https://docs.microsoft.com/azure/batch/private-connectivity) |

最新情報については、[Azure Private Link の更新情報ページ](https://azure.microsoft.com/updates/?product=private-link)をご覧ください。

## <a name="logging-and-monitoring"></a>ログ記録と監視

Azure Private Link は Azure Monitor と連携します。 この組み合わせによって、次のことが可能となります。

 - ストレージ アカウントへのログのアーカイブ。
 - イベント ハブへのイベントのストリーム配信。
 - Azure Monitor ログ記録。

Azure Monitor で次の情報にアクセスできます。 
- **プライベート エンドポイント**: 
    - プライベート エンドポイントによって処理されるデータ (入力/出力)
 
- **Private Link サービス**:
    - Private Link サービスによって処理されるデータ (入力/出力)
    - NAT ポートの使用可能性  
 
## <a name="pricing"></a>価格   
料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link/)」をご覧ください。
 
## <a name="faqs"></a>FAQ  
FAQ については、「[Azure Private Link のよくあるご質問](private-link-faq.md)」を参照してください。
 
## <a name="limits"></a>制限  
制限については、[Azure Private Link の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)に関するページを参照してください。

## <a name="service-level-agreement"></a>サービス レベル アグリーメント
SLA については、「[Azure Private Link の SLA](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [クイック スタート: Azure portal を使用してプライベート エンドポイントを作成する](create-private-endpoint-portal.md)
- [クイック スタート: Azure portal を使用して Private Link サービスを作成する](create-private-link-service-portal.md)


 
