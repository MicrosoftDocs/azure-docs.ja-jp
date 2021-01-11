---
title: リージョンごとのリソースの可用性
description: さまざまな Azure リージョンにある Azure Container Instances サービス用のコンピューティング リソースとメモリ リソースの可用性。
ms.topic: article
ms.date: 04/27/2020
ms.author: danlep
ms.openlocfilehash: 591d7dd07ea3717303589cdc070623068c4d3864
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500608"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure リージョンの Azure Container Instances のリソースの可用性

この記事では、Azure リージョン内とターゲット オペレーティング システムによる Azure Container Instances のコンピューティング、メモリ、およびストレージのリソースの可用性について詳しく説明します。 

表示されている値は、[コンテナー グループ](container-instances-container-groups.md)のデプロイごとに使用できる最大リソースです。 値は公開時現在のものです。 

> [!NOTE]
> これらのリソース制限内で作成されたコンテナー グループは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。 このようなデプロイの失敗を減らすために、より低いリソースの設定でインスタンスをデプロイしてみるか、デプロイを後で試すか、使用可能なリソースのある別のリージョンでデプロイしてみてください。

デプロイのクォータとその他の制限については、「[Azure Container Instances のクォータとリージョンの可用性](container-instances-quotas.md)」を参照してください。

## <a name="availability---general"></a>可用性 - 一般提供

次のリージョンと最大リソースは、Linux および[サポートされている](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016 ベースのコンテナーを含むコンテナー グループで使用できます。

| リージョン | OS | Max CPU (最大 CPU) | 最大メモリ (GB) | ストレージ (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| ブラジル南部、カナダ中部、インド中部、米国中部、東アジア、米国東部、米国東部 2、北ヨーロッパ、米国中南部、東南アジア、インド南部、英国南部、西ヨーロッパ、米国西部、米国西部 2 | Linux | 4 | 16 | 50 |
| オーストラリア東部、東日本 | Linux | 2 | 8 | 50 |
| 米国中北部 | Linux | 2 | 3.5 | 50 |
| ブラジル南部、東日本、西ヨーロッパ | Windows | 4 | 16 | 20 |
| 米国東部、米国西部 | Windows | 4 | 14 | 20 |
| オーストラリア東部、カナダ中部、インド中部、米国中部、東アジア、米国東部 2、米国中北部、北ヨーロッパ、米国中南部、東南アジア、インド南部、英国南部、米国西部 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC、1809 デプロイ (プレビュー)

次のリージョンと最大リソースは、Windows Server 2019 ベースのコンテナー (プレビュー) を含むコンテナー グループで使用できます。

| リージョン | OS | Max CPU (最大 CPU) | 最大メモリ (GB) | ストレージ (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| オーストラリア東部、ブラジル南部、カナダ中部、インド中部、米国中部、東アジア、米国東部、東日本、米国中北部、北ヨーロッパ、米国中南部、東南アジア、インド南部、英国南部、西ヨーロッパ | Windows | 4 | 16 | 20 |
| 米国東部 2、米国西部 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>可用性 - 仮想ネットワークのデプロイ

次のリージョンと最大リソースは、[Azure 仮想ネットワーク](container-instances-vnet.md)にデプロイされたコンテナー グループで使用できます。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU リソース (プレビュー)

次のリージョンと最大リソースは、[GPU リソース](container-instances-gpu.md) (プレビュー) でデプロイされたコンテナー グループで使用できます。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>次のステップ

追加のリージョンやリソースの可用性の増加については、[aka.ms/aci/feedback](https://aka.ms/aci/feedback) でチームにお知らせください。

コンテナー インスタンスのデプロイに関するトラブルシューティングについては、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」を参照してください。


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
