---
title: Azure のリージョンと Availability Zones
description: 技術要件および規制要件を満たすために、Azure のリージョンと Availability Zones について説明します。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: dad8661de55fc90c9f3d3782c402deb519d16536
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596010"
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
| メインストリーム サービス | リージョンの一般提供 (または代替リージョンでの需要に応じた提供) から 90 日以内にすべての推奨されるリージョンで利用可能な Azure サービス。 |
| 専用サービス | カスタマイズされたハードウェア、または専用ハードウェアによって支えられているリージョン間で需要主導で提供されている Azure サービス。 |
| リージョン サービス | リージョンごとにデプロイされた Azure サービス。顧客は、サービスのデプロイ先となるリージョンを指定できます。 詳細なリストについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all)に関するページを参照してください。 |
| リージョンに依存しないサービス | 特定の Azure リージョンへの依存関係がない Azure サービス。 リージョンに依存しないサービスは 2 つ以上のリージョンにデプロイされており、あるリージョンで障害が発生した場合は、別のリージョンのサービスのインスタンスが引き続き顧客にサービスを提供します。 詳細なリストについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all)に関するページを参照してください。 |

## <a name="regions"></a>リージョン

リージョンは、待ち時間で定義された境界内でデプロイされ、待ち時間の短い専用リージョン ネットワークを介して接続された一連のデータセンターです。 Azure では、複数のリージョンにわたってデプロイしてリージョンをまたがる回復性を提供するなど、必要な場所にアプリケーションをデプロイできる柔軟性があります。 詳細については、「[回復性の柱の概要](/azure/architecture/framework/resiliency/overview)」を参照してください。

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

- **基本** – リージョンが一般提供されている場合、または新しい基本サービスの一般提供から 90 日以内に推奨されるリージョンと代替リージョンで利用できます。
- **メインストリーム** – リージョンの一般提供から 90 日以内に、すべての推奨されるリージョンで利用できます。代替リージョンでは、需要主導です (多くは、既に別のリージョンの大きなサブセットにデプロイされています)。
- **専用** - 特に業界に重点を置いているハードウェア、またはカスタマイズや専用化されたハードウェアによってサポートされる、対象指定のサービス内容。 複数のリージョンにわたり、需要主導で提供されます (多くは、既に別のリージョンの大きなサブセットにデプロイされています)。

特定のリージョンにデプロイされているサービスと、リージョン内のサービスのプレビューまたは一般提供のロードマップを確認するには、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=all)に関するページを参照してください。

サービス内容が特定のリージョンで利用できない場合は、Microsoft の営業担当者に連絡してリクエストすることができます。

| リージョンの種類 | リージョン依存なし | 基本 | メインストリーム | 専用イメージ | 可用性ゾーン | データの保存場所 |
| --- | --- | --- | --- | --- | --- | --- |
| 推奨 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需要主導型 | :heavy_check_mark: | :heavy_check_mark: |
| 代替 | :heavy_check_mark: | :heavy_check_mark: | 需要主導型 | 需要主導型 | 該当なし | :heavy_check_mark: |

### <a name="services-by-category"></a>カテゴリ別のサービス

前述のように、Azure のサービスは、基本サービス、メインストリーム サービス、および専用サービスという 3 つのカテゴリに分類されています。 サービス カテゴリは、一般提供の際に割り当てられます。 多くの場合、サービスのライフサイクルは専用サービスから始まり、その需要や使用率が増加すると、メインストリーム サービスまたは基本サービスに昇格される可能性があります。 次の表では、サービスを基本、メインストリームのカテゴリでリストしています。 表については、次の点に注意してください。

- 一部のサービスはリージョンに依存しません。 リージョンに依存しないサービスの詳細と一覧については、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページを参照してください。
- 旧世代のサービスや仮想マシンは一覧表示されていません。 詳しくは、「[旧世代の仮想マシンのサイズ](../virtual-machines/sizes-previous-gen.md)」をご覧ください
- 一般公開 (GA) になるまでは、サービスにカテゴリは割り当てられません。 プレビュー サービスの詳細と一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」を参照してください。 

> [!div class="mx-tableFixed"]
> | 基本                           | メインストリーム                                        | 
> |----------------------------------------|---------------------------------------------------|
> | ストレージ アカウント                       | API Management                                    | 
> | Application Gateway                    | App Configuration                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | オートメーション                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure パブリック IP                        | Azure Cache for Redis                             | 
> | Azure SQL データベース                     | Azure Cognitive Search                            | 
> | Azure SQL Managed Instance             | Azure Cognitive Services                          | 
> | Disk Storage                           | Azure Cognitive Services: Computer Vision         | 
> | Event Hubs                             | Azure Cognitive Services: Content Moderator       | 
> | Key Vault                              | Azure Cognitive Services: Face                    | 
> | Load Balancer                          | Azure Cognitive Services: Immersive Reader        | 
> | Service Bus                            | Azure Cognitive Services: Language Understanding  | 
> | Service Fabric                         | Azure Cognitive Services: Speech Services         | 
> | ストレージ:ホット/クール BLOB ストレージ レベル   | Azure Cognitive Services: Text Analytics          | 
> | ストレージ:Managed Disks                 | Azure Cognitive Services: Translator              | 
> | Virtual Machine Scale Sets             | Azure Data Explorer                               | 
> | Virtual Machines                       | Azure Data Share                                  | 
> | 仮想マシン: Azure Dedicated Host | Azure Database for MySQL                          | 
> | Virtual Machines: Av2 シリーズ           | Azure Database for PostgreSQL                     | 
> | Virtual Machines: Bs シリーズ            | Azure DDoS Protection                             | 
> | Virtual Machines: DSv2 シリーズ          | Azure Firewall                                    | 
> | Virtual Machines: DSv3 シリーズ          | Azure Firewall Manager                            | 
> | Virtual Machines: Dv2 シリーズ           | Azure Functions                                   | 
> | Virtual Machines: Dv3 シリーズ           | Azure IoT Hub                                     |     
> | Virtual Machines: ESv3 シリーズ          | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Ev3 シリーズ           | Azure Machine Learning                            | 
> | Virtual Network                        | Azure Monitor:Application Insights               | 
> | VPN Gateway                            | Azure Monitor:Log Analytics                      | 
> |                                        | Azure Private Link                                | 
> |                                        | Azure Red Hat OpenShift                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Azure Stream Analytics                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Batch                                             | 
> |                                        | クラウド サービス:M シリーズ                          | 
> |                                        | Container Instances                               | 
> |                                        | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Notification Hubs                                 | 
> |                                        | Premium Blob Storage                              | 
> |                                        | Premium File Storage                             | 
> |                                        | Virtual Machines: Ddsv4 シリーズ                    | 
> |                                        | Virtual Machines: Ddv4 シリーズ                     | 
> |                                        | Virtual Machines: Dsv4 シリーズ                     | 
> |                                        | Virtual Machines: Dv4 シリーズ                      | 
> |                                        | Virtual Machines: Edsv4 シリーズ                    | 
> |                                        | Virtual Machines: Edv4 シリーズ                     | 
> |                                        | Virtual Machines: Esv4 シリーズ                     | 
> |                                        | Virtual Machines: Ev4 シリーズ                      | 
> |                                        | Virtual Machines: Fsv2 シリーズ                     | 
> |                                        | Virtual Machines: M シリーズ                        | 
> |                                        | Virtual WAN                                       | 



### <a name="specialized-services"></a>専用サービス
前述のように、Azure のサービスは、基本サービス、メインストリーム サービス、および専用サービスという 3 つのカテゴリに分類されています。 サービス カテゴリは、一般提供の際に割り当てられます。 多くの場合、サービスのライフサイクルは専用サービスから始まり、その需要や使用率が増加すると、メインストリーム サービスまたは基本サービスに昇格される可能性があります。 次の表は、専用サービスの一覧です。 

> [!div class="mx-tableFixed"]
> | 専用イメージ                                          |
> |------------------------------------------------------|
> | FHIR 用の Azure API                                   |
> | Azure Analysis Services                              |
> | Azure Cognitive Services: Anomaly Detector           |
> | Azure Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: Form Recognizer            |
> | Azure Cognitive Services: Personalizer               |
> | Azure Cognitive Services: QnA Maker                  |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Azure の専用 HSM                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure SignalR Service                                |
> | Azure Spring Cloud Service                           |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure Machine Learning Studio (クラシック)              |
> | Spatial Anchors                                      |
> | ストレージ:Archive Storage                             |
> | Ultra Disk Storage                                   |
> | Video Indexer                                        |
> | Virtual Machines: DASv4 シリーズ                       |
> | Virtual Machines: DAv4 シリーズ                        |
> | Virtual Machines: DCsv2 シリーズ                       |
> | Virtual Machines: EASv4 シリーズ                       |
> | Virtual Machines: EAv4 シリーズ                        |
> | Virtual Machines: HBv1 シリーズ                        |
> | Virtual Machines: HBv2 シリーズ                        |
> | Virtual Machines: HCv1 シリーズ                        |
> | Virtual Machines: H シリーズ                           |
> | Virtual Machines: LSv2 シリーズ                        |
> | Virtual Machines: Mv2 シリーズ                         |
> | Virtual Machines: NCv3 シリーズ                        |
> | Virtual Machines: NDv2 シリーズ                        |
> | Virtual Machines: NVv3 シリーズ                        |
> | Virtual Machines: NVv4 シリーズ                        | 
> | Virtual Machines: SAP HANA on Azure Large Instances  |




## <a name="next-steps"></a>次のステップ

- [Azure の Availability Zones をサポートしているリージョン](az-region.md)
- [クイック スタート テンプレート](https://aka.ms/azqs)
