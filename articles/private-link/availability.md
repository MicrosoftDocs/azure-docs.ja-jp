---
title: Azure Private Link の可用性
description: この記事では、Private Link をサポートしている Azure サービスについて説明します。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555172"
---
# <a name="azure-private-link-availability"></a>Azure Private Link の可用性

Azure Private Link を使用すると、お使いの仮想ネットワーク内の[プライベート エンドポイント](private-endpoint-overview.md)経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできます。 

> [!IMPORTANT]
> Azure Private Link は、現在、一般提供されています。 プライベート エンドポイントと Private Link サービス (Standard ロード バランサーの背後にあるサービス) の両方が一般提供されています。 さまざまな Azure PaaS が異なるスケジュールで Azure Private Link にオンボードされます。 既知の制約については、[プライベート エンドポイント](private-endpoint-overview.md#limitations)と [Private Link サービス](private-link-service-overview.md#limitations)の説明を参照してください。 

## <a name="service-availability"></a>サービスの提供状況

次の表に、Private Link サービスと、それらを使用可能なリージョンの一覧を示します。 

### <a name="ai--machine-learning"></a>AI + 機械学習

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | すべてのパブリック リージョン    |  | GA   <br/> [Azure Machine Learning のプライベート エンドポイントを作成する方法を確認します。](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analytics

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| すべてのパブリック リージョン <br/> すべての Government リージョン |  プロキシの[接続ポリシー](../azure-sql/database/connectivity-architecture.md#connection-policy)についてサポートされます |GA <br/> [Azure Synapse Analytics のプライベート エンドポイントを作成する方法を確認します。](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | すべてのパブリック リージョン<br/>すべての Government リージョン      |   | GA   <br/> [Azure Event Hub のプライベート エンドポイントを作成する方法を確認します。](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(ログ分析と Application Insights) | すべてのパブリック リージョン      |  | GA   <br/> [Azure Monitor のプライベート エンドポイントを作成する方法を確認します。](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン    | 資格情報は Azure Key Vault に格納する必要があります| GA   <br/> [Azure Data Factory のプライベート エンドポイントを作成する方法を確認します。](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | すべてのパブリック リージョン      |  | プレビュー  </br> [Azure App Configuration のプライベート エンドポイントを作成する方法を確認します](../azure-app-configuration/concept-private-endpoint.md) |
|Azure マネージド ディスク | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン    | [既知の制限についてはこちらを選択してください](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Azure Managed Disks のプライベート エンドポイントを作成する方法を確認します。](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Containers

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | すべてのパブリック リージョン<br/> すべての Government リージョン    | コンテナー レジストリの Premium レベルでサポートされます。 [レベルを選択してください](../container-registry/container-registry-skus.md)| GA   <br/> [Azure Container Registry のプライベート エンドポイントを作成する方法を確認します。](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service - Kubernetes API | すべてのパブリック リージョン      |  | GA   <br/> [Azure Kubernetes Service のプライベート エンドポイントを作成する方法を確認します。](../aks/private-clusters.md)   |

### <a name="databases"></a>データベース

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL データベース         | すべてのパブリック リージョン <br/> すべての Government リージョン<br/>すべての中国リージョン      |  プロキシの[接続ポリシー](../azure-sql/database/connectivity-architecture.md#connection-policy)についてサポートされます | GA <br/> [Azure SQL のプライベート エンドポイントを作成する方法を確認します](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  すべてのパブリック リージョン<br/> すべての Government リージョン</br> すべての中国リージョン | |GA <br/> [Cosmos DB のプライベート エンドポイントを作成する方法を確認します。](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - 単一サーバー         | すべてのパブリック リージョン <br/> すべての Government リージョン<br/>すべての中国リージョン     | General Purpose とメモリ最適化の価格レベルでサポートされます | GA <br/> [Azure Database for PostgreSQL のプライベート エンドポイントを作成する方法を確認します。](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン      |  | GA <br/> [Azure Database for MySQL のプライベート エンドポイントを作成する方法を確認します。](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン     |  | GA <br/> [Azure Database for MariaDB のプライベート エンドポイントを作成する方法を確認します。](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>統合

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| すべてのパブリック リージョン<br/> すべての Government リージョン       |  | GA   <br/> [Azure Event Grid のプライベート エンドポイントを作成する方法を確認します。](../event-grid/network-security.md) |
|Azure Service Bus | すべてのパブリック リージョン<br/>すべての Government リージョン  | Azure Service Bus の Premium レベルでサポートされます。 [レベルを選択してください](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Azure Service Bus のプライベート エンドポイントを作成する方法を確認します。](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>モノのインターネット(IoT)

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | すべてのパブリック リージョン    |  | GA   <br/> [Azure IoT Hub のプライベート エンドポイントを作成する方法を確認します。](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Azure Digital Twins でサポートされるすべてのパブリック リージョン     |  | プレビュー <br/> [Azure Digital Twins のプライベート エンドポイントを作成する方法を確認します。](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>管理とガバナンス

| サポートされているサービス | 対応リージョン | その他の考慮事項 | Status  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | すべてのパブリック リージョン<br/> すべての Government リージョン |  | プレビュー </br> [Azure Automation のプライベート エンドポイントを作成する方法を確認します。](../automation/how-to/private-link-security.md)|
|Azure Backup | すべてのパブリック リージョン<br/> すべての Government リージョン   |  | GA <br/> [Azure Backup のプライベート エンドポイントを作成する方法を確認します。](../backup/private-endpoints.md)   |

### <a name="security"></a>セキュリティ

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | すべてのパブリック リージョン<br/> すべての Government リージョン      |  | GA   <br/> [Azure Key Vault のプライベート エンドポイントを作成する方法を確認します。](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>ストレージ
|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob storage (Data Lake Storage Gen2 を含む)       |  すべてのパブリック リージョン<br/> すべての Government リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [Blob Storage のプライベート エンドポイントを作成する方法を確認します。](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | すべてのパブリック リージョン<br/> すべての Government リージョン      | |   GA <br/> [Azure Files ネットワーク エンドポイントを作成する方法を確認します。](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | すべてのパブリック リージョン      | |   GA <br/> [Azure Files ネットワーク エンドポイントを作成する方法を確認します。](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  すべてのパブリック リージョン<br/> すべての Government リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [Queue storage のプライベート エンドポイントを作成する方法を確認します。](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  すべてのパブリック リージョン<br/> すべての Government リージョン       |  アカウントの種類 General Purpose V2 でサポートされます | GA <br/> [Table Storage のプライベート エンドポイントを作成する方法を確認します。](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | 以下を除くすべてのパブリック リージョン:ドイツ中部、ドイツ北東部 <br/> すべての Government リージョン  | | GA <br/> [Azure Batch のプライベート エンドポイントを作成する方法を確認します。](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | 米国東部、米国中南部、<br/>米国西部 2、すべての中国リージョン      |  | プレビュー   <br/> [Azure SignalR のプライベート エンドポイントを作成する方法を確認します。](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | すべてのパブリック リージョン<br/> 中国北部 2 および東部 2    | PremiumV2、PremiumV3、または Function Premium プランでサポートされます  | GA   <br/> [Azure Web Apps のプライベート エンドポイントを作成する方法を確認します。](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | すべてのパブリック リージョン <br/> すべての Government リージョン | プライベート モードのサービスでサポートされます | GA   <br/> [Azure Search のプライベート エンドポイントを作成する方法を確認します。](../search/service-create-private-endpoint.md)    |
|Azure Relay | すべてのパブリック リージョン      |  | プレビュー <br/> [Azure Relay のプライベート エンドポイントを作成する方法を確認します。](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Standard Load Balancer を使用した Private Link サービス

|サポートされているサービス  |対応リージョン | その他の考慮事項 | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Standard Azure Load Balancer の背後にある Private Link サービス | すべてのパブリック リージョン<br/> すべての Government リージョン<br/>すべての中国リージョン  | Standard Load Balancer でサポートされます | GA <br/> [プライベート リンク サービスの作成方法を確認します。](create-private-link-service-portal.md) |

## <a name="next-steps"></a>次のステップ

Azure Private Link サービスについてさらに学習する
- [Azure Private Link とは](private-link-overview.md)
- [Azure portal を使用してプライベート エンドポイントを作成する](create-private-endpoint-portal.md)
