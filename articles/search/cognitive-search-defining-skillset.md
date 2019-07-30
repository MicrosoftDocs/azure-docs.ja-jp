---
title: コグニティブ検索パイプラインにスキルセットを作成する - Azure Search
description: データの抽出、自然言語処理、または画像分析のステップを定義して、データから構造化された情報を抽出および強化して、Azure Search で使用できるようにします。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: eb85c4c56d8464d4078564c707efabf60dc5aa99
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501456"
---
# <a name="how-to-create-a-skillset-in-an-enrichment-pipeline"></a>エンリッチメント パイプラインにスキルセットを作成する方法

コグニティブ検索では、Azure Search で検索できるように、データを抽出および拡充します。 Microsoft では抽出ステップとエンリッチメント ステップを "*コグニティブ スキル*" と呼び、インデックスの作成中に参照される "*スキルセット*" と組み合わせます。 スキルセットでは、[組み込みのスキル](cognitive-search-predefined-skills.md)またはカスタム スキルを使用できます (詳細については、[コグニティブ検索用のカスタム スキルの作成例](cognitive-search-create-custom-skill-example.md)に関するページを参照してください)。

この記事では、使用するスキル用にエンリッチメント パイプラインを作成する方法を学習します。 スキルセットは、Azure Search [インデクサー](search-indexer-overview.md)に接続されます。 この記事で取り上げているパイプライン デザインの一環として、スキルセット自体を構築します。 

> [!NOTE]
> インデクサーの指定はパイプライン デザインの別の部分として取り上げます (「[次の手順](#next-step)」で説明します)。 インデクサーの定義には、スキルセットの参照と、入力をターゲット インデックスの出力に接続するために使用されるフィールド マッピングが含まれます。

次の重要な点を覚えておいてください。

+ 1 つのインデクサーで使用できるスキルセットは 1 つのみです。
+ 1 つのスキルセットには、スキルが少なくとも 1 つ必要です。
+ 同じ種類のスキルを複数作成できます (画像分析スキルのバリアントなど)。

## <a name="begin-with-the-end-in-mind"></a>目的を念頭に置いて始める

推奨される最初のステップは、生データからどのデータを抽出し、そのデータを検索ソリューションでどのように使用するかを決定することです。 エンリッチメント パイプライン全体の図を作成すると、必要なステップを特定しやすくなります。

金融アナリストの一連のコメントの処理に関心があるとします。 ファイルごとに、会社名と、コメントの一般的なセンチメントを抽出する必要があります。 Bing Entity Search サービスを使用するカスタム エンリッチャーを記述することで、会社が携わっているビジネスの種類など、その会社に関する追加情報を検索することもできます。 実質的に、ドキュメントごとにインデックスが付けられている、次のような情報を抽出する必要があります。

| レコード テキスト | 会社 | センチメント | 会社の説明 |
|--------|-----|-----|-----|
|サンプル レコード| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation is an American multinational technology company ..." , "LinkedIn is a business- and employment-oriented social networking..."]

次の図は、仮定のエンリッチメント パイプラインを示しています。

![仮定のエンリッチメント パイプライン](media/cognitive-search-defining-skillset/sample-skillset.png "仮定のエンリッチメント パイプライン")


このパイプラインで行うことをきちんと把握できたら、これらのステップを提供するスキルセットを表現できます。 機能上、このスキルセットは、Azure Search にインデクサー定義をアップロードするときに表現されます。 インデクサーのアップロード方法の詳細については、[インデクサーに関するドキュメント](https://docs.microsoft.com/rest/api/searchservice/create-indexer)を参照してください。


この図で、"*ドキュメント クラッキング*" のステップは自動的に行われます。 基本的に、Azure Search では、よく知られているファイルを開く方法を把握しており、各ドキュメントから抽出されたテキストを含む "*content*" フィールドが作成されます。 白いボックスは組み込みエンリッチャーで、ドットのある "Bing Entity Search" ボックスは、作成するカスタム エンリッチャーを表します。 図に示すように、スキルセットには、3 つのスキルが含まれています。

## <a name="skillset-definition-in-rest"></a>REST でのスキルセットの定義

スキルセットは、スキルの配列として定義されます。 スキルごとに、入力ソースと生成される出力の名前を定義します。 [スキルセット REST API の作成](https://docs.microsoft.com/rest/api/searchservice/create-skillset)に関するページに従って、前の図に対応するスキルセットを定義することができます。 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>スキルセットを作成する

スキルセットを作成するときに、スキルセットを自己文書化するための説明を指定できます。 説明は省略可能ですが、スキルセットの動作の追跡に役立ちます。 スキルセットは JSON ドキュメントであり、コメントが許可されていないため、説明には `description` 要素を使用する必要があります。

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

スキルセットの次の部分は、スキルの配列です。 各スキルはエンリッチメントの基本要素と考えることができます。 このエンリッチメント パイプライン内で、各スキルによって小さなタスクが実行されます。 各スキルが、1 つの入力 (または一連の入力) を受け取り、いくつかの出力を返します。 次のいくつかのセクションでは、組み込みのスキルとカスタム スキルを指定して、入出力の参照を介してスキルを連鎖させる方法に注目します。 入力は、ソース データまたは別のスキルから取得できます。 出力は、検索インデックス内のフィールドにマッピングしたり、ダウンストリームのスキルへの入力として使用したりできます。

## <a name="add-built-in-skills"></a>組み込みのスキルを追加する

最初のスキルを見てみましょう。組み込みの[エンティティ認識スキル](cognitive-search-skill-entity-recognition.md)です。

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* どの組み込みのスキルにも、`odata.type`、`input`、および `output` プロパティがあります。 スキル固有のプロパティによって、そのスキルに適用できる追加の情報が提供されます。 エンティティの認識では、`categories` は、事前トレーニング済みモデルが認識できる固定されたエンティティ型セットのうちのエンティティの 1 つです。

* スキルごとに ```"context"``` が必要です。 コンテキストは、操作が実行されるレベルを表します。 上記のスキルでは、コンテキストはドキュメント全体であり、つまり、認識スキルがドキュメントごとに 1 回呼び出されることになります。 出力もそのレベルで生成されます。 具体的には、```"organizations"``` は、```"/document"``` のメンバーとして生成されます。 ダウンストリーム スキルでは、新しく作成されたこの情報を ```"/document/organizations"``` として参照できます。  ```"context"``` フィールドが明示的に設定されていない場合、このドキュメントが既定のコンテキストになります。

* このスキルには、ソースの入力が ```"/document/content"``` に設定されている "text" と呼ばれる入力があります。 このスキル (エンティティ認識) は、各ドキュメントの *content* フィールド (Azure BLOB インデクサーによって作成される標準のフィールドです) 上で動作します。 

* このスキルには、```"organizations"``` と呼ばれる出力があります。 出力は、処理中にのみ存在します。 この出力をダウンストリーム スキルの入力に連結するには、```"/document/organizations"``` としてこの出力を参照します。

* 特定のドキュメントでは、```"/document/organizations"``` の値は、テキストから抽出された組織の配列になります。 例:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

状況によっては、配列の各要素を別々に参照する必要があります。 たとえば、```"/document/organizations"``` の各要素を別のスキル (カスタム Bing Entity Search エンリッチャーなど) に別々に渡す必要があるとします。 この配列の各要素は、```"/document/organizations/*"``` のようにパスにアスタリスクを追加することで参照できます。 

2 番目の、センチメント抽出のスキルは、最初のエンリッチャーと同じパターンに従います。 入力として ```"/document/content"``` を受け取り、コンテンツのインスタンスごとにセンチメント スコアを返します。 ```"context"``` フィールドを明示的に設定していないため、出力 (mySentiment) は、```"/document"``` の子になります。

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>カスタム スキルを追加する

Bing Entity Search カスタム エンリッチャーの構造体を思い出してください。

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

この定義は、エンリッチメント プロセスの一環として Web API を呼び出す[カスタム スキル](cognitive-search-custom-skill-web-api.md)です。 このスキルでは、エンティティ認識によって識別される組織ごとに、Web API を呼び出してその組織の説明を検索します。 Web API を呼び出すタイミングと受信した情報を送る方法のオーケストレーションは、エンリッチメント エンジンによって内部的に処理されます。 ただし、このカスタム API を呼び出すために必要な初期化は、JSON (URI、httpHeaders、想定される入力など) で提供する必要があります。 エンリッチメント パイプライン用にカスタム Web API を作成する際のガイダンスについては、[カスタム インターフェイスを定義する方法](cognitive-search-custom-skill-interface.md)に関するページを参照してください。

"context" フィールドが、アスタリスク付きで ```"/document/organizations/*"``` に設定されていることに注目してください。これは、エンリッチメント ステップが```"/document/organizations"``` の下にある組織 "*ごと*" に呼び出されることを意味します。 

出力 (ここでは会社の説明) が、特定された組織ごとに生成されます。 ダウンストリームのステップ (キー フレーズの抽出など) で説明を参照するときは、パス ```"/document/organizations/*/description"``` を使用して実行します。 

## <a name="add-structure"></a>構造を追加する

スキルセットによって、非構造化データから構造化情報が生成されます。 次の例を考えてみます。

"*Microsoft では第 4 四半期に、昨年買収したソーシャル ネットワー キング会社 LinkedIn からの収益として 11 億ドルを記録しました。Microsoft は、この買収によって、LinkedIn の機能を自社の CRM と Office の機能に結合することができます。株主は、これまでの経過に興奮しています。* "

結果として生成される構造体は、次の図のようになります。

![サンプル出力構造体](media/cognitive-search-defining-skillset/enriched-doc.png "サンプル出力構造体")

ここまでは、この構造は、内部のみ、メモリのみであり、Azure Search インデックス内だけで使用されていました。 検索以外で使用するシェイプによるエンリッチメントを保存するための 1 つの方法として、ナレッジ ストアの追加が提供されています。

## <a name="add-a-knowledge-store"></a>ナレッジ ストアを追加する

[ナレッジ ストア](knowledge-store-concept-intro.md)は、エンリッチされたドキュメントを保存するための Azure Search 内のプレビュー機能です。 Azure ストレージ アカウントによってサポートされている、自身で作成したナレッジ ストアは、エンリッチされたデータが配置されるリポジトリになります。 

ナレッジ ストアの定義は、スキルセットに追加されます。 プロセス全体のチュートリアルについては、[ナレッジ ストアの使用を開始する方法](knowledge-store-howto.md)に関するページをご覧ください。

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

エンリッチされたドキュメントは、階層のリレーションシップを保管するテーブルとして保存するか、またはBLOB ストレージ内に JSON ドキュメントとして保存できます。 スキルセット内のどのスキルからの出力も、プロジェクションへの入力のソースにすることが可能です。 特定のシェイプへのデータのプロジェクションを検討している場合は、[Shaper スキル](cognitive-search-skill-shaper.md)によって、ユーザーが使用する複合型をモデル化できるようになりました。 

<a name="next-step"></a>

## <a name="next-steps"></a>次の手順

エンリッチメント パイプラインとスキルセットについて学習したので、引き続き[スキルセットの注釈を参照する方法](cognitive-search-concept-annotations-syntax.md)または[インデックス内のフィールドに出力をマップする方法](cognitive-search-output-field-mapping.md)を確認してください。 
