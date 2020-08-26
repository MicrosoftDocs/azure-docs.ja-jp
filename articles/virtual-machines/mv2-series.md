---
title: Mv2 シリーズ - Azure Virtual Machines
description: Mv2 シリーズ VM の仕様。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: f6dc732a512368b007a289c3aaabc3123a696d96
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505259"
---
# <a name="mv2-series"></a>Mv2 シリーズ

Mv2 シリーズは、高スループットで低待機時間のプラットフォームを特長としています。このプラットフォームは、全コア ベース周波数 2.5 GHz、最大ターボ周波数 3.8 GHz を備えたハイパースレッド Intel® Xeon® Platinum 8180 M 2.5 GHz (Skylake) プロセッサで実行されています。 すべての Mv2 シリーズ仮想マシンで、標準またはプレミアムの永続ディスクを使用できます。 Mv2 シリーズ インスタンスはメモリ最適化済み VM サイズで、非常に優れたコンピューティング性能によって大規模なメモリ内データベースとワークロードをサポートし、リレーショナル データベース サーバー、大規模キャッシュ、インメモリ分析に適した高いメモリ対 CPU 比率を提供します。

Mv2 シリーズの VM は Intel® ハイパー スレッディング テクノロジを利用しています

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション: サポートされていません

メモリ保持更新: サポートされていません

書き込みアクセラレータ:[サポートされています](./how-to-enable-write-accelerator.md)

|サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 | 必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 | 32000 |

<sup>1</sup> Mv2 シリーズの VM は第 2 世代のみであり、第 2 世代でサポートされているイメージのサブセットがサポートされます。 Mv2 シリーズでサポートされているイメージの完全な一覧については、以下を参照してください。 Linux を使用している場合、イメージを検索して選択する方法については、[Azure での第 2 世代 VM のサポート](./linux/generation-2.md)に関するページを参照してください。 Windows を使用している場合、イメージを検索して選択する方法については、[Azure での第 2 世代 VM のサポート](./windows/generation-2.md)に関するページを参照してください。 

- Windows Server 2019 以降
- SUSE Linux Enterprise Server 12 SP4 以降または SUSE Linux Enterprise Server 15 SP1 以降
- Red Hat Enterprise Linux 7.6、7.7、8.1 以降 
- Oracle Enterprise Linux 7.7 以降



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報: [ディスクの種類](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。