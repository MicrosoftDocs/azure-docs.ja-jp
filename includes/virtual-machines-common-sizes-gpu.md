---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 26a5baf07ee31bdf155629139e12ef1977ddca1d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457422"
---
GPU 最適化済み VM サイズは、1 つまたは複数の NVIDIA GPU を備えた、特殊な用途に特化した仮想マシンです。 これらのサイズは、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードを意図して設計されています。 この記事では、GPU、vCPU、データ ディスク、NIC の数と種類についての情報を提供します。 このグループ内の各サイズのストレージのスループットおよびネットワーク帯域幅も含まれています。

* **NC、NCv2、NCv3** の各サイズは、コンピューティング集中型やネットワーク集中型のアプリケーションおよびアルゴリズム向けに最適化されています。 例としては、CUDA および OpenCL ベースのアプリケーションやシミュレーション、AI、ディープ ラーニングなどが挙げられます。 NCv3 シリーズは、NVIDIA の Tesla V100 GPU を搭載したハイ パフォーマンス コンピューティング ワークロードにフォーカスしています。 NC シリーズは Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) プロセッサを使用し、NCv2 シリーズおよび NCv3 シリーズ VM は Intel Xeon E5-2690 v4 (Broadwell) プロセッサを使用しています。

* **ND および NDv2** ND シリーズは、ディープ ラーニング用のトレーニングと推論のシナリオにフォーカスしています。 これは、NVIDIA Tesla P40 GPU および Intel Xeon E5-2690 v4 (Broadwell) プロセッサを使用しています。 NDv2 シリーズは、Intel Xeon Platinum 8168 (Skylake) プロセッサを使用しています。

* **NV と NVv3** のサイズは、リモートの視覚化、ストリーミング、ゲーム、エンコーディング、および OpenGL や DirectX などのフレームワークを使用する VDI シナリオ用に最適化および設計されています。  これらの VM は、NVIDIA Tesla M60 GPU によってバックアップされされます。

## <a name="nc-series"></a>NC シリーズ

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

NC シリーズ VM は、[NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) カードおよび Intel Xeon E5-2690 v3 (Haswell) プロセッサを搭載しています。 エネルギー調査アプリケーション向け CUDA やクラッシュ シミュレーション、レイ トレーシング レンダリング、ディープ ラーニングなどを活用することで、ユーザーはデータをさらに高速に処理することができます。 NC24r 構成には、密結合並列コンピューティングのワークロード向けに最適化された、低待機時間かつ高スループットのネットワーク インターフェイスが搭載されています。

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = K80 カードの 2 分の 1 相当。

*RDMA 対応

## <a name="ncv2-series"></a>NCv2 シリーズ

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

NCv2 シリーズ VM は [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU を備えています。 これらの GPU は、NC シリーズの 2 倍以上の計算性能を有しています。 貯留層モデリング、DNA シーケンシング、タンパク質解析、モンテ カルロ シミュレーションをはじめとする従来の HPC ワークロードに、これらの最新の GPU を活用することができます。 GPU に加えて、NCv2 シリーズ VM は Intel Xeon E5-2690 v4 (Broadwell) CPU も搭載しています。

NC24rs v2 構成には、密結合並列コンピューティングのワークロード向けに最適化された、低待機時間かつ高スループットのネットワーク インターフェイスが搭載されています。

> [!IMPORTANT]
> このサイズ ファミリーでは、ご利用のサブスクリプションの vCPU (コア) クォータが、各リージョンで 0 に初期設定されています。 このファミリーについては、[提供リージョン](https://azure.microsoft.com/regions/services/)で [vCPU クォータの引き上げを要求](../articles/azure-supportability/resource-manager-core-quotas-request.md)してください。
>

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = P100 カード 1 枚

*RDMA 対応

## <a name="ncv3-series"></a>NCv3 シリーズ

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

NCv3 シリーズ VM は [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU を備えています。 これらの GPU は、NCv2 シリーズの 1.5 倍以上の計算性能を有しています。 貯留層モデリング、DNA シーケンシング、タンパク質解析、モンテ カルロ シミュレーションをはじめとする従来の HPC ワークロードに、これらの最新の GPU を活用することができます。 NC24rs v3 構成には、密結合並列コンピューティングのワークロード向けに最適化された、低待機時間かつ高スループットのネットワーク インターフェイスが搭載されています。 GPU に加えて、NCv3 シリーズ VM は Intel Xeon E5-2690 v4 (Broadwell) CPU も搭載しています。

> [!IMPORTANT]
> このサイズ ファミリーでは、ご利用のサブスクリプションの vCPU (コア) クォータが、各リージョンで 0 に初期設定されています。 このファミリーについては、[提供リージョン](https://azure.microsoft.com/regions/services/)で [vCPU クォータの引き上げを要求](../articles/azure-supportability/resource-manager-core-quotas-request.md)してください。
>

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = V100 カード 1 枚。

*RDMA 対応

## <a name="ndv2-series-preview"></a>NDv2 シリーズ (プレビュー)

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

Infiniband: サポートされていません

NDv2 シリーズは、HPC、AI、機械学習ワークロードのニーズに合わせて設計された GPU ファミリに新たに追加された仮想マシンです。 これは、8 個の NVIDIA Tesla V100 NVLINK が相互接続された GPU、40 個の Intel Xeon Platinum 8168 (Skylake) コア、および 672 GiB のシステム メモリを搭載しています。 NDv2 インスタンスは、Cuda、TensorFlow、Pytorch、Caffe、その他のフレームワークを利用して、HPC および AI ワークロードに対して優れた FP32 および FP64 パフォーマンスを提供します。

[ぜひプレビュー期間中にサインアップして、これらのマシンをご利用ください](https://aka.ms/ndv2signup)。
<br>

| Size | vCPU | GPU | メモリ | NIC (最大) | 一時ストレージ (SSD) GiB | 最大 データ ディスク | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大ネットワーク帯域幅 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000/800 | 24000 Mbps |

## <a name="nd-series"></a>ND シリーズ

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ND シリーズは、AI やディープ ラーニングのワークロードを想定して GPU ファミリーに新たに追加された仮想マシンです。 トレーニングや推論で優れたパフォーマンスを発揮します。 ND インスタンスは、[NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU および Intel Xeon E5-2690 v4 (Broadwell) CPU を搭載しています。 これらのインスタンスは、Microsoft Cognitive Toolkit、TensorFlow、Caffe などのフレームワークを活用する AI ワークロードの単精度浮動小数点演算において、非常に高いパフォーマンスを発揮します。 ND シリーズでは GPU のメモリ サイズ (24 GB) も大幅に増強されているため、より大規模なニューラル ネット モデルにも対応できます。 NC シリーズと同様に、ND シリーズでは 2 番目に少ない待機時間、RDMA を利用した高スループットのネットワーク、InfiniBand との接続性などを備えた構成が利用できます。これにより、多数の GPU を利用した大規模なトレーニング ジョブを実行できます。

> [!IMPORTANT]
> このサイズ ファミリーでは、ご利用のサブスクリプションの vCPU (コア) クォータが、各リージョンで 0 に初期設定されています。 このファミリーについては、[提供リージョン](https://azure.microsoft.com/regions/services/)で [vCPU クォータの引き上げを要求](../articles/azure-supportability/resource-manager-core-quotas-request.md)してください。
>

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = P40 カード 1 枚

*RDMA 対応

## <a name="nv-series"></a>NV シリーズ

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

NV シリーズの仮想マシンは、[NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU およびデスクトップ アクセラレータ アプリケーションや仮想デスクトップ向けの NVIDIA GRID テクノロジを備えていて、お客様は、データやシミュレーションを視覚化することができます。 NV インスタンスでは、グラフィックス処理を要するワークフローを視覚化して優れたグラフィックス機能を活用し、さらにエンコードやレンダリングなどの単精度のワークロードを実行することもできます。 NV シリーズ VM は、Intel Xeon E5-2690 v3 (Haswell) CPU も搭載しています。

NV インスタンスの GPU ごとに GRID ライセンスが付属します。 このライセンスでは柔軟性が確保され、NV インスタンスを仮想ワークステーションとして 1 人のユーザーに対して使用したり、仮想アプリケーションのシナリオで 25 人のユーザーが同時に VM に接続したりできます。

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 | 仮想ワークステーション | 仮想アプリケーション |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = M60 カードの 2 分の 1 相当。

## <a name="nvv3-series-preview-sup1sup"></a>NVv3 シリーズ (プレビュー) <sup>1</sup>

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

NVv3 シリーズの仮想マシンは、Intel E5-2690 v4 (Broadwell) CPU を使用した [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU および NVIDIA GRID テクノロジを搭載しています。 これらの仮想マシンは、お客様がデータを視覚化したり、表示する結果をシミュレートしたり、CAD で作業したり、コンテンツをレンダリングおよびストリーミングしたりしたいと考える、GPU で高速化されたグラフィックス アプリケーションや仮想デスクトップを対象にしています。 さらに、これらの仮想マシンは、エンコーディングやレンダリングなどの単精度のワークロードを実行できます。 NVv3 仮想マシンは Premium Storage をサポートしており、以前の NV シリーズと比較して 2 倍のシステム メモリ (RAM) を備えています。  

NVv3 インスタンス内の各 GPU には GRID ライセンスが付属しています。 このライセンスでは柔軟性が確保され、NV インスタンスを仮想ワークステーションとして 1 人のユーザーに対して使用したり、仮想アプリケーションのシナリオで 25 人のユーザーが同時に VM に接続したりできます。

| Size | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数 | 仮想ワークステーション | 仮想アプリケーション | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = M60 カードの 2 分の 1 相当。

<sup>1</sup> NVv3 シリーズ VM は Intel ハイパースレッディング テクノロジを搭載しています。
