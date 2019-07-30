---
title: H シリーズ VM でのハイ パフォーマンス コンピューティング - Azure Virtual Machines | Microsoft Docs
description: HPC 用に最適化された H シリーズ VM の機能について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799007"
---
# <a name="high-performance-computing-on-h-series-vms"></a>H シリーズ VM でのハイ パフォーマンス コンピューティング

HB シリーズおよび HC シリーズの VM のハイ パフォーマンス コンピューティング (HPC) では、Azure 上のすべての VM の最も最適化された HPC パフォーマンスが実現されます。 HPC に最適化された VM は、流体力学、石油とガスのシミュレーション、気象モデリングなど、いくつかの最も困難な数学的問題を解決するために使用されます。

この記事では、HB シリーズと HC シリーズの VM の主要な機能、HPC のシナリオでこれらの VM のパフォーマンスが優れている理由、使い始める方法について説明します。

## <a name="features-and-capabilities"></a>特徴と機能

HB シリーズと HC シリーズの VM は、最高の HPC パフォーマンス、Message Passing Interface (MPI) のスケーラビリティ、HPC ワークロードのコスト効率を提供するように設計されています。

### <a name="message-passing-interface"></a>Message Passing Interface

HB シリーズと HC シリーズでは、ほぼすべての MPI の種類とバージョンがサポートされています。 サポートされている MPI の種類の中でも最も一般的なものを次に示します。OpenMPI、MVAPICH2、Platform MPI、Intel MPI、およびすべてのリモート ダイレクト メモリ アクセス (RDMA) の動詞。 詳しくは、「[HPC の Message Passing Interface を設定する](setup-mpi.md)」をご覧ください。

### <a name="rdma-and-infiniband"></a>RDMA と InfiniBand

RDMA インターフェイスは、HB シリーズと HC シリーズの VM で標準になっています。 RDMA 対応のインスタンスでは、HB シリーズと HC シリーズの仮想マシン用に強化されたデータ レート (EDR) で動作する InfiniBand ネットワークを介して、通信が行われます。 RDMA 対応のインスタンスでは、一部の MPI アプリケーションのスケーラビリティとパフォーマンスを向上させることができます。

HB シリーズと HC シリーズの VM をサポートする InfiniBand 構成は、一貫した RDMA パフォーマンス用の低直径設計による、非ブロッキングのファット ツリーです。

HB シリーズまたは HC シリーズの VM での InfiniBand の設定について詳しくは、[InfiniBand の有効化](enable-infiniband.md)に関する記事をご覧ください。

## <a name="get-started"></a>作業開始

まず、どの H シリーズの VM を使うかを決定します。 HPC 用に最適化された VM について詳しくは、[HB シリーズの概要](hb-series-overview.md)および [HC シリーズの概要](hc-series-overview.md)に関する記事をご覧ください。 仕様については、「[ハイ パフォーマンス コンピューティング仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)」をご覧ください。

アプリケーション用の VM を選択して作成した後は、InfiniBand を有効にして構成する必要があります。 Windows VM と Linux VM の両方で InfiniBand を有効にする方法については、[InfiniBand の有効化](enable-infiniband.md)に関する記事をご覧ください。

HPC ワークロードの重要なコンポーネントは、MPI です。 HB シリーズと HC シリーズでは、ほぼすべての MPI の種類とバージョンがサポートされています。 詳しくは、「[HPC の Message Passing Interface を設定する](setup-mpi.md)」をご覧ください。

VM シリーズを選択し、InfiniBand と MPI を設定した後は、HPC ワークロードの構築を始めることができます。

## <a name="next-steps"></a>次の手順

- [HB シリーズの概要](hb-series-overview.md)と [HC シリーズの概要](hc-series-overview.md)を確認し、主な相違点と仕様について学習します。

- HPC ワークロードの実行をアーキテクチャの面から見た概要については、「[Azure でのハイ パフォーマンス コンピューティング (HPC)](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)」をご覧ください。
