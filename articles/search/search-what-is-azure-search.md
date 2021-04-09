---
title: Azure Cognitive Search の概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search は、Microsoft が提供するフル マネージドのクラウド検索サービスです。 ユース ケース、開発ワークフロー、Microsoft の他の検索製品との比較、および開始方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549891"
---
# <a name="what-is-azure-cognitive-search"></a>Azure Cognitive Search とは

Azure Cognitive Search ([旧称 Azure Search](whats-new.md#new-service-name)) は、Web、モバイル、エンタープライズの各アプリケーション内のプライベートな異種コンテンツに対する豊富な検索エクスペリエンスを構築するための API とツールを開発者に提供するクラウド検索サービスです。 

検索は、ユーザーにコンテンツを表示するすべてのアプリの基本となるものです。一般的なシナリオには、カタログやドキュメントの検索、e コマース サイトの検索、データ サイエンスのための知識マイニングなどがあります。 Cognitive Search の API とアーキテクチャにより、あらゆるソリューションに高度な情報取得を簡単に追加できます。

検索サービスには次のコンポーネントがあります。

+ フルテキスト検索の検索エンジン
+ ユーザーが所有するインデックス付けされたコンテンツの永続的ストレージ
+ コンテンツのインデックス作成とクエリのための API
+ (省略可能) [AI ベースのエンリッチメント](cognitive-search-concept-intro.md) (画像、生の非構造化テキスト、アプリケーション ファイルから検索可能なコンテンツを作成する)
+ (省略可能) データ、機械学習または AI、監視、およびセキュリティを目的とした他の Azure サービスとの統合
+ (省略可能) 関連性を向上させるための[セマンティック検索 (プレビュー)](semantic-search-overview.md) の実装

アーキテクチャ上、検索サービスは、インデックスが設定されていないデータを含む外部データ ストアと、検索インデックスにクエリ要求を送信して応答を処理するクライアント アプリとの間に配置されます。

![Azure Cognitive Search のアーキテクチャ](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search のアーキテクチャ")

検索は、対外的に他の Azure サービスと統合できます。これには、Azure データ ソースからのデータのインジェストと取得を自動化する "*インデクサー*" という形式があるほか、Cognitive Services で提供されている AI (画像やテキストの分析など) や、Azure Machine Learning 内で作成したり Azure Functions 内にラップしたりするカスタム AI を組み込んだ "*スキルセット*" という形式があります。

## <a name="inside-a-search-service"></a>検索サービスの内部

検索サービス自体は、"*インデックスの作成*" と "*クエリの実行*" の 2 つが主なワークロードとなります。 

+ [インデックス作成](search-what-is-an-index.md)は、取り込みのプロセスです。コンテンツを検索サービスに読み込んで検索可能にします。 内部では、受信テキストはトークンへと処理され、高速スキャンできるように逆インデックスに格納されます。 JSON ドキュメント形式であればどのようなテキストでもアップロードできます。

  加えて、コンテンツに雑多なファイルが含まれている場合は、[コグニティブ スキル](cognitive-search-working-with-skillsets.md)を通じて "*AI エンリッチメント*" を追加するという選択肢があります。 AI エンリッチメントは、アプリケーション ファイルに埋め込まれているテキストを抽出できるほか、非テキスト ファイルから、そのコンテンツを分析することによってテキストと構造を推測することもできます。 

  分析機能を備えたスキルは、Microsoft によってあらかじめ定義されているものと、ユーザーによって作成されるカスタム スキルとがあります。 その後の分析と変換によって、以前は存在していなかった新しい情報と構造が生成されるため、さまざまな検索およびナレッジ マイニング シナリオに対応した高度な実用性が実現します。

+ [クエリ](search-query-overview.md)が実行できるようになるのは、検索可能なテキストがインデックスに投入された後です。クライアント アプリが検索サービスにクエリ要求を送信して応答を処理します。 すべてのクエリは、お客様のサービス内で作成、所有、保存する検索インデックスに対して実行されます。 クライアント アプリでの検索エクスペリエンスは Azure Cognitive Search の API を使用して定義され、関連性のチューニング、オートコンプリート、シノニムの一致、あいまい一致、パターン マッチング、フィルター、および並べ替えを含めることができます。

機能は、情報の検索に固有の複雑さを感じさせないシンプルな [REST API](/rest/api/searchservice/) または [.NET SDK](search-howto-dotnet-sdk.md) を使って公開されます。 また、Azure portal を使用して、サービスの管理とコンテンツの管理を行うこともできます。インデックスとスキルセットのプロトタイプ作成やクエリを実行するためのツールが用意されています。 サービスはクラウドで実行されるため、インフラストラクチャと可用性は Microsoft によって管理されます。

## <a name="why-use-cognitive-search"></a>Cognitive Search を使用する理由

Azure Cognitive Search は、次のアプリケーション シナリオに適しています。

+ ユーザー定義のプライベートな検索インデックスへの異種コンテンツの統合。

+ 関連性のチューニング、ファセット ナビゲーション、フィルター (地理空間検索)、同意語マッピング、オートコンプリートなど、検索に関連した機能を容易に実装。

+ Azure Blob Storage や Cosmos DB に格納されている未区分の大きなテキストや画像ファイル、アプリケーション ファイルを検索可能な JSON ドキュメントに変換。 これは、外部処理を追加する[コグニティブ スキル](cognitive-search-concept-intro.md)を通してインデックス時に行われます。

+ 言語分析またはカスタム テキスト分析を追加。 英語以外のコンテンツがある場合、Azure Cognitive Search では、Lucene アナライザーと Microsoft の自然言語プロセッサの両方がサポートされます。 また、生コンテンツの特殊な処理 (分音記号のフィルター処理や、文字列内のパターンの認識と保持など) を実行するようにアナライザーを構成することもできます。

特定の機能の詳細については、「[Azure Cognitive Search の機能](search-features-list.md)」を参照してください

## <a name="how-to-get-started"></a>ファースト ステップ

次の 4 つのステップにより、主要な検索機能をエンド ツー エンドで試すことができます。

1. 共有される Free レベルの [**検索サービスを作成**](search-create-service-portal.md)します。または [課金対象レベル](https://azure.microsoft.com/pricing/details/search/)を選択して、自分のサービスでのみ使用される専用リソースを確保することもできます。 すべてのクイックスタートとチュートリアルの作業は、共有サービスで完結できます。

1. ポータル、[REST API](/rest/api/searchservice/create-index)、[.NET SDK](search-howto-dotnet-sdk.md)、または他の SDK を使用して [**検索インデックスを作成**](search-what-is-an-index.md)します。 検索可能なコンテンツの構造は、インデックス スキーマによって定義されます。

1. ["プッシュ" モデル](tutorial-optimize-indexing-push-api.md)を使用して任意のソースから JSON ドキュメントをプッシュして [**コンテンツをアップロード**](search-what-is-data-import.md)します。または、ソース データが Azure 上にある場合は ["プル" モデル (インデクサー)](search-indexer-overview.md) を使用します。

1. ポータルの [検索エクスプローラー](search-explorer.md)、[REST API](search-get-started-rest.md)、[.NET SDK](/dotnet/api/azure.search.documents.searchclient.search)、または他の SDK を使用して、[**インデックスを照会**](search-query-overview.md)します。

最初の探索として [**データ インポート ウィザード**](search-get-started-portal.md)と組み込みの Azure データ ソースから始めることによって、インデックスの作成、読み込み、照会を数分で行うことができます。

複雑なソリューションやカスタム ソリューションについては、Cognitive Search テクノロジに関する深い専門知識を備える [**パートナーにお問い合わせください**](resource-partners-knowledge-mining.md)。

## <a name="compare-search-options"></a>検索オプションの比較

お客様から、Azure Cognitive Search が他の検索に関連するソリューションと比較してどうなのかよくお問い合わせいただきます。 主な相違点を次の表に示します。

| 比較対象 | 主な相違点 |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search) は、SharePoint 内のコンテンツに対してクエリを実行する必要がある、Microsoft 365 の認証済みユーザーを対象としています。 すぐに使用できる検索環境として提供され、管理者によって有効にされて構成が行われます。Microsoft や他のソースから提供されているコネクタを通じて外部コンテンツを受け入れることができます。 それが目的に合致していれば、Microsoft Search と Microsoft 365 は、試す価値のある選択肢となります。<br/><br/>これに対し、Azure Cognitive Search は、所有するデータやドキュメントを、多くの場合さまざまなソースから取り込んで定義したインデックスに対してクエリを実行します。 Azure Cognitive Search は[インデクサー](search-indexer-overview.md)により複数の Azure データ ソースに対するクローラー機能を備えていますが、インデックス スキーマに準拠している任意の JSON ドキュメントを 1 つの統合された検索可能なリソースにプッシュできます。 また、機械学習と字句解析器を含むようにインデックス作成パイプラインをカスタマイズすることもできます。 Cognitive Search は、より大きなソリューションのプラグイン コンポーネントとなるように作成されているため、プラットフォームにかかわらず、ほぼすべてのアプリに検索機能を統合することができます。|
|Bing | [Bing Web Search API](../cognitive-services/bing-web-search/index.yml) は Bing.com のインデックスで送信された語句と一致するものを検索します。 インデックスは、公開サイトの HTML、XML、および他の Web コンテンツから構築されます。 同じ基板上に構築されている [Bing Custom Search](/azure/cognitive-services/bing-custom-search/) は、個々の Web サイトに範囲指定して、各 Web コンテンツ タイプに対して同じクローラー テクノロジを提供します。<br/><br/>Cognitive Search では、インデックスを自分で定義してデータを投入することができます。 [インデクサー](search-indexer-overview.md)を使用して Azure データ ソース上のデータをクロールしたり、インデックスに適合する JSON ドキュメントを検索サービスにプッシュしたりすることができます。 |
|データベース検索 | 多くのデータベース プラットフォームには、組み込みの検索エクスペリエンスが含まれます。 SQL Server には[フル テキスト検索](/sql/relational-databases/search/full-text-search)があります。 Cosmos DB や同種のテクノロジには、クエリ可能なインデックスがあります。 検索とストレージを結合する製品を評価するとき、採用する方法を決めるのが困難な場合があります。 多くのソリューションでは両方が採用され、ストレージには DBMS が、専用の検索機能には Azure Cognitive Search が使用されています。<br/><br/>DBMS 検索と比較すると、Azure Cognitive Search は異種ソースからのコンテンツを格納し、[56 言語](/rest/api/searchservice/language-support)で言語対応のテキスト処理 (語幹検索、レンマ化、語形) などの専用のテキスト処理機能を提供します。 また、スペルミスの単語のオート コレクト、[シノニム](/rest/api/searchservice/synonym-map-operations)、[候補](/rest/api/searchservice/suggestions)、[スコアリング コントロール](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[ファセット](./search-filters-facets.md)、および[カスタムトークン化](/rest/api/searchservice/custom-analyzers-in-azure-search)もサポートしています。 Azure Cognitive Search の[フル テキスト検索エンジン](search-lucene-query-architecture.md)は情報取得の業界標準である Apache Lucene に基づいて構築されています。 ただし、Azure Cognitive Search では逆インデックスの形式でデータを保持しますが、真のデータ ストレージの代わりにはならないため、その役割として使用することはお勧めしません。 詳しくは、こちらの[フォーラム投稿](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)をご覧ください。 <br/><br/>リソース使用率も、このカテゴリで別途考慮する必要があります。 インデックス付けと一部のクエリ操作は、多くの場合、計算を大量に使用します。 DBMS からクラウドの専用ソリューションに検索をオフロードすることにより、トランザクション処理用のシステム リソースを保持します。 さらに、検索を外部化することで、クエリのボリュームに合わせてスケールを簡単に調整できます。|
|専用の検索ソリューション | 全機能を持つ専用の検索を使用する場合、最後の比較カテゴリはオンプレミス ソリューションかクラウド サービスかです。 多くの検索テクノロジには、インデックスの作成とクエリ パイプラインの管理、豊富なクエリとフィルター構文へのアクセス、ランクと関連性の管理、自主的な高度な検索の機能が用意されています。 <br/><br/>クラウド サービスは、オーバーヘッドとメンテナンスが最小限で済み、スケールが調整可能なターンキー ソリューションを望むお客様に最適の選択肢です。 <br/><br/>クラウドの枠組みでは、複数のプロバイダーがフルテキスト検索、地理空間検索、検索入力の程度のあいまいさを処理する機能など、同等の基本機能を備えるソリューションを提供しています。 使用環境に最も合うものを判断する場合、通常は、[特化した機能](search-features-list.md)か、API、ツール、管理の容易さと全体的な単純さが決め手になります。 |

クラウド プロバイダーの中で Azure Cognitive Search は、Azure 上のコンテンツ ストアとデータベースに対するフルテキスト検索のワークロードに関して最も強力で、情報の取得とコンテンツのナビゲーションの両方を主に検索に依存するアプリに適しています。 

以下のような大きな強みがあります。

+ インデックス層での Azure データ統合 (クローラー)
+ Azure Private Link との統合によって、インターネットを回避するというセキュリティ要件をサポート
+ AI 処理との統合によって、検索不可能な各種コンテンツがテキスト検索可能に。
+ 56 の言語での堅実なフルテキスト検索を実現するアナライザーによる言語分析とカスタム分析
+ [重要な機能](search-features-list.md): 豊富な照会言語、関連性チューニング、ファセット、オートコンプリート、同意語、地理空間検索、結果の構成。
+ Azure のスケール、信頼性、世界クラスの可用性

Microsoft のお客様は、オンライン カタログ、基幹業務プログラム、およびドキュメント検索アプリケーションを含む、Azure Cognitive Search の機能を最大限利用できます。

## <a name="watch-this-video"></a>次の動画をご覧ください

この 15 分間のビデオでは、プログラム マネージャー Luis Cabrera が Azure Cognitive Search を紹介しています。

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]