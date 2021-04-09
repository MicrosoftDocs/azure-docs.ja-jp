---
title: Azure の VM のサイズ - 汎用 | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまな汎用サイズを一覧表示します。 このシリーズの各サイズにおけるストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 46a9b7e630001161fbf2790cfb9be474379864b6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557626"
---
# <a name="general-purpose-virtual-machine-sizes"></a>汎用仮想マシンのサイズ

汎用 VM サイズは、CPU とメモリのバランスの取れた比率を提供します。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 この記事では、汎用コンピューティングのためのサービスに関する情報を提供します。

- [Av2 シリーズ](av2-series.md) VM は多様なハードウェアの種類とプロセッサにデプロイできます。 A シリーズの VM は、開発とテストのような、エントリ レベルのワークロードに最適な CPU のパフォーマンスとメモリ構成を備えています。 デプロイされるハードウェアに関係なく、実行中のインスタンスに対して一貫したプロセッサ パフォーマンスを提供するため、ハードウェアに基づいてサイズが調整されます。 このサイズがデプロイされる物理ハードウェアを判断するには、仮想マシン内から仮想ハードウェアをクエリします。 ユース ケースの例には、開発とテスト用のサーバー、低トラフィックの Web サーバー、小規模から中規模のデータベース、概念実証、コード リポジトリがあります。

  > [!NOTE]
  > A8、A9、A10、A11 VM は、2021 年 3 月に廃止される予定です。 詳細については、「[HPC マイグレーション ガイド](https://azure.microsoft.com/resources/hpc-migration-guide/)」を参照してください。 これらの VM サイズは "v2" ではなく、元の "A_v1" シリーズにあります。

- Web サーバー、小規模なデータベース、開発とテスト環境など、CPU が常時最大限のパフォーマンスを発揮している必要のないワークロードでは、[B シリーズのバースト可能な](sizes-b-series-burstable.md) VM が最適です。 このようなワークロードでは通常、負荷の急増に対応できることがパフォーマンスの要件となります。 B シリーズでは、価格を重視するベースライン パフォーマンスが設定されている VM サイズを購入でき、VM インスタンスの使用量がベース パフォーマンスを下回る場合にはクレジットが蓄積されていきます。 VM にクレジットが蓄積されていると、アプリケーションでより高い CPU パフォーマンスが必要な場合に、VM のベースラインを上回り、CPU を 100% まで使用できます。

- [Dav4 シリーズと Dasv4 シリーズ](dav4-dasv4-series.md)は、マルチスレッド構成で AMD の 2.35 Ghz EPYC<sup>TM</sup> 7452 プロセッサを使用する新しいサイズです。最大で 256 MB の L3 キャッシュを備え、8 コアのそれぞれにその L3 キャッシュの 8 MB が専用に割り当てられ、汎用ワークロードを実行するためのカスタマー オプションが増えます。 Dav4 シリーズと Dasv4 シリーズは、D および Dsv3 シリーズと同じメモリおよびディスク構成を備えています。

- [Dv4 および Dsv4 シリーズ](dv4-dsv4-series.md) Dv4 および Dsv4 シリーズは、ハイパースレッド構成の Intel® Xeon® Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 これは、3.4 GHz の全コア ターボ クロック速度を特徴としています。

- [Ddv4 および Ddsv4 シリーズ](ddv4-ddsv4-series.md) Ddv4 および Ddsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 3\.4 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 また、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) がサポートされています。 これらの新しい VM サイズでは、50% 大きいローカル ストレージが使用されるほか、[Gen2 VM](./generation-2.md) の [Dv3/Dsv3](./dv3-dsv3-series.md) サイズと比べて読み取りと書き込みの両方のローカル ディスク IOPS が向上します。

- [Dv3 および Dsv3 シリーズ](dv3-dsv3-series.md) VM は、第二世代 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのハイパースレッド構成のプロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値を提供します。 メモリが増設 (約 3.5 GiB/vCPU から 4 GiB/vCPU) される一方、ディスクおよびネットワークの制限は、ハイパースレッディングへの移行に合わせてコア単位ベースで調整されています。 Dv3 シリーズには、D および Dv2 シリーズのハイ メモリの VM サイズがなくなりました。これらは、メモリ最適化された [Ev3 および Esv3 シリーズ](ev3-esv3-series.md)に移動されました。

- オリジナルの D シリーズの後継である [Dv2 および Dsv2 シリーズ](dv2-dsv2-series.md) VM は、より強力な CPU と最適な CPU 対メモリ構成を備えているため、ほとんどの運用環境のワークロードに適しています。 Dv2 シリーズは D シリーズよりも、およそ 35% 高速です。 Dv2 シリーズは、Intel Turbo Boost Technology 2.0 を備えた、第二世代 Intel® Xeon® Platinum 8272CL (Cascade Lake)、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのプロセッサ上で実行されています。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

- [DCv2 シリーズ](dcv2-series.md)は、パブリック クラウドで処理中のデータおよびコードの機密性と整合性を保護するために役立ちます。 これらのマシンは、最新世代の Intel XEON E-2288G プロセッサと SGX テクノロジによって支援されています。 Intel Turbo Boost Technology により、これらのマシンは最大 5.0 GHz まで高速化できます。 DCv2 シリーズのインスタンスを使用すると、安全なエンクレーブ ベースのアプリケーションを構築して、コードやデータを使用中に保護することができます。

## <a name="other-sizes"></a>その他のサイズ

- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

Azure で、その VM の名前付けが行われる方法の詳細については、「[Azure 仮想マシンのサイズの名前付け規則](./vm-naming-conventions.md)」を参照してください。
