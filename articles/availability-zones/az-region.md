---
title: Azure での Availability Zones をサポートしているリージョン
description: Azure に高可用性で回復力のあるアプリケーションを作成できるように、Availability Zones ではリソースの実行に使用できる物理的に独立した場所が提供されています。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853162"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Azure での Availability Zones をサポートしているリージョン

Availability Zones は高可用性を備えたサービスで、アプリケーションとデータをデータセンターの障害から保護します。 Availability Zones の詳細については、「[Azure のリージョンと Availability Zones](az-overview.md)」を参照してください。

このセクションでは、Availability Zones がサポートされている Azure のサービスとリージョンの一覧を表示します。

各リージョンで利用可能なサービスと、今後の可用性のためのロードマップについては、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関するページを参照してください。

## <a name="americas"></a>アメリカ

| サービス | 米国中部 | 米国東部 | 米国東部 2 | 米国西部 2 |
| --- | :---: | :---: | :---: | :---: |
| **Compute** |  |  |  |  |
| Linux Virtual Machines             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows Virtual Machines           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service Environment ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ゾーン冗長ストレージ             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ネットワーク** |  |  |  |  |
| 標準 IP アドレス                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute ゲートウェイ               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (V2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **データベース** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:(プレビュー) |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analytics** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **統合** |  |  |  |  |
| Service Bus (Premium レベルのみ)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ID** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>ヨーロッパ

| サービス | フランス中部 | 北ヨーロッパ | 英国南部 | 西ヨーロッパ |
| --- | :---: | :---: | :---: | :---: |
| **Compute** |  |  |  |  |
| Linux Virtual Machines             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows Virtual Machines           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service Environment ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ゾーン冗長ストレージ             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ネットワーク** |  |  |  |  |
| 標準 IP アドレス                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute ゲートウェイ               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (V2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **データベース** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark:(プレビュー) | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analytics** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **統合**  |  |  |  |  |
| Service Bus (Premium レベルのみ)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ID** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>アジア太平洋

| サービス | 東日本 | 東南アジア | オーストラリア東部 |
| --- | :---: | :---: | :---: |
| **Compute** |  |  |  |
| Linux Virtual Machines             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows Virtual Machines           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Virtual Machine Scale Sets         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service Environment ILB | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Storage** |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ゾーン冗長ストレージ             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **ネットワーク** |  |  |  |
| 標準 IP アドレス                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute ゲートウェイ               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (V2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **データベース** |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: |  |
| SQL Database                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analytics** |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **統合** |  |  |  |
| Service Bus (Premium レベルのみ)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **ID** |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>その他

Azure は、次のリージョンでも Availability Zones サポートを提供しています。

- US Gov バージニア州
- 南アフリカ北部
- 米国中南部
- カナダ中部

これらの 4 つのリージョンでの Availability Zones サポートの詳細については、Microsoft の営業担当者かカスタマー担当者にお問い合わせください。または、テクニカル サポート リクエストを開きます。

## <a name="next-steps"></a>次のステップ

- [Azure のリージョンと Availability Zones](az-overview.md)
