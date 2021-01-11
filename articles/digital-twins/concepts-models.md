---
title: カスタム モデル
titleSuffix: Azure Digital Twins
description: Azure Digital Twins でユーザー定義モデルを使用して環境内のエンティティを記述する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 1f6fc7bff31faa62c290a4c02be3e80fee6fa200
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042634"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Azure Digital Twins のツイン モデルについて

Azure Digital Twins の重要な特性は、独自のボキャブラリを定義し、ビジネスの自己定義用語でツイン グラフを構築できることです。 この機能は、ユーザー定義**モデル**を通じて提供されます。 モデルは、世界を説明するための名詞と考えることができます。 

モデルは、オブジェクト指向プログラミング言語の**クラス**に似ており、実際の作業環境における 1 つの特定の概念のデータ シェイプを定義します。 モデルには名前 (*Room* や *TemperatureSensor* など) があり、プロパティ、テレメトリまたはイベント、環境内でこの型のエンティティに何ができるかを説明するコマンドなどの要素が含まれています。 後で、これらのモデルを使用して、この型の説明と一致する特定のエンティティを表す[**デジタル ツイン**](concepts-twins-graph.md)を作成します。

モデルは、JSON-LD ベースの **Digital Twin Definition Language (DTDL)** を使用して記述されます。  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>モデルを記述するための Digital Twin Definition Language (DTDL)

Azure Digital Twins のモデルは、Digital Twins Definition language (DTDL) を使用して定義されます。 DTDL は JSON-LD に基づいており、プログラミング言語に依存しません。 DTDL は Azure Digital Twins 専用ではなく、[IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)などの他の IoT サービスのデバイス データを表すためにも使用されます。 

Azure Digital Twins では、DTDL "**_バージョン 2_**" が使用されます。 このバージョンの DTDL の詳細については、GitHub で次の仕様ドキュメントを参照してください: [*Digital Twins Definition Language (DTDL) - バージョン 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 Azure Digital Twins での DTDL "_バージョン 1_" の使用は、非推奨になっています。

> [!TIP] 
> DTDL を使用するすべてのサービスで、DTDL の機能がまったく同じに実装されるわけではありません。 たとえば、IoT プラグ アンド プレイではグラフ用の DTDL 機能は使用されず、Azure Digital Twins では現在、DTDL コマンドが実装されていません。 Azure Digital Twins に固有の DTDL 機能の詳細については、この記事の後のセクション「[Azure Digital Twins DTDL の実装の仕様](#azure-digital-twins-dtdl-implementation-specifics)」を参照してください。

## <a name="elements-of-a-model"></a>モデルの要素

モデル定義内では、最上位レベルのコード項目は**インターフェイス**です。 これにより、モデル全体がカプセル化され、モデルの残りの部分はインターフェイス内で定義されます。 

DTDL モデルのインターフェイスには、以下の各フィールドをゼロ個、1 個、または複数個、含めることができます。
* **Property** - プロパティは、エンティティの状態を表すデータ フィールドです (多くのオブジェクト指向プログラミング言語のプロパティと同様)。 プロパティはバッキング ストレージを備えていて、いつでも読み取ることができます。
* **Telemetry** - テレメトリ フィールドは測定値またはイベントを表し、多くの場合、デバイスのセンサーの読み取りを説明するために使用されます。 プロパティとは異なり、テレメトリはデジタル ツインに格納されません。これは、発生時に処理される必要がある一連の期限付きデータ イベントです。 プロパティとテレメトリの違いの詳細については、下の「[*プロパティとテレメトリ*](#properties-vs-telemetry)」セクションを参照してください。
* **Component** - コンポーネントを使用すると、必要に応じてモデル インターフェイスを他のインターフェイスの組み合わせとして構築できます。 コンポーネントの例として、"*電話*" のモデルの定義に使用される *frontCamera* インターフェイス (およびもう 1 つのコンポーネント インターフェイスである *backCamera*) があります。 最初に *frontCamera* のインターフェイスを独自のモデルであるかのように定義する必要があります。その後、*Phone* を定義するときに、それを参照できます。

    コンポーネントを使用するのは、ソリューションの不可欠な要素でありながら、個別の ID を必要とせず、ツイン グラフで独立して作成、削除、および再配置する必要がないものを記述する場合です。 エンティティがツイン グラフ内で独立した存在になるようにするには、それらを異なるモデルの個別のデジタル ツインとして表現し、"*リレーションシップ*" (次の箇条書き項目を参照) で接続します。
    
    >[!TIP] 
    >コンポーネントは、モデル インターフェイス内の関連するプロパティをグループ化するために、組織に使用することもできます。 この状況では、各コンポーネントをインターフェイス内の名前空間または "フォルダー" と考えることができます。
* **Relationship** - リレーションシップを使用すると、あるデジタル ツインと他のデジタル ツインの関係性を表すことができます。 リレーションシップは、さまざまなセマンティックな意味を表すことができます。たとえば、*contains* ("フロアが部屋を含む")、*cools* ("hvac が部屋を冷房する")、*isBilledTo* ("コンプレッサーがユーザーに請求される") などです。リレーションシップにより、ソリューションは相互に関連するエンティティのグラフを提供できます。

> [!NOTE]
> [DTDL の仕様](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)では、**Commands** も定義されています。これは、デジタル ツインに対して実行できるメソッドです (リセット コマンドや、ファンをオンまたはオフに切り替えるためのコマンドなど)。 ただし、"*Azure Digital Twins では現在、コマンドがサポートされていません*"。

### <a name="properties-vs-telemetry"></a>プロパティとテレメトリ

Azure Digital Twins の DTDL の**プロパティ**と**テレメトリ**のフィールドを区別するための追加のガイダンスを次に示します。

Azure Digital Twins モデルのプロパティとテレメトリの違いは次のとおりです。
* **プロパティ**には、バッキング ストレージがあることが想定されています。 これは、プロパティをいつでも読み取って、その値を取得できることを意味します。 プロパティが書き込み可能であれば、プロパティに値を格納することもできます。  
* **テレメトリ**は、イベントのストリームに似たものであり、存続期間の短い一連のデータ メッセージです。 イベントのリッスンおよびその発生時に実行するアクションを設定していないと、後でイベントをトレースできません。 後でそこに戻って読み取ることはできません。 
  - C# の用語では、テレメトリは C# イベントに似ています。 
  - IoT の用語では、テレメトリは通常、デバイスによって送信される 1 つの測定値です。

**テレメトリ**は、IoT デバイスで多く使用されます。多くのデバイスには、生成される測定値を格納する能力もその必要性もないためです。 これらは単に、"テレメトリ" イベントのストリームとして送信されます。 この場合、テレメトリ フィールドの最新の値について、いつでもデバイスに照会できるわけではありません。 代わりに、デバイスからのメッセージをリッスンし、メッセージが到着したときにアクションを実行する必要があります。 

そのため、Azure Digital Twins でモデルを設計する際は、ほとんどの場合**プロパティ**を使用して、ツインをモデル化することになると考えられます。 これにより、バッキング ストレージが得られ、データ フィールドの読み取りとクエリを行うことができます。

多くの場合、テレメトリとプロパティは連携して、デバイスからのデータのイングレスを処理します。 Azure Digital Twins へのすべてのイングレスは [API](how-to-use-apis-sdks.md) を通じて行われるため、通常はイングレス関数を使用してデバイスからテレメトリまたはプロパティのイベントを読み取り、それに応じて ADT でプロパティを設定します。 

Azure Digital Twins API からテレメトリ イベントを発行することもできます。 他のテレメトリと同様に、これは存続期間の短いイベントであり、リスナーによる処理が必要です。

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins DTDL の実装の仕様

DTDL モデルに Azure Digital Twins との互換性を与えるには、これらの要件を満たす必要があります。

* モデルの最上位レベルの DTDL 要素は、すべて "*インターフェイス*" 型である必要があります。 これは、Azure Digital Twins モデル API が、インターフェイスまたはインターフェイスの配列を表す JSON オブジェクトを受け取ることができるためです。 その結果、最上位レベルでは他の DTDL 要素型を使用できません。
* Azure Digital Twins の DTDL では、"*コマンド*" を定義しないでください。
* Azure Digital Twins では、単一レベルのコンポーネントの入れ子のみが許可されます。 これは、コンポーネントとして使用されているインターフェイスが、それ自体はコンポーネントを含められないことを意味します。 
* インターフェイスは、他の DTDL インターフェイスの内部にインラインで定義することはできません。それらは、独自の ID を備えた個別の最上位レベルのエンティティとして定義する必要があります。 その後、別のインターフェイスがそのインターフェイスをコンポーネントとして含めるか、継承を通じて含めようとする場合は、その ID を参照できます。

## <a name="example-model-code"></a>モデル コードの例

ツインの型モデルは、任意のテキスト エディターで記述できます。 DTDL 言語は、JSON 構文に従います。そのため、モデルは *json* という拡張子で保存する必要があります。 JSON 拡張子を使用すると、多くのプログラミング テキスト エディターで、DTDL ドキュメントの基本的な構文チェックと強調表示ができるようになります。 また、[Visual Studio Code](https://code.visualstudio.com/) には、[DTDL 拡張子](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)も使用できます。

このセクションでは、DTDL インターフェイスとして記述された一般的なモデルの例を示します。 このモデルでは、**惑星**とそれぞれの名前、質量、および温度が記述されています。
 
惑星は衛星である**月**とも相互作用する可能性があり、**クレーター**が含まれる場合もあります。 次の例の `Planet` モデルでは、2 つの外部モデル `Moon` と `Crater` を参照することにより、これらの他のエンティティへの接続が表されています。 以下のコード例ではこれらのモデルも定義されていますが、主要な `Planet` の例から注目が逸れないように、非常に単純にされています。

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

モデルのフィールドは次のとおりです。

| フィールド | 説明 |
| --- | --- |
| `@id` | モデルの識別子。 `dtmi:<domain>:<unique model identifier>;<model version number>` の形式でなければなりません。 |
| `@type` | 記述されている情報の種類を識別します。 インターフェイスの場合、型は *Interface* です。 |
| `@context` | JSON ドキュメントの[コンテキスト](https://niem.github.io/json/reference/json-ld/context/)を設定します。 モデルは `dtmi:dtdl:context;2` を使用する必要があります。 |
| `displayName` | [オプション] 必要に応じて、モデルにフレンドリ名を付けることができます。 |
| `contents` | 残りのすべてのインターフェイス データは、属性定義の配列としてここに配置されます。 各属性には、記述するインターフェイス情報の種類を特定するために、`@type` (*Property*、*Telemetry*、*Command*、*Relationship*、または *Component*) を指定する必要があります。また、実際の属性を定義する一連のプロパティ (たとえば、*Property* を定義するための `name` および `schema`) を指定する必要もあります。 |

> [!NOTE]
> コンポーネント インターフェイス (この例では *Crater*) が、それ (*Planet*) を使用するインターフェイスと同じ配列内に定義されていることに注意してください。 コンポーネントは、インターフェイスが検出されるようにするために、API 呼び出しの中でこのように定義する必要があります。

### <a name="possible-schemas"></a>使用可能なスキーマ

DTDL によると、*Property* および *Telemetry* 属性のスキーマは、標準プリミティブ型の `integer`、`double`、`string`、および `Boolean` と、`DateTime`、`Duration` などのその他の型にすることができます。 

プリミティブ型のほか、*Property* および *Telemetry* フィールドは、これらの複合型を含むことができます。
* `Object`
* `Map`
* `Enum`

*Telemetry* フィールドでは、`Array` もサポートされています。

### <a name="model-inheritance"></a>モデルの継承

場合によっては、モデルをさらに特殊化したい場合があります。 たとえば、汎用的なモデル *Room* と、特殊化したバリアント *ConferenceRoom* および *Gym* を作成すると便利な場合があります。 特殊化を表現するために、DTDL では継承がサポートされています。インターフェイスは、他の 1 つ以上のインターフェイスを継承できます。 

次の例では、*Planet* モデルを前の DTDL 例からより大きな *CelestialBody* モデルのサブタイプとして再イメージ化しています。 "親" モデルが先に定義され、次にフィールド `extends` を使用してその上に "子" モデルが構築されます。

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

この例では、*CelestialBody* によって *Planet* に名前、質量、および温度が提供されます。 `extends` セクションは、インターフェイス名、またはインターフェイス名の配列です (必要に応じて、拡張インターフェイスが複数の親モデルを継承できます)。

継承が適用されると、拡張インターフェイスは継承チェーン全体のすべてのプロパティを公開します。

拡張インターフェイスでは、親インターフェイスの定義は変更できません。定義を追加することだけができます。 また、いずれかの親インターフェイスで既に定義されている機能を再定義することもできません (機能が同じになるように定義される場合でも)。 たとえば、親インターフェイスが `double` プロパティの *mass* を定義している場合、拡張インターフェイスに *mass* の宣言を含めることはできません (それが同様に `double` である場合でも)。

## <a name="validating-models"></a>モデルの検証

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="next-steps"></a>次のステップ

DigitalTwinsModels API を使用してモデルを管理する方法を参照してください。
* [*方法: カスタム モデルを管理する*](how-to-manage-model.md)"

または、モデルに基づいてデジタル ツインがどのように作成されるかについて学習してください。
* "[*概念: デジタル ツインとツイン グラフ*](concepts-twins-graph.md)"

