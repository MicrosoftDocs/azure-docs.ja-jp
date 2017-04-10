---
title: "Azure Search とは何か | Microsoft Docs"
description: "カスタム Web サイト、アプリ、企業データ、ファイル ストアの検索バー向けの高度な検索機能を備えた検索エンジン。"
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 8f4947ba2641bc8a39a71945ff2a6818d1ff7830
ms.lasthandoff: 03/08/2017

---
# <a name="what-is-azure-search"></a>Azure Search とは

Azure Search は、インテリジェントな検索動作を備えたプログラム可能な専用の検索エンジンを提供します。Azure Search は、Web サイト、アプリ、企業のファイルまたはデータ ストアのコンテンツ向けの検索バーで利用できます。 

![Azure Search を利用する検索バー](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

ほとんどの Web アプリとモバイル アプリでは、充実した検索エクスペリエンスを提供する必要があります。 検索語句の自動補完、誤字の修正、意味的に同一でも表記が異なる入力 (たとえば、"車" と "自動車") に基づく一致機能を備えたフルテキスト検索機能は、それを支える技術が複雑であるにもかかわらず、ほぼ最低ラインの条件です。 加えて、スケール、信頼性、検索とバックエンドのデータ ストア間の同期に関する運用要件も同等に重要です。 

Azure Search は包括的な機能を提供するため、これらの検索要件と運用要件の両方を満たすことができます。

![一般的な検索機能を備えた検索バーとカスタム検索ページ](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>動作のしくみ

Azure Search を使用するには、Azure サブスクリプションで無料サービスまたは有料サービスをプロビジョニングします。次に、検索可能なコンテンツを含むインデックスを作成し、読み込みます。最後に、API を呼び出して、検索要求を発行し、結果を処理します。 大規模な専用リソースを必要とする場合は、有料サービスが必要です。

|レベル |Description |
|-----|------------|
|[Free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | 迅速にプロビジョニングでき、小規模なワークロード (一般的には学習や評価のシナリオ) に使用できる共有サービスです。 |
|[課金対象](search-sku-tier.md) | さまざまな価格レベルの幅広いデプロイ構成に対応するように、Basic から Standard 高密度まで容量が段階的に用意されている専用サービスです。|

Azure Search は、Microsoft の管理されたサービスとしてクラウドで動作します。 Azure Search は、任意のアプリケーション プラットフォーム上でカスタム コードと統合できます。 完全に管理されたサービスとプライベート コンテンツは、グローバルな利用可能性、プログラミング可能性、拡張性、回復可能性を備えます。 

専用サービスは、Microsoft Azure の [99.9% のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)に基づいて、24 時間 365 日動作します。

## <a name="how-it-compares"></a>他のソリューションとの比較

いくつかのクラウド サービス プロバイダーから、カスタム アプリで検索バーを動作させる機能を備えたカスタムの検索エンジンが提供されています。 フルテキスト検索、地理空間検索、検索入力の程度のあいまいさを処理する機能など、同等の基本機能を備えるソリューションもあります。 使用環境に最も合うものを判断する場合、通常は、[特化した機能](#feature-drilldown)か、API、ツール、管理の容易さと全体的な単純さが決め手になります。

他の検索ソリューションと比較した場合、Azure Search は、Azure 上のコンテンツ ストアとデータベースに対するフルテキスト検索のワークロードに関して最も強力であり、情報の取得とコンテンツのナビゲーションを主に検索に依存するアプリに必要な機能層を備えています。 以下のような大きな強みがあります。

+ インデックス層での Azure データ統合 (クローラー)
+ 中央管理のための Azure Portal
+ Azure のスケール、信頼性、世界クラスの可用性
+ 56 の言語での堅実なフルテキスト検索を実現するアナライザーによる言語分析とカスタム分析
+ 関連性、ファセット、検索候補、シノニム、地理空間検索など (後述)、検索中心のアプリに共通のコア機能。

> [!Note]
> Azure 以外のデータ ソースが完全にサポートされます。 任意の JSON ドキュメント コレクションを Azure Search インデックスにパイプすることができます。

Azure Search の機能を最大限利用できるユース ケースには、オンライン カタログ、基幹業務プログラム、およびドキュメント検索アプリケーションが含まれます。

### <a name="feature-drilldown"></a>機能のドリルダウン

#### <a name="full-text-search-and-text-analysis"></a>フルテキスト検索とテキスト分析

論理演算子、語句検索演算子、後置演算子、優先順位の演算子を備えた[簡単なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)を使用してクエリを作成できます。 さらに、 [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) では、あいまい検索、近接検索、項目ブースト、および正規表現を有効にすることができます。 さらに、Azure Search は、アプリケーションで発音照合と正規表現を使用して、複雑な検索クエリを処理できる[カスタム字句アナライザー](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)もサポートしています。

#### <a name="language-support"></a>言語のサポート

Azure Search は、[56 の異なる言語](https://docs.microsoft.com/rest/api/searchservice/language-support)に対応する字句アナライザーをサポートしています。 Lucene アナライザーと Microsoft アナライザー (Office および Bing での長年にわたる自然言語処理により改良されています) の両方を使用することで、Azure Search はアプリケーションの検索ボックス内のテキストを分析し、動詞の時制や名詞の性、不規則な複数形の名詞 (例: 'mouse' と 'mice')、二重複合語、(スペースを使用しない言語の) 改行などをインテリジェントに処理できます。

#### <a name="data-integration"></a>データ統合

JSON データ構造をプッシュして Azure Search インデックスを設定できます。 また、サポートされているデータ ソースに対しては、[インデクサー](search-indexer-overview.md)を使用して、Azure SQL Database、Azure DocumentDB、または [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) を自動的にクロールし、検索インデックスの内容とプライマリ データ ストアを同期できます。

**ドキュメント クラッキング**を使用して、Microsoft Office、PDF、HTML ドキュメントなどの[主要なファイル形式に対してインデックスを作成](search-howto-indexing-azure-blob-storage.md)できます。

#### <a name="search-experience"></a>検索エクスペリエンス

+ オートコンプリート入力検索バーと先行入力クエリで、**検索候補**を有効にすることができます。 [インデックス内の実際のドキュメントが提案されます。](https://docs.microsoft.com/rest/api/searchservice/suggesters) 

+ [1 つのクエリ パラメーター](https://docs.microsoft.com/azure/search/search-faceted-navigation)で**ファセット ナビゲーション**を有効にできます。 Azure Search は、ファセット ナビゲーション構造を返します。これをカテゴリ一覧の背後のコードとして使用すると、自律フィルター処理 (たとえば、価格帯やブランド別のカタログ品目のフィルター処理) を実現できます。

+ **フィルター**を使用することで、ファセット ナビゲーションをアプリケーションの UI に組み込み、クエリ形成を拡張し、ユーザーまたは開発者が指定した条件に基づいてフィルター処理することができます。 フィルターを作成するには、[OData 構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)を使用します。

+ **ヒットの強調表示**は、[検索結果内の一致するキーワードに視覚的な書式を適用](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)します。 強調表示されるスニペットを返すフィールドを選択できます。

+ **簡単なスコアリング** は Azure Search の主な利点です。 [スコアリング プロファイル](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)を使用して、ドキュメント自体の値の関数として、関連性をモデル化できます。 たとえば、新しい製品や割り引き製品を検索結果の上位に表示することが望ましい場合があります。 あるいは、追跡記録し、個別に保存しておいた顧客の検索傾向に基づいてパーソナライズされたスコアリングのタグを利用し、スコアリング プロファイルを作成できます。

+ **並べ替え** は、インデックス スキーマを介して、複数のフィールドで利用でき、クエリ時に&1; つの検索パラメーターで切り替えることができます。

+ **ページング** とスロットルは、Azure Search が検索結果に適用する [微調整された制御によって簡単に行うことができます](search-pagination-page-layout.md) 。  

#### <a name="geosearch"></a>地理空間検索

Azure Search は、地理的な場所をインテリジェントに処理、フィルター、表示する機能を備えています。 Azure Search により、ユーザーは指定した場所との検索結果の近接度に基づいて、または特定の地理的領域に基づいて、データを探索できます。 「 [チャンネル 9: Azure Search と地理空間データ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)」という動画でそのしくみが説明されています。

#### <a name="cloud-service-advantages"></a>クラウド サービスの利点

+ **高可用性** により、極めて信頼性の高い検索サービス エクスペリエンスを確保します。 適切に拡張された場合に、 [Azure Search は 99.9% の SLA を実現](https://azure.microsoft.com/support/legal/sla/search/v1_0/)します。

+ **完全に管理** された Azure Search ではインフラストラクチャ管理をまったく必要としません。 サービスは&2; 次元での拡張によって、ニーズに合わせてカスタマイズでき、より大きなドキュメント ストレージ、より高いクエリ負荷、またはその両方を処理できます。

#### <a name="monitoring-and-reporting"></a>監視と報告

+ **検索トラフィック分析**を[収集し、分析して](search-traffic-analytics.md)、ユーザーが検索ボックスに入力した内容からインサイトを明らかにします。

+ 1 秒あたりのクエリ数、待ち時間、スロットルに関するメトリックが取得され、ポータル ページで報告されます。追加の構成は必要ありません。 また、インデックスとドキュメントの数を簡単に監視できるため、必要に応じて容量を調整できます。 

#### <a name="tools-for-prototyping-and-inspection"></a>プロトタイピングと検査のためのツール

ポータルでは、**データのインポート** ウィザードを使ってインデクサーを構成したり、インデックス デザイナーを使ってインデックスを設定したりできます。また、**Search エクスプローラー**を使うとアカウントの Azure Portal からすべてのインデックスに対してクエリを直接発行できるため、クエリをテストしたりスコアリング プロファイルを調整したりできます。 また、任意のインデックスを開いてスキーマを表示することもできます。

## <a name="how-to-get-started"></a>ファースト ステップ

無料のサービスから始め、[サンプルの組み込みデータを使ってインデックスを作成し、クエリを実行](search-get-started-portal.md)します。 すべてのタスクにポータルを使用することができます。ポータルを使用すると、コードを記述する前に Azure Search を手軽に試すことができます。

1. Azure サブスクライバーは [Free レベルでサービスをプロビジョニング](search-create-service-portal.md)できます。

  サブスクライバー以外の方は、[無料の Azure アカウント](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)を開き、無料クレジットを使って有料の Azure サービスを試用できます。 このクレジットを使い切った後、アカウントを保持して、Websites などの無料の Azure サービスを使用できます。 明示的に設定を変更して課金を求めない限り、クレジット カードに課金されることはありません。

  また、[MSDN サブスクライバーの特典を有効にします](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。 

2. [組み込みの不動産サンプル データセット](search-get-started-portal.md#create-index)に対して**データのインポート** ウィザードを実行します。

  このウィザードは、ほとんどの Azure データ ソースからのインデックスの生成と読み込みに対応します。 ウィザードで明確にサポートされていないデータ ソースの場合はコードが必要になります。

3. [Search エクスプローラー](search-get-started-portal.md#query-index)を使って、インデックスを照会します。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

多くのタスクを実行できるポータルに対し、Azure Search は既存のアプリケーションに検索機能を統合したいと考えている開発者向けのものです。 次のプログラミング インターフェイスが用意されています。

|Platform |説明 |
|-----|------------|
|[REST ()](https://docs.microsoft.com/rest/api/searchservice/) | あらゆるプログラミング プラットフォームと言語 (Xamarin、Java、JavaScript など) でサポートされている HTTP コマンド|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 用の .NET ラッパーにより、C# と .NET Framework をターゲットとするその他のマネージ コード言語で効率的なコーディングを実現できます。 |

## <a name="watch-a-short-video"></a>短いビデオの視聴

検索エンジンは、モバイル アプリ、Web、企業データ ストアでの情報の取得を推進する一般的な要素です。 Azure Search は、大規模な商用 Web サイトに類似する検索エクスペリエンスを作成するためのツールを提供します。 この 9 分間のビデオでは、プログラム マネージャーの Liam Cavanagh が、検索エンジンを統合することでアプリにもたらされるメリット、Azure Search の主な機能、一般的なワークフローについて説明します。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0 ～&3; 分: 主要な機能とユースケースについて説明します。
+ 3 ～&4; 分: サービスのプロビジョニングについて説明します。 
+ 4 ～&6; 分: データのインポート ウィザードで組み込みの不動産データセットを使ってインデックスを作成する方法について説明します。
+ 6 ～&9; 分: Search エクスプローラーとさまざまなクエリについて説明します。


