---
title: HC シリーズ VM の概要 - Azure Virtual Machines | Microsoft Docs
description: Azure での HC シリーズ VM サイズのプレビューサポートについて説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: de6051e8880bbe3df42031a0d0d7b60abc27d2b0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689801"
---
# <a name="hc-series-virtual-machine-overview"></a>HC シリーズの仮想マシンの概要

Intel Xeon スケーラブル プロセッサで HPC アプリケーションのパフォーマンスを最大限に引き出すには、この新しいアーキテクチャにプロセスを配置する慎重なアプローチが必要です。 ここでは、HPC アプリケーション用の Azure HC シリーズ VM への実装の概要を説明します。 物理 NUMA ドメインを指して "pNUMA" という用語を、また仮想化 NUMA ドメインを指して "vNUMA" を使用します。 同様に、CPU コアを指して "pCore" という用語を、仮想化 CPU コアを指して "仮想コア" という用語を使用します。

物理的には、[HC シリーズ](../../hc-series.md)のサーバーは 2 * 24 コアの Intel Xeon Platinum 8168 CPU で合計 48 個の物理コアです。 各 CPU は 1 つの pNUMA ドメインであり、6 チャネルの DRAM に統一されたアクセス権を持っています。 Intel Xeon Platinum CPU は、以前の世代よりも 4 倍大きい L2 キャッシュ (256 KB/コア -> 1 MB/コア) を備えていると同時に、以前の Intel CPU (2.5 MB/コア -> 1.375 MB/コア) と比較して L3 キャッシュも削減します。

上記のトポロジは、HC シリーズのハイパーバイザー構成にも引き継がれます。 Azure ハイパーバイザーが VM に干渉せずに動作する余地を確保するために、pCore 0-1 および 24-25 (つまり、各ソケットの最初の 2 つの pCore) を予約します。 次に、pNUMA ドメインの残りのすべてのコアを VM に割り当てます。 そのため、VM では以下が認識されます。

VM あたり `(2 vNUMA domains) * (22 cores/vNUMA) = 44` コア

VM は、pCore 0-1 と 24-25 が与えられていないことを認識していません。 そのため、ネイティブに 22 個のコアがあるかのように各 vNUMA が公開されています。

Intel Xeon Platinum、Gold、および Silver の CPU は、CPU ソケット内外と通信するためのオンダイ 2D メッシュ ネットワークも導入しています。 最適なパフォーマンスと一貫性のために、プロセス固定を強くお勧めします。 基になるシリコンはそのままゲスト VM に公開されるため、プロセス固定は HC シリーズ VM で機能します。

次の図は、Azure Hypervisor と HC シリーズ VM 用に予約されているコアの分離を示しています。

![Azure Hypervisor と HC シリーズ VM 用に予約されているコアの分離](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>ハードウェア仕様

| ハードウェア仕様          | HC シリーズ VM                     |
|----------------------------------|----------------------------------|
| コア                            | 44 (HT 無効)                 |
| CPU                              | Intel Xeon Platinum 8168         |
| CPU 周波数 (非 AVX)          | 3.7 GHz (シングル コア)、2.7-3.4 GHz (すべてのコア) |
| メモリ                           | 8 GB/コア (合計 352)            |
| ローカル ディスク                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5   |
| ネットワーク                          | 50 GB イーサネット (40 GB 使用可能) Azure 第 2 世代 SmartNIC    |

## <a name="software-specifications"></a>ソフトウェア仕様

| ソフトウェア仕様     |HC シリーズ VM           |
|-----------------------------|-----------------------|
| 最大 MPI ジョブ サイズ            | 13200 コア (1 つの仮想マシン スケール セットに 300 台の VM、singlePlacementGroup=true)  |
| MPI のサポート                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| その他のフレームワーク       | Unified Communication X、libfabric、PGAS |
| Azure Storage のサポート       | Standard および Premium ディスク (最大 4 ディスク) |
| SRIOV RDMA の OS サポート   | CentOS/RHEL 7.6 以降、SLES 12 SP4 以降、WinServer 2016 以降  |
| Orchestrator のサポート        | CycleCloud、Batch  |

## <a name="next-steps"></a>次のステップ

- [Intel Xeon SP アーキテクチャ](https://bit.ly/2RCYkiE)の詳細を確認します。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
