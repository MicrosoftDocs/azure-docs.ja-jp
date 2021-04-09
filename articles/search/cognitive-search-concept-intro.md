---
title: AI エンリッチメントの概念
titleSuffix: Azure Cognitive Search
description: コンテンツ抽出、自然言語処理 (NLP)、および画像処理は、定義済みのコグニティブ スキルとカスタム AI アルゴリズムの両方を用いて、Azure Cognitive Search インデックスで検索可能なコンテンツを作成するために使用されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037253"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search における AI エンリッチメント

AI エンリッチメントは、画像、BLOB、その他の構造化されていないデータ ソースからテキストを抽出するために使用できる[インデクサー](search-indexer-overview.md)の拡張機能です。 エンリッチメントと抽出によって、[検索インデックス](search-what-is-an-index.md)または[ナレッジ ストア](knowledge-store-concept-intro.md)でも、インデクサーの出力オブジェクトでコンテンツがより検索しやすくなります。 

抽出とエンリッチメントは、インデックサー ドリブン パイプラインにアタッチされた "*コグニティブ スキル*" を使用して実装されています。 Microsoft の組み込みのスキルを使用することも、作成した "[*カスタム スキル*](cognitive-search-create-custom-skill-example.md)" に外部処理を埋め込むこともできます。 カスタム スキルの例としては、金融、科学出版物、医療などの専門分野を対象としたカスタム エンティティ モジュールまたはドキュメント分類子が挙げられます。

組み込みのスキルは、次のカテゴリに分類されます。 

+ **自然言語処理** スキルには、[エンティティ認識](cognitive-search-skill-entity-recognition.md)、[言語検出](cognitive-search-skill-language-detection.md)、[キー フレーズ抽出](cognitive-search-skill-keyphrases.md)、テキスト操作、[センチメント検出](cognitive-search-skill-sentiment.md)、[PII 検出](cognitive-search-skill-pii-detection.md)が含まれます。 これらのスキルによって、構造化されていないテキストが、インデックスで検索可能およびフィルター可能なフィールドとしてマップされます。

+ **画像処理** スキルには、[光学式文字認識 (OCR)](cognitive-search-skill-ocr.md) および [ビジュアル フィーチャー](cognitive-search-skill-image-analysis.md)の特定 (例: 顔検出、画像の解釈、画像の認識 (有名な人やランドマーク)、画像の向きなどの属性) が含まれます。 これらのスキルによって、画像コンテンツのテキスト表現が作成され、Azure Cognitive Search のクエリ機能を使用して検索できるようになります。

![エンリッチメント パイプラインの図](./media/cognitive-search-intro/cogsearch-architecture.png "エンリッチメント パイプラインの概要")

Azure Cognitive Search の組み込みのスキルは、Cognitive Services APIs の事前トレーニング済み機械学習モデル ([Computer Vision](../cognitive-services/computer-vision/index.yml) と[テキスト分析](../cognitive-services/text-analytics/overview.md)) に基づいています。 コンテンツの処理中にこれらのリソースを活用する場合は、Cognitive Services リソースをアタッチできます。

自然言語および画像処理はデータ インジェスト フェーズで適用され、結果は Azure Cognitive Search における検索可能なインデックス内のドキュメントの構成の一部になります。 データは Azure データ セットとして調達され、必要な[組み込みのスキル](cognitive-search-predefined-skills.md)を使用してインデックス パイプライン経由でプッシュされます。  

## <a name="feature-availability"></a>使用可能な機能

AI エンリッチメントは、Azure Cognitive Services も利用できるリージョンでご利用いただけます。  AI エンリッチメントを現在ご利用いただけるかどうかは、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)ページでご確認いただけます。  AI エンリッチメントは、以下のリージョンを除き、サポートされているすべてのリージョンでご利用いただけます。

+ オーストラリア南東部
+ 中国北部 2
+ ノルウェー東部
+ ドイツ中西部

お使いの検索サービスがこれらのリージョンに置かれている場合、スキルセットを作成したり、使用したりできませんが、その他の検索サービス機能はすべてご利用いただけます。また、完全サポートされています。

## <a name="when-to-use-ai-enrichment"></a>どのような場合に AI エンリッチメントを使用するか

生コンテンツが、テキスト、画像コンテンツ、または言語検出と翻訳を必要とするコンテンツである場合、組み込みのコグニティブ スキルの使用を検討する必要があります。 組み込みのコグニティブ スキルを通じて AI を適用することで、このコンテンツのロックを解除して、検索およびデータ サイエンス アプリの価値と有用性を向上させることができます。 

さらに、パイプラインに統合したいオープンソース コード、サードパーティ製コード、またはファースト パーティ製コードの追加を検討することもできます。 さまざまなドキュメントの種類の顕著な特徴を識別する分類モデルはこのカテゴリに分類されますが、コンテンツの価値を高める任意のパッケージを使用できます。

### <a name="more-about-built-in-skills"></a>組み込みのスキルの詳細

組み込みのスキルを使用して作られた[スキルセット](cognitive-search-defining-skillset.md)は、次のアプリケーション シナリオに適しています。

+ フルテキスト検索を必要とするスキャンされたドキュメント (JPEG)。 光学式文字認識 (OCR) スキルをアタッチして、JPEG ファイルからのテキストの識別、抽出、取り込みを行うことができます。

+ 画像とテキストが組み合わされた PDF。 PDF のテキストは、エンリッチメント ステップを使用せずにインデックス作成中に抽出できますが、画像と自然言語の処理を追加すると、標準のインデックス作成よりも優れた結果が得られやすくなります。

+ 言語検出と状況に応じたテキスト翻訳を適用する多言語コンテンツ。

+ 大きなドキュメントでは表示されない固有の意味またはコンテキストを持つコンテンツを含む、非構造化または半構造化ドキュメント。 

  多くの場合、BLOB には、単一の "フィールド" にパックされた大規模なコンテンツが含まれています。 画像と自然言語処理のスキルをインデクサーにアタッチすることによって、生のコンテンツに現存するが、それ以外に個別のフィールドとして表示されない新しい情報を作成することができます。 キー フレーズ抽出、感情分析、エンティティ認識 (人、組織、場所) などの、すぐに使用できる組み込みのコグニティブなスキルが役立ちます。

  また、組み込みのスキルのテキスト分割、マージ、シェイプ操作を通じて、コンテンツを再構築することもできます。

### <a name="more-about-custom-skills"></a>カスタム スキルの詳細

カスタム スキルは、フォームの認識や、[カスタム スキル Web インターフェイス](cognitive-search-custom-skill-interface.md)で指定してラップするモデルを使用するカスタム エンティティ検出などの、より複雑なシナリオに対応できます。 カスタム スキルの例としては、[Forms Recognizer](../cognitive-services/form-recognizer/overview.md)、[Bing Entity Search API](./cognitive-search-create-custom-skill-example.md) の統合、[カスタム エンティティ認識](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)などがあります。

## <a name="steps-in-an-enrichment-pipeline"></a>エンリッチメント パイプラインの手順<a name="enrichment-steps"></a>

エンリッチメント パイプラインは "[*インデクサー*](search-indexer-overview.md)" に基づきます。 インデクサーは、ドキュメント解析のために、インデックスとデータ ソース間のフィールド対フィールドのマッピングに基づいてインデックスを設定します。 インデクサーにアタッチされたスキルにより、ユーザーが定義した 1 つ以上のスキルセットに従ってドキュメントがインターセプトされ、エンリッチメントされます。 インデックスが作成されると、[Azure Cognitive Search でサポートされているすべての種類のクエリ](search-query-overview.md)から検索を要求してコンテンツにアクセスできます。  インデクサーを使い慣れていない場合は、このセクションで手順を示します。

### <a name="step-1-connection-and-document-cracking-phase"></a>手順 1:接続とドキュメント解読フェーズ

パイプラインの先頭には、非構造化テキストまたはテキスト以外のコンテンツ (画像、スキャンされたドキュメント、JPEG ファイルなど) があります。 データは、インデクサーによってアクセスできる Azure のデータ ストレージ サービスに存在する必要があります。 インデクサーは、ソース ドキュメントを "解読" してソース データからテキストを抽出できます。 ドキュメント解析は、インデックス作成時にテキスト以外のソースからテキスト コンテンツを抽出または作成するプロセスです。

![ドキュメント解析フェーズ](./media/cognitive-search-intro/document-cracking-phase-blowup.png "ドキュメント解析")

 サポートされているソースには、Azure Table Storage、Azure Table Storage、Microsoft Azure SQL Database、および Microsoft Azure Cosmos DB などがあります。 テキスト ベースのコンテンツは、次のファイル タイプから抽出できます:PDF、Word、PowerPoint、CSV ファイル。 詳細な一覧については、[サポートされている形式](search-howto-indexing-azure-blob-storage.md#SupportedFormats)に関するページをご覧ください。 インデックスの作成には時間がかかるため、代表的な少量のデータ セットから始め、ソリューションの成熟度に応じて段階的に構築します。

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>手順 2:認知スキルとエンリッチメント フェーズ

エンリッチメントは、アトミック操作を実行する "*コグニティブ スキル*" を使用して実行されます。 たとえば、PDF を解析した後、エンティティの認識、言語検出、またはキー フレーズ抽出を適用して、ソースではネイティブで使用できない新しいフィールドをインデックスで生成できます。 パイプラインで使用される技術を総称して *スキルセット* と呼びます。  

![エンリッチメント フェーズ](./media/cognitive-search-intro/enrichment-phase-blowup.png "エンリッチメント フェーズ")

スキルセットには、[組み込みのコグニティブ スキル](cognitive-search-predefined-skills.md)に基づくものと、ユーザーが提供してスキルセットに接続する[カスタム スキル](cognitive-search-create-custom-skill-example.md)に基づくものとがあります。 スキルセットは、単純なものから複雑なものまで設定でき、処理の種類だけでなく、演算の順序も決定します。 スキルセットに加え、インデクサーの一部として定義されたフィールド マッピングによってエンリッチメント パイプラインが指定されます。 これらのコンポーネントの統合方法については、[スキルセットの定義](cognitive-search-defining-skillset.md)に関する記事を参照してください。

内部的には、パイプラインは、エンリッチメントされたドキュメントのコレクションを生成します。 エンリッチメントされたドキュメントのどの部分を、検索インデックス内のインデックスを作成可能なフィールドにマップするかをユーザーが決定できます。 たとえば、キー フレーズ抽出およびエンティティ認識スキルを適用した場合、これらの新しいフィールドはエンリッチメントされたドキュメントの一部になり、インデックス上のフィールドにマッピングできます。 入出力の構造の詳細については、[注釈](cognitive-search-concept-annotations-syntax.md)を参照してください。

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>knowledgeStore 要素を追加してエンリッチメントを保存する

[Search REST api-version=2020-06-30](/rest/api/searchservice/) では、Azure ストレージ接続を提供する `knowledgeStore` 定義とエンリッチメントの格納方法について説明するプロジェクションによってスキルセットが拡張されます。 これは、インデックスに追加されます。 標準的な AI パイプラインでは、エンリッチメントされたドキュメントは一時的なものであり、インデックス作成時にのみ使用され、その後破棄されます。 エンリッチメントされたドキュメントは、ナレッジ ストアを使用して保存されます。 詳細については、[ナレッジ ストア](knowledge-store-concept-intro.md)に関するページを参照してください。

### <a name="step-3-search-index-and-query-based-access"></a>手順 3:検索インデックスとクエリ ベースのアクセス

処理が完了したら、エンリッチメントされたドキュメントで構成された、Azure Cognitive Search で完全にテキスト検索が可能な検索インデックスを取得できます。 [インデックスをクエリ](search-query-overview.md)することで、開発者やユーザーは、パイプラインによって生成されるエンリッチメントされたコンテンツにアクセスできます。 

![インデックスと検索のアイコン](./media/cognitive-search-intro/search-phase-blowup.png "インデックスと検索のアイコン")

インデックスは、Azure Cognitive Search のために作成する他のコンポーネント同様、カスタム アナライザーで補完したり、ファジー検索クエリを呼び出したり、フィルターを設定して検索したり、スコアリング プロファイルを使って検索結果の形式を調整することができます。

インデックスは、フィールド、属性、スコアリング プロファイルやシノニム マップなどの特定のインデックスに関連づけられているその他のコンストラクトを定義するインデックス スキーマから生成されます。 インデックスが定義され、作成されたら、新規あるいは更新されたソース ドキュメントに対応してインデックスを増分できます。 特定の変更では、完全な再構築が必要です。 スキーマ デザインが安定するまで、小規模のデータ セットを使用することをお勧めします。 詳細については、「[インデックスの再構成と再構築](search-howto-reindex.md)」をご覧ください。

**チェックリスト:一般的なワークフロー**

1. 代表的なサンプルに Azure ソース データをサブセット化します。 インデックスの作成には時間がかかるため、代表的な少量のデータ セットから始め、ソリューションの成熟度に応じて段階的に構築します。

1. データ取得のための接続文字列を指定する[データ ソース オブジェクト](/rest/api/searchservice/create-data-source)を Azure Cognitive Search で作成します。

1. エンリッチメント手順に従って[スキルセット](/rest/api/searchservice/create-skillset)を作成します。

1. [インデックス スキーマ](/rest/api/searchservice/create-index)を定義します。 *フィールド* コレクションには、ソース データからのフィールドが含まれます。 エンリッチメント中に作成された、コンテンツのために生成された値を保管するための追加フィールドも削除する必要があります。

1. データ ソース、スキルセット、およびインデックスを参照する[インデクサー](/rest/api/searchservice/create-indexer)を定義します。

1. インデクサー内に *outputFieldMappings* を追加します。 このセクションでは、(手順 4 の) インデックス スキーマ内の入力フィールドに、(手順 3 の) スキルセットからの出力をマッピングします。

1. Azure Cognitive Search のインデクサーを表現するため、前の手順で作成した *Create Indexer* 要求を (要求本文にインデクサー定義を含む POST 要求) 送信します。 この手順では、パイプラインを呼び出しインデクサーを実行する方法について説明します。

1. クエリを実行して結果を評価し、スキルセット、スキーマ、またはインデクサー構成を更新するためにコードを編集します。

1. パイプラインを再構築する前に[インデクサーをリセット](search-howto-reindex.md)します。

## <a name="next-steps"></a>次のステップ

+ [AI エンリッチメント ドキュメント リンク](cognitive-search-resources-documentation.md)
+ [例:AI エンリッチメント用のカスタム スキルを作成する (C#)](cognitive-search-create-custom-skill-example.md)
+ [クイック スタート: ポータルにおける AI エンリッチメントのチュートリアル](cognitive-search-quickstart-blob.md)
+ [チュートリアル:AI エンリッチメント API について](cognitive-search-tutorial-blob.md)
+ [ナレッジ ストア](knowledge-store-concept-intro.md)
+ [REST でナレッジ ストアを作成する](knowledge-store-create-rest.md)
+ [トラブルシューティングのヒント](cognitive-search-concept-troubleshooting.md)
