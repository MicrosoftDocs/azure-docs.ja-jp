---
title: オントロジの変換
titleSuffix: Azure Digital Twins
description: 業界標準モデルを Azure Digital Twins 用の DTDL に変換するプロセスについて説明します
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100563388"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>業界標準オントロジを Azure Digital Twins 用の DTDL に変換する

ほとんどの[オントロジ](concepts-ontologies.md)は、[OWL](https://www.w3.org/OWL/)、[RDF](https://www.w3.org/2001/sw/wiki/RDF)、[RDFS](https://www.w3.org/2001/sw/wiki/RDFS) などのセマンティック Web 標準に基づいています。 

Azure Digital Twins でモデルを使用するには、それが DTDL 形式である必要があります。 この記事では、RDF ベースのモデルを DTDL に変換して Azure Digital Twins で使用できるようにするための **変換パターン** の形式で、一般的な設計ガイダンスについて説明します。 

この記事には、RDF および OWL コンバーターの [**サンプル コンバーター コード**](#converter-samples)も含まれます。これは、ビルド業界の他のスキーマ向けに拡張できます。

## <a name="conversion-pattern"></a>変換パターン

RDF ベースのモデルを DTDL に変換するときに使用できるサードパーティ製のライブラリがいくつかあります。 これらのライブラリの一部では、モデル ファイルをグラフに読み込むことができます。 グラフ内をループ処理して特定の RDFS および OWL コンストラクトを探し、これらを DTDL に変換することができます。   

次の表は、RDFS および OWL コンストラクトをどのように DTDL にマップできるかの例を示しています。 

| RDFS/OWL の概念 | RDFS/OWL コンストラクト | DTDL の概念 | DTDL コンストラクト |
| --- | --- | --- | --- |
| クラス | `owl:Class`<br>IRI サフィックス<br>``rdfs:label``<br>``rdfs:comment`` | インターフェイス | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
|  をサブクラス化する | `owl:Class`<br>IRI サフィックス<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | インターフェイス | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| データ型のプロパティ | `owl:DatatypeProperty`<br>`rdfs:label` または `INode`<br>`rdfs:label`<br>`rdfs:range` | インターフェイスのプロパティ | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| オブジェクトのプロパティ | `owl:ObjectProperty`<br>`rdfs:label` または `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (`rdfs:range` がない場合は省略)<br>`comment`<br>`displayName`<br>

次の C# コード スニペットは、[**dotNetRDF**](https://www.dotnetrdf.org/) ライブラリを使用して、RDF モデル ファイルがどのようにグラフに読み込まれ、DTDL に変換されるかを示しています。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>コンバーターのサンプル

### <a name="rdf-converter-application"></a>RDF コンバーター アプリケーション 

Azure Digital Twins サービスで使用するために、RDF ベースのモデル ファイルを [DTDL (バージョン 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) に変換するサンプル アプリケーションがあります。 これは、[Brick](https://brickschema.org/ontology/) スキーマで検証済みであり、ビルド業界の他のスキーマ向けに拡張することができます ([Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/)、[Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/)、[buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) など)。

このサンプルは、**RdfToDtdlConverter** という名前の .NET Core コマンド ライン アプリケーションです。

サンプルはここから入手できます:[**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)。 

コードをお使いのマシンにダウンロードするには、サンプルのランディング ページのタイトルの下にある *[ZIP のダウンロード]* ボタンをクリックします。 これで、*ZIP* ファイルが *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* という名前でダウンロードされます。その後、このファイルを解凍して探索することができます。

このサンプルを使用して、コンテキストでの変換パターンを確認し、独自の具体的なニーズに応じてモデルの変換を実行する独自のアプリケーションのために、構成要素として用意することができます。

### <a name="owl2dtdl-converter"></a>OWL2DTDL コンバーター 

[**OWL2DTDL コンバーター**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL)は、Azure Digital Twins サービスで使用できる一連の DTDL インターフェイス宣言に OWL オントロジを変換するサンプルです。 また、これは、`owl:imports` 宣言を通じて他のオントロジを再利用する 1 つのルート オントロジで構成されたオントロジ ネットワークに対して機能します。

このコンバーターを使用して、[Real Estate Core Ontology](https://doc.realestatecore.io/3.1/full.html) を DTDL に変換しましたが、このコンバーターは OWL ベースの任意のオントロジに対して使用できます。

## <a name="next-steps"></a>次のステップ 

* 仕様に合うように業界標準のオントロジを拡張する方法について学習します。"[*概念: 業界のオントロジの拡張*](concepts-ontologies-extend.md)" に関する記事。

* または、オントロジに基づいてモデルを開発するためのパスを続行します。[*モデル開発パスでのオントロジ戦略の使用*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)に関する記事。