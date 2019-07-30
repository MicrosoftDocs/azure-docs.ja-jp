---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: bb945695e0525876e044117e26c239e21d66473f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673405"
---
このセクションでは、仮想マシンのサイズの前の世代の情報を提供します。 これらのサイズも、使用できますが、より新しい世代が使用可能です。 

## <a name="f-series"></a>F シリーズ

F シリーズは 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサを基盤としています。このプロセッサは Intel Turbo Boost Technology 2.0 によって最高 3.1 GHz のクロック速度を達成できます。 これは、Dv2 シリーズ VM と同じ CPU パフォーマンスです。  

F シリーズ VM は、より高速の CPU を必要としつつも、vCPU あたりのメモリや一時ストレージについてはそれほど多くを要求しないワークロードに最適です。  F シリーズのもたらす価値は、分析、ゲーム サーバー、Web サーバー、およびバッチ処理などのワークロードに恩恵を与えます。

ACU: 210 から 250

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size         | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000/46/23                                           | 4 / 4x500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6000/93/46                                           | 8 / 8x500                         | 2/1,500                     |
| Standard_F4  | 4         | 8           | 64             | 12000/187/93                                         | 16 / 16x500                         | 4/3,000                     |
| Standard_F8  | 8         | 16          | 128            | 24000/375/187                                        | 32 / 32x500                       | 8/6,000                     |
| Standard_F16 | 16        | 32          | 256            | 48000/750/375                                        | 64 / 64x500                       | 8/12,000           |

## <a name="fs-series-sup1sup"></a>Fs シリーズ <sup>1</sup>

Fs シリーズには、Premium Storage に加え、F シリーズのすべての利点が備わっています。

ACU: 210 から 250

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000/32 (12) |3200/48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8000/64 (24) |6400/96 |2/1,500 |
| Standard_F4s |4 |8 |16 |16 |16000/128 (48) |12800/192 |4/3,000 |
| Standard_F8s |8 |16 |32 |32 |32000/256 (96) |25600/384 |8/6,000 |
| Standard_F16s |16 |32 |64 |64 |64000/512 (192) |51200/768 |8/12,000 |

MBps = 10^6 バイト/秒、GiB = 1024^3 バイト。

<sup>1</sup> Fs シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、[高パフォーマンス用の設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。  

## <a name="ls-series"></a>Ls シリーズ

Ls シリーズでは、[Intel® Xeon® プロセッサ E5 v3 ファミリ](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)を使用し、最大 32 個の vCPU を提供します。 Ls シリーズは、G/GS シリーズと同じ CPU パフォーマンスであり、vCPU あたり 8 GiB のメモリを搭載しています。

Ls シリーズでは、持続性のあるデータ ディスクで実現できる IOPS を向上させるためのローカル キャッシュの作成はサポートされません。 Ls シリーズの VM は、ローカル ディスクの高スループットと IOPS によって、1 つの VM で障害が発生した場合に複数の VM にデータをレプリケートして永続性を実現する Apache Cassandra や MongoDB などの NoSQL ストアにとって最適なものになっています。

ACU: 180 から 240

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされていません
 
| Size          | vCPU | メモリ (GiB) | 一時ストレージ (GiB) | 最大データ ディスク数 | 一時ストレージの最大スループット (IOPS / MBps) | キャッシュ不使用時の最大ディスク スループット (IOPS / MBps) | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2/4,000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8,000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/1,6000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8/20,000 | 

Ls シリーズの VM で実現可能な最大ディスク スループットは、接続されたディスクの数、サイズ、ストライピングによって制限される場合があります。 詳細については、[高パフォーマンス用の設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。

<sup>1</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

## <a name="nvv2-series-preview"></a>NVv2 シリーズ (プレビュー)

**新しいサイズ (推奨)** :[NVv3 シリーズ (プレビュー)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series-preview-1)

NVv2 シリーズの仮想マシンは、Intel Broadwell CPU を使用した [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU および NVIDIA GRID テクノロジを搭載しています。 これらの仮想マシンは、お客様がデータを視覚化したり、表示する結果をシミュレートしたり、CAD で作業したり、コンテンツをレンダリングおよびストリーミングしたりしたいと考える、GPU で高速化されたグラフィックス アプリケーションや仮想デスクトップを対象にしています。 さらに、これらの仮想マシンは、エンコーディングやレンダリングなどの単精度のワークロードを実行できます。 NVv2 仮想マシンは Premium Storage をサポートし、以前の NV シリーズと比較して 2 倍のシステム メモリ (RAM) を備えています。  

NVv2 インスタンス内の各 GPU には GRID ライセンスが付属しています。 このライセンスでは柔軟性が確保され、NV インスタンスを仮想ワークステーションとして 1 人のユーザーに対して使用したり、仮想アプリケーションのシナリオで 25 人のユーザーが同時に VM に接続したりできます。

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 | 仮想ワークステーション | 仮想アプリケーション | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 ～ A4 (CLI と PowerShell の使用)

クラシック デプロイ モデルでは、一部の VM サイズが CLI と PowerShell で若干異なります。

* Standard_A0: ExtraSmall
* Standard_A1: Small
* Standard_A2: Medium
* Standard_A3: Large
* Standard_A4: ExtraLarge
