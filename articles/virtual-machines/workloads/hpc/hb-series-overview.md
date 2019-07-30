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
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707776"
---
# <a name="hb-series-virtual-machines-overview"></a>HB シリーズ仮想マシンの概要

AMD EPYC でハイパフォーマンス コンピューティング (HPC) アプリケーションのパフォーマンスを最大化するには、綿密なアプローチによるメモリの局所性とプロセスの配置が必要です。 以下、AMD EPYC アーキテクチャと、Azure での HPC アプリケーション向けのその実装について概説します。 物理 NUMA ドメインを指して "pNUMA" という用語を、また仮想化 NUMA ドメインを指して "vNUMA" を使用します。

物理的には、HB シリーズは 2 * 32 コアの EPYC 7551 CPU であり、物理コアは合計 64 個です。 これら 64 個のコアは、それぞれ 4 コアであり "CPU コンプレックス" (または "CCX") と呼ばれる 16 の pNUMA ドメイン (ソケットあたり 8 つ) に分割されます。 各 CCX には独自の L 3 キャッシュがあり、OS はこれによって pNUMA/vNUMA 境界を認識します。 隣接する CCX のペアは、2 チャネルの物理 DRAM (HB シリーズ サーバーでは 32 GB の DRAM) へのアクセスを共有します。

Azure ハイパーバイザーが VM に干渉せずに動作する余地を提供するために、物理 pNUMA ドメイン 0 (最初の CCX) を予約します。 次に、pNUMA ドメイン 1 ～ 15 (残りの CCX ユニット) を VM に割り当てます。 VM は次のものを認識します。

VM あたり `(15 vNUMA domains) * (4 cores/vNUMA) = 60` コア

VM 自体は、pNUMA 0 がそれに与えられなかったことを知りません。 VM は pNUMA 1 ～ 15 を vNUMA 0 ～ 14 と解釈し、vSocket 0 では 7 vNUMA、vSocket 1 では 8 vNUMA です。 これは非対称ですが、OS は正常に起動し、動作するはずです。 このガイドの後半では、この非対称の NUMA レイアウトで MPI アプリケーションを最適に実行する方法を説明します。

基になるシリコンはそのままゲスト VM に公開されるため、プロセス固定は HB シリーズ VM で機能します。 最適なパフォーマンスと一貫性のために、プロセス固定を強くお勧めします。

詳細については、LinkedIn の [AMD EPYC アーキテクチャ](https://bit.ly/2Epv3kC)および[マルチチップ アーキテクチャ](https://bit.ly/2GpQIMb)に関するページを参照してください。 詳細については、[AMD EPYC プロセッサ向けの HPC チューニング ガイド](https://bit.ly/2T3AWZ9)を参照してください。

次の図は、Azure Hypervisor と HB シリーズ VM 用に予約されているコアの分離を示しています。

![Azure Hypervisor と HB シリーズ VM 用に予約されているコアの分離](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>ハードウェア仕様

| HW 仕様                | HB シリーズ VM                     |
|----------------------------------|----------------------------------|
| コア                            | 60 (SMT 無効)                |
| CPU                              | AMD EPYC 7551*                   |
| CPU 周波数 (非 AVX)          | 最大 2.55 GHz (シングル + 全コア)   |
| メモリ                           | 4 GB/コア (合計 240)            |
| ローカル ディスク                       | 700 GB NVMe                      |
| Infiniband                       | 100 GB EDR Mellanox ConnectX-5** |
| ネットワーク                          | 50 GB イーサネット (40 GB 使用可能) Azure 第 2 世代 SmartNIC*** |

## <a name="software-specifications"></a>ソフトウェア仕様

| SW 仕様           |HB シリーズ VM           |
|-----------------------------|-----------------------|
| 最大 MPI ジョブ サイズ            | 6000 コア (100 仮想マシン スケール セット) 12000 コア (200 仮想マシン スケール セット)  |
| MPI のサポート                 | MVAPICH2、OpenMPI、MPICH、Platform MPI、Intel MPI  |
| その他のフレームワーク       | Unified Communication X、libfabric、PGAS |
| Azure Storage のサポート       | Standard + Premium (最大 4 ディスク) |
| SRIOV RDMA の OS サポート   | CentOS/RHEL 7.6 以降、SLES 12 SP4 以降、WinServer 2016 以降  |
| Azure CycleCloud のサポート    | はい                         |
| Azure Batch のサポート         | はい                         |

## <a name="next-steps"></a>次の手順

* Azure での [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) および [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) の HPC VM サイズの詳細を確認する。

* Azure での [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) の詳細を確認する。
