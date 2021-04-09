---
title: Dv3 および Dsv3 シリーズ
description: Dv3 および Dsv3 シリーズ VM の仕様。
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 22400164ac82df3ec0e0e24daf6d823eae219837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566432"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 および Dsv3 シリーズ

Dv3 シリーズは、ハイパースレッド構成での Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案を提示します。 メモリが増設 (約 3.5 GiB/vCPU から 4 GiB/vCPU) される一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Dv3 シリーズには、D および Dv2 シリーズのハイ メモリの VM サイズがなくなりました。これらは、メモリ最適化された [Ev3 および Esv3 シリーズ](ev3-esv3-series.md)に移動されました。

D シリーズのユース ケースの例には、エンタープライズ級のアプリケーション、リレーショナル データベース、メモリ内キャッシュ、分析があります。

## <a name="dv3-series"></a>Dv3 シリーズ

Dv3 シリーズのサイズは、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) を備えた Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行されます。 Dv3 シリーズのサイズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ストレージの組み合わせを提供します。

データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium Storage ディスクを使用するには、Dsv3 サイズを使用してください。 Dsv3 サイズの料金および課金の計算方法は、Dv3 シリーズと同じです。

Dv3 シリーズの VM は、Intel® ハイパースレッディング テクノロジを備えています。

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大 NIC/ネットワーク帯域幅 |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 シリーズ

Dsv3 シリーズのサイズは、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) を備えた Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行され、Premium Storage が使用されます。 Dsv3 シリーズのサイズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ストレージの組み合わせを提供します。

Dsv3 シリーズの VM は、Intel® ハイパースレッディング テクノロジを備えています。

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | バースト キャッシュが有効な一時ストレージの最大スループット: IOPS/MBps<sup>1</sup> | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup> Dsv3 シリーズの VM では、ディスクのパフォーマンスを[バースト](./disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
- ディスクの種類の詳細については、「[Azure で利用できるディスクの種類](disks-types.md)」を参照してください

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。