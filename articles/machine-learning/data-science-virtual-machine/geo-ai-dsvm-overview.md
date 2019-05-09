---
title: Geo 人工知能の Data Science Virtual Machine の概要
titleSuffix: Azure
description: Geo 人工知能の Data Science Virtual Machine は、地理的なデータを操作するための ArcGIS Pro を提供しています。 また、機械学習と人工知能を操作するための Python、R、およびデータ サイエンスのツールキットも提供しています。
keywords: ディープ ラーニング、AI、データ サイエンス ツール、データ サイエンス仮想マシン、地理空間分析
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 6588690629d2e266469a4b8ea533c5650f3fc0ea
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890884"
---
# <a name="introduction-to-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Geo 人工知能データ サイエンス仮想マシンの概要

Azure **Geo AI データ サイエンス VM** (Geo-DSVM) は、Microsoft のデータ サイエンス VM から地理空間分析機能を提供するものです。 具体的に言うと、この VM は市場をリードする ESRI の [ArcGIS Pro](https://www.esri.com/arcgis/products/arcgis-pro/overview)地理情報システムを追加することで、[データ サイエンス VM](overview.md) 内の AI およびデータ サイエンス ツールキットを拡張します。

Geo AI データ サイエンス VM は、ESRI と Microsoft によって共同開発された製品です。 この強力な環境を使用することで、次のことが可能になります。

- 最も人気のある AI およびデータ サイエンス ツールを使用してデータを探索できます
- 地理的または空間的な側面を踏まえた統計分析をデータに適用できます
- 機械学習およびディープ ラーニング アルゴリズムを使用して、予測分析用のモデルをトレーニングできます
- 高度な視覚化機能を作成できます

この製品は、Windows 2016 データ サイエンス仮想マシンでサポートされます。 Geo-DSVM では、ArcGIS に対する Python および R インターフェイスも事前構成されているので、ご使用の AI アプリケーション内からプログラムで地理空間分析機能にアクセスできます。 Jupyter Notebook のサンプルも用意されているので、地理空間インテリジェンスを取り入れた AI アプリケーションの構築をスムーズに開始できます。


## <a name="why-geo-ai-data-science-vm"></a>Geo AI データ サイエンス VM を使用するメリット 

データに地理情報や所在地情報を統合すれば、普及が進んでいる IOT デバイスの世界を理解するための、強力で新しいディメンションを得ることができます。 現代の社会では、商業組織、政府機関、教育機関、非営利組織のいずれを問わず、あらゆる組織でさまざまなアプリケーションが使用されています。 地理空間分析機能は、地理的データや位置ベースのデータを収集、管理、分析するための堅牢なツールとなるだけでなく、それらのデータの重要性について独自のインサイトを得るための強力な視覚化ツールとなります。 

Microsoft の[データ サイエンス仮想マシン (DSVM)](overview.md) は、Azure で広く使用されている実験およびモデリング環境であり、AI、機械学習、およびデータ サイエンス ツールのホストとして機能します。 これらのプラットフォームやツールはすべて事前構成されており、すぐに使用することができます。 

データ サイエンス VM の予測機能に、Azure Cloud のスケーラビリティや [ESRI](https://www.esri.com) の地理空間分析ソフトウェアを組み合わせれば、データ サイエンティストや地理的空間アナリストがデータから新たなインテリジェンスを取得し、新たな機会を特定するための、生産性に優れたプラットフォームを提供できます。 


## <a name="next-steps"></a>次の手順

Geo AI データ サイエンス VM をプロビジョニングし、次のトピックのガイダンスに従って使用を開始しましょう。

* [Geo AI データ サイエンス VM のプロビジョニング](provision-geo-ai-dsvm.md)
* [Geo AI データ サイエンス VM の使用](use-geo-ai-dsvm.md)
