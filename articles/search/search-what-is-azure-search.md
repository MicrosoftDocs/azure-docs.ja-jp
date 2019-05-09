---
title: Azure Search サービスとは - Azure Search
description: Azure Search は、Microsoft が提供するフルマネージド ホストのクラウド検索サービスです。 機能の説明、開発ワークフロー、Azure Search と Microsoft の他の検索製品との比較、および開始する方法を確認します。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 02/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8893d8a55f77b54c9327e913af192ad39df46ba2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090017"
---
# <a name="what-is-azure-search"></a>Azure Search とは
Azure Search は、Web、モバイル、およびエンタープライズ アプリケーションのプライベートな異種コンテンツに対するリッチな検索機能を追加するための API とツールを開発者に提供する、サービスとしての検索クラウド ソリューションです。 クエリはユーザー定義インデックスを介して実行されます。

+ 複数のコンテンツ タイプとプラットフォームをソースとして、お客様のデータのみが含まれた検索コーパスを構築します。 
+ AI によるインデックス作成を利用して、画像ファイルからテキストと特徴を抽出したり、未加工のテキストからエンティティとキー フレーズを抽出したりします。
+ ファセット ナビゲーションおよびファセット フィルター、シノニム、オートコンプリートのほか、"もしかして" で自動修正される検索語句のテキスト分析を備えた、直感的な検索エクスペリエンスを作成します。
+ "近くを検索" の地理空間検索、英語以外のフルテキスト検索の言語アナライザー、検索順位のスコアリング ロジックを追加します。

機能は、情報の検索に固有の複雑さを感じさせないシンプルな [REST API](/rest/api/searchservice/) または [.NET SDK](search-howto-dotnet-sdk.md) を使って公開されます。 API だけでなく、Azure Portal では、管理とコンテンツ管理のサポートおよびプロトタイプの作成とインデックスのクエリのためのツールも提供されます。 サービスはクラウドで実行されるため、インフラストラクチャと可用性は Microsoft によって管理されます。

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>機能の概要

| Category | 機能 |
|----------|----------|
|フル テキスト検索とテキスト分析 | [フル テキスト検索](search-lucene-query-architecture.md)は、大部分の検索ベース アプリの主な用途です。 クエリは、サポートされている構文を使用して作成できます。 <br/><br/>[**単純なクエリ構文**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)では、論理演算子、語句検索演算子、後置演算子、優先順位演算子を使用できます。<br/><br/>[**Lucene クエリ構文**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)には、あいまい検索、近接検索、用語ブースト、正規表現の拡張機能を含む、簡単な構文でのすべての操作が含まれています。|
|コグニティブ検索 (プレビュー) | イメージおよびテキストの解析を目的とする [AI を使用したアルゴリズム](cognitive-search-concept-intro.md)は、インデックス作成パイプラインに利用して、生のコンテンツからテキスト情報を抽出できます。 [組み込みのスキル](cognitive-search-predefined-skills.md)の例には、光学式文字認識 (スキャン済みの JPEG を検索可能にする)、エンティティ認識 (組織、名前、または場所を識別する)、キー フレーズ認識などがあります。 [カスタム スキルをコーディング](cognitive-search-create-custom-skill-example.md)してパイプラインにアタッチすることもできます。 |
| データ統合 | Azure Search インデックスは、JSON データ構造として送信されていれば、すべてのソースからのデータを受け入れます。 <br/><br/> 必要に応じて、Azure でサポートされるデータ ソースについて、[**インデクサー**](search-indexer-overview.md)を使用して、[Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md)、または [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) で、プライマリ データ ストア内の検索可能コンテンツを自動的にクロールできます。 Azure Blob インデクサーは、"*ドキュメント クラッキング*" を実行して、Microsoft Office、PDF、HTML ドキュメントなどの[主要なファイル形式からテキストを抽出](search-howto-indexing-azure-blob-storage.md)することができます。 |
| Linguistic Analysis | アナライザーは、インデックス作成および検索操作中のテキスト処理に使用するコンポーネントです。 次の 2 つの種類があります。 <br/><br/>[**カスタム字句アナライザー**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)は、発音照合と正規表現を使用する複雑な検索クエリで使用されます。 <br/><br/>Lucene または Microsoft の[**言語アナライザー**](https://docs.microsoft.com/rest/api/searchservice/language-support)は、動詞の時制や名詞の性、不規則な複数形の名詞 (例: 'mouse' と 'mice')、二重複合語、(スペースを使用しない言語の) 改行などをインテリジェントに処理するために使用されます。 |
| 地理空間検索 | Azure Search は、地理的な場所を処理、フィルター、表示する機能を備えています。 Azure Search により、ユーザーは物理的な場所に対する検索結果の近接度に基づいてデータを探索できます。 詳細については、[このビデオを視聴する](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)か[このサンプルを確認](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)してください。 |
| ユーザー エクスペリエンス機能 | 検索バーでの先行入力クエリで、[**オートコンプリート (プレビュー)**](search-autocomplete-tutorial.md) を有効にすることができます。 <br/><br/>[**検索候補**](https://docs.microsoft.com/rest/api/searchservice/suggesters) も検索バーでの部分テキスト入力に使用できますが、結果はクエリ用語ではなくインデックス内の実際のドキュメントです。 <br/><br/>[**シノニム**](search-synonyms.md)は、ユーザーが代替用語を提供する必要がなく、クエリのスコープを暗黙的に拡張する同等の用語を関連付けます。 <br/><br/>1 つのクエリ パラメーターで[**ファセット ナビゲーション**](search-faceted-navigation.md)を有効にできます。 Azure Search は、ファセット ナビゲーション構造を返します。これをカテゴリ一覧の背後のコードとして使用すると、自律フィルター処理 (たとえば、価格帯やブランド別のカタログ品目のフィルター処理) を実現できます。 <br/><br/> [**フィルター**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)を使用することで、ファセット ナビゲーションをアプリケーションの UI に組み込み、クエリ形成を拡張し、ユーザーまたは開発者が指定した条件に基づいてフィルター処理することができます。 フィルターを作成するには、OData 構文を使用します。<br/><br/> [**ヒットの強調表示**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)は、検索結果内の一致するキーワードにテキスト書式を適用します。 強調表示されるスニペットを返すフィールドを選択できます。<br/><br/>[**並べ替え**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)は、インデックス スキーマを介して、複数のフィールドで利用でき、クエリ時に 1 つの検索パラメーターで切り替えることができます。<br/><br/> [**ページング**](search-pagination-page-layout.md)とスロットルは、Azure Search が検索結果に適用する微調整された制御によって簡単に行うことができます。  
| 関連性 | [**簡単なスコアリング**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)は Azure Search の主な利点です。 スコアリング プロファイルを使用して、ドキュメント自体の値の関数として、関連性をモデル化できます。 たとえば、新しい製品や割り引き製品を検索結果の上位に表示することが望ましい場合があります。 あるいは、追跡記録し、個別に保存しておいた顧客の検索傾向に基づいてパーソナライズされたスコアリングのタグを利用し、スコアリング プロファイルを作成できます。 |
| 監視と報告 | [**検索トラフィック分析**](search-traffic-analytics.md)を収集し、分析して、ユーザーが検索ボックスに入力した内容からインサイトを明らかにします。 <br/><br/>1 秒あたりのクエリ数、待ち時間、スロットルに関するメトリックが取得され、ポータル ページで報告されます。追加の構成は必要ありません。 また、インデックスとドキュメントの数を簡単に監視できるため、必要に応じて容量を調整できます。 詳細については、[サービス管理](search-manage.md)に関する記事をご覧ください。 |
| プロトタイピングと検査のためのツール | ポータルでは、[**データのインポート ウィザード**](search-import-data-portal.md)を使用してインデクサーを構成したり、インデックス デザイナーを使用してインデックスを設定したりできます。また、[**Search エクスプローラー**](search-explorer.md)を使用してクエリをテストしたり、スコアリング プロファイルを調整したりできます。 また、任意のインデックスを開いてスキーマを表示することもできます。 |
| インフラストラクチャ | **可用性の高いプラットフォーム**により、極めて信頼性の高い検索サービス エクスペリエンスを確保します。 適切に拡張された場合に、 [Azure Search は 99.9% の SLA を実現](https://azure.microsoft.com/support/legal/sla/search/v1_0/)します。<br/><br/> エンド ツー エンドのソリューションとして**完全に管理され、スケーラブルな** Azure Search では、インフラストラクチャ管理をまったく必要としません。 サービスは 2 次元での拡張によって、ニーズに合わせてカスタマイズでき、より大きなドキュメント ストレージ、より高いクエリ負荷、またはその両方を処理できます。

## <a name="how-to-use-azure-search"></a>Azure Search を使用する方法
### <a name="step-1-provision-service"></a>手順 1:サービスのプロビジョニング
[Azure portal](https://portal.azure.com/) または [Azure Resource Management API](/rest/api/searchmanagement/) を使用して、Azure Search サービスをプロビジョニングできます。 他のサブスクライバーと共有する無料サービス、または自分のサービスのみで使用するリソース専用の[有料レベル](https://azure.microsoft.com/pricing/details/search/)を選択できます。 有料レベルでは、2 つの面でサービスを拡張できます。 

- 大量のクエリ負荷を処理できるように、レプリカを追加して容量を拡張する   
- より多くのドキュメントに対応できるように、パーティションを追加して記憶域を拡大する 

ドキュメント ストレージとクエリのスループットを個別に処理することによって、運用環境の要件に基づいてリソースの割り当てを調整できます。

### <a name="step-2-create-index"></a>手順 2:インデックスの作成
検索可能なコンテンツをアップロードする前に、まず Azure Search インデックスを定義する必要があります。 インデックスは、データを保持し、検索クエリを受け付けることができるデータベース テーブルに似ています。 データベースのフィールドに似た、検索するドキュメントの構造を反映するように、マップするインデックス スキーマを定義します。

スキーマは Azure Portal で作成するか、[.NET SDK](search-howto-dotnet-sdk.md) または [REST API](/rest/api/searchservice/) を使用してプログラムによって作成できます。

### <a name="step-3-load-data"></a>手順 3:データを読み込む
インデックスを定義したら、コンテンツをアップロードする準備が完了します。 プッシュ モデルまたはプル モデルを使用できます。

プル モデルは、外部データ ソースからデータを取得します。 データへの接続、読み取り、シリアル化など、データ取り込みの諸側面を効率化および自動化する "*インデクサー*" を通じてサポートされます。 [インデクサー](/rest/api/searchservice/Indexer-operations)は、Azure Cosmos DB、Azure SQL Database、Azure Blob Storage、および Azure VM でホストされている SQL Server で使用できます。 インデクサーは、オンデマンドで、またはスケジュールされたデータ更新のために構成できます。

プッシュ モデルは SDK または REST API によって提供され、更新したドキュメントをインデックスに送信するために使用されます。 JSON 形式を使用して、事実上すべてのデータセットからデータをプッシュできます。 データの読み込み方法については、「[ドキュメントの追加、更新、削除](/rest/api/searchservice/addupdate-or-delete-documents)」または「[.NET SDK の使用方法](search-howto-dotnet-sdk.md)」を参照してください。

### <a name="step-4-search"></a>手順 4:Search
インデックスを入力したら、REST API または .NET SDK によって簡単な HTTP 要求を使用して、サービス エンドポイントに[検索クエリを発行](/rest/api/searchservice/Search-Documents)できます。

## <a name="how-it-compares"></a>他のソリューションとの比較

お客様から、Azure Search が他の検索に関連するソリューションと比較してどうなのかよくお問い合わせいただきます。 主な相違点を次の表に示します。

| 比較対象 | 主な相違点 |
|-------------|-----------------|
|Bing | [Bing Web Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) は Bing.com のインデックスで送信された語句と一致するものを検索します。 インデックスは、公開サイトの HTML、XML、および他の Web コンテンツから構築されます。 同じ基板上に構築されている [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) は、個々の Web サイトに範囲指定して、各 Web コンテンツ タイプに対して同じクローラー テクノロジを提供します。<br/><br/>Azure Search では、所有するデータおよびドキュメントを入力して定義したインデックスを多くの場合さまざまなソースから検索します。 Azure Search は[インデクサー](search-indexer-overview.md)により複数のデータ ソースに対するクローラー機能を備えていますが、インデックス スキーマに準拠している任意の JSON ドキュメントを 1 つの統合された検索可能なリソースにプッシュできます。 |
|データベース検索 | 多くのデータベース プラットフォームには、組み込みの検索エクスペリエンスが含まれます。 SQL Server には[フル テキスト検索](https://docs.microsoft.com/sql/relational-databases/search/full-text-search)があります。 Cosmos DB や同種のテクノロジには、クエリ可能なインデックスがあります。 検索とストレージを結合する製品を評価するとき、採用する方法を決めるのが困難な場合があります。 多くのソリューションでは両方が採用され、ストレージには DBMS が、専用の検索機能には Azure Search が使用されています。<br/><br/>DBMS 検索と比較すると、Azure Search は異種ソースからのコンテンツを格納し、[56 言語](https://docs.microsoft.com/rest/api/searchservice/language-support)で言語対応のテキスト処理 (語幹検索、レンマ化、語形) などの専用のテキスト処理機能を提供します。 また、スペルミスの単語のオート コレクト、[シノニム](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations)、[候補](https://docs.microsoft.com/rest/api/searchservice/suggestions)、[スコアリング コントロール](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[ファセット](https://docs.microsoft.com/azure/search/search-filters-facets)、および[カスタムトークン化](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)もサポートしています。 Azure Search の[フル テキスト検索エンジン](search-lucene-query-architecture.md)は情報取得の業界標準である Apache Lucene に基づいて構築されています。 Azure Search は逆インデックスの形式でデータを保持しますが、真のデータ ストレージの代わりになることはほとんどありません。 詳しくは、こちらの[フォーラム投稿](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)をご覧ください。 <br/><br/>リソース使用率も、このカテゴリで別途考慮する必要があります。 インデックス付けと一部のクエリ操作は、多くの場合、計算を大量に使用します。 DBMS からクラウドの専用ソリューションに検索をオフロードすることにより、トランザクション処理用のシステム リソースを保持します。 さらに、検索を外部化することで、クエリのボリュームに合わせてスケールを簡単に調整できます。|
|専用の検索ソリューション | 全機能を持つ専用の検索を使用する場合、最後の比較カテゴリはオンプレミス ソリューションかクラウド サービスかです。 多くの検索テクノロジには、インデックスの作成とクエリ パイプラインの管理、豊富なクエリとフィルター構文へのアクセス、ランクと関連性の管理、自主的な高度な検索の機能が用意されています。 <br/><br/>クラウド サービスは、オーバーヘッドとメンテナンスが最小限で済み、スケールが調整可能なターンキー ソリューションを望むお客様に最適の選択肢です。 <br/><br/>クラウドの枠組みでは、複数のプロバイダーがフルテキスト検索、地理空間検索、検索入力の程度のあいまいさを処理する機能など、同等の基本機能を備えるソリューションを提供しています。 使用環境に最も合うものを判断する場合、通常は、[特化した機能](#feature-drilldown)か、API、ツール、管理の容易さと全体的な単純さが決め手になります。 |

クラウド プロバイダーの中で Azure Search は、Azure 上のコンテンツ ストアとデータベースに対するフルテキスト検索のワークロードに関して最も強力で、情報の取得とコンテンツのナビゲーションの両方を主に検索に依存するアプリに適しています。 

以下のような大きな強みがあります。

+ インデックス層での Azure データ統合 (クローラー)
+ 中央管理のための Azure Portal
+ Azure のスケール、信頼性、世界クラスの可用性
+ 56 の言語での堅実なフルテキスト検索を実現するアナライザーによる言語分析とカスタム分析
+ [検索中心のアプリに共通のコア機能](#feature-drilldown): スコアリング、ファセット、検索候補、シノニム、地理空間検索など。

> [!Note]
> Azure 以外のデータ ソースは完全にサポートされていますが、インデクサーではなく、コードをより大量に使用するプッシュ手法に依存します。 Microsoft 提供の API を使用して、任意の JSON ドキュメント コレクションを Azure Search インデックスにパイプすることができます。

Microsoft のお客様は、オンライン カタログ、基幹業務プログラム、およびドキュメント検索アプリケーションを含む、Azure Search の機能を最大限利用できます。

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

多くのタスクを実行できるポータルに対し、Azure Search は既存のアプリケーションに検索機能を統合したいと考えている開発者向けのものです。 次のプログラミング インターフェイスが用意されています。

|プラットフォーム |説明 |
|-----|------------|
|[REST](/rest/api/searchservice/) | あらゆるプログラミング プラットフォームと言語 (Xamarin、Java、JavaScript など) でサポートされている HTTP コマンド|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 用の .NET ラッパーにより、C# と .NET Framework をターゲットとするその他のマネージ コード言語で効率的なコーディングを実現できます。 |

## <a name="free-trial"></a>無料試用版
Azure サブスクライバーは [Free レベルでサービスをプロビジョニング](search-create-service-portal.md)できます。

サブスクライバーでない場合は、[Azure アカウントを無料で開設できます](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。 Azure の有料サービスを試用する際に、クレジットを入手してください。 このクレジットを使い切った後に、アカウントを保持して、[無料の Azure サービス](https://azure.microsoft.com/free/)を使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。

または、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。 

## <a name="how-to-get-started"></a>ファースト ステップ

1. [無料サービス](search-create-service-portal.md)を作成します。 すべてのクイック スタートとチュートリアルは、無料のサービスで完了できます。

2. [組み込みのツールを使用してインデックス作成とクエリを行う方法に関するチュートリアル](search-get-started-portal.md)の手順を完了します。 重要な概念を学習して、ポータルで提供される情報に親しみます。

3. .NET と REST API のいずれかを使用してコーディングを進めます。

   + [.NET SDK を使用する方法](search-howto-dotnet-sdk.md)では、マネージド コードにおける主なワークフローについて説明します。  
   + [REST API の概要](https://github.com/Azure-Samples/search-rest-api-getting-started)では、REST API の使用と同じ手順を示します。 さらに、次のクイック スタートを使用して、Postman または Fiddler から REST API を呼び出すこともできます([Azure Search REST API の探索](search-fiddler.md)に関するクイック スタート)。

## <a name="watch-this-video"></a>次の動画をご覧ください

検索エンジンは、モバイル アプリ、Web、企業データ ストアでの情報の取得を推進する一般的な要素です。 Azure Search は、大規模な商用 Web サイトに類似する検索エクスペリエンスを作成するためのツールを提供します。

プログラム マネージャー Liam Cavanagh によるこの 9 分間のビデオでは、検索エンジンを統合することでアプリにもたらされるメリットについて説明しています。 短いデモでは、Azure Search の重要な機能と一般的なワークフローについて説明します。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0 ～ 3 分: 主要な機能とユースケースについて説明します。
+ 3 ～ 4 分: サービスのプロビジョニングについて説明します。 
+ 4 ～ 6 分: データのインポート ウィザードで組み込みの不動産データセットを使ってインデックスを作成する方法について説明します。
+ 6 分から 9 分: Search エクスプローラーとさまざまなクエリについて説明します。
