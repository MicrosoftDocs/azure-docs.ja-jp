---
title: 業界標準のオントロジの採用
titleSuffix: Azure Digital Twins
description: Azure Digital Twins で採用できる既存の業界オントロジについて説明します。
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124229"
---
# <a name="adopting-an-industry-ontology"></a>業界オントロジの採用

空白のページから開始するよりも、オープンソースの DTDL オントロジから開始した方が簡単な場合があるため、Microsoft は、特定分野の専門家と提携してオントロジを発行しています。これらは、広く受け入れられている業界の規則を表し、さまざまな顧客のユース ケースをサポートします。 

結果として、業界標準に基づいて学習または構築された、あるいはそれを直接使用する、一連のオープンソースの DTDL ベースのオントロジが提供されます。 このオントロジは、ダウンストリームの開発者のニーズを満たすように設計されており、業界で広く採用または拡張される可能性があります。

現時点では、Microsoft は、パートナーと提携して[スマート ビル](#realestatecore-smart-building-ontology)のオントロジと[スマート シティ](#smart-cities-ontology)のオントロジを開発しています。これにより、これらの業界の標準に基づいてモデリングするための共通基盤が得られ、再発明が回避されます。 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore スマート ビル オントロジ

*このオントロジについてはこちら: [**スマート ビルに関する Digital Twins Definition Language ベースの RealEstateCore オントロジ**](https://github.com/Azure/opendigitaltwins-building)* 。

Microsoft は、不動産所有者、ソフトウェア ベンダー、研究機関からなるスウェーデンのコンソーシアムである [RealEstateCore](https://www.realestatecore.io/) と提携しています。この目的は、不動産業界向けにこのオープンソースの  DTDL オントロジを提供することです。

このスマート ビル オントロジにより、( [BRICK スキーマ](https://brickschema.org/ontology/)や  [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html) などの) 業界標準を使用してスマート ビルをモデリングするための共通基盤が提供され、再発明が回避されます。 また、このオントロジには、その使用方法および適切な拡張方法に関するベスト プラクティスも付属します。 

このオントロジの構造とモデリング規則、使用方法、拡張方法、参加方法について詳しくは、オントロジの GitHub のリポジトリ ([Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building)) を参照してください。 

また、こちらのブログ投稿と付随する動画で、RealEstateCore とのパートナーシップ、およびこのイニシアティブの目標について詳細を確認できます。"[デジタル ツインのスマート ビル オントロジ RealEstateCore が使用可能に](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)"

## <a name="smart-cities-ontology"></a>スマート シティのオントロジ

*このオントロジについてはこちら: [**スマート シティに関する Digital Twins Definition Language (DTDL) オントロジ**](https://github.com/Azure/opendigitaltwins-smartcities)* 。

Microsoft は、[Open Agile Smart Cities (OASC)](https://oascities.org/) および [Sirus](https://sirus.be/) と共同で、[ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim) を始めとする、スマート シティに関する DTDL ベースのオントロジを提供しています。 ETSI NGSI-LD に加え、Saref4City、CityGML、ISO なども評価しています。

現在のリリースのオントロジでは、初期のモデル セットに焦点を絞っています。 オントロジの作成者は、初期のユース ケース セットを拡張し、既存のモデルを改良するために強力してくれるユーザーの参加を歓迎します。 

オントロジと、その使用方法、参加方法について詳しくは、このオントロジの GitHub のリポジトリ ([Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities)) を参照してください。 

スマート シティに関するパートナーシップとアプローチの詳細については、「[Digital Twins のスマート シティ オントロジ](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585)」というブログ記事とそこに掲載されている動画も参照してください。

## <a name="next-steps"></a>次のステップ

* 仕様に合うように業界標準のオントロジを拡張する方法について学習します。"[*概念: 業界のオントロジの拡張*](concepts-ontologies-extend.md)" に関する記事。

* または、オントロジに基づいてモデルを開発するためのパスを続行します。[*モデル開発パスでのオントロジ戦略の使用*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)に関する記事。