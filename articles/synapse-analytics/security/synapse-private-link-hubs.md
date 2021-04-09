---
title: プライベート リンクを使用して Synapse Studio に接続する
description: この記事では、プライベート リンクを使用して Azure Synapse Studio に接続する方法について説明します
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d39beca60264023c8eb7c1bc78cd1ac15c3b45dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586626"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Azure Private Link ハブを使用して Azure Synapse Studio に接続する 

この記事では、Azure Synapse Analytics プライベート リンク ハブのプライベート リンクを使用して Synapse Studio に安全に接続する方法について説明します。 

## <a name="azure-private-link-hubs"></a>Azure Private Link ハブ 
プライベート リンクを使用して、Azure 仮想ネットワークから Azure Synapse Studio に安全に接続できます。 Azure Synapse Analytics プライベート リンク ハブは、セキュリティで保護されたネットワークと Synapse Studio Web エクスペリエンスの間のコネクタとして機能する Azure リソースです。 

プライベート リンクを使用して Synapse Studio に接続するには、2 つの手順があります。 まず、プライベート リンク ハブ リソースを作成する必要があります。 次に、Azure 仮想ネットワークからこのプライベート リンク ハブにプライベート エンドポイントを作成する必要があります。 その後、プライベート エンドポイントを使用して、Synapse Studio と安全に通信できます。 プライベート エンドポイントは、オンプレミス ソリューションまたは Azure プライベート DNS のどちらかの DNS ソリューションと統合する必要があります。 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure プライベート リンク ハブと Azure Synapse Studio
単一の Azure Synapse プライベート リンク ハブ リソースを使用して、Azure Synapse Studio を使用するすべての Azure Synapse Analytics ワークスペースにプライベート接続できます。 ワークスペースは、Azure Synapse プライベート リンク ハブと同じリージョンに存在する必要はありません。 Azure Synapse プライベート リンク ハブ リソースは、さまざまなサブスクリプションまたは Azure AD テナントの Synapse ワークスペースへの接続にも使用できます。

プライベート リンク ハブを作成するには、Azure portal で "*Synapse プライベート リンク ハブ*" を検索し、[サービス] から **[Azure Synapse Analytics (プライベート リンク ハブ)]** を選択します。 詳細については、[制限されたネットワークからのワークスペース リソースへの接続](./how-to-connect-to-workspace-from-restricted-network.md)の方法についてのガイドの手順に従ってください。

>[!NOTE]
>プライベート リンク ハブは、静的コンテンツ Synapse Studio をプライベート リンク経由で安全に読み込むことを目的としています。 プロビジョニング済み/専用 SQL プールや Spark プールなど、ワークスペース内で接続するリソースへの **個別のプライベート エンドポイント** を作成する必要があります。 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure プライベート リンク ハブと Azure Virtual Network
Synapse Studio へのエンドツーエンド接続をセキュリティで保護するには、Azure 仮想ネットワークを Synapse プライベート リンク ハブ リソースに接続する必要があります。 そのためには、ユーザーの仮想ネットワークから、作成したプライベート リンク ハブにプライベート エンドポイントを作成する必要があります。 プライベート リンク ハブについては Azure portal を使用して、プライベート エンドポイント セクションに進むことができます。 [+ プライベート エンドポイント] を選択して、プライベート リンク ハブに接続する新しいプライベート エンドポイントを作成します。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="プライベート エンドポイント接続ページを示すスクリーンショット。":::

[リソース] タブで、"Microsoft.Synapse/privateLinkHubs" というリソースの種類を選択してください。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="[プライベート エンドポイントの作成] ページのスクリーンショット。[リソースの種類] が強調されています。":::

[構成] タブで、仮想ネットワークをプライベート DNS ゾーンと統合するときに、プライベート DNS ゾーンとして "privatelink.azuresynapse.net" を選択します。

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="プライベート リンク ハブへのプライベート エンドポイントを作成する":::

## <a name="next-steps"></a>次の手順

[制限されたネットワークからのワークスペース リソースへの接続](./how-to-connect-to-workspace-from-restricted-network.md)

[マネージド ワークスペースの Virtual Network](./synapse-workspace-managed-vnet.md) の詳細を学習する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を学習する

[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)

[プライベート エンドポイントを使用して Synapse ワークスペースに接続する](./how-to-connect-to-workspace-with-private-links.md)

