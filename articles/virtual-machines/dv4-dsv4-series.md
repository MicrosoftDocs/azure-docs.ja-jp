---
title: Dv4 および Dsv4 シリーズ - Azure Virtual Machines
description: Dv4 および Dsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: cc6ce30855d17f45636e0df04978fed88dcecff7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549143"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 および Dsv4 シリーズ

Dv4 および Dsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 これは、3.4 GHz の全コア ターボ クロック速度を特徴としています。 

> [!NOTE]
> よく寄せられる質問については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.md)に関する記事をご覧ください。
## <a name="dv4-series"></a>Dv4 シリーズ

Dv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Dv4 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、およびリモート ストレージ オプションの組み合わせが提供されます。 Dv4 シリーズ VM は [Intel&reg; ハイパースレッディング テクノロジ](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)を備えています。

リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium Storage ディスクを使用するには、Dsv4 サイズを使用してください。 Dsv4 サイズの価格および課金の計算方法は、Dv4 シリーズと同じです。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | リモート ストレージのみ | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | リモート ストレージのみ | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | リモート ストレージのみ | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | リモート ストレージのみ | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | リモート ストレージのみ | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | リモート ストレージのみ | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | リモート ストレージのみ | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 シリーズ

Dsv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Dv4 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、およびリモート ストレージ オプションの組み合わせが提供されます。 Dsv4 シリーズ VM は [Intel&reg; ハイパースレッディング テクノロジ](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)を備えています。 リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています ("*4 vCPU 以上が必要*")<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | リモート ストレージのみ | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | リモート ストレージのみ | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | リモート ストレージのみ | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | リモート ストレージのみ | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | リモート ストレージのみ | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | リモート ストレージのみ | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | リモート ストレージのみ | 32 | 80000/1200 | 8|30000 |
