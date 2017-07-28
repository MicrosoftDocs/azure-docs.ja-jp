---
title: "Azure Search とは何か | Microsoft Docs"
description: "Azure Search は完全に管理されたホスト型クラウド検索サービスです。 この機能の概要で詳しく説明します。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 99685748120b40cd02328f6ece0b8dde55042d61
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017

---
# <a name="what-is-azure-search"></a>Azure Search とは
Azure Search は、サーバーとインフラストラクチャの管理を Microsoft に委任するクラウドの Search-as-a-service (サービスとしての検索) ソリューションです。データを取り込んだら、Web サイトやモバイル アプリケーションに検索機能を追加して、すぐに利用を開始できます。 簡単な [REST API](/rest/api/searchservice/) または [.NET SDK](search-howto-dotnet-sdk.md) を使用してアプリケーションに強力な検索エクスペリエンスを簡単に追加できるので、検索インフラストラクチャを管理する必要も、検索のエキスパートになる必要もありません。

<a name="feature-drilldown"></a>

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>アプリケーションやサイトに強力な検索エクスペリエンスを埋め込む 

Azure Search の機能について説明します。

### <a name="full-text-search-and-text-analysis"></a>フルテキスト検索とテキスト分析

[フル テキスト検索](search-lucene-query-architecture.md)は、大部分の検索ベース アプリの主な用途です。 Azure Search では、論理演算子、語句検索演算子、後置演算子、優先順位の演算子を備えた[簡単なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)を使用してクエリを作成できます。 さらに、 [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) では、あいまい検索、近接検索、項目ブースト、および正規表現を有効にすることができます。 さらに、Azure Search は、アプリケーションで発音照合と正規表現を使用して、複雑な検索クエリを処理できる[カスタム字句アナライザー](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)もサポートしています。

### <a name="language-support"></a>言語のサポート

Azure Search は、[56 の異なる言語](https://docs.microsoft.com/rest/api/searchservice/language-support)に対応する字句アナライザーをサポートしています。 Lucene アナライザーと Microsoft アナライザー (Office および Bing での長年にわたる自然言語処理により改良されています) の両方を使用することで、Azure Search はアプリケーションの検索ボックス内のテキストを分析し、動詞の時制や名詞の性、不規則な複数形の名詞 (例: 'mouse' と 'mice')、二重複合語、(スペースを使用しない言語の) 改行などをインテリジェントに処理できます。

### <a name="data-integration"></a>データ統合

JSON データ構造をプッシュして Azure Search インデックスを設定できます。 また、サポートされているデータ ソースに対しては、[インデクサー](search-indexer-overview.md)を使用して、[Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-documentdb.md)、または [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) を自動的にクロールし、検索インデックスの内容とプライマリ データ ストアを同期できます。

*ドキュメント クラッキング*を使用して、Microsoft Office、PDF、HTML ドキュメントなどの[主要なファイル形式に対してインデックスを作成](search-howto-indexing-azure-blob-storage.md)できます。

### <a name="search-experience"></a>検索エクスペリエンス

+ オートコンプリート入力検索バーと先行入力クエリで、**検索候補**を有効にすることができます。 [インデックス内の実際のドキュメントが提案されます。](https://docs.microsoft.com/rest/api/searchservice/suggesters) 

+ [1 つのクエリ パラメーター](https://docs.microsoft.com/azure/search/search-faceted-navigation)で**ファセット ナビゲーション**を有効にできます。 Azure Search は、ファセット ナビゲーション構造を返します。これをカテゴリ一覧の背後のコードとして使用すると、自律フィルター処理 (たとえば、価格帯やブランド別のカタログ品目のフィルター処理) を実現できます。

+ **フィルター**を使用することで、ファセット ナビゲーションをアプリケーションの UI に組み込み、クエリ形成を拡張し、ユーザーまたは開発者が指定した条件に基づいてフィルター処理することができます。 フィルターを作成するには、[OData 構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)を使用します。

+ **ヒットの強調表示**は、[検索結果内の一致するキーワードに視覚的な書式を適用](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)します。 強調表示されるスニペットを返すフィールドを選択できます。

+ **簡単なスコアリング** は Azure Search の主な利点です。 [スコアリング プロファイル](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)を使用して、ドキュメント自体の値の関数として、関連性をモデル化できます。 たとえば、新しい製品や割り引き製品を検索結果の上位に表示することが望ましい場合があります。 あるいは、追跡記録し、個別に保存しておいた顧客の検索傾向に基づいてパーソナライズされたスコアリングのタグを利用し、スコアリング プロファイルを作成できます。

+ **並べ替え** は、インデックス スキーマを介して、複数のフィールドで利用でき、クエリ時に 1 つの検索パラメーターで切り替えることができます。

+ **ページング** とスロットルは、Azure Search が検索結果に適用する [微調整された制御によって簡単に行うことができます](search-pagination-page-layout.md) 。  

### <a name="geosearch"></a>地理空間検索

Azure Search は、地理的な場所をインテリジェントに処理、フィルター、表示する機能を備えています。 Azure Search により、ユーザーは物理的な場所に対する検索結果の近接度に基づいてデータを探索できます。 「 [チャンネル 9: Azure Search と地理空間データ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)」という動画でそのしくみが説明されています。

### <a name="monitoring-and-reporting"></a>監視と報告

+ **検索トラフィック分析**を[収集し、分析して](search-traffic-analytics.md)、ユーザーが検索ボックスに入力した内容からインサイトを明らかにします。

+ 1 秒あたりのクエリ数、待ち時間、スロットルに関するメトリックが取得され、ポータル ページで報告されます。追加の構成は必要ありません。 また、インデックスとドキュメントの数を簡単に監視できるため、必要に応じて容量を調整できます。 

### <a name="tools-for-prototyping-and-inspection"></a>プロトタイピングと検査のためのツール

ポータルでは、**データのインポート** ウィザードを使ってインデクサーを構成したり、インデックス デザイナーを使ってインデックスを設定したりできます。また、**Search エクスプローラー**を使ってクエリをテストしたり、スコアリング プロファイルを調整したりできます。 また、任意のインデックスを開いてスキーマを表示することもできます。

<a name="cloud-service-advantage"></a>

### <a name="cloud-service-advantages"></a>クラウド サービスの利点

+ **高可用性** により、極めて信頼性の高い検索サービス エクスペリエンスを確保します。 適切に拡張された場合に、 [Azure Search は 99.9% の SLA を実現](https://azure.microsoft.com/support/legal/sla/search/v1_0/)します。

+ エンド ツー エンドのソリューションとして**完全に管理され、スケーラブルな** Azure Search では、インフラストラクチャ管理をまったく必要としません。 サービスは 2 次元での拡張によって、ニーズに合わせてカスタマイズでき、より大きなドキュメント ストレージ、より高いクエリ負荷、またはその両方を処理できます。

## <a name="how-it-works"></a>動作のしくみ
### <a name="step-1-provision-service"></a>手順 1: サービスのプロビジョニング
[Azure Portal](https://portal.azure.com/) または [Azure Resource Management API](/rest/api/searchmanagement/) を使用して、Azure Search サービスを高速化できます。 他のサブスクライバーと共有する無料サービス、または自分のサービスのみで使用するリソース専用の[有料レベル](https://azure.microsoft.com/pricing/details/search/)を選択できます。

有料レベルでは、2 つの面でサービスを拡張できます。 

- 大量のクエリ負荷を処理できるように、レプリカを追加して容量を拡張する   
- より多くのドキュメントに対応できるように、パーティションを追加して記憶域を拡大する 

ドキュメント ストレージとクエリのスループットを個別に処理することによって、運用環境の要件に基づいてリソースの割り当てを調整できます。

### <a name="step-2-create-index"></a>手順 2: インデックスの作成
検索可能なコンテンツをアップロードする前に、まず Azure Search インデックスを定義する必要があります。 インデックスは、データを保持し、検索クエリを受け付けることができるデータベース テーブルに似ています。 データベースのフィールドに似た、検索するドキュメントの構造を反映するように、マップするインデックス スキーマを定義します。

スキーマは Azure Portal で作成するか、[.NET SDK](search-howto-dotnet-sdk.md) または [REST API](/rest/api/searchservice/) を使用してプログラムによって作成できます。

### <a name="step-3-index-data"></a>手順 3: データのインデックス登録
インデックスを定義したら、コンテンツをアップロードする準備が完了します。 プッシュ モデルまたはプル モデルを使用できます。

プル モデルは、外部データ ソースからデータを取得します。 データへの接続、読み取り、シリアル化など、データ取り込みの諸側面を効率化および自動化する "*インデクサー*" を通じてサポートされます。 [インデクサー](/rest/api/searchservice/Indexer-operations)は、Azure Cosmos DB、Azure SQL Database、Azure Blob Storage、および Azure VM でホストされている SQL Server で使用できます。 インデクサーは、オンデマンドで、またはスケジュールされたデータ更新のために構成できます。

プッシュ モデルは SDK または REST API によって提供され、更新したドキュメントをインデックスに送信するために使用されます。 JSON 形式を使用して、事実上すべてのデータセットからデータをプッシュできます。 データの読み込み方法については、「[ドキュメントの追加、更新、削除](/rest/api/searchservice/addupdate-or-delete-documents)」または「[.NET SDK の使用方法](search-howto-dotnet-sdk.md)」を参照してください。

### <a name="step-4-search"></a>手順 4: 検索
インデックスを入力したら、REST API または .NET SDK によって簡単な HTTP 要求を使用して、サービス エンドポイントに[検索クエリを発行](/rest/api/searchservice/Search-Documents)できます。

## <a name="how-it-compares"></a>他のソリューションとの比較

お客様からたびたび、[Azure Search のフルテキスト検索](search-lucene-query-architecture.md)と、ご使用のデータベース製品の[フル テキスト検索](https://en.wikipedia.org/wiki/Full_text_search)の違いについて質問を受けることがあります。 その回答として、Azure Search の言語機能は、Lucene クエリ、Lucene および Microsoft の言語アナライザー、音声その他の特殊な入力向けのカスタム アナライザー、および検索インデックス内の複数のソースのデータをマージする機能をサポートしているため、豊富で柔軟性に富んでいます、とお答えできます。 

他には、検索ソリューションが検索環境全体を扱っている点も挙げることができます。 たとえば、結果のスコアリングのカスタマイズ、自律フィルター処理のファセット ナビゲーション、検索結果の強調表示、先行入力クエリによる検索候補を使用できます。 

クエリ アクティビティを監視および把握するためのツールも、検索ソリューションの決定で考慮することができます。 たとえば、Azure Search では、クリックスルー率、上位検索、クリックされない検索などの指標に関する[検索トラフィック分析](search-traffic-analytics.md)をサポートしています。 検索がアドオンの製品では、これらの機能は用意されていないこともあります。

リソース使用率も、別途考慮する必要があります。 自然言語検索は、多くの場合、コンピューターに高い負荷をかけます。 検索操作の負荷を Azure Search で負担することで、コンピューター リソースをトランザクション処理のために確保しているお客様もいらっしゃいます。 検索を外部化することで、クエリのボリュームに合わせてスケールを簡単に調整できます。

専用の検索を使用することに決めたら、次は、クラウド サービスとオンプレミス サーバーのどちらを使用するかを決定します。 クラウド サービスは、[オーバーヘッドと保守が最小限で済み、スケールが調整可能なターンキー ソリューション](#cloud-service-advantage)を望むお客様に最適の選択肢です。

クラウドの枠組みでは、複数のプロバイダーがフルテキスト検索、地理空間検索、検索入力の程度のあいまいさを処理する機能など、同等の基本機能を備えるソリューションを提供しています。 使用環境に最も合うものを判断する場合、通常は、[特化した機能](#feature-drilldown)か、API、ツール、管理の容易さと全体的な単純さが決め手になります。

クラウド プロバイダーの中で Azure Search は、Azure 上のコンテンツ ストアとデータベースに対するフルテキスト検索のワークロードに関して最も強力で、情報の取得とコンテンツのナビゲーションの両方を主に検索に依存するアプリに適しています。 以下のような大きな強みがあります。

+ インデックス層での Azure データ統合 (クローラー)
+ 中央管理のための Azure Portal
+ Azure のスケール、信頼性、世界クラスの可用性
+ 56 の言語での堅実なフルテキスト検索を実現するアナライザーによる言語分析とカスタム分析
+ [検索中心のアプリに共通のコア機能](#feature-drilldown): スコアリング、ファセット、検索候補、シノニム、地理空間検索など。

> [!Note]
> 補足すると、Azure 以外のデータ ソースは完全にサポートされていますが、インデクサーではなく、コードをより大量に使用するプッシュ手法に依存します。 Microsoft 提供の API を使用して、任意の JSON ドキュメント コレクションを Azure Search インデックスにパイプすることができます。

Microsoft のお客様は、オンライン カタログ、基幹業務プログラム、およびドキュメント検索アプリケーションを含む、Azure Search の機能を最大限利用できます。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

多くのタスクを実行できるポータルに対し、Azure Search は既存のアプリケーションに検索機能を統合したいと考えている開発者向けのものです。 次のプログラミング インターフェイスが用意されています。

|Platform |説明 |
|-----|------------|
|[REST](/rest/api/searchservice/) | あらゆるプログラミング プラットフォームと言語 (Xamarin、Java、JavaScript など) でサポートされている HTTP コマンド|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 用の .NET ラッパーにより、C# と .NET Framework をターゲットとするその他のマネージ コード言語で効率的なコーディングを実現できます。 |

## <a name="free-trial"></a>無料試用版
Azure サブスクライバーは [Free レベルでサービスをプロビジョニング](search-create-service-portal.md)できます。

サブスクライバーでない場合は、[Azure アカウントを無料で開設できます](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。 Azure の有料サービスを試用する際に、クレジットを入手してください。 このクレジットを使い切った後に、アカウントを保持して、[無料の Azure サービス](https://azure.microsoft.com/free/)を使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。

または、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) こともできます - MSDN サブスクリプションにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。 

## <a name="watch-a-short-video"></a>短いビデオの視聴

検索エンジンは、モバイル アプリ、Web、企業データ ストアでの情報の取得を推進する一般的な要素です。 Azure Search は、大規模な商用 Web サイトに類似する検索エクスペリエンスを作成するためのツールを提供します。

プログラム マネージャー Liam Cavanagh によるこの 9 分間のビデオでは、検索エンジンを統合することでアプリにもたらされるメリットについて説明しています。 短いデモでは、Azure Search の重要な機能と一般的なワークフローについて説明します。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0 ～ 3 分: 主要な機能とユースケースについて説明します。
+ 3 ～ 4 分: サービスのプロビジョニングについて説明します。 
+ 4 ～ 6 分: データのインポート ウィザードで組み込みの不動産データセットを使ってインデックスを作成する方法について説明します。
+ 6 ～ 9 分: Search エクスプローラーとさまざまなクエリについて説明します。



