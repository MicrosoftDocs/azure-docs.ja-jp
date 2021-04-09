---
title: フィールド プログラマブル ゲート アレイ (FPGA) のための Azure 仮想マシン サイズ
description: Azure の仮想マシンで使用できるさまざまな FPGA 最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557677"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA 最適化済み仮想マシンのサイズ

FPGA 最適化済み VM サイズは、1 つまたは複数の FPGA を備えた、特殊な用途に特化した仮想マシンです。 これらのサイズは、コンピューティング処理の負荷が高いワークロードを意図して設計されています。 この記事では、FPGA、vCPU、データ ディスク、NIC の数と種類についての情報を提供します。 このグループ内の各サイズのストレージのスループットおよびネットワーク帯域幅も含まれています。

- [NP シリーズ](np-series.md)のサイズは、機械学習推論、ビデオ コード変換、データベース検索と分析などのワークロードに最適化されています。 NP シリーズは Xilinx U250 アクセラレータを利用しています。


## <a name="deployment-considerations"></a>デプロイに関する考慮事項

- N シリーズ VM を利用できるリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。

- N シリーズ VM をデプロイするには、Resource Manager デプロイ モデルを使用する必要があります。

- 多数の N シリーズ VM をデプロイする場合は、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

- Azure サブスクリプションの (リージョンごとの) コア クォータと NP のコアの個別のクォータを増やす必要が生じる場合があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../azure-portal/supportability/how-to-create-azure-support-request.md) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [GPU 高速コンピューティング](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
