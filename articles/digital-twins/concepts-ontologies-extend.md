---
title: オントロジの拡張
titleSuffix: Azure Digital Twins
description: オントロジを拡張する理由と戦略について説明します
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100563358"
---
# <a name="extending-ontologies"></a>オントロジの拡張 

[スマート ビル用の DTDL ベースの RealEstateCore オントロジ](https://github.com/Azure/opendigitaltwins-building)など、業界標準の[オントロジ](concepts-ontologies.md)は、IoT ソリューションの構築を開始するための優れた方法です。 業界オントロジにより、特定の分野向けに設計され、かつ Azure Digital Twins などの Azure IoT サービスですぐに使用できるように設計された、豊富な基本インターフェイス セットが提供されます。 

ただし、お客様のソリューションには、業界オントロジではカバーしきれない特定のニーズがある場合があります。 たとえば、デジタル ツインを別のシステムに格納されている 3D モデルにリンクしたい場合があります。 この場合、元のオントロジの利点をすべて維持しながら、これらのオントロジの 1 つを拡張して、独自の機能を追加できます。

この記事では、新しい DTDL プロパティを使用してオントロジを拡張する例の基礎として、DTDL ベースの [RealEstateCore](https://www.realestatecore.io/) オントロジを使用します。 ここで説明する手法は一般的なものですが、DTDL 機能 (テレメトリ、プロパティ、リレーションシップ、コンポーネント、またはコマンド) がある DTDL ベースのオントロジの任意の部分に適用することができます。 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore の Space 階層 

DTDL ベースの RealEstateCore オントロジでは、Space 階層を使用して、さまざまな種類のスペースが定義されています。Room、Building、Zone などです。さまざまな種類の Room、Building、Zone を定義するために、これらの各モデルから階層が拡張されます。 

下の図は、階層の一部を示しています。 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="RealEstateCore の Space 階層の一部を示すフロー図。最上位レベルには、Space という要素があります。これは、&quot;extends&quot; 矢印によって 1 レベル下の Room に接続されています。Room は、2 つの &quot;extends&quot; 矢印によって 1 レベル下の ConferenceRoom と Office に接続されています。"::: 

RealEstateCore オントロジの詳細については、"[*概念: 業界標準のオントロジの採用*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)" に関する記事を参照してください。

## <a name="extending-the-realestatecore-space-hierarchy"></a>RealEstateCore の Space 階層の拡張 

お客様のソリューションには、業界オントロジではカバーしきれない特定のニーズがある場合があります。 この場合、階層を拡張することで、業界オントロジをニーズに合わせてカスタマイズしながら、引き続き使用できます。 

この記事では、オントロジの階層を拡張することが有用な 2 種類のケースについて説明します。 

* 業界オントロジにない概念のための新しいインターフェイスを追加する。 
* 既存のインターフェイスにプロパティ (またはリレーションシップ、コンポーネント、テレメトリ、コマンド) をさらに追加する。

### <a name="add-new-interfaces-for-new-concepts"></a>新しい概念のための新しいインターフェイスを追加する 

このケースでは、自分のソリューションに必要である一方で、業界オントロジには存在しない概念のためのインターフェイスを追加します。 たとえば、DTDL ベースの RealEstateCore オントロジでは表されていない他の種類の部屋が自分のソリューションにある場合は、RealEstateCore インターフェイスから直接拡張することで、それらを追加できます。 

下の例は、RealEstateCore オントロジには存在しない "フォーカス ルーム" を表す必要があるソリューションを示しています。 フォーカス ルームは、一度に数時間、ユーザーが作業に専念できるように設計された小さなスペースです。 

この新しい概念を使用して業界オントロジを拡張するには、業界オントロジのインターフェイス[から拡張](concepts-models.md#model-inheritance)した新しいインターフェイスを作成します。 

フォーカス ルーム インターフェイスを追加した後、拡張された階層には、新しい部屋の種類が示されます。 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="RealEstateCore の Space 階層を示すフロー図 (上記の図に新しい要素が追加されています)。ConferenceRoom と Office がある最下位レベルに、FocusRoom という名前の新しい要素があります (これも、Room からの &quot;extends&quot; 矢印で接続されています)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>既存のインターフェイスに機能をさらに追加する 

このケースでは、業界オントロジに存在するインターフェイスにプロパティ (またはリレーションシップ、コンポーネント、テレメトリ、コマンド) をさらに追加します。

このセクションでは、2 つの例について説明します。 
* 既存のシステムに既にあるスペースの 3D 描画を表示するソリューションを構築する場合に、各デジタル ツインをその 3D 描画に (ID によって) 関連付けて、ソリューションでスペースに関する情報を表示するときに、その既存のシステムから 3D 描画も取得できるようにしたい場合があります。 
* 自分のソリューションで会議室 (ConferenceRoom) のオンラインとオフラインの状態を追跡する必要がある場合に、表示またはクエリで使用するために会議室の状態を追跡したい場合があります。 

どちらの例も、新しいプロパティ (3D 描画をデジタル ツインに関連付ける `drawingId` プロパティと、会議室がオンラインかどうかを示す "online" プロパティ) を使用して実装できます。 

通常、業界オントロジを直接変更することは避けます。これは、将来的に、それに対する更新を自分のソリューションに組み込むことができるようにするためです (これにより、自分の追加要素が上書きされます)。 代わりに、DTDL ベースの RealEstateCore オントロジから拡張した独自のインターフェイス階層で、このような種類の追加を行うことができます。 作成する各インターフェイスでは、複数のインターフェイス継承を使用して、その親の RealEstateCore インターフェイスを拡張し、拡張したインターフェイス階層からその親のインターフェイスを拡張します。 この方法を使用すると、業界オントロジと自分の追加要素を組み合わせて使用できます。 

業界オントロジを拡張するには、その業界オントロジのインターフェイスから拡張した独自のインターフェイスを作成し、その拡張したインターフェイスに新しい機能を追加します。 拡張するインターフェイスごとに、新しいインターフェイスを作成します。 拡張したインターフェイスは DTDL で記述されます (このドキュメントで後述する「拡張したインターフェイスの DTDL」セクションを参照してください)。 

上に示した階層の一部を拡張すると、拡張した階層は下の図のようになります。 ここでは、拡張した Space インターフェイスは、デジタル ツインを 3D 描画に関連付ける ID を格納する `drawingId` プロパティを追加します。 さらに、ConferenceRoom インターフェイスは、会議室のオンライン状態を格納する "online" プロパティを追加します。 継承によって、ConferenceRoom インターフェイスには、RealEstateCore ConferenceRoom インターフェイスのすべての機能だけでなく、拡張された Space インターフェイスのすべての機能も含まれます。 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="拡張された RealEstateCore の Space 階層を示すフロー図 (上記の図にさらに新しい要素が追加されています)。Room はそのレベルを Space 要素と共有するようになりました。この Space 要素は、&quot;extends&quot; 矢印によって 1 レベル下の新しい Room 要素 (ConferenceRoom と Office の横) に接続されています。新しい要素は、追加の &quot;extends&quot; リレーションシップによって既存のオントロジに接続されています。"::: 

## <a name="using-the-extended-space-hierarchy"></a>拡張した Space 階層を使用する 

拡張した Space 階層を使用してデジタル ツインを作成すると、各デジタル ツインのモデルは、(元の業界オントロジではなく) 拡張した Space 階層からのものになり、インターフェイス継承を通じて、業界オントロジと拡張したインターフェイスのすべての機能が組み込まれます。

各デジタル ツインのモデルは、下の図に示すように、拡張した階層のインターフェイスになります。 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="拡張した RealEstateCore の Space 階層からの抜粋。Space (最上位レベル)、1 つの Room (中間レベル)、ConferenceRoom、Office、FocusRoom (下位レベル) が含まれます。モデルの名前は、各要素に接続されています (たとえば、Room は Room101 というモデルに接続されています)。"::: 

モデル ID (`IS_OF_MODEL` 演算子) を使用してデジタル ツインのクエリを実行する場合は、拡張した階層のモデル ID を使用する必要があります。 たとえば、「 `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')` 」のように入力します。 

## <a name="contributing-back-to-the-original-ontology"></a>元のオントロジに寄与する 

場合によっては、業界オントロジを、そのオントロジの大部分のユーザーに広く役立つ方法で拡張することがあります。 この場合、元のオントロジにその拡張を寄与することを検討してください。 オントロジごとに寄与のプロセスは異なるため、寄与の詳細については、オントロジの GitHub リポジトリを確認してください。 

## <a name="dtdl-for-new-interfaces"></a>新しいインターフェイスのための DTDL 

業界オントロジから直接拡張した新しいインターフェイスのための DTDL は、このようになります。 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>拡張したインターフェイスの DTDL 

拡張したインターフェイスの DTDL (上で説明した部分のみ) は、このようになります。 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>次のステップ

オントロジに基づいてモデルを開発するためのパスを続行します。"[*モデル開発パスでのオントロジ戦略の使用*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)" に関する記事。