---
title: Azure VM のサイズ - ストレージ | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまなストレージ最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d97fe6cda1134d45468e257965fd5e28fe170e6f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561026"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>ストレージ最適化済み仮想マシンのサイズ

ストレージ最適化済み VM サイズは高いディスク スループットと IO を実現し、ビッグ データ、SQL、NoSQL データベース、データ ウェアハウス、および大規模なトランザクション データベースに最適です。  例としては、Cassandra、MongoDB、Cloudera、および Redis があります。 この記事では、最適化された各サイズのローカル ストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を提供します。

[Lsv2 シリーズ](lsv2-series.md)は、2.55 GHz の全コア ブーストと 3.0 GHz の最大ブーストが可能な [AMD EPYC<sup>TM</sup> 7551 プロセッサ](https://www.amd.com/en/products/epyc-7000-series)上で実行され、高スループット、低待ち時間、直接マッピングされたローカル NVMe ストレージを特長としています。 Lsv2 シリーズの VM には、同時マルチスレッド構成で 8 から 80 vCPU のサイズが用意されています。  vCPU あたり 8 GiB のメモリ、8 vCPU あたり 1 つの 1.92 TB NVMe SSD M.2 デバイスが用意され、L80s v2 では最大 19.2 TB (10 x 1.92 TB) を使用できます。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

[Windows](windows/storage-performance.md) または [Linux](linux/storage-performance.md) 用の Lsv2 シリーズの仮想マシンでパフォーマンスを最適化する方法について説明します。

Azure での VM の名前付け方法の詳細については、「[Azure 仮想マシンのサイズの名前付け規則](./vm-naming-conventions.md)」を参照してください。