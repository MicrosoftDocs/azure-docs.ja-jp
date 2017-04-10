---
title: "Azure での Linux VM のサイズ | Microsoft Docs"
description: "Azure の Linux 仮想マシンで使用できるさまざまなサイズを一覧表示します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 446f4257979e6725186b32ad65cef31cdd3e7ede
ms.lasthandoff: 03/24/2017

---

# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure の Linux 仮想マシンのサイズ
この記事では、Linux アプリとワークロードの実行に使用できる Azure 仮想マシンに利用可能なサイズとオプションについて説明します。 また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。 この記事は、 [Windows 仮想マシン](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にも利用できます。

> [!IMPORTANT]
> * さまざまなサイズの価格について詳しくは、「 [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)」を参照してください。 
> * 各 Azure リージョンで利用可能な VM サイズについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。
> * Azure VM の一般的な制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。
> * [Azure コンピューティング ユニット (ACU)](virtual-machines-linux-acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
> 
> 

<br>   


| 型                     | サイズ           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](virtual-machines-linux-sizes-general.md)          | DSv2、Dv2、DS、D、Av2、A0 ～ 7  | バランスのとれた CPU 対メモリ比。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 |
| [コンピューティングの最適化](virtual-machines-linux-sizes-compute.md)        | Fs、F             | 高い CPU 対メモリ比。 トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。        |
| [メモリの最適化](virtual-machines-linux-sizes-memory.md)         | GS、G、DSv2、DS   | 高いメモリ対コア比。 リレーショナル データベース サーバー、中～大規模のキャッシュ、およびメモリ内分析に適しています。                 |
| [ストレージの最適化](virtual-machines-linux-sizes-storage.md)        | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](virtual-machines-linux-sizes-gpu.md)            | NV、NC            | 負荷の高いグラフィックスのレンダリングやビデオ編集に特化した仮想マシン。 1 つまたは複数の GPU で利用できます。       |
| [ハイ パフォーマンス コンピューティング](virtual-machines-linux-sizes-hpc.md) | H、A8 ～ 11          | 高スループットのネットワーク インターフェイス (RDMA) のオプションを備えた、最も高速かつ強力な CPU 仮想マシン。 

<br>

[Azure コンピューティング ユニット (ACU)](virtual-machines-linux-acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

利用可能な VM のサイズの種類について詳しく説明します。
- [汎用](virtual-machines-linux-sizes-general.md)
- [コンピューティングの最適化](virtual-machines-linux-sizes-compute.md)
- [メモリの最適化](virtual-machines-linux-sizes-memory.md)
- [ストレージの最適化](virtual-machines-linux-sizes-storage.md)
- [GPU](virtual-machines-linux-sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](virtual-machines-linux-sizes-hpc.md)




