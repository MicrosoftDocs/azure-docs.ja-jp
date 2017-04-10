
---
title: "Azure での Windows VM のサイズ | Microsoft Docs"
description: "Azure の Windows 仮想マシンで使用できるさまざまなサイズを一覧表示します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 365d6ad9ec0e0a7ad8d9742d863540646257e298
ms.lasthandoff: 03/27/2017

---

# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Azure の Windows 仮想マシンのサイズ

この記事では、Windows アプリとワークロードの実行に使用できる Azure 仮想マシンに利用可能なサイズとオプションについて説明します。 また、これらのリソースの使用を計画するときに注意する必要のあるデプロイの考慮事項も示します。  この記事は、 [Linux 仮想マシン](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にも利用できます。

> [!IMPORTANT]
>* さまざまなサイズの価格について詳しくは、「 [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)」を参照してください。 
>* Azure VM の一般的な制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。
>* Storage のコストは、ストレージ アカウントで使用されるページに基づいて個別に計算されます。 詳細については、「[Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/)」をご覧ください。
> * [Azure コンピューティング ユニット (ACU)](virtual-machines-windows-acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
>
>
<br>    




| 型                     | サイズ           |    説明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [汎用](virtual-machines-windows-sizes-general.md)          | DSv2、Dv2、DS、D、Av2、A0 ～ 7 | バランスのとれた CPU 対メモリ比。 テストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに最適です。 |
| [コンピューティングの最適化](virtual-machines-windows-sizes-compute.md)        | Fs、F             | 高い CPU 対メモリ比。 トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリケーション サーバーに適しています。        |
| [メモリの最適化](virtual-machines-windows-sizes-memory.md)         | GS、G、DSv2、DS   | 高いメモリ対コア比。 リレーショナル データベース サーバー、中～大規模のキャッシュ、およびメモリ内分析に適しています。                 |
| [ストレージの最適化](virtual-machines-windows-sizes-storage.md)        | Ls                | 高いディスク スループットと IO。 ビッグ データ、SQL、および NoSQL のデータベースに最適です。                                                         |
| [GPU](virtual-machines-windows-sizes-gpu.md)            | NV、NC            | 負荷の高いグラフィックスのレンダリングやビデオ編集に特化した仮想マシン。 1 つまたは複数の GPU で利用できます。       |
| [ハイ パフォーマンス コンピューティング](virtual-machines-windows-sizes-hpc.md) | H、A8 ～ 11          | 高スループットのネットワーク インターフェイス (RDMA) のオプションを備えた、最も高速かつ強力な CPU 仮想マシン。 

<br>

[Azure コンピューティング ユニット (ACU)](virtual-machines-windows-acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

利用可能な VM のサイズの種類について詳しく説明します。
- [汎用](virtual-machines-windows-sizes-general.md)
- [コンピューティングの最適化](virtual-machines-windows-sizes-compute.md)
- [メモリの最適化](virtual-machines-windows-sizes-memory.md)
- [ストレージの最適化](virtual-machines-windows-sizes-storage.md)
- [GPU の最適化](virtual-machines-windows-sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](virtual-machines-windows-sizes-hpc.md)




