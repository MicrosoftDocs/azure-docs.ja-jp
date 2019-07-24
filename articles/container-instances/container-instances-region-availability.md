---
title: Azure Container Instances リソースの可用性
description: さまざまな Azure リージョンにある Azure Container Instances サービス用のコンピューティング リソースとメモリ リソースの可用性。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554870"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure リージョンの Azure Container Instances のリソースの可用性

この記事では、Azure リージョンの Azure Container Instances のコンピューティング リソースとメモリ リソースの可用性について詳しく説明します。 

表示されている値は、[コンテナー グループ](container-instances-container-groups.md)のデプロイごとに使用できる最大リソースです。 値は公開時現在のものです。 最新情報については、[一覧表示機能](/rest/api/container-instances/listcapabilities/listcapabilities) API を使用します。 

> [!NOTE]
> これらのリソース制限内で作成されたコンテナー グループは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。 このようなデプロイの失敗を減らすために、より低いリソースの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

デプロイのクォータとその他の制限については、「[Azure Container Instances のクォータとリージョンの可用性](container-instances-quotas.md)」を参照してください。

## <a name="availability---general"></a>可用性 - 一般提供

| Location | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| カナダ中部、米国中部、米国東部 2、米国中南部 | Linux | 4 | 16 |
| 米国東部、北ヨーロッパ、西ヨーロッパ、米国西部、米国西部 2 | Linux | 4 | 14 |
| 東日本 | Linux | 2 | 8 |
| オーストラリア東部、東南アジア | Linux | 2 | 7 |
| インド中部、東アジア、米国中北部、インド南部 | Linux | 2 | 3.5 |
| 米国東部、西ヨーロッパ、米国西部 | Windows | 4 | 14 |
| オーストラリア東部、カナダ中部、インド中部、米国中部、東アジア、米国東部 2、東日本、米国中北部、北ヨーロッパ、米国中南部、インド南部、東南アジア、米国西部 2 | Windows | 2 | 3.5 |

## <a name="availability---virtual-network-deployment-preview"></a>可用性 - 仮想ネットワークのデプロイ (プレビュー)

次のリージョンとリソースは、[Azure 仮想ネットワーク](container-instances-vnet.md) (プレビュー) にデプロイされたコンテナー グループに使用できます。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU リソース (プレビュー)

次のリージョンとリソースは、[GPU リソース](container-instances-gpu.md) (プレビュー) を使用してデプロイされたコンテナー グループに使用できます。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>次の手順

追加のリージョンやリソースの可用性の増加については、[aka.ms/aci/feedback](https://aka.ms/aci/feedback) でチームにお知らせください。

コンテナー インスタンスのデプロイに関するトラブルシューティングについては、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」を参照してください。
