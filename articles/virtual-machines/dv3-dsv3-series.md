---
title: Dv3 および Dsv3 シリーズ
description: Dv3 および Dsv3 シリーズ VM の仕様。
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 7d6f9d69f04444c01f35437e5300c3033daed5ea
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648447"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 および Dsv3 シリーズ

Dv3 シリーズは、ハイパースレッド構成での Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案を提示します。 メモリが増設 (約 3.5 GiB/vCPU から 4 GiB/vCPU) される一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Dv3 シリーズには、D および Dv2 シリーズのハイ メモリの VM サイズがなくなりました。これらは、メモリ最適化された [Ev3 および Esv3 シリーズ](ev3-esv3-series.md)に移動されました。

D シリーズのユース ケースの例には、エンタープライズ級のアプリケーション、リレーショナル データベース、メモリ内キャッシュ、分析があります。

## <a name="dv3-series"></a>Dv3 シリーズ

Dv3 シリーズのサイズは、Intel Turbo Boost Technology 2.0 を備えた Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行されます。 Dv3 シリーズのサイズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ストレージの組み合わせを提供します。

データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium Storage ディスクを使用するには、Dsv3 サイズを使用してください。 Dsv3 サイズの料金および課金の計算方法は、Dv3 シリーズと同じです。

Dv3 シリーズの VM は、Intel® ハイパースレッディング テクノロジを備えています。

ACU: 160-190

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大 NIC 数|ネットワーク帯域幅 |
|---|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2|1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8|24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8|30000 |

## <a name="dsv3-series"></a>Dsv3 シリーズ

Dsv3 シリーズのサイズは、Intel Turbo Boost Technology 2.0 を備えた Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行され、Premium Storage が使用されます。 Dsv3 シリーズのサイズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ストレージの組み合わせを提供します。

Dsv3 シリーズの VM は、Intel® ハイパースレッディング テクノロジを備えています。

ACU: 160-190

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション:サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2|1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2|2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4|4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8|8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報: [ディスクの種類](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。