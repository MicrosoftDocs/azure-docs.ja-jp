---
title: Azure のリージョンと Availability Zones
description: 技術要件および規制要件を満たすために、Azure のリージョンと Availability Zones について説明します。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/28/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 78f50abf68412d2edcb7a6504c8e5c1b788e5901
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413163"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure のリージョンと Availability Zones

Microsoft Azure サービスは、クラウド運用を最適なレベルで推進できるよう、グローバルに利用可能です。 サービスの機能、データ所在地、コンプライアンス要件、待機時間などの技術的および規制上の考慮事項に基づいて、ニーズに最適なリージョンを選択できます。

## <a name="terminology"></a>用語

主な用語や概念を理解することは、Azure のリージョンと Availability Zones について理解を深める上で役立ちます。

| 用語または概念 | 説明 |
| --- | --- |
| region | 待機時間の短い専用のリージョン ネットワークを介して接続される、待機時間で定義された境界内にデプロイされている一連のデータセンター。 |
| geography | 少なくとも 1 つの Azure リージョンを含む、世界のいずれかの地域。 地域によって個別のマーケットが定義され、データ所在地とコンプライアンスの境界が保持されます。 地域では、データの保存場所とコンプライアンスに関して特定の要件を持つお客様が、データとアプリケーションを近くの場所に維持できます。 地域はフォールト トレランスを備えており、Microsoft のキャパシティが大きい専用ネットワーク インフラストラクチャへの接続によってリージョン全体の障害に耐えられます。 |
| 可用性ゾーン | 一意の物理的なリージョン内の場所。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 |
| 推奨されるリージョン | 最も広範なサービス機能を提供するリージョンで、現在、または将来 Availability Zones をサポートするように設計されています。 これらは、Azure portal では **[推奨]** に指定されています。 |
| 代替 (その他の) リージョン | 推奨されるリージョンも存在するデータ所在地の境界内で Azure のフットプリントを拡張するリージョン。 代替リージョンは、待機時間を最適化し、ディザスター リカバリーのニーズに対応する 2 つ目のリージョンを提供します。 これらは、Availability Zones をサポートするようには設計されていません (ただし、Azure では、推奨されるリージョンにすべきかどうかを判断するために、これらのリージョンに対して定期的な評価を行っています)。 これらは、Azure portal では **[その他]** に指定されています。 |
| 基本サービス | リージョンが一般提供されている場合に、すべてのリージョンで利用可能なコア Azure サービス。 |
| メインストリーム サービス | リージョンまたはサービスの一般提供、または代替リージョンでの需要主導の提供から 12 か月以内にすべての推奨されるリージョンで利用可能な Azure サービス。 |
| 専用サービス | カスタマイズされたハードウェア、または専用ハードウェアによって支えられているリージョン間で需要主導で提供されている Azure サービス。 |
| リージョン サービス | リージョンごとにデプロイされた Azure サービス。顧客は、サービスのデプロイ先となるリージョンを指定できます。 詳細なリストについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all)に関するページを参照してください。 |
| リージョンに依存しないサービス | 特定の Azure リージョンへの依存関係がない Azure サービス。 リージョンに依存しないサービスは 2 つ以上のリージョンにデプロイされており、あるリージョンで障害が発生した場合は、別のリージョンのサービスのインスタンスが引き続き顧客にサービスを提供します。 詳細なリストについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all)に関するページを参照してください。 |

## <a name="regions"></a>リージョン

リージョンは、待ち時間で定義された境界内でデプロイされ、待ち時間の短い専用リージョン ネットワークを介して接続された一連のデータセンターです。 Azure では、複数のリージョンにわたってデプロイしてリージョンをまたがる回復性を提供するなど、必要な場所にアプリケーションをデプロイできる柔軟性があります。 詳細については、「[回復性の柱の概要](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)」を参照してください。

## <a name="availability-zones"></a>可用性ゾーン

Availability Zone とは高可用性を提供するサービスで、アプリケーションとデータをデータセンターの障害から保護します。 Availability Zones は、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。 Availability Zones は 1 リージョン内で物理的に分離されているため、データセンターで障害が発生した場合でもアプリケーションとデータを保護できます。 ゾーン冗長サービスによって、単一障害点から保護されるように Availability Zones 全体でアプリケーションとデータがレプリケートされます。 Availability Zones では、Azure によって業界最高の 99.99% VM アップタイム SLA が実現されます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

Azure リージョン内の可用性ゾーンは、障害ドメインと更新ドメインを組み合わせたものです。 たとえば、Azure リージョンの 3 つのゾーンに 3 つ以上の VM を作成する場合、VM は実際には 3 つの障害ドメインと 3 つの更新ドメインに分散されます。 Azure プラットフォームによって更新ドメイン全体でこの分散が認識され、異なるゾーンにある VM が同時に更新されるスケジュールにならないことが保証されます。

高可用性をアプリケーションに構築するには、コンピューティング、ストレージ、ネットワーク、およびデータ リソースを 1 つのゾーン内に併置し、他のゾーンでレプリケートします。 Availability Zones をサポートしている Azure サービスは、次の 2 つのカテゴリに分類されます。

- **ゾーン サービス** – 特定のゾーン (たとえば、仮想マシン、マネージド ディスク、標準の IP アドレス) にリソースがピンされます。
- **ゾーン冗長サービス** – Azure プラットフォームが複数のゾーンにわたって自動的にレプリケートされる場合 (ゾーン冗長ストレージ、SQL Database など) はこちらです。

Azure での包括的なビジネス継続性を実現するには、Availability Zones と Azure リージョンの組み合わせを使用してアプリケーション アーキテクチャを構築します。 1 つの Azure リージョン内で Availability Zones を使用してアプリケーションとデータを同期的にレプリケートして高可用性を実現し、複数の Azure リージョン全体で非同期的にレプリケートしてディザスター リカバリー保護を実現できます。
 
![1 リージョン内でゾーンが 1 つダウンした場合の概念図](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> 可用性ゾーン識別子 (上の図の番号 1、2、3) は、各サブスクリプションの実際の物理ゾーンに個別に論理マップされます。 つまり、特定のサブスクリプションの可用性ゾーン 1 が、別のサブスクリプションの可用性ゾーン 1 とは異なる物理ゾーンを参照している可能性があります。 そのため、仮想マシンの配置では、異なるサブスクリプション間で可用性ゾーン ID を使用しないようにお勧めします。

## <a name="region-and-service-categories"></a>リージョンとサービスのカテゴリ

リージョンをまたがる Azure サービスの可用性に関する Azure のアプローチは、推奨されるリージョンと代替リージョンで利用可能なサービスを表現することでうまく示すことができます。

- **推薦されるリージョン** - 最も広範なサービス機能を提供するリージョンで、現在、または将来 Availability Zones をサポートするように設計されています。 これらは、Azure portal では **[推奨]** に指定されています。
- **代替 (その他の) リージョン** - 推奨されるリージョンも存在するデータ所在地の境界内で Azure のフットプリントを拡張するリージョン。 代替リージョンは、待機時間を最適化し、ディザスター リカバリーのニーズに対応する 2 つ目のリージョンを提供します。 これらは、Availability Zones をサポートするようには設計されていません (ただし、Azure では、推奨されるリージョンにすべきかどうかを判断するために、これらのリージョンに対して定期的な評価を行っています)。 これらは、Azure portal では **[その他]** に指定されています。

### <a name="comparing-region-types"></a>リージョンの種類の比較

Azure サービスは、基本サービス、メインストリーム サービス、専用サービスという 3 つのカテゴリに分類されています。 特定のリージョンへのサービスのデプロイに関する Azure の一般的なポリシーは、主にリージョンの種類、サービス カテゴリ、および顧客の需要によって決まります。

- **基本** – リージョンが一般提供されている場合、または新しい基本サービスの一般提供から 12 か月以内に推奨されるリージョンと代替リージョンで利用できます。
- **メインストリーム** – リージョンまたはサービスの一般提供から 12 か月以内に、すべての推奨されるリージョンで利用できます。代替リージョンでは、需要主導です (多くは、既に別のリージョンの大きなサブセットにデプロイされています)。
- **専用** - 特に業界に重点を置いているハードウェア、またはカスタマイズや専用化されたハードウェアによってサポートされる、対象指定のサービス内容。 複数のリージョンにわたり、需要主導で提供されます (多くは、既に別のリージョンの大きなサブセットにデプロイされています)。

特定のリージョンにデプロイされているサービスと、リージョン内のサービスのプレビューまたは一般提供のロードマップを確認するには、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all)に関するページを参照してください。

サービス内容が特定のリージョンで利用できない場合は、Microsoft の営業担当者に連絡してリクエストすることができます。

| リージョンの種類 | リージョン依存なし | 基本 | メインストリーム | 専用イメージ | 可用性ゾーン | データの保存場所 |
| --- | --- | --- | --- | --- | --- | --- |
| 推奨 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需要主導型 | :heavy_check_mark: | :heavy_check_mark: |
| 代替 | :heavy_check_mark: | :heavy_check_mark: | 需要主導型 | 需要主導型 | 該当なし | :heavy_check_mark: |

### <a name="services-by-category"></a>カテゴリ別のサービス

前述のように、Azure のサービスは、基本サービス、メインストリーム サービス、および専用サービスという 3 つのカテゴリに分類されています。 サービス カテゴリは、一般提供の際に割り当てられます。 多くの場合、サービスのライフサイクルは専用サービスから始まり、その需要や使用率が増加すると、メインストリーム サービスまたは基本サービスに昇格される可能性があります。 次の表では、サービスを基本、メインストリーム、または専用のカテゴリでリストしています。 表については、次の点に注意してください。

- 一部のサービスはリージョンに依存しません。 リージョンに依存しないサービスの詳細と一覧については、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページを参照してください。
- 旧世代の仮想マシンは表示されていません。 詳しくは、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」に関するドキュメントをご覧ください。

> [!div class="mx-tableFixed"]
> | 基本 | メインストリーム | 専用イメージ |
> | --- | --- | --- |
> | Account Storage | API Management | FHIR 用の Azure API |
> | Application Gateway | App Configuration | Azure Blockchain サービス |
> | Azure Backup | App Service | Azure Blueprint |
> | Azure Cosmos DB | オートメーション | Azure Database for MariaDB |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure の専用 HSM |
> | Azure ExpressRoute | Azure Analysis Services | Azure Dev Spaces |
> | Azure SQL データベース | Azure Bastion | Azure Digital Twins |
> | Cloud Services | Azure Cache for Redis | Azure Lab Services |
> | クラウド サービス:Av2 シリーズ | Azure Cognitive Search | Azure NetApp Files |
> | クラウド サービス:Dv2 シリーズ | Azure Data Explorer | Azure Quantum |
> | クラウド サービス:Dv3 シリーズ | Azure Data Share | Azure Time Series Insights |
> | クラウド サービス:Ev3 シリーズ | Azure Database for MySQL | Azure VMware Solution by CloudSimple |
> | クラウド サービス:インスタンス レベル IP | Azure Database for PostgreSQL | クラウド サービス:A8 - A11 (コンピューティング集中型) |
> | クラウド サービス:予約済み IP | Azure Database Migration Service | クラウド サービス:G シリーズ |
> | Disk Storage | Azure Databricks | クラウド サービス:H シリーズ |
> | Event Hubs | Azure DDoS Protection | Cognitive Services:Anomaly Detector |
> | Key Vault | Azure DevTest Labs | Cognitive Services :Custom Vision |
> | Load Balancer | Azure Firewall Manager | Cognitive Services :Speaker Recognition |
> | Service Bus | Azure Firewall | Data Box Heavy |
> | Service Fabric | Azure Functions | Data Catalog |
> | Virtual Machine Scale Sets | Azure HPC Cache | Data Factory:Data Factory V1 |
> | Virtual Machines | Azure IoT Hub | Data Lake Analytics |
> | Virtual Machines: Av2 シリーズ | Azure Kubernetes Service (AKS) | Machine Learning Studio |
> | Virtual Machines: Bs シリーズ | Azure Machine Learning | Microsoft Genomics |
> | Virtual Machines: DSv2 シリーズ | Azure Private Link | Remote Rendering |
> | Virtual Machines: DSv3 シリーズ | Azure Red Hat OpenShift | Spatial Anchors |
> | Virtual Machines: Dv2 シリーズ | Azure Site Recovery | StorSimple |
> | Virtual Machines: Dv3 シリーズ | Azure Spring Cloud Service | Video Indexer |
> | Virtual Machines: ESv3 シリーズ | Azure Stack Hub | Virtual Machines: A8 - A11 (コンピューティング集中型) |
> | Virtual Machines: Ev3 シリーズ | Azure Stream Analytics | Virtual Machines: DASv4 シリーズ |
> | Virtual Machines: F シリーズ | Azure Synapse Analytics | Virtual Machines: DAv4 シリーズ |
> | Virtual Machines: FS シリーズ | Azure SignalR Service | Virtual Machines: DCsv2 シリーズ |
> | Virtual Machines: インスタンス レベル IP | Batch | Virtual Machines: EASv4 シリーズ |
> | Virtual Machines: 予約済み IP | クラウド サービス:M シリーズ | Virtual Machines: EAv4 シリーズ |
> | Virtual Network | Cognitive Services | Virtual Machines: G シリーズ |
> | VPN Gateway | Cognitive Services:Computer Vision | Virtual Machines: GS シリーズ |
> |  | Cognitive Services:Content Moderator | Virtual Machines: HBv1 シリーズ |
> |  | Cognitive Services:Face | Virtual Machines: HBv2 シリーズ |
> |  | Cognitive Services:Language Understanding | Virtual Machines: HCv1 シリーズ |
> |  | Cognitive Services:Speech Services | Virtual Machines: H シリーズ |
> |  | Cognitive Services:QnA Maker | Virtual Machines: LS シリーズ |
> |  | Container Instances | Virtual Machines: LSv2 シリーズ |
> |  | Container Registry | Virtual Machines: Mv2 シリーズ |
> |  | Data Factory | Virtual Machines: NC シリーズ |
> |  | Event Grid | Virtual Machines: NCv2 シリーズ |
> |  | HDInsight | Virtual Machines: NCv3 シリーズ |
> |  | Logic Apps | Virtual Machines: ND シリーズ |
> |  | Media Services | Virtual Machines: NDv2 シリーズ |
> |  | Network Watcher | Virtual Machines: NV シリーズ |
> |  | Notification Hubs | Virtual Machines: NVv3 シリーズ |
> |  | Power BI Embedded | Virtual Machines: NVv4 シリーズ |
> |  | Premium Blob Storage | Virtual Machines: SAP HANA on Azure Large Instances |
> |  | Premium File Storage | Visual Studio App Center |
> |  | ストレージ:Archive Storage |  |
> |  | Ultra Disk Storage |  |
> |  | Virtual Machines: Fsv2 シリーズ |  |
> |  | Virtual Machines: M シリーズ |  |
> |  | Virtual WAN |  |

###  <a name="services-resiliency"></a>サービスの回復性

すべての Azure 管理サービスは、リージョン レベルの障害から回復できるように設計されています。 障害にはさまざまなものがありますが、可用性ゾーンの障害は、リージョン内で 1 つ以上発生しても、リージョン全体の障害と比べて、障害の範囲は狭くなります。 Azure では、リージョン内または別の Azure リージョン内で発生した管理サービスのゾーン レベルの障害から復旧できます。 Azure では、リージョン内で 1 度に 1 つのゾーンで重要なメンテナンスが実行されます。これは、何らかの障害が発生した場合でも、リージョン内の可用性ゾーン全体にデプロイされたお客様のリソースに影響が及ばないようにするためです。

### <a name="pricing-for-vms-in-availability-zones"></a>Availability Zones の VM の料金

Availability Zones にデプロイされる仮想マシンに追加のコストは発生しません。 2 つ以上の VM が 1 つの Azure リージョン内の 2 つ以上の可用性ゾーンにデプロイされている場合、99.99% VM アップタイム SLA が実現されます。 可用性ゾーン VM 間データ転送の追加料金がかかります。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。

### <a name="get-started-with-availability-zones"></a>Availability Zones の使用を開始する

- [仮想マシンの作成](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell を使った管理ディスクの追加](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [ゾーン冗長仮想マシン スケール セットの作成](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [ゾーン冗長フロントエンドによる Standard Load Balancer を使用したゾーン間での VM の負荷分散](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [ゾーン フロントエンドによる Standard Load Balancer を使用した単一のゾーン内での VM の負荷分散](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [ゾーン冗長ストレージ](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [Event Hubs の geo ディザスター リカバリー](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [ゾーン冗長仮想ネットワーク ゲートウェイの作成](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB のゾーン冗長リージョンの追加](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure Cache for Redis Availability Zones の概要](https://aka.ms/redis/az/getstarted)
- [Azure Active Directory Domain Services インスタンスの作成](../active-directory-domain-services/tutorial-create-instance.md)
- [可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](../aks/availability-zones.md)

## <a name="next-steps"></a>次のステップ

- [Azure の Availability Zones をサポートしているリージョン](az-region.md)
- [クイック スタート テンプレート](https://aka.ms/azqs)
