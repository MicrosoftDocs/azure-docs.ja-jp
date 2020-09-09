---
title: Azure Cognitive Search の新機能
description: Azure Cognitive Search への Azure Search のサービス名の変更を含む、新機能や拡張機能についてのお知らせです。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 788b3f5e9f4012e418ece691ebb5fbc5d2f866af
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931847"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search の新機能

サービス内の新機能について説明します。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="feature-announcements-in-2020"></a>2020 年の機能のお知らせ

### <a name="august-2020"></a>2020 年 8 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [二重暗号化](search-security-overview.md#encryption) | セキュリティ | 新しい検索サービスでカスタマー マネージド キー (CMK) 暗号化を構成することにより、ストレージ層での二重暗号化を有効にします。 新しいサービスを作成し、[カスタマー マネージド キーを構成してインデックスまたはシノニム マップに適用し](search-security-manage-encryption-keys.md)、そのコンテンツで二重暗号化のメリットを得ます。 | 次のリージョンで 2020 年 8 月 1 日以降に作成されたすべての検索サービスで一般提供されます: 米国西部 2、米国東部、米国中南部、US Gov バージニア、US Gov アリゾナ。 サービスを作成するには、ポータル、管理 REST API、または SDK を使用します。 |

### <a name="july-2020"></a>2020 年 7 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [Azure.Search.Documents クライアント ライブラリ](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) | Azure SDK for .NET | Azure SDK チームによってリリースされた .NET クライアント ライブラリです。他の .NET クライアント ライブラリとの一貫性を保つように設計されています。 <br/><br/>バージョン 11 は Search REST api-version=2020-06-30 を対象としていますが、ナレッジ ストア、地理空間型、[FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet) はまだサポートしていません。 <br/><br/>詳細については、[インデックスの作成に関するクイックスタート](search-get-started-dotnet.md)および [Azure.Search.Documents (v11) へのアップグレード](search-dotnet-sdk-migration-version-11.md)に関するページをご覧ください。 | 一般提供されています。 </br> NuGet から [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)をインストールします。 |
| [azure.search.documents クライアント ライブラリ](/python/api/overview/azure/search-documents-readme?view=azure-python)  | Azure SDK for Python| Azure SDK チームによってリリースされた Python クライアント ライブラリです。他の Python クライアント ライブラリとの一貫性を保つように設計されています。 <br/><br/>バージョン 11 は Search REST api-version=2020-06-30 を対象としています。 | 一般提供されています。 </br> PyPI から [azure-search-documents パッケージ](https://pypi.org/project/azure-search-documents/)をインストールします。 |
| [@azure/search-documents クライアント ライブラリ](/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest)  | Azure SDK for JavaScript | Azure SDK チームによってリリースされた JavaScript クライアント ライブラリです。他の JavaScript クライアント ライブラリとの一貫性を保つように設計されています。 <br/><br/>バージョン 11 は Search REST api-version=2020-06-30 を対象としています。 | 一般提供されています。 </br> npm から [@azure/search-documents パッケージ](https://www.npmjs.com/package/@azure/search-documents)をインストールします。 |

### <a name="june-2020"></a>2020 年 6 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
[**ナレッジ ストア**](knowledge-store-concept-intro.md) | AI エンリッチメント | AI によって強化されたインデクサーの出力。他のアプリやプロセスで使用できるように内容は Azure Storage に格納されます。 | 一般提供されています。 </br> [Search REST API 2020-06-30](/rest/api/searchservice/) 以降、またはポータルを使用します。 |
| [**Search REST API 2020-06-30**](/rest/api/searchservice/) | REST | REST API の新しい安定ージョンです。 ナレッジ ストアに加え、このバージョンには、検索の関連性とスコアリングに対する機能強化が含まれています。 | 一般提供されています。 |
| [**Okapi BM25 関連性アルゴリズム**](https://en.wikipedia.org/wiki/Okapi_BM25) | クエリ | 7 月 15 日以降に作成されたすべての新しい検索サービスに自動的に使用される、新しい関連性ランク付けアルゴリズム。 既に作成済みのサービスについては、インデックス フィールドの `similarity` プロパティを設定することにより、オプトインできます。 | 一般提供されています。 </br> [Search REST API 2020-06-30](/rest/api/searchservice/) 以降、または REST API 2019-05-06 を使用します。 |
| **executionEnvironment** | セキュリティ (インデクサー) | このインデクサー構成プロパティを明示的に `private` に設定して、外部データ ソースへのすべての接続がプライベート エンドポイント経由になるように強制します。 Azure Private Link を活用する検索サービスにのみ適用されます。 | 一般提供されています。 </br> [Search REST API 2020-06-30](/rest/api/searchservice/) を使用して、この一般的な構成パラメーターを設定します。 |

### <a name="may-2020-microsoft-build"></a>2020 年 5 月 (Microsoft Build)

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**デバッグ セッション**](cognitive-search-debug-session.md) | AI エンリッチメント | デバッグ セッションには、既存のスキルセットに関する問題を調査して解決するためのポータルベースのインターフェイスが用意されています。 デバッグ セッションで作成された修正は、運用環境のスキルセットに保存できます。 [このチュートリアル](cognitive-search-tutorial-debug-sessions.md)で作業を開始します。 | パブリック プレビュー (ポータル)。 |
| [**インバウンド ファイアウォールをサポートするための IP 規則**](service-configure-firewall.md) | セキュリティ | 検索サービス エンドポイントへのアクセスを特定の IP アドレスに制限します。 | 一般提供されています。 </br> [Management REST API 2020-03-13](/rest/api/searchmanagement/) 以降、またはポータルを使用します。 |
| [**プライベート検索エンドポイント用の Azure Private Link**](service-create-private-endpoint.md) | セキュリティ| 検索サービスをプライベート リンク リソースとして実行することによって、パブリック インターネットからサービスをシールドします。これにより、同じ仮想ネットワーク上のクライアント アプリやその他の Azure サービスのみがアクセスできます。 | 一般提供されています。 </br> [Management REST API 2020-03-13](/rest/api/searchmanagement/) 以降、またはポータルを使用します。 |
| [**システム マネージド ID (プレビュー)** ](search-howto-managed-identities-data-sources.md) | セキュリティ (インデクサー) | Azure Active Directory を使用して、信頼できるサービスとして検索サービスを登録し、インデックス作成用にサポートされている Azure データ ソースへの接続を設定します。 Azure SQL Database、Azure Cosmos DB、Azure Storage などの Azure データ ソースからコンテンツを取り込む[インデクサー](search-indexer-overview.md)に適用されます。 | パブリック プレビュー。 </br> 検索サービスを登録するには、ポータルを使用します。 |
| [**sessionId クエリ パラメーター**](index-similarity-and-scoring.md)、[scoringStatistics=global パラメーター](index-similarity-and-scoring.md#scoring-statistics) | クエリ (関連性) | クエリに sessionID を追加して、検索スコアを計算するためのセッションを確立します。このとき scoringStatistics=global を指定すると、すべてのシャードからスコアを収集できるため、より一貫性のある検索スコア計算を行うことができます。 | 一般提供されています。 </br> [Search REST API 2020-06-30](/rest/api/searchservice/) 以降、または REST API 2019-05-06 を使用します。 |
| [**featuresMode (プレビュー)** ](index-similarity-and-scoring.md#featuresMode-param) | クエリ | このクエリ パラメーターを追加して関連性スコアを拡張し、より詳細な情報 (フィールドごとの類似性スコア、フィールドごとの用語の頻度、フィールドごとの一致する一意のトークンの数) を表示します。 これらのデータ ポイントは、カスタム スコアリング アルゴリズムで使用できます。 この機能を示すサンプルについては、[検索の関連性への機械学習 (LearnToRank) の追加](https://github.com/Azure-Samples/search-ranking-tutorial)に関するページを参照してください。 | パブリック プレビュー。 </br> [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) または REST API 2019-05-06-Preview を使用します。 |

### <a name="march-2020"></a>2020 年 3 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**ネイティブ BLOB の論理的な削除 (プレビュー)** ](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | インデクサー | Azure Cognitive Search の Azure Blob Storage インデクサーは、論理的に削除された状態にある BLOB を認識し、インデックス作成時に対応する検索ドキュメントを削除します。 | パブリック プレビュー。 </br> [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) および REST API 2019-05-06-Preview を使用します。このとき、ネイティブの "論理的な削除" が有効になっている Azure BLOB データ ソースに対するインデクサー実行を指定します。 |
| [**Management REST API (2020-03-13)** ](/rest/api/searchmanagement/management-api-versions) | REST | 検索サービスを作成および管理するための新しい安定版 REST API。 IP ファイアウォールと Private Link のサポートを追加します | 一般提供されています。 |

### <a name="february-2020"></a>2020 年 2 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**PII 検出 (プレビュー)** ](cognitive-search-skill-pii-detection.md) | AI エンリッチメント | 入力テキストから個人を特定できる情報を抽出する、インデックス作成中に使用される新しいコグニティブ スキルです。ユーザーには、さまざまな方法でこの情報をそのテキストからマスクするためのオプションが提供されます。 | パブリック プレビュー。 </br> ポータル、[Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview)、または REST API 2019-05-06-Preview を使用します。 |
| [**カスタム エンティティの参照 (プレビュー)** ](cognitive-search-skill-custom-entity-lookup.md )| AI エンリッチメント | ユーザーが定義した単語と語句から成るカスタム リストからテキストを検索する新しいコグニティブ スキル。 この一覧を使用して、エンティティが一致するすべての文書がラベル付けされます。 このスキルでは、ある程度のあいまい一致もサポートされており、類似しているが完全一致ではない一致を見つけるために適用できます。 | パブリック プレビュー。 </br> ポータル、[Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview)、または REST API 2019-05-06-Preview を使用します。 |

### <a name="january-2020"></a>2020 年 1 月

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**カスタマー マネージド暗号化キー**](search-security-manage-encryption-keys.md) |セキュリティ | プラットフォームの組み込み暗号化に加えて、別途暗号化レイヤーが追加されます。 作成および管理する暗号化キーを使用して、ペイロードが検索サービスに到達する前に、インデックス コンテンツとシノニム マップを暗号化できます。 | 一般提供されています。 </br> Search REST API 2019-05-06 以降を使用します。 マネージ コードの場合は、機能はプレビューではなくなりましたが、正しいパッケージはまだ [.NET SDK バージョン 8.0-preview](search-dotnet-sdk-migration-version-9.md) です。 |
| [**インバウンド ファイアウォールをサポートするための IP 規則 (プレビュー)** ](service-configure-firewall.md) | セキュリティ | 検索サービス エンドポイントへのアクセスを特定の IP アドレスに制限します。 プレビュー API の [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) には、新しい **IpRule** および **NetworkRuleSet** プロパティがあります。 このプレビュー機能は、特定のリージョンで使用できます。 |  api-version=2019-10-01-Preview を使用したパブリック プレビュー。  |
| [**プライベート検索エンドポイント用の Azure Private Link (プレビュー)** ](service-create-private-endpoint.md) | セキュリティ| 検索サービスをプライベート リンク リソースとして実行することによって、パブリック インターネットからサービスをシールドします。これにより、同じ仮想ネットワーク上のクライアント アプリやその他の Azure サービスのみがアクセスできます。 | api-version=2019-10-01-Preview を使用したパブリック プレビュー。  |

## <a name="feature-announcements-in-2019"></a>2019 年の機能のお知らせ

### <a name="december-2019"></a>2019 年 12 月

+ [デモ アプリの作成 (プレビュー)](search-create-app-portal.md) はポータルの新しいウィザードで、インデックスへのクエリ (読み取り専用) アクセスを使用してダウンロード可能な HTML ファイルを生成できます。 このファイルには、検索サービスのインデックスにバインドされた、操作可能な "localhost" スタイルの Web アプリをレンダリングする埋め込みスクリプトが付属しています。 ページはウィザードで構成できます。また、検索バー、結果領域、サイドバー ナビゲーション、および先行入力クエリのサポートを含めることができます。 HTML をオフラインに変更して、ワークフローや外観を拡張したりカスタマイズしたりすることができます。 運用環境のシナリオで通常必要とされるセキュリティおよびホスト層を含めるようにデモ アプリを拡張することは簡単ではありません。 完全なクライアント アプリへの近道ではなく、検証およびテスト用のツールとして考える必要があります。

+ [安全な接続のためのプライベート エンドポイントの作成 (プレビュー)](service-create-private-endpoint.md) に関する記事では、検索サービスへのセキュリティで保護された接続のために Private Link を設定する方法について説明されています。 このプレビュー機能は要求することで利用できるようになり、ソリューションの一部として [Azure Private Link](../private-link/private-link-overview.md) と [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) が使用されます。

### <a name="november-2019---ignite-conference"></a>2019 年 11 月 - Ignite Conference

+ [インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) では、既に処理されているコンテンツを失うことなく特定のステップまたはフェーズを操作できるように、エンリッチメント パイプラインにキャッシュとステートフル性が追加されます。 これまでは、エンリッチメント パイプラインを変更すると、完全なリビルドが必要でした。 インクリメンタル エンリッチメントを使用すると、コストのかかる分析の出力 (特に画像分析) が維持されます。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [ドキュメント抽出 (プレビュー)](cognitive-search-skill-document-extraction.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、スキルセット内からファイルのコンテンツを抽出できます。 ドキュメント解析は、これまでスキルセットの実行前にのみ行われていました。 このスキルを追加することで、スキルセットの実行内でこの操作を実行することもできます。

+ [テキスト翻訳](cognitive-search-skill-text-translation.md)は、インデックスの作成中に使用される認知スキルで、テキストを評価し、各レコードについて、指定したターゲット言語に翻訳されたテキストを返します。

+ [Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)は、Power BI デスクトップのナレッジ ストアで、強化されたコンテンツの視覚化と分析をすぐに開始することができます。 このテンプレートは、[データのインポート ウィザード](knowledge-store-create-portal.md)を使用して作成された Azure テーブルのプロジェクション向けに設計されています。

+ インデクサーで [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API (プレビュー)](search-howto-index-cosmosdb.md)、[Cosmos DB Cassandra API (プレビュー)](search-howto-index-cosmosdb.md) のサポートを開始しました。 [こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)を使ってサインアップできます。 プレビュー プログラムへの参加が承認されると、確認メールが届きます。

### <a name="july-2019"></a>2019 年 7 月

+ [Azure Government クラウド](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search)で一般提供されています。

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新しいサービス名

Azure Search は、コア操作での拡張された (ただし、省略可能な) 認知スキルと AI 処理の使用を反映するために、**Azure Cognitive Search** に名前が変更されました。 API のバージョン、NuGet のパッケージ、名前空間、およびエンドポイントは変更されません。 新規および既存の検索ソリューションは、サービス名の変更の影響を受けません。

## <a name="service-updates"></a>サービスの更新情報

Azure Cognitive Search の[サービス更新のお知らせ](https://azure.microsoft.com/updates/?product=search&status=all)に関する情報については、Azure の Web サイトで参照できます。