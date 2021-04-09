---
title: HBv3 シリーズ - Azure Virtual Machines
description: HBv3 シリーズ VM の仕様。
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a03eac3969e8918c8da20b90d0dcf8b60b39b8ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800841"
---
# <a name="hbv3-series"></a>HBv3 シリーズ

HBv3 シリーズ VM は、流体力学、明示的および暗黙的な有限要素分析、気象モデリング、地震処理、貯水池シミュレーション、RTL シミュレーションなど、HPC アプリケーションのために最適化されています。 HBv3 VM は、AMD EPYC™ 7003 シリーズ (Milan) CPU を最大 120 コア、448 GB の RAM を備え、ハイパースレッド機能はありません。 HBv3 シリーズ VM も毎秒 350 GB のメモリ帯域幅を提供します。L3 キャッシュはコアあたり最大 32 MB です。ブロック デバイス SSD パフォーマンスは最大毎秒 7 GB です。クロック周波数は最大 3.675 GHz です。 

HBv3 シリーズ VM はすべて、NVIDIA ネットワークの毎秒 200 Gb の HDR InfiniBand を備えており、スーパーコンピューター規模の MPI ワークロードを可能にします。 これらの VM は、最適化された一貫性のある RDMA パフォーマンスを確保するために、ノンブロッキング ファット ツリー構造で接続されています。 HDR InfiniBand ファブリックはまた、アダプティブ ルーティング、および標準 RC トランスポートと UD トランスポートに加え、動的接続トランスポート (DCT) をサポートしています。 これらの機能により、アプリケーションのパフォーマンス、スケーラビリティ、および整合性が向上するため、これらを使用することを強くお勧めします。

[Premium Storage](premium-storage-performance.md):サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): 近日公開予定<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

|サイズ |vCPU |プロセッサ |メモリ (GiB) |メモリ帯域幅 GB/秒 |ベース CPU 周波数 (GHz) |全コア周波数 (GHz、ピーク) |シングルコア周波数 (GHz、ピーク) |RDMA パフォーマンス (GB/秒) |MPI のサポート |一時ストレージ (GiB) |最大データ ディスク数 |最大イーサネット vNIC 数 |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |All |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |All |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |All |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |All |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3.675 |200 |All |2 * 960 |32 |8 |

詳細については、下記を参照してください。
- [アーキテクチャと VM トポロジ](./workloads/hpc/hbv3-series-overview.md)
- サポート対象 OS など、サポート対象の[ソフトウェア スタック](./workloads/hpc/hbv3-series-overview.md#software-specifications)
- HBv3 シリーズ VM に見込まれる[パフォーマンス](./workloads/hpc/hbv3-performance.md)

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
