---
title: HBv3 シリーズの VM サイズのパフォーマンス
description: Azure での HBv3 シリーズの VM サイズのパフォーマンス テスト結果について説明します。
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 87c3e4e9b509589624a228ea2e1f4b68e86e3fa8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721146"
---
# <a name="hbv3-series-virtual-machine-performance"></a>HBv3 シリーズの仮想マシンのパフォーマンス

HBv3 VM の早期アクセス ユーザーが、一般的な HPC マイクロベンチマークで次のパフォーマンス値を期待できます。

| ワークロード                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 GB/秒 (NUMA あたり 最大 82-86 GB/秒)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak、FP64)、120 コア VM サイズで 8 TF (Rpeak、FP32)               |
| RDMA の待機時間と帯域幅                        | 1.2 マイクロ秒 (1 バイト)、192 GB/秒 (一方向)                                        |
| ローカル NVMe SSD の FIO (RAID0)                  | 7 GB/秒の読み取り、3 GB/秒の書き込み。186k IOPS 読み取り、201ｋ IOPS 書き込み |

## <a name="process-pinning"></a>プロセス固定

プロセス固定が HBv3 シリーズ VM 上でうまく機能する理由は、基になるシリコンをそのままゲスト VM に公開しているからです。 最適なパフォーマンスと一貫性のために、プロセス固定を強くお勧めします。

## <a name="mpi-latency"></a>MPI 待機時間

OSU マイクロベンチマーク スイートの MPI 待機時間テストは、次のように実行できます。 サンプル スクリプトは [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) にあります。

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI 帯域幅
OSU マイクロベンチマーク スイートの MPI 帯域幅テストは、次のように実行できます。 サンプル スクリプトは [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) にあります。
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
[Mellanox Perftest パッケージ](https://community.mellanox.com/s/article/perftest-package)には、待機時間 (ib_send_lat) や帯域幅 (ib_send_bw) などの多くの InfiniBand テストがあります。 コマンドの例を次に示します。 
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>次の手順
- [MPI アプリケーションのスケーリング](compiling-scaling-applications.md)について学習する。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表、HPC ワークロードの例、およびパフォーマンスの結果について参照します。
- アーキテクチャの面から見た HPC ワークロードの実行の概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」を参照してください。