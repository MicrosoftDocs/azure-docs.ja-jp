---
title: Azure Container Instances リソースの可用性
description: さまざまな Azure リージョンにある Azure Container Instances サービス用のコンピューティング リソースとメモリ リソースの可用性。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 14e7b9a3ea11e59aabeb901c4039e69208ea0cfd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325720"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure リージョンの Azure Container Instances のリソースの可用性

この記事では、Azure リージョンの Azure Container Instances のコンピューティング リソースとメモリ リソースの可用性について詳しく説明します。 

表示されている値は、[コンテナー グループ](container-instances-container-groups.md)のデプロイごとに使用できる最大リソースです。 値は公開時現在のものです。 

> [!NOTE]
> これらのリソース制限内で作成されたコンテナー グループは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。 このようなデプロイの失敗を減らすために、より低いリソースの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

デプロイのクォータとその他の制限については、「[Azure Container Instances のクォータとリージョンの可用性](container-instances-quotas.md)」を参照してください。

## <a name="availability---general"></a>可用性 - 一般提供

次のリージョンとリソースは、Linux および [サポートされている](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016 ベースのコンテナーを含むコンテナー グループで使用できます。

| Location | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| カナダ中部、インド中部、米国中部、東アジア、米国東部、米国東部 2、北ヨーロッパ、米国中南部、東南アジア、英国南部、米国西部 | Linux | 4 | 16 |
| 西ヨーロッパ、米国西部 2 | Linux | 4 | 14 |
| オーストラリア東部、東日本 | Linux | 2 | 8 |
| 米国中北部、インド南部 | Linux | 2 | 3.5 |
| 西ヨーロッパ | Windows | 4 | 16 |
| 米国東部、米国西部 | Windows | 4 | 14 |
| オーストラリア東部、カナダ中部、インド中部、米国中部、東アジア、米国東部 2、東日本、米国中北部、北ヨーロッパ、米国中南部、東南アジア、インド南部、英国南部、米国西部 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC、1809 デプロイ (プレビュー)

次のリージョンとリソースは、Windows Server 2019 ベースのコンテナー (プレビュー) を含むコンテナー グループで使用できます。

| Location | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| 東南アジア、北ヨーロッパ、西ヨーロッパ、米国中部、米国東部、米国西部、米国西部 2 | Windows | 4 | 16 |
| 米国東部 2 | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>可用性 - 仮想ネットワークのデプロイ (プレビュー)

次のリージョンとリソースは、[Azure 仮想ネットワーク](container-instances-vnet.md) (プレビュー) にデプロイされたコンテナー グループで使用できます。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU リソース (プレビュー)

次のリージョンとリソースは、[GPU リソース](container-instances-gpu.md) (プレビュー) でデプロイされたコンテナー グループで使用できます。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>次の手順

追加のリージョンやリソースの可用性の増加については、[aka.ms/aci/feedback](https://aka.ms/aci/feedback) でチームにお知らせください。

コンテナー インスタンスのデプロイに関するトラブルシューティングについては、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」を参照してください。
