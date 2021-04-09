---
title: 制約付き vCPU サイズ
description: 制約付き vCPU 数に対応する VM サイズを示します。
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 7faeec8494a908b9aab00be9b63904354b5e0994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557082"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>制約付き vCPU 対応の VM サイズ

SQL Server や Oracle などの一部のデータベース ワークロードでは、メモリ、記憶域、I/O 帯域幅は大量に必要ですが、コアの数は多くなくてもかまいません。 多くのデータベース ワークロードは、CPU 集中型ではありません。 Azure で提供される一部の VM サイズでは、VM の vCPU の数を制限してフトウェア ライセンスのコストを抑えながら、同じメモリ、記憶域、I/O 帯域幅を維持できます。

vCPU の数を、元の VM サイズの半分または 4 分の 1 に制限することができます。 これらの新しい VM サイズでは、識別しやすいように、アクティブな vCPU の数を指定するサフィックスが付加されています。

たとえば、現在の VM サイズ Standard_GS5 の仕様は、32 vCPU、448 GB RAM、64 ディスク (最大 256 TB)、80,000 IOP または 2 GB/秒の I/O 帯域幅です。 新しい VM サイズ Standard_GS5-16 および Standard_GS5-8 の仕様は、アクティブな vCPU はそれぞれ 16 個および 8 個ですが、メモリ、記憶域、I/O 帯域幅は Standard_GS5 と同じです。

SQL Server または Oracle のライセンス料金は新しい vCPU の数に制限され、他の製品は新しい vCPU の数に基づいて課金されます。 その結果、アクティブな (課金対象の) vCPU に対する VM の仕様の比は 50 ～ 75% 高くなります。 これらの新しい VM サイズを利用すると、お客様のワークロードで、ソフトウェア ライセンスのコストを最適化しながら、同じメモリ、ストレージ、I/O 帯域幅を使用できます。 現時点では、OS のライセンスを含むコンピューティング コストは元のサイズと同じです。 詳しくは、[コスト効率の高いデータベース ワークロードのための新しい VM サイズ](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)に関するページをご覧ください。


| 名前                | vCPU | 仕様           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | M8ms と同じ    |
| Standard_M8-4ms     | 4    | M8ms と同じ    |
| Standard_M16-4ms    | 4    | M16ms と同じ   |
| Standard_M16-8ms    | 8    | M16ms と同じ   |
| Standard_M32-8ms    | 8    | M32ms と同じ   |
| Standard_M32-16ms   | 16   | M32ms と同じ   |
| Standard_M64-32ms   | 32   | M64ms と同じ   |
| Standard_M64-16ms   | 16   | M64ms と同じ   |
| Standard_M128-64ms  | 64   | M128ms と同じ  |
| Standard_M128-32ms  | 32   | M128ms と同じ  |
| Standard_E4-2s_v3   | 2    | E4s_v3 と同じ  |
| Standard_E8-4s_v3   | 4    | E8s_v3 と同じ  |
| Standard_E8-2s_v3   | 2    | E8s_v3 と同じ  |
| Standard_E16-8s_v3  | 8    | E16s_v3 と同じ |
| Standard_E16-4s_v3  | 4    | E16s_v3 と同じ |
| Standard_E32-16s_v3 | 16   | E32s_v3 と同じ |
| Standard_E32-8s_v3  | 8    | E32s_v3 と同じ |
| Standard_E64-32s_v3 | 32   | E64s_v3 と同じ |
| Standard_E64-16s_v3 | 16   | E64s_v3 と同じ |
| Standard_E4-2s_v4   | 2    | E4s_v4 と同じ  |
| Standard_E8-4s_v4   | 4    | E8s_v4 と同じ  |
| Standard_E8-2s_v4   | 2    | E8s_v4 と同じ  |
| Standard_E16-8s_v4  | 8    | E16s_v4 と同じ |
| Standard_E16-4s_v4  | 4    | E16s_v4 と同じ |
| Standard_E32-16s_v4 | 16   | E32s_v4 と同じ |
| Standard_E32-8s_v4  | 8    | E32s_v4 と同じ |
| Standard_E64-32s_v4 | 32   | E64s_v4 と同じ |
| Standard_E64-16s_v4 | 16   | E64s_v4 と同じ |
| Standard_E4-2ds_v4  | 2    | E4ds_v4 と同じ |
| Standard_E8-4ds_v4  | 4    | E8ds_v4 と同じ |
| Standard_E8-2ds_v4  | 2    | E8ds_v4 と同じ |
| Standard_E16-8ds_v4 | 8    | E16ds_v4 と同じ|
| Standard_E16-4ds_v4 | 4    | E16ds_v4 と同じ|
| Standard_E32-16ds_v4| 16   | E32ds_v4 と同じ|
| Standard_E32-8ds_v4 | 8    | E32ds_v4 と同じ|
| Standard_E64-32ds_v4| 32   | E64ds_v4 と同じ|
| Standard_E64-16ds_v4| 16   | E64ds_v4 と同じ|
| Standard_E4-2as_v4  | 2    | E4as_v4 と同じ |
| Standard_E8-4as_v4  | 4    | E8as_v4 と同じ |
| Standard_E8-2as_v4  | 2    | E8as_v4 と同じ |
| Standard_E16-8as_v4 | 8    | E16as_v4 と同じ|
| Standard_E16-4as_v4 | 4    | E16as_v4 と同じ|
| Standard_E32-16as_v4| 16   | E32as_v4 と同じ|
| Standard_E32-8as_v4 | 8    | E32as_v4 と同じ|
| Standard_E64-32as_v4| 32   | E64as_v4 と同じ|
| Standard_E64-16as_v4| 16   | E64as_v4 と同じ|
| Standard_E96-48as_v4| 48   | E96as_v4 と同じ|
| Standard_E96-24as_v4| 24   | E96as_v4 と同じ|
| Standard_GS4-8      | 8    | GS4 と同じ     |
| Standard_GS4-4      | 4    | GS4 と同じ     |
| Standard_GS5-16     | 16   | GS5 と同じ     |
| Standard_GS5-8      | 8    | GS5 と同じ     |
| Standard_DS11-1_v2  | 1    | DS11_v2 と同じ |
| Standard_DS12-2_v2  | 2    | DS12_v2 と同じ |
| Standard_DS12-1_v2  | 1    | DS12_v2 と同じ |
| Standard_DS13-4_v2  | 4    | DS13_v2 と同じ |
| Standard_DS13-2_v2  | 2    | DS13_v2 と同じ |
| Standard_DS14-8_v2  | 8    | DS14_v2 と同じ |
| Standard_DS14-4_v2  | 4    | DS14_v2 と同じ |
| Standard_M416-208s_v2 | 208    | M416s_v2 と同じ|
| Standard_M416-208ms_v2 | 208    | M416ms_v2 と同じ |

## <a name="other-sizes"></a>その他のサイズ
- [コンピューティングの最適化](./sizes-compute.md)
- [メモリの最適化](./sizes-memory.md)
- [ストレージの最適化](./sizes-storage.md)
- [GPU](./sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](./sizes-hpc.md)

## <a name="next-steps"></a>次のステップ
[Azure コンピューティング ユニット (ACU)](./acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
