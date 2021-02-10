---
title: HC シリーズ - Azure Virtual Machines
description: HC シリーズ VM の仕様。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 38020376f9d86fb074bd6d9c09e3d16e899c4a6f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096900"
---
# <a name="hc-series"></a>HC シリーズ

HC シリーズ VM は、陰解法有限要素解析、分子力学、計算化学などの、高密度計算に基づいたアプリケーション向けに最適化されています。 HC VM は 44 個の Intel Xeon Platinum 8168 プロセッサ コア、CPU コアあたり 8 GB の RAM を搭載し、ハイパースレッディングはありません。 Intel Xeon Platinum プラットフォームは、Intel Math Kernel Library や、AVX-512 などの高度なベクター処理機能など、Intel の豊富なソフトウェア ツールのエコシステムをサポートしています。

HC シリーズ VM は、100 Gb/秒の Mellanox EDR InfiniBand を特徴としています。 これらの VM は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 これらの VM は、アダプティブ ルーティング、および標準 RC トランスポートと UD トランスポートに加え、動的接続トランスポート (DCT) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、それらを使用することをお勧めします。

[ACU](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | プロセッサ | メモリ (GiB) | メモリ帯域幅 GB/秒 | ベース CPU 周波数 (GHz) | 全コア周波数 (GHz、ピーク) | シングルコア周波数 (GHz、ピーク) | RDMA パフォーマンス (GB/秒) | MPI のサポート | 一時ストレージ (GiB) | 最大データ ディスク数 | 最大イーサネット vNIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

- [VM の構成](./workloads/hpc/configure.md)、[InfiniBand の有効化](./workloads/hpc/enable-infiniband.md)、[MPI の設定](./workloads/hpc/setup-mpi.md)、および [HPC ワークロード](./workloads/hpc/overview.md)での Azure 用の HPC アプリケーションの最適化について学習してください。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」を参照してください。
- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
