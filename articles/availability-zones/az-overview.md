---
title: Azure Availability Zones とは | Microsoft Docs
description: Azure に高可用性で回復力のあるアプリケーションを作成できるように、Availability Zones ではリソースの実行に使用できる物理的に独立した場所が提供されています。
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 557757fc4d99fe57ad545e9d2eebcce61ddb3a8f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268723"
---
# <a name="what-are-availability-zones-in-azure"></a>Azure の Availability Zones の概要
Availability Zones は高可用性を備えたサービスで、アプリケーションとデータをデータセンターの障害から保護します。 Availability Zones は、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。 Availability Zones は 1 リージョン内で物理的に分離されているため、データセンターで障害が発生した場合でもアプリケーションとデータを保護できます。 ゾーン冗長サービスによって、単一障害点から保護されるように Availability Zones 全体でアプリケーションとデータがレプリケートされます。 Availability Zones では、Azure によって業界最高の 99.99% VM アップタイム SLA が実現されます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

Azure リージョン内の可用性ゾーンは、障害ドメインと更新ドメインを組み合わせたものです。 たとえば、Azure リージョンの 3 つのゾーンに 3 つ以上の VM を作成する場合、VM は実際には 3 つの障害ドメインと 3 つの更新ドメインに分散されます。 Azure プラットフォームは更新ドメインへのこの分散を認識し、異なるゾーン内の VM が同時に更新されないようにします。

高可用性をアプリケーションに構築するには、コンピューティング、ストレージ、ネットワーク、およびデータ リソースを 1 つのゾーン内に併置し、他のゾーンでレプリケートします。 Availability Zones をサポートしている Azure サービスは、次の 2 つのカテゴリに分類されます。

- **ゾーン サービス** – リソースを特定のゾーン (たとえば、仮想マシン、マネージド ディスク、IP アドレス) にピン留めします。または
- **ゾーン冗長サービス** – プラットフォームが複数のゾーンにわたって自動的にレプリケートされます (ゾーン冗長ストレージ、SQL Database など)。

Azure での包括的なビジネス継続性を実現するには、Availability Zones と Azure リージョンの組み合わせを使用してアプリケーション アーキテクチャを構築します。 1 つの Azure リージョン内で Availability Zones を使用してアプリケーションとデータを同期的にレプリケートして高可用性を実現し、複数の Azure リージョン全体で非同期的にレプリケートしてディザスター リカバリー保護を実現できます。
 
![1 リージョン内でゾーンが 1 つダウンした場合の概念図](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>可用性ゾーンをサポートしているリージョン

- 米国中部
- 米国東部
- 米国東部 2
- フランス中部
- 北ヨーロッパ
- 東南アジア 
- 英国南部 &#42;
- 西ヨーロッパ
- 米国西部 2



## <a name="services-that-support-availability-zones"></a>可用性ゾーンをサポートしているサービス
可用性ゾーンをサポートしている Azure サービスは、次のとおりです:

- Linux Virtual Machines
- Windows Virtual Machines
- Virtual Machine Scale Sets
- Managed Disks
- Standard Load Balancer &#42;
- 標準パブリック IP アドレス &#42;
- ゾーン冗長ストレージ
- SQL Database
- Event Hubs
- Service Bus (Premium レベルのみ)
- VPN Gateway
- ExpressRoute
- Application Gateway (プレビュー)

&#42; 2019 年 3 月 25 日より前に英国南部で作成されたリソースは、間もなくゾーン冗長になるように変換されます。 2019 年 3 月 25 日以降に作成されたリソースは、直ちにゾーン冗長になります。

## <a name="services-resiliency"></a>サービスの回復性
すべての Azure 管理サービスは、リージョン レベルの障害から回復できるように設計されています。 障害にはさまざまなものがありますが、可用性ゾーンの障害は、リージョン内で 1 つ以上発生しても、リージョン全体の障害と比べて、障害の範囲は狭くなります。 Azure では、リージョン内または別の Azure リージョン内で発生した管理サービスのゾーン レベルの障害から復旧できます。 Azure では、リージョン内で 1 度に 1 つのゾーンで重要なメンテナンスが実行されます。これは、何らかの障害が発生した場合でも、リージョン内の可用性ゾーン全体にデプロイされたお客様のリソースに影響が及ばないようにするためです。

## <a name="pricing"></a>価格
Availability Zones にデプロイされる仮想マシンに追加のコストは発生しません。 2 つ以上の VM が 1 つの Azure リージョン内の 2 つ以上の可用性ゾーンにデプロイされている場合、99.99% VM アップタイム SLA が実現されます。 可用性ゾーン VM 間データ転送の追加料金がかかります。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。


## <a name="get-started-with-availability-zones"></a>Availability Zones の使用を開始する
- [仮想マシンの作成](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell を使ったマネージド ディスクの追加](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [ゾーン冗長仮想マシン スケール セットの作成](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [ゾーン冗長フロントエンドによる Standard Load Balancer を使用したゾーン間での VM の負荷分散](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [ゾーン フロントエンドによる Standard Load Balancer を使用した単一のゾーン内での VM の負荷分散](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [ゾーン冗長ストレージ](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Event Hubs の geo ディザスター リカバリー](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [ゾーン冗長仮想ネットワーク ゲートウェイの作成](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>次の手順
- [クイック スタート テンプレート](https://aka.ms/azqs)
