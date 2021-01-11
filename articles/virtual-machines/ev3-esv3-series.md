---
title: Ev3 シリーズと Esv3 シリーズ
description: Ev3 および Esv3 シリーズ VM の仕様。
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f9ef048fa7cc4b795f409326988e3276743b6992
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648957"
---
# <a name="ev3-and-esv3-series"></a>Ev3 および Esv3 シリーズ

Ev3 と Esv3 シリーズは、ハイパースレッド構成の Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、または Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) プロセッサで実行されることで、大半の汎用ワークロード向けに付加価値を高め、他の多くのクラウドの汎用 VM と一線化されています。  メモリが増設 (7 GiB/vCPU から 8 GiB/vCPU) される一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Ev3 は、D/Dv2 ファミリーのハイ メモリ VM サイズのフォローアップです。

## <a name="ev3-series"></a>Ev3 シリーズ

Ev3 シリーズのインスタンスは、Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、または Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) プロセッサで実行され、Intel Turbo Boost Technology 2.0 を搭載しています。 Ev3 シリーズのインスタンスは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。

データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium Storage ディスクを使用するには、ESv3 サイズを使用してください。 ESv3 サイズの料金および課金の計算方法は、Ev3 シリーズと同じです。

Ev3 シリーズの VM は Intel® ハイパー スレッディング テクノロジを利用しています。

ACU: 160 から 190

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大 NIC 数 | ネットワーク帯域幅 |
|---|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2|1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2|2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4|4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8|8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8|10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8|16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8|24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8|30000 |
| Standard_E64i_v3 <sup>1、2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8|30000 |

<sup>1</sup> コア数を制限したサイズも提供しています。

<sup>2</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

## <a name="esv3-series"></a>Esv3-series

Ev3 シリーズのインスタンスは、Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、または Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) プロセッサで実行され、Intel Turbo Boost Technology 2.0 を搭載し、Premium Storage を使用しています。 Esv3 シリーズのインスタンスは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。

Esv3 シリーズの VM は Intel® ハイパー スレッディング テクノロジを利用しています。

ACU: 160-190

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション:サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2|2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> コア数を制限したサイズも提供しています。

<sup>2</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報:[ディスクの種類](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。