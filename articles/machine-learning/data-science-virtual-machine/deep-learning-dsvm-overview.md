---
title: ディープ ラーニング仮想マシンの概要 - Azure | Microsoft Docs
description: ディープ ラーニング仮想マシンの主要な分析シナリオおよびコンポーネント。
keywords: ディープ ラーニング、AI、データ サイエンス ツール、データ サイエンス仮想マシン、データ サイエンス用ツール、Linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 879f5939f110fb841ad160bf09f597edcdd86d31
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452840"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>ディープ ラーニング仮想マシンの概要

## <a name="why-deep-learning-virtual-machine"></a>ディープ ラーニング仮想マシンを採用する理由 

ディープ ラーニング アルゴリズム/ディープ ニューラル ネットワークは、ますます、機械学習の多くの問題で採用される一般的な方法の 1 つになっています。 これらは、イメージ、テキスト、オーディオ/ビデオの理解などの機械認識タスクに特に適しており、多くの場合、高度なディープ ニューラル ネットワーク アーキテクチャや、モデルをトレーニングするための大規模なデータセットへのアクセスなどのいくつかの特定の領域で人間の認識レベルに近づいています。 ディープ ラーニングには、これらの大規模なデータセットを含むモデルをトレーニングするための大量の計算能力が必要です。 クラウドや、グラフィック処理ユニット (GPU) の可用性により、高度なディープ ニューラル アーキテクチャを構築したり、それらをクラウド上の強力なコンピューティング インフラストラクチャの大規模なデータセットでトレーニングしたりすることが可能になっています。  [データ サイエンス仮想マシン](overview.md)は、データ準備、機械学習、およびディープ ラーニングのための豊富なツールセットやサンプルを提供してきました。 しかし、ユーザーが直面する課題の 1 つとして、ディープ ラーニングなどの特定のシナリオのためのツールやサンプルの容易な検出、および GPU ベースの VM インスタンスのより容易なプロビジョニングがあります。 このディープ ラーニング仮想マシン (DLVM) は、これらの課題に対応します。 

## <a name="what-is-deep-learning-virtual-machine"></a>ディープ ラーニング仮想マシンとは 
ディープ ラーニング仮想マシンは、ディープ ラーニング モデルをトレーニングするための GPU ベースの VM インスタンスの使用をより簡単にするために、[データ サイエンス仮想マシン](overview.md) (DSVM) を特別に構成したものです。 これは、Windows 2016 および Ubuntu データ サイエンス仮想マシンでサポートされています。  DSVM と同じコア VM イメージ (したがって、豊富なツールセットのすべて) を共有していますが、ディープ ラーニングをより容易にするように構成されています。 当社はまた、実世界の多くの AI シナリオに幅広く適用できる、イメージおよびテキスト理解のためのエンド ツー エンドのサンプルも提供しています。 ディープ ラーニング仮想マシンはまた、仮想マシン上のツールやサンプルのカタログを明らかにすることによって、DSVM 上の豊富なツールセットやサンプルをより容易に検出できるようにします。 ツールの点から見ると、ディープ ラーニング仮想マシンは、いくつかの一般的なディープ ラーニング フレームワーク、イメージおよびテキスト データを取得して前処理するためのツールを提供します。 ツールの包括的な一覧については、[データ サイエンス仮想マシンの概要ページ](overview.md#whats-included-in-the-data-science-vm)を参照できます。 

## <a name="next-steps"></a>次の手順

次の手順でディープ ラーニング仮想マシンの使用を開始します。

* [ディープ ラーニング仮想マシンのプロビジョニング](provision-deep-learning-dsvm.md)
* [ディープ ラーニング仮想マシンの使用](use-deep-learning-dsvm.md)
* [ツール リファレンス](dsvm-deep-learning-ai-frameworks.md)
* [サンプル](dsvm-samples-and-walkthroughs.md)
