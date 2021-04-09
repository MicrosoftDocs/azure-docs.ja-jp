---
title: 機能の概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の機能カテゴリについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 7615e1dbce040002d9d1a13502637f78100965fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99538909"
---
# <a name="features-of-azure-cognitive-search"></a>Azure Cognitive Search の機能

Azure Cognitive Search は、フルテキスト検索エンジン、検索インデックスの永続的ストレージ、より多くのテキストと構造を抽出するためにインデックス作成時に使用される統合 AI、および API とツールを提供します。 次の表は、カテゴリ別の機能をまとめたものです。 Cognitive Search と他の検索テクノロジの比較については、「[Azure Cognitive Search とは](search-what-is-azure-search.md)」を参照してください。

## <a name="indexing-features"></a>インデックス作成機能

| カテゴリ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特徴 |
|-------------------|----------|
| データ ソース | 検索インデックスは、JSON ドキュメントとして送信されていれば、すべてのソースからのテキストを受け入れることができます。 <br/><br/> [**インデクサー**](search-indexer-overview.md)では、サポートされている Azure データ ソースからのデータ インジェストが自動化され、JSON のシリアル化が処理されます。 [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md)、または [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) に接続して、プライマリ データ ストア内の検索可能なコンテンツを抽出します。 Azure Blob インデクサーは、"*ドキュメント クラッキング*" を実行して、Microsoft Office、PDF、HTML ドキュメントなどの [主要なファイル形式からテキストを抽出](search-howto-indexing-azure-blob-storage.md)することができます。 |
| 階層データ構造と入れ子になったデータ構造 | [**複合型**](search-howto-complex-data-types.md)とコレクションでは、検索インデックス内の事実上すべての種類の JSON 構造をモデル化できます。 一対多と多対多のカーディナリティは、コレクション、複合型、および複合型のコレクションを通じてネイティブに表現できます。|
| Linguistic Analysis | アナライザーは、インデックス作成および検索操作中のテキスト処理に使用するコンポーネントです。 既定では、汎用の標準 Lucene アナライザーを使用するか、言語アナライザー、ユーザーが構成したカスタム アナライザー、または必要な形式でトークンを生成する別の定義済みアナライザーで既定をオーバーライドすることができます。 <br/><br/>Lucene または Microsoft の [**言語アナライザー**](index-add-language-analyzers.md)は、動詞の時制や名詞の性、不規則な複数形の名詞 (例: 'mouse' と 'mice')、二重複合語、(スペースを使用しない言語の) 改行などをインテリジェントに処理するために使用されます。 <br/><br/>[**カスタム字句アナライザー**](index-add-custom-analyzers.md)は、発音照合や正規表現などの複雑なクエリ形式で使用されます。<br/><br/> |

## <a name="ai-enrichment-and-knowledge-mining"></a>AI エンリッチメントとナレッジ マイニング

| カテゴリ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特徴 |
|-------------------|----------|
|インデックス作成中の AI 処理 | イメージおよびテキスト分析のための [**AI エンリッチメント**](cognitive-search-concept-intro.md)をインデックス作成パイプラインに適用して、生のコンテンツからテキスト情報を抽出できます。 [組み込みのスキル](cognitive-search-predefined-skills.md)の例には、光学式文字認識 (スキャン済みの JPEG を検索可能にする)、エンティティ認識 (組織、名前、または場所を識別する)、キー フレーズ認識などがあります。 [カスタム スキルをコーディング](cognitive-search-create-custom-skill-example.md)してパイプラインにアタッチすることもできます。 [Azure Machine Learning で作成されたスキルを統合](./cognitive-search-tutorial-aml-custom-skill.md)することもできます。 |
| 検索以外のシナリオでの分析と使用のためのエンリッチされたコンテンツの格納 | [**ナレッジ ストア**](knowledge-store-concept-intro.md) は、インデックス作成パイプラインの代替出力です。 トークン化された用語をインデックスに送信する代わりに、構成に応じて、Azure Blob Storage またはTable Storage に格納されているナレッジ ストアに、インデックス作成パイプラインによって作成された強化されたドキュメントを送信できます。 ナレッジ ストアは、AI ベースのインデックス作成 (スキルセット) から作成されます。 ナレッジ ストアの目的は、下流の分析または処理をサポートすることです。 ナレッジ ストアに新しい情報と構造を追加したら、それを機械学習処理に使用したり、Power BI から接続してデータを探索したりすることができます。<br/><br/> |
| キャッシュされたコンテンツ | [**インクリメンタル エンリッチメント (プレビュー)**](cognitive-search-incremental-indexing-conceptual.md) では、パイプラインに対する特定の編集によって変更されたドキュメントのみに処理が制限され、パイプラインの変更されていない部分に対してはキャッシュされたコンテンツが使用されます。 |

## <a name="query-and-user-experience"></a>クエリとユーザー エクスペリエンス

| カテゴリ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特徴 |
|-------------------|----------|
|自由形式のテキスト検索 | [**フルテキスト検索**](search-lucene-query-architecture.md)は、大部分の検索ベース アプリの主な用途です。 クエリは、サポートされている構文を使用して作成できます。 <br/><br/>[**単純なクエリ構文**](query-simple-syntax.md)では、論理演算子、語句検索演算子、後置演算子、優先順位演算子を使用できます。<br/><br/>[**完全な Lucene クエリ構文**](query-lucene-syntax.md)には、あいまい検索、近接検索、用語ブースト、正規表現の拡張機能を含む、簡単な構文でのすべての操作が含まれています。|
| 関連性 | [**シンプルなスコアリング**](index-add-scoring-profiles.md)は、Azure Cognitive Search の主な利点です。 スコアリング プロファイルを使用して、ドキュメント自体の値の関数として、関連性をモデル化できます。 たとえば、新しい製品や割り引き製品を検索結果の上位に表示することが望ましい場合があります。 あるいは、追跡記録し、個別に保存しておいた顧客の検索傾向に基づいてパーソナライズされたスコアリングのタグを利用し、スコアリング プロファイルを作成できます。 |
| 地理空間検索 | Azure Cognitive Search は、地理的な場所を処理、フィルター、表示する機能を備えています。 Azure Search により、ユーザーは物理的な場所に対する検索結果の近接度に基づいてデータを探索できます。 詳細については、[このビデオを視聴する](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)か[このサンプルを確認](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)してください。 |
| フィルターとファセット | 1 つのクエリ パラメーターで [**ファセット ナビゲーション**](search-faceted-navigation.md)を有効にできます。 Azure Cognitive Search は、ファセット ナビゲーション構造を返します。これをカテゴリ一覧の背後のコードとして使用すると、自律フィルター処理 (たとえば、価格帯やブランド別のカタログ品目のフィルター処理) を実現できます。 <br/><br/> [**フィルター**](query-odata-filter-orderby-syntax.md)を使用することで、ファセット ナビゲーションをアプリケーションの UI に組み込み、クエリ形成を拡張し、ユーザーまたは開発者が指定した条件に基づいてフィルター処理することができます。 フィルターを作成するには、OData 構文を使用します。 |
| ユーザー エクスペリエンス | 検索バーでの先行入力クエリで、[**オートコンプリート**](search-autocomplete-tutorial.md)を有効にすることができます。 <br/><br/>[**検索候補**](/rest/api/searchservice/suggesters) も検索バーでの部分テキスト入力に使用できますが、結果はクエリ用語ではなくインデックス内の実際のドキュメントです。 <br/><br/>[**シノニム**](search-synonyms.md)は、ユーザーが代替用語を提供する必要がなく、クエリのスコープを暗黙的に拡張する同等の用語を関連付けます。 <br/><br/>[**ヒットの強調表示**](/rest/api/searchservice/Search-Documents)は、検索結果内の一致するキーワードにテキスト書式を適用します。 強調表示されるスニペットを返すフィールドを選択できます。<br/><br/>[**並べ替え**](/rest/api/searchservice/Search-Documents)は、インデックス スキーマを介して、複数のフィールドで利用でき、クエリ時に 1 つの検索パラメーターで切り替えることができます。<br/><br/> [**ページング**](search-pagination-page-layout.md)とスロットルは、Azure Cognitive Search が検索結果に適用する微調整された制御によって簡単に行うことができます。  <br/><br/>|

## <a name="security-features"></a>セキュリティ機能

| カテゴリ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | フィーチャー |
|-------------------|----------|
| データの暗号化 | [**Microsoft によって管理される保存中の暗号化**](search-security-overview.md#encryption)は内部ストレージ レイヤーに組み込まれており、取り消しはできません。 <br/><br/>ユーザーが Azure Key Vault で作成および管理する [**ユーザー管理の暗号化キー**](search-security-manage-encryption-keys.md)は、インデックスとシノニム マップの補助的な暗号化に使用できます。 インデックス付きコンテンツを完全に二重に暗号化する CMK での暗号化は、2020 年 8 月 1 日以降に作成されたサービスでは、一時ディスク上のデータにも拡張されています。|
| エンドポイント保護 | [**受信ファイアウォールの IP ルールのサポート**](service-configure-firewall.md)を使用すると、検索サービスが要求を受け入れる IP 範囲を設定できます。<br/><br/>Azure Private Link を使用して [**プライベート エンドポイントを作成**](service-create-private-endpoint.md)し、すべての要求が仮想ネットワークを経由するようにします。 |
| アウトバウンド セキュリティ (インデクサー) | [**プライベート エンドポイントを使用したデータ アクセス**](search-indexer-howto-access-private.md)により、インデクサーは Azure Private Link を介して保護されている Azure リソースに接続できます。<br/><br/>[**信頼された ID を使用したデータ アクセス**](search-howto-managed-identities-data-sources.md)は、外部データ ソースへの接続文字列でユーザー名とパスワードを省略できることを意味します。 検索サービスが以前に信頼されたサービスとして登録されていた場合、インデクサーがデータ ソースに接続するときに、リソースは接続を許可します。 |

## <a name="portal-features"></a>ポータルの機能

| カテゴリ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 特徴 |
|-------------------|----------|
| プロトタイピングと検査のためのツール | [**インデックスの追加**](search-what-is-an-index.md)は、ポータルのインデックス デザイナーであり、属性付きのフィールドと他のいくつかの設定で構成される基本スキーマを作成するために使用できます。 インデックスを保存したら、SDK または REST API を使用して情報を移入し、データを提供できます。 <br/><br/>[**データのインポート ウィザード**](search-import-data-portal.md)では、インデックス、インデクサー、スキルセット、およびデータ ソースの定義が作成されます。 データが Azure に存在する場合、このウィザードを使用すると、特に概念実証の調査や探究で時間と労力を大幅に節約できます。 <br/><br/>[**検索エクスプローラー**](search-explorer.md)は、クエリをテストしたり、スコアリング プロファイルを調整したりするために使用します。<br/><br/>[**デモ アプリの作成**](search-create-app-portal.md)は、検索エクスペリエンスのテストに使用できる HTML ページを生成するために使用します。  |
| 監視と診断 | [**監視機能を有効にして**](search-monitor-usage.md)、ポータルに常に表示される、ひとめで確認できるメトリックの先に進みます。 1 秒あたりのクエリ数、待ち時間、スロットルに関するメトリックが取得され、ポータル ページで報告されます。追加の構成は必要ありません。|

## <a name="programmability"></a>プログラミング

| カテゴリ&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | フィーチャー |
|-------------------|----------|
| REST | [**サービス REST API**](/rest/api/searchservice/) は、データ プレーン操作で使用します (インデックス作成、クエリ、および AI エンリッチメントに関連するすべての操作を含む)。 また、このクライアント ライブラリを使用して、システム情報と統計を取得することもできます。 <br/><br/>[**管理 REST API**](/rest/api/searchmanagement/) は、Azure Resource Manager によるサービスの作成とクリーンアップで使用します。 また、この API を使用して、キーを管理したり、サービスをプロビジョニングしたりすることもできます。|
| Azure SDK for .NET | [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) は、データ プレーン操作で使用します (インデックス作成、クエリ、および AI エンリッチメントに関連するすべての操作を含む)。 また、このクライアント ライブラリを使用して、システム情報と統計を取得することもできます。 <br/><br/>[**Microsoft.Azure.Management.Search**](/dotnet/api/microsoft.azure.management.search) は、Azure Resource Manager によるサービスの作成とクリーンアップで使用します。 また、この API を使用して、キーを管理したり、サービスをプロビジョニングしたりすることもできます。|
| Azure SDK for Java | [**com.azure.search.documents**](/java/api/com.azure.search.documents) は、データ プレーン操作で使用します (インデックス作成、クエリ、および AI エンリッチメントに関連するすべての操作を含む)。 また、このクライアント ライブラリを使用して、システム情報と統計を取得することもできます。 <br/><br/>[**com.microsoft.azure.management.search**](/java/api/overview/azure/search/management) は、Azure Resource Manager によるサービスの作成とクリーンアップで使用します。 また、この API を使用して、キーを管理したり、サービスをプロビジョニングしたりすることもできます。|
| Azure SDK for Python | [**azure-search-documents**](/python/api/overview/azure/search-documents-readme) は、データ プレーン操作で使用します (インデックス作成、クエリ、および AI エンリッチメントに関連するすべての操作を含む)。 また、このクライアント ライブラリを使用して、システム情報と統計を取得することもできます。 <br/><br/>[**azure-mgmt-search**](/python/api/overview/azure/search/management) は、Azure Resource Manager によるサービスの作成とクリーンアップで使用します。 また、この API を使用して、キーを管理したり、サービスをプロビジョニングしたりすることもできます。 |
| Azure SDK for JavaScript/TypeScript | [**azure/search-documents**](/javascript/api/@azure/search-documents/) は、データ プレーン操作で使用します (インデックス作成、クエリ、および AI エンリッチメントに関連するすべての操作を含む)。 また、このクライアント ライブラリを使用して、システム情報と統計を取得することもできます。 <br/><br/>[**azure/arm-search**](/javascript/api/@azure/arm-search/) は、Azure Resource Manager によるサービスの作成とクリーンアップで使用します。 また、この API を使用して、キーを管理したり、サービスをプロビジョニングしたりすることもできます。 |

## <a name="see-also"></a>関連項目

+ [Cognitive Search の最新情報](whats-new.md)

+ [Cognitive Search のプレビュー機能](search-api-preview.md)