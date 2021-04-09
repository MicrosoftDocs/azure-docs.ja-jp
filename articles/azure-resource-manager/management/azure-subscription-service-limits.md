---
title: Azure サブスクリプションの制限とクォータ
description: 一般的な Azure サブスクリプションとサービスの制限、クォータ、制約の一覧を示します。 この記事では、制限を引き上げる方法と、最大値に関する情報について説明します。
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: 4dd62c926cce765ebc2b21378308650e313716ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471015"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure サブスクリプションとサービスの制限、クォータ、制約

このドキュメントでは、最も一般的な Microsoft Azure の制限を一覧表示しています。これはクォータと呼ばれることもあります。

Azure の価格の詳細については、[Azure の価格の概要](https://azure.microsoft.com/pricing/)に関するページをご覧ください。 そこでは、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もることができます。 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) などの特定のサービスに関する価格詳細ページに移動することもできます。 コスト管理に役立つヒントについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](../../cost-management-billing/cost-management-billing-overview.md)」をご覧ください。

## <a name="managing-limits"></a>管理の制限

> [!NOTE]
> 一部のサービスには、調整可能な制限があります。
>
> 調整可能な制限がサービスにない場合、以下の表では "**制限**" の見出しを使用します。 その場合、既定値と上限値は同じです。
>
> 制限を調整できる場合、表には "**既定の制限**" および "**最大制限**" の見出しが含まれます。 既定の制限を超えて制限を引き上げることはできますが、最大制限を超えることはできません。
>
> 制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請 (無料)](../templates/error-resource-quota.md) してください。
>
> "*ソフト制限*" と "*ハード制限*" という用語が、現在調整可能な制限 (ソフト制限) と最大の制限 (ハード制限) を言い表すために非公式に使用されることがよくあります。 制限を調整できない場合、ソフト制限はなく、ハード制限のみ存在します。
>

[無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳しくは、[無料試用版の従量課金制へのアップグレード](../../cost-management-billing/manage/upgrade-azure-subscription.md)および[無料試用版サブスクリプションの FAQ](https://azure.microsoft.com/free/free-account-faq) に関する記事をご覧ください。

一部の制限は、リージョンのレベルで管理されます。

vCPU クォータを例に説明します。 vCPU のサポートでのクォータ引き上げを要求するには、どのリージョンでいくつの vCPU を使用するかを決める必要があります。 その後、必要な数量とリージョンについて、Azure リソース グループの vCPU クォータの具体的な要求を行います。 西ヨーロッパで 30 の vCPU を使用してアプリケーションを実行する必要がある場合、具体的に、西ヨーロッパで 30 の vCPU を要求します。 他のリージョンでの vCPU クォータは増やされず、西ヨーロッパだけが 30 vCPU クォータになります。

つまり、1 つのリージョンのワークロードに対して必要な Azure リソース グループ クォータを決定します。 その後、デプロイする各リージョンでの量を要求します。 特定のリージョンでの現在のクォータを確認する方法について詳しくは、「[リソース クォータのエラーを解決する](../templates/error-resource-quota.md)」を参照してください。

## <a name="general-limits"></a>全般的な制限

リソース名の制限については、 [Azureリソースに関する名前付け規則と制限事項](resource-name-rules.md)を参照してください。

Resource Manager API の読み取りと書き込みの制限については、「[Resource Manager の要求のスロットル](request-limits-and-throttling.md)」を参照してください。

### <a name="management-group-limits"></a>管理グループの制限

[管理グループ](../../governance/management-groups/overview.md)には、以下の制限が適用されます。

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>サブスクリプションの制限

次の制限は、Azure Resource Manager と Azure リソース グループを使用するときに適用されます。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>リソース グループの制限

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory の制限

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management の制限

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service の制限

次の App Service の制限には、Web Apps、Mobile Apps、および API Apps の制限が含まれます。

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automation の制限

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure App Configuration

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis の制限

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure Cloud Services の制限

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search の制限

価格レベルによって、容量と検索サービスの制限が決定されます。 レベルは次のとおりです。

* **Free**: 他の Azure サブスクライバーと共有するマルチ テナント サービスであり、評価および小規模の開発プロジェクト向けです。
* **Basic**: 小規模環境で運用ワークロードに対して専用コンピューティング リソースを提供します。高可用性のクエリ ワークロード用に最大 3 つのレプリカを備えています。
* **Standard**: S1、S2、S3、S3 High Density が含まれ、大規模な運用ワークロード向けです。 Standard レベルは、ワークロード プロファイルに最適なリソース構成を選択できるように、複数のレベルにわかれています。

**サブスクリプションあたりの制限**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Search サービスあたりの制限**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

ドキュメント サイズ、1 秒あたりのクエリ数、キー数、要求数、応答数など、より細かなレベルでの制限の詳細については、「[Azure Cognitive Search におけるサービスの制限](../../search/search-limits-quotas-capacity.md)」を参照してください。

## <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services の制限

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB の制限

Azure Cosmos DB の制限については、「[Azure Cosmos DB の制限](../../cosmos-db/concepts-limits.md)」を参照してください。

## <a name="azure-data-explorer-limits"></a>Azure Data Explorer の制限

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL の制限については、「[Azure Database for MySQL の制限事項](../../mysql/concepts-limits.md)」を参照してください。

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Azure Database for PostgreSQL の制限については、「[Azure Database for PostgreSQL の制限事項](../../postgresql/concepts-limits.md)」を参照してください。

## <a name="azure-functions-limits"></a>Azure Functions の制限

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

詳細については、[Functions のホスティング プランの比較](../../azure-functions/functions-scale.md)に関するページを参照してください。

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service の制限

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning の制限

Azure Machine Learning コンピューティング クォータの最新の値については、[Azure Machine Learning クォータ ページ](../../machine-learning/how-to-manage-quotas.md)をご覧ください。

## <a name="azure-maps-limits"></a>Azure Maps の制限

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Monitor の制限

### <a name="alerts"></a>警告

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>アクション グループ

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>自動スケール

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>ログ クエリと言語

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics ワークスペース

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/application-insights-limits.md)]

## <a name="azure-policy-limits"></a>Azure Policy の制限

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Azure Quantum の制限

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Azure のロールベースのアクセス制御の制限

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure SignalR Service の制限

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="azure-vmware-solution-limits"></a>Azure VMware Solution の制限

[!INCLUDE [azure-vmware-solutions-limits](../../azure-vmware/includes/azure-vmware-solutions-limits.md)]

## <a name="backup-limits"></a>Backup の制限

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch の制限

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>クラシック デプロイ モデルの制限

Azure Resource Manager デプロイ モデルではなくクラシック デプロイ モデルを使用する場合は、以下の制限が適用されます。

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances の制限

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry の制限

次の表に、Basic、Standard、および Premium [サービス階層](../../container-registry/container-registry-skus.md)の機能と制限について説明します。

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Content Delivery Network の制限

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Data Factory の制限

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics の制限

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Data Lake Storage の制限

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Data Share の制限

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Database Migration Service の制限

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Digital Twins の制限

> [!NOTE]
> このサービスには、調整可能な制限がある領域と、そうではない領域があります。 これは、次の表の *調整可能?* 列に示されています。 制限が調整可能な場合、*調整可能?* の値は *はい* になります。

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Event Grid の制限

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs の制限

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>IoT Central の制限
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub の制限

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service の制限

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Key Vault の制限

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>マネージド ID の制限

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Media Services の制限

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (レガシ)

Media Services v2 (レガシ) に固有の制限については、「[Media Services v2 (レガシ)](../../media-services/previous/media-services-quotas-and-limitations.md)」を参照してください。

## <a name="mobile-services-limits"></a>Mobile Services の制限

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication の制限

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>ネットワークの制限

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute の制限

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>仮想ネットワーク ゲートウェイの制限

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>NAT Gateway の制限

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Virtual WAN の制限

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway の制限

特に記載のない限り、次の表は v1、v2、Standard、および WAF SKU に適用されます。
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Network Watcher の制限

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link の制限

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Purview の制限

Azure Purview のクォータの最新の値については、 [Azure Purview のクォータに関するページ](../../purview/how-to-manage-quotas.md)を参照してください

### <a name="traffic-manager-limits"></a>Traffic Manager の制限

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure Bastion の制限

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS の制限

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure Firewall の制限

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure Front Door Service の制限

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Notification Hubs の制限

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Service Bus の制限

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery の制限

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database の制限

SQL Database の制限については、[単一データベースの SQL Database のリソース制限](../../azure-sql/database/resource-limits-vcore-single-databases.md)、[エラスティック プールとプールされたデータベースの SQL Database のリソース制限](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)、および[SQL Managed Instance に対する SQL Database のリソース制限](../../azure-sql/managed-instance/resource-limits.md)に関する記事をご覧ください。

## <a name="azure-synapse-analytics-limits"></a>Azure Synapse Analytics の制限

Azure Synapse Analytics の制限については、[Azure Synaplse リソースの制限](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)に関する記事を参照してください。

## <a name="azure-files-and-azure-file-sync"></a>Azure Files と Azure File Sync
Azure Files と Azure File Sync の制限については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../../storage/files/storage-files-scale-targets.md)」を参照してください。

## <a name="storage-limits"></a>ストレージの制限

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Standard ストレージ アカウントの制限について詳しくは、[Standard Storage アカウントのスケーラビリティ ターゲット](../../storage/common/scalability-targets-standard-account.md)に関する記事をご覧ください。

### <a name="storage-resource-provider-limits"></a>ストレージ リソース プロバイダーの制限

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob Storage の制限

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure Queue Storage の制限

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure Table Storage の制限

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>仮想マシン ディスクの制限

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

詳しくは、[仮想マシンのサイズ](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

#### <a name="disk-encryption-sets"></a>ディスク暗号化セット

リージョンごと、サブスクリプションごとに、1000 のディスク暗号化セットという制限があります。 詳細については、[Linux](../../virtual-machines/disk-encryption.md#restrictions) または [Windows](../../virtual-machines/disk-encryption.md#restrictions) 仮想マシンの暗号化ドキュメントを参照してください。 クォータを増やす必要がある場合は、Azure サポートにお問い合わせください。

### <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple システムの制限

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics の制限

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Virtual Machines の制限

### <a name="virtual-machines-limits"></a>Virtual Machines の制限

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines の制限 - Azure リソース マネージャー

次の制限は、Azure Resource Manager と Azure リソース グループを使用するときに適用されます。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>共有イメージ ギャラリーの制限

共有イメージ ギャラリーを使用したリソースのデプロイに対しては、サブスクリプションあたりの制限があります。

- 100 個の共有イメージ ギャラリー (サブスクリプション別、リージョン別)
- 1,000 個のイメージ定義 (サブスクリプション別、リージョン別)
- 10,000 個のイメージ バージョン (サブスクリプション別、リージョン別)

## <a name="virtual-machine-scale-sets-limits"></a>仮想マシン スケール セットの制限

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>関連項目

* [Azure の制限と増設について](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Azure での仮想マシンとクラウド サービスのサイズ](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Cloud Services のサイズ](../../cloud-services/cloud-services-sizes-specs.md)
* [Azure リソースの名前付け規則と制限事項](resource-name-rules.md)
