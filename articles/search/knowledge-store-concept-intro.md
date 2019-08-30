---
title: ナレッジ ストア (プレビュー) の概要 - Azure Search
description: Azure ストレージにエンリッチメントされたドキュメントを送信し、そこで Azure Search および他のアプリケーション内のエンリッチメントされたドキュメントを表示、整形、および使用することができます。
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: f4308cf0309725fc0ba3b5feb047d04af2ebbe66
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638182"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Azure Search 内のナレッジ ストアとは

> [!Note]
> ナレッジ ストアはプレビュー段階にあり、運用環境での使用は意図していません。 [REST API バージョン 2019-05-06-Preview](search-api-preview.md) でこの機能を提供します。 現時点で .NET SDK のサポートはありません。
>

ナレッジ ストアは、Azure Search の機能であり、AI ベースのインデックス作成パイプライン [(コグニティブ検索)](cognitive-search-concept-intro.md) によって作成される、エンリッチメントされたドキュメントとメタデータを保存します。 エンリッチメントされたドキュメントとは、Cognitive Services のリソースを使用して抽出、構造化、および分析されたコンテンツから作成される、パイプラインの出力のことです。 標準的な AI ベースのパイプラインでは、エンリッチメントされたドキュメントは一時的なものであり、インデックス作成時にのみ使用され、その後破棄されます。 ナレッジ ストアを使用すると、ドキュメントは後続の評価や探索のために保存され、下流のデータ サイエンス ワークロードへの入力になる可能性があります。 

過去にコグニティブ検索を使用したことがある場合は、スキルセットを使用して、一連のエンリッチメントを通じてドキュメントを移動することは既にわかっています。 結果は、Azure Search インデックスまたは (このプレビューの新機能である) ナレッジ ストア内のプロジェクションです。 2 つの出力 (検索インデックスとナレッジ ストア) は、互いに物理的に異なります。 同じコンテンツを共有していますが、まったく異なる方法で格納および使用されます。

物理的には、ナレッジ ストアはパイプラインの構成方法に応じて、Azure Table Storage または Blob Storage として Azure Storage アカウントで作成されます。 Azure Storage に接続できるすべてのツールまたはプロセスは、ナレッジ ストアのコンテンツを使用できます。

プロジェクションとは、ナレッジ ストア内にデータを構築するためのメカニズムのことです。 たとえば、プロジェクションを使用して、出力を 1 つの BLOB として保存するのか、関連するテーブルのコレクションとして保存するのかを選択できます。 ナレッジ ストアのコンテンツを表示する簡単な方法は、Azure Storage の組み込みの [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) を使用することです。

![パイプライン ダイアグラム内のナレッジ ストア](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "パイプライン ダイアグラム内のナレッジ ストア")

ナレッジ ストアを使用するには、インデックス作成パイプラインに段階的な操作を定義するスキルセットに `knowledgeStore` 要素を追加します。 実行中に、Azure Search によって Azure ストレージ アカウント内にスペースが作成され、パイプラインによって作成された定義とコンテンツがそこに入力されます。

## <a name="benefits-of-knowledge-store"></a>ナレッジ ストアのメリット

ナレッジ ストアでは、BLOB などの非構造化および半構造化データ ファイル、分析が実行されたイメージ ファイル、または新しいフォームに整形された構造化データから収集された構造体、コンテキスト、および実際のコンテンツが提供されます。 このプレビュー用に記述された[ステップ バイ ステップ チュートリアル](knowledge-store-howto.md)では、高密度の JSON ドキュメントが下部構造にパーティション分割される方法、新しい構造体に再構築される方法、または機械学習やデータ サイエンスのワークロードなどのダウンストリーム プロセスで使用可能にされる方法を直接確認できます。

AI ベースのインデックス作成パイプラインで何を生成できるかを確認するのに便利ですが、ナレッジ ストアの真価はデータを整形する機能にあります。 基本的なスキルセットから開始し、反復処理して構造のレベルを追加し、それを Azure Search 以外のアプリ内でも使用できるように新しい構造に結合できます。

ナレッジ ストアのメリットの列挙には以下が含まれます。

+ 検索以外の[分析およびレポート作成ツール](#tools-and-apps)内でエンリッチメントされたドキュメントを使用する。 Power Query を使用した Power BI は魅力的な選択肢ですが、Azure ストレージに接続できる任意のツールまたはアプリで、作成するナレッジ ストアからプルできます。

+ 手順とスキルセットの定義をデバッグ中に、AI インデックス作成パイプラインを調整する。 ナレッジ ストアによって、AI インデックス作成パイプライン内のスキルセット定義の製品が示されます。 これらの結果を使用すると、エンリッチメントがどのようになるかを正確に確認できるので、より優れたスキルセットを設計できます。 Azure ストレージ内で [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) を使用して、ナレッジ ストアのコンテンツを表示できます。

+ データを新しいフォームに整形する。 整形はスキルセット内で体系化されていますが、ポイントは、スキルセットでこの機能を提供できるようになったことです。 Azure Search 内の[シェーパー スキル](cognitive-search-skill-shaper.md)は、このタスクに対応するために拡張されました。 整形により、関係を維持しながら、データの使用目的に合致したプロジェクションを定義することができます。

> [!Note]
> Cognitive Services を使用した AI ベースのインデックス作成に慣れていませんか? Azure Search は Cognitive Services の Vision および Language 機能と統合され、イメージ ファイルの光学式文字認識 (OCR)、エンティティの認識、テキスト ファイルからのキー フレーズの抽出などを使用してソース データが抽出およびエンリッチメントされます。 詳しくは、[コグニティブ検索](cognitive-search-concept-intro.md)に関する記事をご覧ください。

## <a name="create-a-knowledge-store"></a>ナレッジ ストアの作成

ナレッジ ストアは、スキルセットの定義の一部です。 このプレビューでは、これを作成するには、`api-version=2019-05-06-Preview` を使用した REST API またはポータル内の**データのインポート** ウィザードが必要です。

次の JSON では、`knowledgeStore` を指定します。これは、インデクサー (示されていません) によって呼び出されるスキルセットの一部です。 `knowledgeStore` 内のプロジェクション内の指定によって、Azure ストレージ内にテーブルとオブジェクトのどちらが作成されるかが決まります。

既に AI ベースのインデックス作成に慣れている場合、スキルセット定義によって、エンリッチメントされた各ドキュメントの作成、編成、内容が決定されます。

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>ナレッジ ストアを支援するコンポーネント

ナレッジ ストアを作成するには、次のサービスと成果物が必要です。

### <a name="1---source-data"></a>1 - ソース データ

エンリッチメントするデータまたはドキュメントは、Azure Search インデクサーでサポートされている Azure データ ソース内に存在する必要があります。 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure BLOB Storage](search-howto-indexing-azure-blob-storage.md)

[Azure Table Storage](search-howto-indexing-azure-tables.md) は、ナレッジ ストア内の送信データに使用できますが、AI ベースのインデックス作成パイプラインへの受信データのリソースとして使用することはできません。

### <a name="2---azure-search-service"></a>2 - Azure Search サービス

データ エンリッチメントに使用するオブジェクトを作成および構成する場合も、Azure Search サービスと REST API が必要です。 ナレッジ ストアを作成するための REST API は `api-version=2019-05-06-Preview` です。

Azure Search ではインデクサー機能が提供されます。インデクサーはプロセス全体のエンド ツー エンドの駆動に使用され、結果として Azure ストレージ内に永続化されエンリッチメントされたドキュメントが生成されます。 インデクサーでは、データ ソース、インデックス、およびスキルセットが使用されます。ナレッジ ストアの作成と設定にはこれらすべてが必要です。

| Object | REST API | 説明 |
|--------|----------|-------------|
| データ ソース | [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | エンリッチメントされたドキュメントを作成するために使用されるソース データを提供する外部 Azure データ ソースを識別するリソースです。  |
| スキルセット | [スキルセットの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | エンリッチメント パイプラインでインデックス作成時に使用される[組み込みのスキル](cognitive-search-predefined-skills.md)と[カスタム コグニティブ スキル](cognitive-search-custom-skill-interface.md)の使用を調整するリソース。 |
| index | [インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Azure Search インデックスを表すスキーマです。 ソース データ内のフィールドやエンリッチメント フェーズで作成されたフィールドにマッピングされるインデックス内のフィールド (たとえば、エンティティ認識によって作成された組織名のためのフィールド)。 |
| インデクサー | [インデクサーの作成 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | データ ソース、スキルセット、ソースからのフィールドの関連付け、ターゲット インデックスまでの中間データ構造、およびインデックス自体などの、インデックス作成時に使用されるコンポーネントを定義するリソースです。 インデクサーを実行すると、データの取り込みやエンリッチメントがトリガーされます。 出力は、インデックス スキーマを基に作成され、ソース データが入力され、スキルセットでエンリッチメントされた検索インデックスです。  |

### <a name="3---cognitive-services"></a>3 - Cognitive Services

スキルセット内で指定されたエンリッチメントは、Cognitive Services の Computer Vision および Language 機能に基づいています。 Cognitive Services 機能は、スキルセットを通じたインデックス作成時に利用されます。 スキルセットはスキルの合成であり、スキルは特定の Computer Vision および Language 機能にバインドされます。 Cognitive Services を統合するには、[Cognitive Services リソースをスキルセットにアタッチ](cognitive-search-attach-cognitive-services.md)します。

### <a name="4---storage-account"></a>4 - ストレージ アカウント

Azure ストレージ アカウントでは、Azure Search によって、スキルセットの構成方法に応じて BLOB コンテナーまたはテーブルが作成されます。 データのソースが Azure Blob または Table Storage の場合は、既に設定しています。 そうでない場合は、Azure ストレージ アカウントを作成する必要があります。 Azure ストレージ内のテーブルとオブジェクトには、AI ベースのインデックス作成パイプラインによって作成されるエンリッチメントされたドキュメントが含まれます。

ストレージ アカウントは、スキルセット内で指定されます。 `api-version=2019-05-06-Preview` では、スキルセット定義には、アカウント情報を提供できるようにナレッジ ストア定義が含まれます。

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 - アクセスと使用

エンリッチメントがストレージ内に存在した後、Azure Blob または Table Storage に接続する任意のツールまたはテクノロジを使用して、コンテンツを探索、分析、または使用できます。 次の一覧が開始点です。

+ エンリッチメントされたドキュメントの構造とコンテンツを表示する [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。 これは、ナレッジ ストアのコンテンツを表示するためのベースライン ツールと考えてください。

+ 自然言語クエリ用の [Power Query を使用した Power BI](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e)、または数値データがある場合はレポートおよび分析ツールを使用します。

+ さらに操作するための [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)。

+ [コグニティブ検索](cognitive-search-concept-intro.md)を使用してインデックスを作成したコンテンツに対するフルテキスト検索用の Azure Search インデックス。

## <a name="document-persistence"></a>ドキュメントの永続性

ストレージ アカウント内のエンリッチメントは、Azure Table Storage 内のテーブル、または Azure Blob Storage 内のオブジェクトとして表現できます。 保存されたエンリッチメントは、他のデータベースやツールにデータを読み込むためのソースとして使用できることを思い出してください。

+ Table Storage は、表形式のデータのスキーマ対応表現が必要な場合に便利です。 要素を新しい方法で整形または再結合する場合は、Table Storage によって必要な細分性が提供されます。

+ Blob Storage では、ドキュメントごとに 1 つの包括的な JSON 表現が作成されます。 両方のストレージ オプションを 1 つのスキルセット内で使用して、さまざまな表現を取得できます。

+ Azure Search では、コンテンツがインデックス内に永続化されます。 シナリオが検索に関連しない場合、たとえば、別のツールで分析することが目的の場合は、パイプラインによって作成されるインデックスを削除することができます。 しかし、インデックスを保持し、コンテンツと対話するために [Search エクスプローラー](search-explorer.md)などの組み込みツールを (Storage Explorer と分析アプリの後ろの) 第 3 のメディアとして使用することもできます。

ドキュメントのコンテンツと共に、エンリッチメントされたドキュメントには、エンリッチメントを生成したスキルセット バージョンのメタデータが含まれます。  

## <a name="inside-a-knowledge-store"></a>ナレッジ ストアの内部

ナレッジ ストアは、注釈キャッシュとプロジェクションで構成されます。 "*キャッシュ*" は、スキルからの結果をキャッシュして変更を追跡するためにサービスによって内部的に使用されます。 "*プロジェクション*" では、目的の用途に一致するエンリッチメントのスキーマと構造を定義します。 ナレッジ ストアごとに 1 つのキャッシュがありますが、プロジェクションは複数あります。 

キャッシュは常に BLOB コンテナーですが、プロジェクションはテーブルまたはオブジェクトとして明確に示すことができます。

+ オブジェクトとして、プロジェクションは Blob Storage にマップされ、プロジェクションはコンテナーに保存されます。コンテナー内には、データ サイエンス パイプラインなどのシナリオ用に JSON でのオブジェクトまたは階層表現があります。

+ テーブルとして、プロジェクションは Table Storage にマップされます。 表形式では、機械学習用のデータ フレームとしてのデータの分析やエクスポートなどのシナリオの関係が維持されます。 エンリッチメントされたプロジェクションは、他のデータ ストアに簡単にインポートできます。 

組織内のさまざまな立場に対応するために、ナレッジ ストアには複数のプロジェクションを作成できます。 開発者は、エンリッチメントされたドキュメントの完全な JSON 表現にアクセスする必要がありますが、データ サイエンティストやアナリストは、スキルセットによって整形されたきめ細かいまたはモジュール形式のデータ構造を必要とする可能性があります。

たとえば、エンリッチメント プロセスの目標の 1 つがモデルのトレーニングに使用されるデータセットも作成することである場合、オブジェクト ストアへのデータの投影は、データ サイエンス パイプライン内でデータを使用する 1 つの方法です。 または、エンリッチメントされたドキュメントに基づく簡単な Power BI ダッシュボードを作成する場合は、表形式のプロジェクションがうまく機能します。

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>どこから始めるか

学習目的には無料のサービスをお勧めしますが、無料のトランザクションの数はサブスクリプションごとに 1 日あたり 20 のドキュメントまでに制限されることに注意してください。

複数のサービスを使用する場合は、パフォーマンスを最適化し、コストを最小限に抑えるために、すべてのサービスを同じリージョン内に作成します。 受信データまたは同じリージョン内の別のサービスへの送信データの帯域幅には課金されません。

**手順 1:[Azure Search リソースの作成](search-create-service-portal.md)** 

**手順 2:[Azure ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**手順 3:[Cognitive Services リソースの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**手順 4:[ポータルの概要](cognitive-search-quickstart-blob.md)- または - [REST と Postman を使用したサンプル データの概要](knowledge-store-howto.md)** 

REST `api-version=2019-05-06-Preview` を使用して、ナレッジ ストアを含む AI ベースのパイプラインを構築できます。 最新のプレビュー API では、スキルセット オブジェクトによって `knowledgeStore` 定義が提供されます。

## <a name="takeaways"></a>重要なポイント

ナレッジ ストアには、さまざまな利点があります。たとえば、検索以外のシナリオでのエンリッチメントされたドキュメントの使用、コスト管理、エンリッチメント プロセスでの誤差の管理などが有効になるという利点がありますが、これらに限定されません。 これらすべての機能は、「[ナレッジ ストアの使用を開始する方法](knowledge-store-howto.md)」の説明に従ってスキルセットにストレージ アカウントを追加し、更新された式言語を使用するだけで使用可能になります。 

## <a name="next-steps"></a>次の手順

エンリッチメントされたドキュメントを作成する最も簡単なアプローチは、**データのインポート** ウィザードを使用することです。

> [!div class="nextstepaction"]
> [クイック スタート:ポータルでコグニティブ検索を使ってみる](cognitive-search-quickstart-blob.md)
