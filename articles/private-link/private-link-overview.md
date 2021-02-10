---
title: Azure Private Link とは
description: Azure Private Link の機能、アーキテクチャ、実装の概要。 Azure プライベート エンドポイントおよび Azure Private Link サービスのしくみとその使用方法について説明します。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/28/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 681e4efc8a9edda896249fdc939d19b4f644f246
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986482"
---
# <a name="what-is-azure-private-link"></a>Azure Private Link とは 
Azure Private Link を使用すると、お使いの仮想ネットワーク内の[プライベート エンドポイント](private-endpoint-overview.md)経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできます。

仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。 パブリック インターネットにサービスを公開する必要はありません。 お使いの仮想ネットワークに独自の[プライベート リンク サービス](private-link-service-overview.md)を作成して顧客に提供することができます。 Azure Private Link を使用した設定と消費は、Azure PaaS サービス、顧客所有サービス、共有パートナー サービス間で一貫しています。

> [!IMPORTANT]
> Azure Private Link は、現在、一般提供されています。 プライベート エンドポイントと Private Link サービス (Standard ロード バランサーの背後にあるサービス) の両方が一般提供されています。 さまざまな Azure PaaS が異なるスケジュールで Azure Private Link にオンボードされます。 Private Link 上の Azure PaaS の正確な状態については、この記事の[対応リージョン](#availability)に関するセクションを確認してください。 既知の制約については、[プライベート エンドポイント](private-endpoint-overview.md#limitations)と [Private Link サービス](private-link-service-overview.md#limitations)の説明を参照してください。 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure portal の Azure Private Link センター" border="false":::

## <a name="key-benefits"></a>主な利点
Azure Private Link には次のような利点があります。  
- **Azure プラットフォーム上のサービスへのプライベート アクセス**: 接続元または接続先にパブリック IP アドレスを使用せずに、Azure 内のサービスに仮想ネットワークを接続できます。 サービス プロバイダーは独自のローカル仮想ネットワークでサービスを提供することができ、また、コンシューマーは自身のローカル仮想ネットワークでサービスにアクセスすることができます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 
 
- **オンプレミスおよびピアリングされたネットワーク**: Azure で実行されているサービスに対し、ExpressRoute プライベート ピアリングや VPN トンネル経由でオンプレミスから、さらには、ピアリングされた仮想ネットワークから、プライベート エンドポイントを使用してアクセスすることができます。 ExpressRoute Microsoft ピアリングを構成したり、インターネットを経由してサービスに接続したりする必要はありません。 Private Link を使用すれば、Azure にワークロードを安全に移行することができます。
 
- **データの漏えいに対する保護**: プライベート エンドポイントは、サービス全体にではなく、PaaS リソースの特定のインスタンスにマップされます。 コンシューマーが接続できるのは、その特定のリソースだけです。 サービス内の他のリソースへのアクセスはブロックされます。 このメカニズムにより、データの漏えいリスクを防ぐことができます。 
 
- **グローバルな展開**: 他のリージョンで実行中のサービスにプライベートに接続します。 リージョン A にあるコンシューマーの仮想ネットワークから、リージョン B の、Private Link の背後にあるサービスに接続することができます。  
 
- **独自のサービスへの拡張**: Azure 上のコンシューマーに対し、同じエクスペリエンスと機能でプライベートにサービスを提供できます。 Standard Azure Load Balancer の内側にサービスを配置することで、サービスを Private Link 対応にすることができます。 そうすれば、コンシューマーは自身の仮想ネットワーク内のプライベート エンドポイントを使用して直接そのサービスに接続できます。 接続要求は、承認呼び出しフローを使用して管理できます。 Azure Private Link は、異なる Azure Active Directory テナントに属するコンシューマーとサービスに対しても機能します。 

## <a name="availability"></a>利用可能なサービス 
 次の表に、Private Link サービスと、それらを使用可能なリージョンの一覧を示します。 

|サポートされているサービス  |対応リージョン | その他の注意点 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Standard Azure Load Balancer の背後にある Private Link サービス | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン  | Standard Load Balancer でサポートされます | GA <br/> [プライベート リンク サービスの作成方法を確認します。](create-private-link-service-portal.md) |
| Azure Blob storage (Data Lake Storage Gen2 を含む)       |  すべてのパブリック リージョン<br/> すべての Government リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [Blob Storage のプライベート エンドポイントを作成する方法を確認します。](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | すべてのパブリック リージョン<br/> すべての Government リージョン      | |   GA <br/> [Azure Files ネットワーク エンドポイントを作成する方法を確認します。](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | すべてのパブリック リージョン      | |   GA <br/> [Azure Files ネットワーク エンドポイントを作成する方法を確認します。](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  すべてのパブリック リージョン<br/> すべての Government リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [Queue storage のプライベート エンドポイントを作成する方法を確認します。](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  すべてのパブリック リージョン<br/> すべての Government リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [Table Storage のプライベート エンドポイントを作成する方法を確認します。](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL データベース         | すべてのパブリック リージョン <br/> すべての Government リージョン<br/>すべての中国リージョン      |  プロキシの[接続ポリシー](../azure-sql/database/connectivity-architecture.md#connection-policy)についてサポートされます | GA <br/> [Azure SQL のプライベート エンドポイントを作成する方法を確認します](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| すべてのパブリック リージョン <br/> すべての Government リージョン |  プロキシの[接続ポリシー](../azure-sql/database/connectivity-architecture.md#connection-policy)についてサポートされます |GA <br/> [Azure Synapse Analytics のプライベート エンドポイントを作成する方法を確認します。](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  すべてのパブリック リージョン<br/> すべての Government リージョン</br> すべての中国リージョン | |GA <br/> [Cosmos DB のプライベート エンドポイントを作成する方法を確認します。](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - 単一サーバー         | すべてのパブリック リージョン <br/> すべての Government リージョン<br/>すべての中国リージョン     | General Purpose とメモリ最適化の価格レベルでサポートされます | GA <br/> [Azure Database for PostgreSQL のプライベート エンドポイントを作成する方法を確認します。](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン      |  | GA <br/> [Azure Database for MySQL のプライベート エンドポイントを作成する方法を確認します。](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン     |  | GA <br/> [Azure Database for MariaDB のプライベート エンドポイントを作成する方法を確認します。](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure Digital Twins         | Azure Digital Twins でサポートされるすべてのパブリック リージョン     |  | プレビュー <br/> [Azure Digital Twins のプライベート エンドポイントを作成する方法を確認します。](../digital-twins/how-to-enable-private-link.md)      |
|  Azure Key Vault         | すべてのパブリック リージョン<br/> すべての Government リージョン      |  | GA   <br/> [Azure Key Vault のプライベート エンドポイントを作成する方法を確認します。](../key-vault/general/private-link-service.md)   |
|Azure Kubernetes Service - Kubernetes API | すべてのパブリック リージョン      |  | GA   <br/> [Azure Kubernetes Service のプライベート エンドポイントを作成する方法を確認します。](../aks/private-clusters.md)   |
|Azure Search | すべてのパブリック リージョン <br/> すべての Government リージョン | プライベート モードのサービスでサポートされます | GA   <br/> [Azure Search のプライベート エンドポイントを作成する方法を確認します。](../search/service-create-private-endpoint.md)    |
|Azure Container Registry | すべてのパブリック リージョン<br/> すべての Government リージョン    | コンテナー レジストリの Premium レベルでサポートされます。 [レベルを選択してください](../container-registry/container-registry-skus.md)| GA   <br/> [Azure Container Registry のプライベート エンドポイントを作成する方法を確認します。](../container-registry/container-registry-private-link.md)   |
|Azure App Configuration | すべてのパブリック リージョン      |  | プレビュー  </br> [Azure App Configuration のプライベート エンドポイントを作成する方法を確認します](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Backup | すべてのパブリック リージョン<br/> すべての Government リージョン   |  | GA   <br/> [Azure Backup のプライベート エンドポイントを作成する方法を確認します。](../backup/private-endpoints.md)   |
|Azure Event Hub | すべてのパブリック リージョン<br/>すべての Government リージョン      |   | GA   <br/> [Azure Event Hub のプライベート エンドポイントを作成する方法を確認します。](../event-hubs/private-link-service.md)  |
|Azure Service Bus | すべてのパブリック リージョン<br/>すべての Government リージョン  | Azure Service Bus の Premium レベルでサポートされます。 [レベルを選択してください](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Azure Service Bus のプライベート エンドポイントを作成する方法を確認します。](../service-bus-messaging/private-link-service.md)    |
|Azure Relay | すべてのパブリック リージョン      |  | プレビュー <br/> [Azure Relay のプライベート エンドポイントを作成する方法を確認します。](../azure-relay/private-link-service.md)  |
|Azure Event Grid| すべてのパブリック リージョン<br/> すべての Government リージョン       |  | GA   <br/> [Azure Event Grid のプライベート エンドポイントを作成する方法を確認します。](../event-grid/network-security.md) |
|Azure Web Apps | すべてのパブリック リージョン      | PremiumV2、PremiumV3、または Function Premium プランでサポートされます  | GA   <br/> [Azure Web Apps のプライベート エンドポイントを作成する方法を確認します。](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | すべてのパブリック リージョン    |  | GA   <br/> [Azure Machine Learning のプライベート エンドポイントを作成する方法を確認します。](../machine-learning/how-to-configure-private-link.md)   |
| Azure Automation  | すべてのパブリック リージョン<br/> すべての Government リージョン |  | プレビュー </br> [Azure Automation のプライベート エンドポイントを作成する方法を確認します。](../automation/how-to/private-link-security.md)| |
| Azure IoT Hub | すべてのパブリック リージョン    |  | GA   <br/> [Azure IoT Hub のプライベート エンドポイントを作成する方法を確認します。](../iot-hub/virtual-network-support.md) |
| Azure SignalR | 米国東部、米国中南部、<br/>米国西部 2、すべての中国リージョン      |  | プレビュー   <br/> [Azure SignalR のプライベート エンドポイントを作成する方法を確認します。](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(ログ分析と Application Insights) | すべてのパブリック リージョン      |  | GA   <br/> [Azure Monitor のプライベート エンドポイントを作成する方法を確認します。](../azure-monitor/platform/private-link-security.md)   | 
| Azure Batch | 以下を除くすべてのパブリック リージョン:ドイツ中部、ドイツ北東部 <br/> すべての Government リージョン  | | GA <br/> [Azure Batch のプライベート エンドポイントを作成する方法を確認します。](../batch/private-connectivity.md) |
|Azure Data Factory | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン    | 資格情報は Azure Key Vault に格納する必要があります| GA   <br/> [Azure Data Factory のプライベート エンドポイントを作成する方法を確認します。](../data-factory/data-factory-private-link.md)   |
|Azure Managed Disks | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン    | [既知の制限についてはここをクリックしてください](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Azure Managed Disks のプライベート エンドポイントを作成する方法を確認します。](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |



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