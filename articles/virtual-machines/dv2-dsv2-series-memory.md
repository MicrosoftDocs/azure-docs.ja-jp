---
title: メモリを最適化した Dv2 と DSv2 シリーズ VM - Azure Virtual Machines
description: Dv2 と DSv2 シリーズ VM の仕様。
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: f07e4542e380e4e8057d9011a6b613e9ad55a058
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560329"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>メモリが最適化された Dv2 と Dsv2 シリーズ

オリジナルの D シリーズに続く Dv2 と Dsv2 シリーズには、より強力な CPU が備わっています。 DSv2 シリーズのサイズは、Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行されます。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

## <a name="dv2-series-11-15"></a>Dv2 シリーズ 11 - 15

Dv2 シリーズのサイズは、Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行されます。

[ACU](acu.md): 210 から 250<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。
<sup>2</sup> 高速ネットワークを使用した場合、25,000 Mbps。

## <a name="dsv2-series-11-15"></a>DSv2 シリーズ 11 - 15

DSv2 シリーズのサイズは、Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、または Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) プロセッサ上で実行されます。

[ACU](acu.md): 210 から 250 <sup>1</sup><br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
<br> 

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> DSv2 シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、[高パフォーマンス用の設計](./premium-storage-performance.md)に関する記事を参照してください。
<sup>2</sup> インスタンスは Intel Haswell ベースのハードウェアに分離され、1 人の顧客専用となります。  
<sup>3</sup> コア数を制限したサイズも提供しています。  
<sup>4</sup> 高速ネットワークを使用した場合、25,000 Mbps。

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