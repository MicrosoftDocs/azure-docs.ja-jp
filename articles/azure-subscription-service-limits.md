---
title: "Azure サブスクリプションの制限とクォータ | Microsoft Docs"
description: "一般的な Azure サブスクリプションとサービスの制限、クォータ、制約の一覧を示します。 制限を引き上げる方法と、最大値に関する情報も記載されています。"
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 8b4818ccea1ac5025e93fa0939735b3ed606f47c
ms.lasthandoff: 03/22/2017


---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure サブスクリプションとサービスの制限、クォータ、制約
このドキュメントでは、最も一般的な Microsoft Azure の制限を一覧表示しています。これはクォータと呼ばれることもあります。 現時点では、すべての Azure サービスをカバーするものではありません。 今後、プラットフォームを広くカバーするように一覧を拡大し、更新していく予定です。

Azure の価格については、 [Azure の価格の概要](https://azure.microsoft.com/pricing/) に関するページを参照してください。 価格の概要に関するページでは、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用したり、サービス (たとえば、[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)) の価格の詳細ページに移動したりして、費用を見積もることができます。 コスト管理に役立つヒントについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing/billing-getting-started.md)」をご覧ください。

> [!NOTE]
> **既定の制限**を超える制限またはクォータの引き上げを希望される場合は、[オンライン カスタマー サポートに申請 (無料)](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) してください。 次のテーブルにある**上限**の値を超える制限の引き上げはできません。 **上限**列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。 
> 
> 無料試用版サブスクリプションは、制限およびクォータ引き上げの適用対象外です。 無料試用版をお持ちの場合、 [従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/) のサブスクリプションにアップグレードしてください。 詳細については、「[Azure 無料試用版を従量課金制にアップグレード](billing-upgrade-azure-subscription.md)」を参照してください。
> 

## <a name="limits-and-the-azure-resource-manager"></a>制限と Azure リソース マネージャー
複数の Azure リソースを 1 つの Azure リソース グループに結合できるようになりました。 リソース グループを使用するとき、グローバルになった制限が Azure リソース マネージャーによりリージョン レベルでの管理対象になります。 Azure リソース グループの詳細については、「 [Azure Resource Manager の概要](azure-resource-manager/resource-group-overview.md)」を参照してください。

次の制限では、Azure リソース マネージャーを使用する際の制限の相違点を反映するために新しい表が追加されました。 たとえば、**サブスクリプションの制限**の表と、**サブスクリプションの制限 - Azure Resource Manager**の表などがあります。 制限が 2 つのシナリオに適用される場合、制限は最初の表にのみ表示されます。 特に記載のない限り、制限はすべてのリージョンに適用されます。

> [!NOTE]
> Azure リソース グループ内のリソースのクォータは、サブスクリプションごとではなく、サブスクリプションのリージョンごとにアクセスできることに注意してください。また、サービス管理クォータも同様です。 コア クォータを例に説明します。 コア対応のクォータの増量をリクエストする場合、どのリージョンでいくつのコアを使用するかを決めてから、Azure リソース グループのコア クォータの数量と目的のリージョンをリクエストする必要があります。 したがって、西ヨーロッパで 30 のコアを使用してアプリケーションを実行する必要がある場合、具体的に、西ヨーロッパで 30 のコアをリクエストしてください。 この場合、他のリージョンのコア クォータは増量されません -- 西ヨーロッパでのみ 30 コア クォータが増量されます。
> <!-- -->
> 結果として、1 つのリージョンのワークロードに対して Azure リソース グループのクォータがどれくらい必要かを判断し、デプロイメントを検討している各リージョンでその量を要求するのが便利です。 特定のリージョンの現在のクォータを判断する方法について詳しくは、 [デプロイメントの問題のトラブルシューティング](resource-manager-common-deployment-errors.md) に関するページを参照してください。
> 
> 

## <a name="service-specific-limits"></a>サービス固有の制限
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [DocumentDB](#documentdb-limits)
* [Event Hubs](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics / Operational Insights](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [監視](#monitoring-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [ネットワーク](#networking-limits)
* [Notification Hub Service](#notification-hub-service-limits)
* [リソース グループ](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [Search](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [Storage](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [サブスクリプション](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [仮想マシン スケール セット](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>サブスクリプションの制限
#### <a name="subscription-limits"></a>サブスクリプションの制限
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>サブスクリプションの制限 - Azure Resource Manager
次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。 Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。 これらの制限については、前の表を参照してください。

Resource Manager 要求の処理制限については、[Throttling Resource Manager requests (Resource Manager 要求を調整する)](resource-manager-request-limits.md)をご覧ください。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>リソース グループの制限
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines の制限
#### <a name="virtual-machine-limits"></a>仮想マシンの制限
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines の制限 - Azure リソース マネージャー
次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。 Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。 これらの制限については、前の表を参照してください。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>仮想マシン スケールセットの制限
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>ネットワークの制限
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>ネットワークの制限
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway の制限
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager の制限
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS の制限
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>ストレージの制限
ストレージ アカウントの上限の詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage/storage-scalability-targets.md)」を参照してください。
<!--like # storage accts --> 
#### <a name="storage-service-limits"></a>ストレージ サービスの制限
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>仮想マシン ディスクの制限 
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

その他の詳細については、「 [仮想マシンのサイズ](virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 」を参照してください。

#### <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>ストレージ リソース プロバイダーの制限
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>Cloud Services の制限
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service の制限
次の App Service の制限には、Web Apps、Mobile Apps、API Apps、および Logic Apps の制限が含まれます。

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Scheduler の制限
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch の制限
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services の制限
次の表に Azure Biztalk Services の制限内容を示します。

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="documentdb-limits"></a>DocumentDB の制限
DocumentDB は世界規模のデータベースであり、アプリケーションで要求されることをすべて処理するようにスループットとストレージをスケールできます。 DocumentDB が提供するスケールについて質問がある場合は、askdocdb@microsoft.com に電子メールをお送りください。

### <a name="mobile-engagement-limits"></a>Mobile Engagement の制限
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Search の制限
価格レベルによって、容量と検索サービスの制限が決定されます。 レベルは次のとおりです。

* *Free* : 他の Azure サブスクライバーと共有する、評価および小規模の開発プロジェクトのためのマルチ テナント サービス。
* *Basic*: 小規模環境で運用ワークロードに対して専用コンピューティング リソースを提供します。高可用性のクエリ ワークロード用に最大 3 つのレプリカを備えています。
* *Standard (S1、S2、S3、S3 High Density)* : 大規模な運用ワークロード向けです。 Standard レベルは、ワークロード プロファイルに最適なリソース構成を選択できるように、複数のレベルに分かれています。

**サブスクリプションあたりの制限**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Search サービスあたりの制限**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

ドキュメント サイズ、1 秒あたりのクエリ数、キー数、要求数、応答数などのより細かなレベルでの制限の詳細については、「[Azure Search サービスの制限](search/search-limits-quotas-capacity.md)」を参照してください。

### <a name="media-services-limits"></a>Media Services の制限
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN の制限
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services の制限
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>監視の制限
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Notification Hubs サービスの制限
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs の制限
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus の制限
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub の制限
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Data Factory の制限
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics の制限
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store の制限
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics の制限
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory の制限
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-remoteapp-limits"></a>Azure RemoteApp の制限
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple システムの制限
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics の制限
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Backup の制限
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Site Recovery の制限
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights の制限
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API Management の制限
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis Cache の制限
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault の制限
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation の制限
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL Database の制限
SQL Database の制限については、「 [SQL Database のリソース制限](sql-database/sql-database-resource-limits.md)」を参照してください。

## <a name="see-also"></a>関連項目
[Understanding Azure Limits and Increases (Azure の制限と増設について)](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure の仮想マシンおよびクラウド サービスのサイズ](virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[クラウド サービスのサイズ](cloud-services/cloud-services-sizes-specs.md)


