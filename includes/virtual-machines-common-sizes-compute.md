---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7bfb96a40dedf780920e6fdd6e08e1b583bea902
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501248"
---
<!-- F-series, Fs-series* -->

コンピューティングに最適化された VM サイズは、高い CPU 対メモリ比を備えており、トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。 この記事では、このグループ内の各サイズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。

Fsv2 シリーズは、Intel® Xeon® Platinum 8168 プロセッサを基盤とし、3.4 GHz の全コア ターボ クロック速度の持続と、3.7 GHz の最大のシングル コア ターボ周波数を特長とします。 Intel のスケーラブル プロセッサでは新しい Intel® AVX-512 命令によって、単精度浮動小数点演算と倍精度浮動小数点演算の両方でベクター処理ワークロードのパフォーマンスが最大で 2 倍向上します。 つまり、これらはあらゆるコンピューティング ワークロードで非常に高速です。 

Fsv2 シリーズは時間あたりの料金が抑えられており、vCPU あたりの Azure コンピューティング ユニット (ACU) に基づく Azure ポートフォリオにおいて、最もコスト パフォーマンスに優れています。

## <a name="fsv2-series-sup1sup"></a>Fsv2 シリーズ <sup>1</sup>

ACU: 195 - 210

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size             | vCPU の数 | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4,000/31 (32)           | 3,200/47                | 2/875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8,000/63 (64)           | 6,400/95                | 2/1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16,000/127 (128)        | 12,800/190              | 4/3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32,000/255 (256)        | 25,600/380              | 4/7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64,000/512 (512)        | 51,200/750              | 8/14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800/1100             | 8/21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128,000/1,024 (1024)     | 80,000/1,100             | 8/28000              |
| Standard_F72s_v2<sup>2、3</sup> | 72 | 144 | 576         | 32             | 144,000/1,152 (1520)     | 80,000/1,100             | 8/30,000              |

<sup>1</sup> Fsv2 シリーズの VM は Intel® ハイパー スレッディング テクノロジを利用しています

<sup>2</sup> 64 個を超える vCPU では、Windows Server 2016、Ubuntu 16.04 LTS、SLES 12 SP2、および LIS 4.2.1 が付属する Red Hat Enterprise Linux、CentOS 7.3、または Oracle Linux 7.3 の、サポートされているゲスト OS のいずれかが必要です。

<sup>3</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。