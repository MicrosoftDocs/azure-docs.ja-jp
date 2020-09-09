---
title: HB シリーズ VM の概要 - Azure Virtual Machines | Microsoft Docs
description: Azure での HB シリーズ VM サイズのプレビューサポートについて説明します。
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
ms.openlocfilehash: 7c66af5184c4a943fd4b3185a87623112fe0d954
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691243"
---
# <a name="hb-series-virtual-machines-overview"></a>HB シリーズ仮想マシンの概要

AMD EPYC でハイパフォーマンス コンピューティング (HPC) アプリケーションのパフォーマンスを最大化するには、綿密なアプローチによるメモリの局所性とプロセスの配置が必要です。 以下、AMD EPYC アーキテクチャと、Azure での HPC アプリケーション向けのその実装について概説します。 物理 NUMA ドメインを指して "pNUMA" という用語を、また仮想化 NUMA ドメインを指して "vNUMA" を使用します。

物理的には、[HB シリーズ](../../hb-series.md) サーバーは 2 * 32 コアの EPYC 7551 CPU であり、物理コアは合計 64 個です。 これら 64 個のコアは、それぞれ 4 コアであり "CPU コンプレックス" (または "CCX") と呼ばれる 16 の pNUMA ドメイン (ソケットあたり 8 つ) に分割されます。 各 CCX には独自の L 3 キャッシュがあり、OS はこれによって pNUMA/vNUMA 境界を認識します。 隣接する CCX のペアは、2 チャネルの物理 DRAM (HB シリーズ サーバーでは 32 GB の DRAM) へのアクセスを共有します。

Azure ハイパーバイザーが VM に干渉せずに動作する余地を提供するために、物理 pNUMA ドメイン 0 (最初の CCX) を予約します。 次に、pNUMA ドメイン 1 ～ 15 (残りの CCX ユニット) を VM に割り当てます。 VM は次のものを認識します。

VM あたり `(15 vNUMA domains) * (4 cores/vNUMA) = 60` コア

VM 自体は、pNUMA 0 がそれに与えられなかったことを知りません。 VM は pNUMA 1 ～ 15 を vNUMA 0 ～ 14 と解釈し、vSocket 0 では 7 vNUMA、vSocket 1 では 8 vNUMA です。 これは非対称ですが、OS は正常に起動し、動作するはずです。 このガイドの後半では、この非対称の NUMA レイアウトで MPI アプリケーションを最適に実行する方法を説明します。

基になるシリコンはそのままゲスト VM に公開されるため、プロセス固定は HB シリーズ VM で機能します。 最適なパフォーマンスと一貫性のために、プロセス固定を強くお勧めします。

次の図は、Azure Hypervisor と HB シリーズ VM 用に予約されているコアの分離を示しています。

![Azure Hypervisor と HB シリーズ VM 用に予約されているコアの分離](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>ハードウェア仕様

| ハードウェア仕様                | HB シリーズ VM                     |
|----------------------------------|----------------------------------|
| コア                            | 60 (SMT 無効)                |
| CPU                              | AMD EPYC 7551                    |
| CPU 周波数 (非 AVX)          | 最大 2.55 GHz (シングル + 全コア)   |
| メモリ                           | 4 GB/コア (合計 240 GB)         |
| ローカル ディスク                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 |
| ネットワーク                          | 50 GB イーサネット (40 GB 使用可能) Azure 第 2 世代 SmartNIC |

## <a name="software-specifications"></a>ソフトウェア仕様

| ソフトウェア仕様           |HB シリーズ VM           |
|-----------------------------|-----------------------|
| 最大 MPI ジョブ サイズ            | 18000 コア (1 つの仮想マシン スケール セットに 300 台の VM、singlePlacementGroup=true)  |
| MPI のサポート                 | HPC-X、Intel MPI、OpenMPI、MVAPICH2、MPICH、Platform MPI  |
| その他のフレームワーク       | Unified Communication X、libfabric、PGAS |
| Azure Storage のサポート       | Standard および Premium ディスク (最大 4 ディスク) |
| SRIOV RDMA の OS サポート   | CentOS/RHEL 7.6 以降、SLES 12 SP4 以降、WinServer 2016 以降  |
| Orchestrator のサポート        | CycleCloud、Batch  |

## <a name="next-steps"></a>次のステップ

- [AMD EPYC アーキテクチャ](https://bit.ly/2Epv3kC)および[マルチチップ アーキテクチャ](https://bit.ly/2GpQIMb)の詳細を確認します。 詳細については、[AMD EPYC プロセッサ向けの HPC チューニング ガイド](https://bit.ly/2T3AWZ9)を参照してください。
- [Azure Compute Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)で、最新の発表および HPC の例と結果について参照します。
- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](/azure/architecture/topics/high-performance-computing/)」をご覧ください。
