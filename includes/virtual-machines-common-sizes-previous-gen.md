---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: d0802cfcf05874044b6e116ba194c16a79f9d309
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550140"
---
このセクションでは、仮想マシンのサイズの前の世代の情報を提供します。 これらのサイズも、使用できますが、より新しい世代が使用可能です。 

## <a name="f-series"></a>F シリーズ

F シリーズは 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサを基盤としています。このプロセッサは Intel Turbo Boost Technology 2.0 によって最高 3.1 GHz のクロック速度を達成できます。 これは、Dv2 シリーズ VM と同じ CPU パフォーマンスです。  

F シリーズ VM は、より高速の CPU を必要としつつも、vCPU あたりのメモリや一時ストレージについてはそれほど多くを要求しないワークロードに最適です。  F シリーズのもたらす価値は、分析、ゲーム サーバー、Web サーバー、およびバッチ処理などのワークロードに恩恵を与えます。

ACU: 210 から 250

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

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

Premium Storage Caching: サポートされています

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000/32 (12) |3,200/48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8,000/64 (24) |6,400/96 |2/1,500 |
| Standard_F4s |4 |8 |16 |16 |16,000/128 (48) |12,800/192 |4/3,000 |
| Standard_F8s |8 |16 |32 |32 |32,000/256 (96) |25,600/384 |8/6,000 |
| Standard_F16s |16 |32 |64 |64 |64,000/512 (192) |51,200/768 |8/12,000 |

MBps = 10^6 バイト/秒、GiB = 1024^3 バイト。

<sup>1</sup> Fs シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、[高パフォーマンス用の設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。  

## <a name="ls-series"></a>Ls シリーズ

Ls シリーズでは、[Intel® Xeon® プロセッサ E5 v3 ファミリ](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)を使用し、最大 32 個の vCPU を提供します。 Ls シリーズは、G/GS シリーズと同じ CPU パフォーマンスであり、vCPU あたり 8 GiB のメモリを搭載しています。

Ls シリーズでは、持続性のあるデータ ディスクで実現できる IOPS を向上させるためのローカル キャッシュの作成はサポートされません。 Ls シリーズの VM は、ローカル ディスクの高スループットと IOPS によって、1 つの VM で障害が発生した場合に複数の VM にデータをレプリケートして永続性を実現する Apache Cassandra や MongoDB などの NoSQL ストアにとって最適なものになっています。

ACU: 180 から 240

Premium Storage: サポートされています

Premium Storage Caching: サポートされていません
 
| Size          | vCPU | メモリ (GiB) | 一時ストレージ (GiB) | 最大データ ディスク数 | 一時ストレージの最大スループット (IOPS / MBps) | キャッシュ不使用時の最大ディスク スループット (IOPS / MBps) | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000/125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000/500 | 8 / 16,000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000/1,000     | 8 / 20,000 | 

Ls シリーズの VM で実現可能な最大ディスク スループットは、接続されたディスクの数、サイズ、ストライピングによって制限される場合があります。 詳細については、[高パフォーマンス用の設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。

<sup>1</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 ～ A4 (CLI と PowerShell の使用)

クラシック デプロイ モデルでは、一部の VM サイズが CLI と PowerShell で若干異なります。

* Standard_A0: ExtraSmall
* Standard_A1: Small
* Standard_A2: Medium
* Standard_A3: Large
* Standard_A4: ExtraLarge
