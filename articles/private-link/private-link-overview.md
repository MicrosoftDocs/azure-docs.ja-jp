---
title: Azure Private Link とは
description: Azure Private Link の機能、アーキテクチャ、実装の概要。 Azure プライベート エンドポイントおよび Azure Private Link サービスのしくみとその使用方法について説明します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 6a85bfe7b3390b32fc220000b0c710b5a4e35067
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496492"
---
# <a name="what-is-azure-private-link"></a>Azure Private Link とは 
Azure Private Link を使用すると、お使いの仮想ネットワーク内の[プライベート エンドポイント](private-endpoint-overview.md)経由で Azure PaaS サービス (Azure Storage、SQL Database など) と Azure でホストされている顧客所有の、またはパートナー サービスにアクセスできます。

仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。 パブリック インターネットにサービスを公開する必要はありません。 お使いの仮想ネットワークに独自の[プライベート リンク サービス](private-link-service-overview.md)を作成して顧客に提供することができます。 Azure Private Link を使用した設定と消費は、Azure PaaS サービス、顧客所有サービス、共有パートナー サービス間で一貫しています。

> [!IMPORTANT]
> Azure Private Link は、現在、一般提供されています。 プライベート エンドポイントと Private Link サービス (Standard ロード バランサーの背後にあるサービス) の両方が一般提供されています。 さまざまな Azure PaaS が異なるスケジュールで Azure Private Link にオンボードされます。 Private Link 上の Azure PaaS の正確な状態については、[プライベート リンクの可用性](availability.md)に関する記事を参照してください。 既知の制約については、[プライベート エンドポイント](private-endpoint-overview.md#limitations)と [Private Link サービス](private-link-service-overview.md#limitations)の説明を参照してください。 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure portal の Azure Private Link センター" border="false":::

## <a name="key-benefits"></a>主な利点
Azure Private Link には次のような利点があります。  
- **Azure プラットフォーム上のサービスへのプライベート アクセス**: 接続元または接続先にパブリック IP アドレスを使用せずに、Azure 内のサービスに仮想ネットワークを接続できます。 サービス プロバイダーは独自のローカル仮想ネットワークでサービスを提供することができ、また、コンシューマーは自身のローカル仮想ネットワークでサービスにアクセスすることができます。 Private Link プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 
 
- **オンプレミスおよびピアリングされたネットワーク**: Azure で実行されているサービスに対し、ExpressRoute プライベート ピアリングや VPN トンネル経由でオンプレミスから、さらには、ピアリングされた仮想ネットワークから、プライベート エンドポイントを使用してアクセスすることができます。 ExpressRoute Microsoft ピアリングを構成したり、インターネットを経由してサービスに接続したりする必要はありません。 Private Link を使用すれば、Azure にワークロードを安全に移行することができます。
 
- **データの漏えいに対する保護**: プライベート エンドポイントは、サービス全体にではなく、PaaS リソースの特定のインスタンスにマップされます。 コンシューマーが接続できるのは、その特定のリソースだけです。 サービス内の他のリソースへのアクセスはブロックされます。 このメカニズムにより、データの漏えいリスクを防ぐことができます。 
 
- **グローバルな展開**: 他のリージョンで実行中のサービスにプライベートに接続します。 リージョン A にあるコンシューマーの仮想ネットワークから、リージョン B の、Private Link の背後にあるサービスに接続することができます。  
 
- **独自のサービスへの拡張**: Azure 上のコンシューマーに対し、同じエクスペリエンスと機能でプライベートにサービスを提供できます。 Standard Azure Load Balancer の内側にサービスを配置することで、サービスを Private Link 対応にすることができます。 そうすれば、コンシューマーは自身の仮想ネットワーク内のプライベート エンドポイントを使用して直接そのサービスに接続できます。 接続要求は、承認呼び出しフローを使用して管理できます。 Azure Private Link は、異なる Azure Active Directory テナントに属するコンシューマーとサービスに対しても機能します。 

## <a name="availability"></a>可用性 

Private Link をサポートする Azure サービスの詳細については、「[Azure Private Link の可用性](availability.md)」を参照してください。

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
