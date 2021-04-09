---
title: オントロジとは
titleSuffix: Azure Digital Twins
description: 特定分野でのモデリングのための DTDL 業界オントロジについて説明します
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034695"
---
# <a name="what-is-an-ontology"></a>オントロジとは 

Azure Digital Twins ソリューションのボキャブラリは、ご自身の環境内に存在するエンティティの種類を示す[モデル](concepts-models.md)を使用して定義されます。

自分のソリューションが特定の業界に関連している場合、独自のモデル セットをゼロから作成するよりも、既に存在するその業界用の一連のモデルから開始する方が簡単で効率的な場合があります。 これらの既存のモデル セットを **オントロジ** と呼びます。 

一般的に、オントロジとは、特定の分野 (建造物、IoT システム、スマート シティ、エネルギー グリッド、Web コンテンツなど) の一連のモデルです。オントロジは、次のことを実現できるため、ナレッジ グラフのスキーマとしてよく使用されます。
* ソフトウェア コンポーネント、ドキュメント、クエリ ライブラリなどの調和
* 概念モデリングとシステム開発への投資の削減
* セマンティック レベルでのデータ相互運用性の向上
* ゼロから開始したり、"車輪の再発明" をしたりせずに、ベスト プラクティスを再利用

この記事では、Azure Digital Twins モデルでオントロジを使用する理由と方法、およびそのために現在利用できるオントロジとツールについて説明します。

## <a name="using-ontologies-for-azure-digital-twins"></a>Azure Digital Twins のオントロジを使用する

オントロジは、デジタル ツイン ソリューションの出発点として最適です。 これらには、デジタル ツイン グラフの設計、作成、解析のための、一連の分野固有のモデルとエンティティ間のリレーションシップが含まれます。 オントロジを使用すると、ソリューション開発者は、実証済みの出発点からデジタル ツイン ソリューションを開始し、ビジネス上の問題の解決に専念できます。 また、Microsoft が提供するオントロジは、簡単に拡張できるように設計されているため、自分のソリューションに合わせてカスタマイズできます。 

さらに、オントロジはソリューション間で共通のボキャブラリを提供できるため、自分のソリューションでこれらのオントロジを使用すると、異なるパートナーおよびベンダー間でのシームレスな統合のためにそれらを設定できます。

Azure Digital Twins のモデルは [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) で表されているため、Azure Digital Twins で使用されるオントロジも DTDL で記述されています。 

## <a name="strategies-for-integrating-ontologies"></a>オントロジを統合するための戦略

DTDL を使用した業界標準のオントロジを統合するための戦略は 3 つ考えられます。 自分のニーズに応じて最適なものを選択できます。

| 戦略 | 説明 | リソース |
| --- | --- | --- |
| **採用** | 広く採用されている業界標準に基づいて構築されたオープンソースの DTDL オントロジーを使用して、ソリューションを開始できます。 これらのモデル セットは、すぐに使用することも、カスタマイズされたソリューションのために独自の追加を加えて拡張することもできます。 | "[*概念: 業界標準のオントロジの採用*](concepts-ontologies-adopt.md)"&nbsp;&nbsp;&nbsp;<br><br>"[*概念: オントロジの拡張*](concepts-ontologies-extend.md)"&nbsp;&nbsp; |
| **CONVERT** | 別の標準形式で表されている既存のモデルが既にある場合は、それらを DTDL に変換して Azure Digital Twins で使用できます。 | "[*概念: オントロジの変換*](concepts-ontologies-convert.md)"&nbsp;&nbsp;<br><br>"[*概念: オントロジの拡張*](concepts-ontologies-extend.md)"&nbsp;&nbsp; |
| **作成者** | インスピレーションとして適用可能な業界標準を使用して、独自のカスタム DTDL モデルをゼロからいつでも作成できます。 | "[*概念: DTDL モデル*](concepts-models.md)" |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>モデル開発パスでのオントロジ戦略の使用

オントロジを Azure Digital Twins に統合するためにどのような戦略を選択した場合でも、下の完全なパスに従って、オントロジを DTDL モデルとして作成し、アップロードできます。

1. 最初に、[Azure Digital Twins での DTDL モデリング](concepts-models.md)を確認し、理解します。
1. 上で選択したオントロジ統合戦略を進めます。自分のオントロジに基づいて、モデルを [**採用**](concepts-ontologies-adopt.md)、[**変換**](concepts-ontologies-convert.md)、または [**作成**](concepts-models.md)します。
    1. 必要に応じて、オントロジを[拡張](concepts-ontologies-extend.md)して、自分のニーズに合わせてカスタマイズします。
1. モデルを[検証](how-to-parse-models.md)して、それが有効な DTDL ドキュメントであることを確認します。
1. [API](how-to-manage-model.md#upload-models) または [Azure Digital Twins のモデル アップローダー](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader)のようなサンプルを使用して、完成したモデルを Azure Digital Twins にアップロードします。

この後、Azure Digital Twins インスタンスで自分のモデルを使用できるようになります。 

[Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) や [Azure Digital Twins Model Visualizer](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) などのサンプルを使用してそれらを視覚化できます。また、それらを使用した[デジタル ツイン](concepts-twins-graph.md)の作成に進むこともできます。

## <a name="next-steps"></a>次のステップ

オントロジの採用、変換、作成の戦略について詳しく確認します。
* "[*概念: 業界標準のオントロジの採用*](concepts-ontologies-adopt.md)"
* "[*概念: オントロジの変換*](concepts-ontologies-convert.md)"
* [*方法: DTDL モデルの管理*](how-to-manage-model.md)

または、モデルを使用してデジタル ツインを作成する方法について学習してください。"[*概念: デジタル ツインとツイン グラフ*](concepts-twins-graph.md)"。