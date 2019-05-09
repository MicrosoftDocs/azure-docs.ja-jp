---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: d89a9c4c4498e249dbfbd453ef9772d18ffd213f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550143"
---
このセクションでは、より古い世代の仮想マシンのサイズの情報が提供されます。 これらのサイズは引き続きサポートされますが、追加容量は得られません。 一般提供されるより新しいまたは代替のサイズがあります。 ニーズに最も合う VM サイズを選択する場合は、「[Sizes for Windows virtual machines in Azure](../articles/virtual-machines/windows/sizes.md)」 (Azure の Windows 仮想マシンのサイズ) または「[Sizes for Linux virtual machines in Azure](../articles/virtual-machines/linux/sizes.md)」 (Azure の Linux 仮想マシンのサイズ) を参照してください。  

Linux VM のサイズ変更の詳細については、「[Resize a Linux virtual machine using Azure CLI](../articles/virtual-machines/linux/change-vm-size.md)」 (Azure CLI を使用して Linux 仮想マシンのサイズを変更する) を参照してください。 Windows VM を使用しており、PowerShell を使用したい場合は、「[Resize a Windows VM](../articles/virtual-machines/windows/resize-vm.md)」 (Windows VM のサイズを変更する) を参照してください。  

<br>

### <a name="basic-a"></a>Basic A  

**新しいサイズ (推奨)**:[Av2 シリーズ](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

Basic レベルのサイズは主に、負荷分散や自動スケール、メモリ消費量の多い仮想マシンのいずれも必要としない用途 (開発ワークロードなど) 向けです。

|サイズ – サイズ\名前 | vCPU |メモリ|NIC (最大)|一時ディスクの最大サイズ |最大 データ ディスク数 (各ディスク 1,023 GB)|最大 IOPS (各ディスク 300)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1.75 GB|2| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3.5 GB|2| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 x 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 x 300|

<br>

### <a name="a-series"></a>A シリーズ  

**新しいサイズ (推奨)**:[Av2 シリーズ](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

| Size | vCPU | メモリ: GiB | 一時ストレージ (HDD):GiB | 最大データ ディスク数 | データ ディスクの最大スループット:IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2/100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2/500  |
| Standard_A2 |2 |3.5 |135 |4 |4 x 500 |2/500 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2/1,000 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4/2,000 |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |2/500 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2/1,000 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4/2,000 |

<sup>1</sup> A0 サイズは、物理ハードウェアでオーバーサブスクライブされます。 この特定のサイズの場合のみ、他の顧客デプロイメントは、実行中のワークロードのパフォーマンスに影響することがあります。 下に、予想される基準として相対パフォーマンスを示していますが、約 15% の変動の可能性があります。

<br>

### <a name="a-series---compute-intensive-instances"></a>A シリーズ - コンピューティング集中型インスタンス  

**新しいサイズ (推奨)**:[Av2 シリーズ](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

A8 ～ A11 と H シリーズのサイズは、 *コンピューティング集中型インスタンス*とも呼ばれます。 これらのサイズを実行するハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティング集中型およびネットワーク集中型アプリケーション用に設計および最適化されています。 A8 ～ A11 シリーズは Intel Xeon E5-2670 @ 2.6 GHZ を使用し、H シリーズは Intel Xeon E5-2667 v3 @ 3.2 GHz を使用します。  

| Size | vCPU | メモリ: GiB | 一時ストレージ (HDD):GiB | 最大データ ディスク数 | データ ディスクの最大スループット:IOPS | 最大 NIC 数|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup> MPI アプリケーションの場合、専用の RDMA バックエンド ネットワークが FDR InfiniBand ネットワークによって有効になり、超低待機時間と高帯域幅を実現します。  

<br>

### <a name="d-series"></a>D シリーズ  

**新しいサイズ (推奨)**:[Dv3 シリーズ](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160 から 250 <sup>1</sup>

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

| Size         | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000/46/23                                           | 4 / 4x500                         | 2/500                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 8 / 8x500                         | 2/1,000                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 16 / 16x500                         | 4/2,000                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 32 / 32x500                       | 8/4,000                     |

<sup>1</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D シリーズ - メモリ最適化済み  

**新しいサイズ (推奨)**:[Dv3 シリーズ](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160 から 250 <sup>1</sup>

Premium Storage: サポートされていません

Premium Storage Caching: サポートされていません

| Size         | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 8 / 8x500                         | 2/1,000                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 16 / 16x500                         | 4/2,000                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 32 / 32x500                       | 8/4,000                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 64 / 64x500                       | 8/8,000                |

<sup>1</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS シリーズ  

**新しいサイズ (推奨)**:[DSv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160 から 250 <sup>1</sup>

Premium Storage: サポートされています

Premium Storage Caching: サポートされています

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000/32 (43) |3,200/32 |2/500 |
| Standard_DS2 |2 |7 |14 |8 |8,000/64 (86) |6,400/64 |2/1,000 |
| Standard_DS3 |4 |14 |28 |16 |16,000/128 (172) |12,800/128 |4/2,000 |
| Standard_DS4 |8 |28 |56 |32 |32,000/256 (344) |25,600/256 |8/4,000 |

<sup>1</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS シリーズ - メモリ最適化済み  

**新しいサイズ (推奨)**:[DSv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160 から 250 <sup>1、2</sup>

Premium Storage: サポートされています

Premium Storage Caching: サポートされています

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000/64 (72) |6,400/64 |2/1,000 |
| Standard_DS12 |4 |28 |56 |16 |16,000/128 (144) |12,800/128 |4/2,000 |
| Standard_DS13 |8 |56 |112 |32 |32,000/256 (288) |25,600/256 |8/4,000 |
| Standard_DS14 |16 |112 |224 |64 |64,000/512 (576) |51,200/512 |8/8,000 |

<sup>1</sup> DS シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、[高パフォーマンス用の設計](../articles/virtual-machines/windows/premium-storage-performance.md)に関する記事を参照してください。   
<sup>2</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>
