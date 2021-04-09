---
title: Ddv4 および Ddsv4 シリーズ
description: Dv4、Ddv4、Dsv4、および Ddsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: feabdcef9298c93f0cba93d3eeb9ebb0a32d6ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560346"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 および Ddsv4 シリーズ

Ddv4 および Ddsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 3\.4 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 また、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) がサポートされています。 これらの新しい VM サイズでは、50% 大きいローカル ストレージが使用されるほか、[Gen2 VM](./generation-2.md) の [Dv3/Dsv3](./dv3-dsv3-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します。

D シリーズのユース ケースには、エンタープライズ級のアプリケーション、リレーショナル データベース、メモリ内キャッシュ、分析があります。

## <a name="ddv4-series"></a>Ddv4 シリーズ

Ddv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ddv4 シリーズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ディスクの組み合わせを提供します。

新しい Ddv4 VM のサイズには、高速で大規模なローカル SSD ストレージ (最大 2400 GiB) が含まれており、低待機時間で高速のローカル ストレージを利用するアプリケーション向けに設計されています。そのようなアプリケーションとしては、一時ストレージへの高速な読み取り/書き込みを必要とするものや、キャッシュまたは一時ファイル用の一時ストレージを必要とするものなどがあります。 Ddv4 VM には Standard SSD および Standard HDD ストレージを接続できます。 リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | <sup>**</sup> キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>**</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します

## <a name="ddsv4-series"></a>Ddsv4 シリーズ

Ddv4 シリーズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ddsv4 シリーズは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、一時ディスクの組み合わせを提供します。

新しい Ddsv4 VM のサイズには、高速で大規模なローカル SSD ストレージ (最大 2400 GiB) が含まれており、低待機時間で高速のローカル ストレージを利用するアプリケーション向けに設計されています。そのようなアプリケーションとしては、一時ストレージへの高速な読み取り/書き込みを必要とするものや、キャッシュまたは一時ファイル用の一時ストレージを必要とするものなどがあります。 

 > [!NOTE]
 >Ddsv4 サイズの価格および課金の計算方法は、Ddv4 シリーズと同じです。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | <sup>**</sup> キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

<sup>**</sup> これらの IOPS 値を保証するには、[Gen2 VM](generation-2.md) を使用します

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
