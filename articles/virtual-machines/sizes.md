---
title: VM サイズ
description: Azure の仮想マシンで使用できるさまざまなサイズを一覧表示します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: d9377ba22f1461762e53b1004dfe5f06c2d7b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89420219"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Azure の仮想マシンのサイズ

この記事では、アプリとワークロードの実行に使用できる Azure 仮想マシンの利用可能なサイズとオプションについて説明します。 また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。 

| Type | サイズ | 説明 |
|------|-------|-------------|
| [汎用](sizes-general.md)   | B、Dsv3、Dv3、Dasv4、Dav4、DSv2、Dv2、Av2、DC、DCv2、Dv4、Dsv4、Ddv4、Ddsv4  | バランスのとれた CPU 対メモリ比。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 |
| [コンピューティングの最適化](sizes-compute.md) | F、Fs、Fsv2 | 高い CPU 対メモリ比。 トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。 |
| [メモリの最適化](sizes-memory.md) | Esv3、Ev3、Easv4、Eav4、Ev4、Esv4、Edv4、Edsv4、Mv2、M、DSv2、Dv2 | 高いメモリ対 CPU 比。 リレーショナル データベース サーバー、中～大規模のキャッシュ、およびメモリ内分析に適しています。                 |
| [ストレージの最適化](sizes-storage.md) | Lsv2 | ビッグ データ、SQL、NoSQL データベース、データ ウェアハウス、および大規模なトランザクション データベースに最適な、高いディスク スループットと IO。  |
| [GPU](sizes-gpu.md) | NC、NCv2、NCv3、NCasT4_v3 (プレビュー)、ND、NDv2 (プレビュー)、NV、NVv3、NVv4 | 負荷の高いグラフィックスのレンダリングやビデオ編集、ディープ ラーニングを使用したモデル トレーニングと推論 (ND) に特化した仮想マシン。 1 つまたは複数の GPU で利用できます。 |
| [ハイ パフォーマンス コンピューティング](sizes-hpc.md) | HB、HBv2、HC、H | 高スループットのネットワーク インターフェイス (RDMA) のオプションを備えた、最も高速かつ強力な CPU 仮想マシン。 |

- さまざまなサイズの価格については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows) の価格に関するページをご覧ください。
- 各 Azure リージョンで利用可能な VM サイズについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。
- Azure VM の一般的な制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。
- Azure で VM の名前がどのように付けられるかの詳細については、「[Azure 仮想マシンのサイズの名前付け規則](./vm-naming-conventions.md)」を参照してください。

## <a name="rest-api"></a>REST API

VM サイズを照会するための REST API の使用については、以下を参照してください。

- [サイズ変更に使用可能な仮想マシンを一覧表示](/rest/api/compute/virtualmachines/listavailablesizes)
- [サブスクリプションに使用可能な仮想マシンのサイズを一覧表示](/rest/api/compute/resourceskus/list)
- [可用性セットに使用可能な仮想マシンのサイズを一覧表示](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

## <a name="benchmark-scores"></a>ベンチマーク スコア

[CoreMark ベンチマーク スコア](./linux/compute-benchmark-scores.md)を使用して、Linux VM の処理性能について学習します。

[SPECInt ベンチマーク スコア](./windows/compute-benchmark-scores.md)を使用して、Windows VM の処理性能について学習します。

## <a name="manage-costs"></a>コストを管理する

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>次のステップ

利用可能な VM のサイズの種類について詳しく説明します。

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- A Standard、Dv1 (D1-4 および D11-14 v1)、および A8-A11 シリーズの[旧世代](sizes-previous-gen.md)に関するページを確認してください。
