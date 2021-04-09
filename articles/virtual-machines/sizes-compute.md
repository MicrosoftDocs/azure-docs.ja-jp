---
title: Azure VM のサイズ - コンピューティング最適化済み |Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまなコンピューティング最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557694"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>コンピューティング最適化済み仮想マシンのサイズ

コンピューティング最適化 VM サイズは高い CPU 対メモリ比を備えています。 これらのサイズは、中程度のトラフィックの Web サーバー、ネットワーク アプライアンス、バッチ プロセス、およびアプリケーション サーバーに適しています。 この記事では、vCPU、データ ディスク、および NIC の数に関する情報を提供します。 また、このグループ内のサイズごとのストレージ スループットとネットワーク帯域幅に関する情報も含まれています。

[Fsv2 シリーズ](fsv2-series.md) は、第 2 世代 Intel® Xeon® Platinum 8272CL (Cascade Lake) プロセッサと Intel® Xeon® Platinum 8168 (Skylake) プロセッサ上で実行されます。 これは、持続する 3.4 GHz の全コア ターボ クロック速度と 3.7 GHz の最大シングルコア ターボ周波数を備えています。 Intel® AVX-512 命令は、Intel スケーラブル プロセッサでの新機能です。 これらの命令は、単精度浮動小数点演算と倍精度浮動小数点演算の両方でベクトル処理ワークロードに最大 2 倍のパフォーマンス向上を提供します。 つまり、これらは、あらゆるコンピューティング ワークロードで実際に高速です。

Fsv2 シリーズは時間あたりの料金が抑えられており、vCPU あたりの Azure コンピューティング ユニット (ACU) に基づく Azure ポートフォリオにおいて、最もコスト パフォーマンスに優れています。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

Azure で、その VM の名前付けが行われる方法の詳細については、「[Azure 仮想マシンのサイズの名前付け規則](./vm-naming-conventions.md)」を参照してください。