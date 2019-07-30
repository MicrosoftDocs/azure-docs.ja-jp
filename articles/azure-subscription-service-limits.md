---
title: Azure サブスクリプションの制限とクォータ
description: 一般的な Azure サブスクリプションとサービスの制限、クォータ、制約の一覧を示します。 この記事では、制限を引き上げる方法と、最大値に関する情報について説明します。
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: d1043f254d2a2cc2804ab8a4d68770b6d57cbed4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295946"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure サブスクリプションとサービスの制限、クォータ、制約
このドキュメントでは、最も一般的な Microsoft Azure の制限を一覧表示しています。これはクォータと呼ばれることもあります。 現時点では、すべての Azure サービスをカバーするものではありません。 今後、より多くのサービスをカバーするように一覧を拡大し、更新していく予定です。

Azure の価格の詳細については、[Azure の価格の概要](https://azure.microsoft.com/pricing/)に関するページをご覧ください。 そこでは、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もることができます。 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) などの特定のサービスに関する価格詳細ページに移動することもできます。 コスト管理に役立つヒントについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing/billing-getting-started.md)」をご覧ください。

> [!NOTE]
> 制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請 (無料)](azure-resource-manager/resource-manager-quota-errors.md) してください。 次の表に示されている上限の値を超える制限の引き上げはできません。 上限列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。
>
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳しくは、[無料試用版の従量課金制へのアップグレード](billing/billing-upgrade-azure-subscription.md)および[無料試用版サブスクリプションの FAQ](https://azure.microsoft.com/free/free-account-faq) に関する記事をご覧ください。
>

## <a name="limits-and-azure-resource-manager"></a>制限と Azure Resource Manager
複数の Azure リソースを 1 つの Azure リソース グループに結合できるようになりました。 リソース グループを使用すると、グローバルであった制限が、Azure Resource Manager によるリージョン レベルでの管理対象になります。 Azure リソース グループについて詳しくは、「[Azure Resource Manager の概要](azure-resource-manager/resource-group-overview.md)」をご覧ください。

次の制限の一覧で、新しい表には Azure Resource Manager を使用するときの制限の相違が反映されます。 たとえば、「**サブスクリプションの制限**」の表と、「**サブスクリプションの制限 - Azure Resource Manager**」の表があります。 制限が 2 つのシナリオに適用される場合、制限は最初の表でのみ示されています。 特に記載のない限り、制限はすべてのリージョンに適用されます。

> [!NOTE]
> Azure リソース グループ内のリソースのクォータは、サービス管理クォータと同様に、サブスクリプションごとではなく、サブスクリプションによってアクセス可能なリージョンごとです。 vCPU クォータを例に説明します。 vCPU のサポートでのクォータ引き上げを要求するには、どのリージョンでいくつの vCPU を使用するかを決める必要があります。 その後、必要な数量とリージョンについて、Azure リソース グループの vCPU クォータの具体的な要求を行います。 西ヨーロッパで 30 の vCPU を使用してアプリケーションを実行する必要がある場合、具体的に、西ヨーロッパで 30 の vCPU を要求します。 他のリージョンでの vCPU クォータは増やされず、西ヨーロッパだけが 30 vCPU クォータになります。
> <!-- -->
> つまり、1 つのリージョンのワークロードに対して必要な Azure リソース グループ クォータを決定します。 その後、デプロイする各リージョンでの量を要求します。 特定のリージョンでの現在のクォータを確認する方法について詳しくは、[デプロイに関する問題のトラブルシューティング](resource-manager-common-deployment-errors.md)に関するページをご覧ください。
>
>

## <a name="service-specific-limits"></a>サービス固有の制限
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning サービス](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [コンテナー レジストリ](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning Service](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [ネットワーク](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [パブリック IP アドレス](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [リソース グループ](#resource-group-limits)
* [ロールベースのアクセス制御](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [サブスクリプション](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [仮想マシン スケール セット](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>サブスクリプションの制限
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>サブスクリプションの制限 - Azure Service Management (クラシック デプロイ モデル)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>サブスクリプションの制限 - Azure Resource Manager
次の制限は、Azure Resource Manager と Azure リソース グループを使用するときに適用されます。 Azure Resource Manager で変更されていない制限は一覧に含まれません。 これらの制限については、前の表を参照してください。

Resource Manager API の読み取りと書き込みの制限については、「[Resource Manager の要求のスロットル](resource-manager-request-limits.md)」を参照してください。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>リソース グループの制限
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines の制限
#### <a name="virtual-machines-limits"></a>Virtual Machines の制限
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines の制限 - Azure リソース マネージャー
次の制限は、Azure Resource Manager と Azure リソース グループを使用するときに適用されます。 Azure Resource Manager で変更されていない制限は一覧に含まれません。 これらの制限については、前の表を参照してください。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>共有イメージ ギャラリーの制限

共有イメージ ギャラリーを使用したリソースのデプロイに対しては、サブスクリプションあたりの制限があります。
- 100 個の共有イメージ ギャラリー (サブスクリプション別、リージョン別)
- 1,000 個のイメージ定義 (サブスクリプション別、リージョン別)
- 10,000 個のイメージ バージョン (サブスクリプション別、リージョン別)

### <a name="virtual-machine-scale-sets-limits"></a>仮想マシン スケール セットの制限
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances の制限
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry の制限
次の表に、Basic、Standard、および Premium [サービス階層](./container-registry/container-registry-skus.md)の機能と制限について説明します。

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service の制限
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning サービスの制限
Azure Machine Learning コンピューティング クォータの最新の値については、[Azure Machine Learning クォータ ページ](../articles/machine-learning/service/how-to-manage-quotas.md)をご覧ください。

### <a name="networking-limits"></a>ネットワークの制限
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute の制限
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway の制限

特に記載のない限り、次の表は v1、v2、Standard、および WAF SKU に適用されます。
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher の制限
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager の制限
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS の制限
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Firewall の制限
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure Front Door Service の制限
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>ストレージの制限
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

ストレージ アカウントの制限について詳しくは、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage/common/storage-scalability-targets.md)」をご覧ください。

#### <a name="storage-resource-provider-limits"></a>ストレージ リソース プロバイダーの制限 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure BLOB ストレージの制限
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files の制限
ストレージ アカウントの制限について詳しくは、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](storage/files/storage-files-scale-targets.md)」をご覧ください。

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure File Sync の制限
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue Storage の制限
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table Storage の制限
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>仮想マシン ディスクの制限
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

詳しくは、[仮想マシンのサイズ](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

#### <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services の制限
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service の制限
次の App Service の制限には、Web Apps、Mobile Apps、および API Apps の制限が含まれます。

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Functions の制限
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Scheduler の制限
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch の制限
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services の制限
次の表に Azure BizTalk Services の制限内容を示します。

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB の制限
Azure Cosmos DB の制限については、「[Azure Cosmos DB の制限](cosmos-db/concepts-limits.md)」を参照してください。

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database for MySQL の制限については、「[Azure Database for MySQL の制限事項](mysql/concepts-limits.md)」を参照してください。

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database for PostgreSQL の制限については、「[Azure Database for PostgreSQL の制限事項](postgresql/concepts-limits.md)」を参照してください。

### <a name="azure-search-limits"></a>Azure Search の制限
価格レベルによって、容量と検索サービスの制限が決定されます。 レベルは次のとおりです。

* **Free**: 他の Azure サブスクライバーと共有する、評価および小規模の開発プロジェクトのためのマルチ テナント サービスです。
* **Basic**: 小規模環境で運用ワークロードに対して専用コンピューティング リソースを提供します。高可用性のクエリ ワークロード用に最大 3 つのレプリカを備えています。
* **Standard**: S1、S2、S3、S3 High Density が含まれ、大規模な運用ワークロード向けです。 Standard レベルは、ワークロード プロファイルに最適なリソース構成を選択できるように、複数のレベルにわかれています。

**サブスクリプションあたりの制限**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Search サービスあたりの制限**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

ドキュメント サイズ、1 秒あたりのクエリ数、キー数、要求数、応答数などのより細かなレベルでの制限の詳細については、「[Azure Search サービスの制限](search/search-limits-quotas-capacity.md)」を参照してください。

### <a name="media-services-limits"></a>Media Services の制限
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network の制限
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services の制限
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor の制限

#### <a name="alerts"></a>アラート

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>アクション グループ

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-analytics-workspaces"></a>Log Analytics ワークスペース

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs の制限
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs の制限
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus の制限
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub の制限
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service の制限
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory の制限
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics の制限
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store の制限
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service の制限
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics の制限
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory の制限
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid の制限
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps の制限
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy の制限
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple システムの制限
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Backup の制限
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR Service の制限
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Site Recovery の制限
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>API Management の制限
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis の制限
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault の制限
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication の制限
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation の制限
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager の制限
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>ロール ベースのアクセス制御の制限
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database の制限
SQL Database の制限については、[単一データベースの SQL Database のリソース制限](sql-database/sql-database-vcore-resource-limits-single-databases.md)、[エラスティック プールとプールされたデータベースの SQL Database のリソース制限](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)、および[マネージド インスタンスに対する SQL Database のリソース制限](sql-database/sql-database-managed-instance-resource-limits.md)に関する記事をご覧ください。

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse の制限
SQL Data Warehouse の制限については、[SQL Data Warehouse のリソース制限](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)に関する記事をご覧ください。

## <a name="see-also"></a>関連項目
- [Azure の制限と増設について](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Azure での仮想マシンとクラウド サービスのサイズ](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Cloud Services のサイズ](cloud-services/cloud-services-sizes-specs.md)
