---
title: プレビュー機能一覧
titleSuffix: Azure Cognitive Search
description: プレビュー機能は、お客様が設計とユーティリティに関してフィードバックできるようにリリースされています。 この記事では、現在プレビュー段階にあるすべての機能を一覧表示しています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 596113f9fcb7326010090e67b3a52ca03faa25d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041979"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure Cognitive Search のプレビュー機能

この記事では、パブリック プレビュー段階にあるすべての機能を一覧表示しています。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

プレビュー機能が一般公開に切り替えられると、この一覧から削除されます。 以下に記載されていない機能は、一般公開されているとお考えください。 一般公開については、[サービスの更新情報](https://azure.microsoft.com/updates/?product=search)、または[新機能](whats-new.md)に関するページでご確認いただけます。

|機能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | カテゴリ | 説明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**セマンティック検索**](semantic-search-overview.md) | 関連性 (スコアリング) | セマンティック ランク付けアルゴリズム、クエリの種類、およびクエリの応答。 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) |
| [**スペル チェック**](cognitive-search-aml-skill.md) | クエリ | 単純、完全、およびセマンティック クエリのクエリ用語入力に対するスペル修正 (オプション)。 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) |
| [**SharePoint Online インデクサー**](search-howto-index-sharepoint-online.md) | インデクサー データ ソース | SharePoint コンテンツのインデクサーベースのインデックス作成用の新しいデータソースです。 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) |
| [**Azure Machine Learning (AML) スキル**](cognitive-search-aml-skill.md) | AI エンリッチメント| Azure Machine Learning からの推論エンドポイントを統合するための新しいスキルの種類です。 [このチュートリアル](cognitive-search-tutorial-aml-custom-skill.md)で作業を開始します。 | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) または 2019-05-06-Preview を使用します。 また、Cognitive Search と Azure ML サービスが同じサブスクリプションにデプロイされていることを前提として、ポータル (スキルセットの設計) でも利用できます。 |
| [**featuresMode パラメーター**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | 関連性 (スコアリング) | 関連性のスコアリングで、フィールドごとの類似性スコア、フィールドごとの用語の頻度、およびフィールドごとの一致した一意のトークン数の詳細が含まれ強化されました。 これらのデータ ポイントは、[カスタム スコアリング ソリューション](https://github.com/Azure-Samples/search-ranking-tutorial)で使用できます。 | このクエリ パラメーターを、api-version=2020-06-30-Preview または 2019-05-06-Preview の [Search Documents (REST)](/rest/api/searchservice/preview-api/search-documents) を使用して追加します。 |
| [**デバッグ セッション**](cognitive-search-debug-session.md) | ポータル、AI エンリッチメント (スキルセット) | スキルセットの問題を調査して解決するために使用する、セッション内スキルセット エディター。 デバッグ セッション中に適用された修正は、サービス内のスキルセットに保存できます。 | ポータルのみで、デバッグ セッションを開くための [概要] ページの中間ページのリンクを使用します。 |
| [**ネイティブ BLOB の論理的な削除**](search-howto-index-changed-deleted-blobs.md) | インデクサー、Azure BLOB| Azure Cognitive Search の Azure Blob Storage インデクサーは、論理的に削除された状態にある BLOB を認識し、インデックス作成時に対応する検索ドキュメントを削除します。 | この構成設定を追加するには、api-version=2020-06-30-Preview または api-version=2019-05-06-Preview の[Create Indexer (REST)](/rest/api/searchservice/create-indexer) を使用します。 |
| [**カスタム エンティティの参照スキル**](cognitive-search-skill-custom-entity-lookup.md ) | AI エンリッチメント (スキルセット) | ユーザーが定義したカスタムの単語と語句のリストからテキストを検索するコグニティブ スキル。 この一覧を使用して、エンティティが一致するすべての文書がラベル付けされます。 このスキルでは、ある程度のあいまい一致もサポートされており、類似しているが完全一致ではない一致を見つけるために適用できます。 | このプレビュー スキルを参照するには、ポータルのスキルセット エディターを使用するか、api-version=2020-06-30-Preview または api-version=2019-05-06-Preview の [Create Skillset (REST)](/rest/api/searchservice/create-skillset) を使用します。 |
| [**PII 検出スキル**](cognitive-search-skill-pii-detection.md) | AI エンリッチメント (スキルセット) | 入力テキストから個人情報を抽出する、インデックス作成中に使用されるコグニティブ スキルです。ユーザーには、さまざまな方法でこの情報をそのテキストからマスクするためのオプションが提供されます。 | このプレビュー スキルを参照するには、ポータルのスキルセット エディターを使用するか、api-version=2020-06-30-Preview または api-version=2019-05-06-Preview の [Create Skillset (REST)](/rest/api/searchservice/create-skillset) を使用します。 |
| [**インクリメンタル エンリッチメント**](cognitive-search-incremental-indexing-conceptual.md) | インデクサーの構成| エンリッチメント パイプラインにキャッシュが追加され、スキルセットや別のオブジェクトに対する更新など、目的の変更によってコンテンツが変更されない場合に既存の出力を再利用できます。 キャッシュは、スキルセットによって生成されるエンリッチされたドキュメントにのみ適用されます。| この構成設定を追加するには、api-version=2020-06-30-Preview または api-version=2019-05-06-Preview の[Create Indexer (REST)](/rest/api/searchservice/create-indexer) を使用します。 |
| [**Cosmos DB インデクサー:MongoDB API、Gremlin API、Cassandra API**](search-howto-index-cosmosdb.md) | インデクサー データ ソース | Cosmos DB には、SQL の API が一般公開されていますが、MongoDB、Gremlin、Cassandra 用の API はプレビュー段階です。 | Gremlin と Cassandra のみでは、バックエンドでご自分のサブスクリプションがサポートされるように、[最初にサインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)します。 MongoDB データ ソースは、ポータルで構成できます。 それ以外の場合、3 つのすべての API のデータ ソースは、api-version=2020-06-30-Preview または api-version=2019-05-06-Preview の [Create Data Source (REST)](/rest/api/searchservice/create-data-source) を使用して構成できます。 |
|  [**Azure Data Lake Storage Gen2 インデクサー**](search-howto-index-azure-data-lake-storage.md) | インデクサー データ ソース | Azure Data Lake Storage Gen2 の内容とメタデータからインデックスを作成します。| バックエンドでご自分のサブスクリプションがサポートされるように、[サインアップ](https://aka.ms/azure-cognitive-search/indexer-preview)する必要があります。 このデータ ソースにアクセスするには、api-version=2020-06-30-Preview または api-version=2019-05-06-Preview の [Create Data Source (REST)](/rest/api/searchservice/create-data-source) を使用します。 |
| [**moreLikeThis**](search-more-like-this.md) | クエリ | 特定のドキュメントに関連する別のドキュメントを検索します。 この機能は、以前のプレビューからありました。 | このクエリ パラメーターを、api-version=2020-06-30-Preview、2019-05-06-Preview、2016-09-01-Preview または 2017-11-11-Preview の [Search Documents (REST)](/rest/api/searchservice/search-documents) 呼び出しに追加します。 |

## <a name="how-to-call-a-preview-rest-api"></a>プレビューの REST API を呼び出す方法

Azure Cognitive Search では常に、まず REST API によって、次に .NET SDK のプレリリース版で実験機能を事前公開しています。

プレビュー機能は、機能の設計と実装に関するフィードバックの収集を目的とするテストと実験のために提供されています。 このため、プレビュー機能は、下位互換性を損なう可能性のある形で、時間の経過と共に変更されることがあります。 これは、下位互換性のある小規模な修正プログラムと機能強化以外は変更される可能性が低い安定した GA バージョンの機能とは対照的です。 また、プレビュー機能は必ずしも GA リリースに反映されません。

一部のプレビュー機能にはポータルと .NET SDK で使用できるものがありますが、REST API には常にプレビュー機能があります。

+ 検索操作の場合、現在のプレビュー バージョンは [ **`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) です。

+ 管理操作の場合、現在のプレビュー バージョンは [ **`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) です。

古いプレビューはまだ動作しますが、時間がたてば古くなります。 ご自分のコードで `api-version=2019-05-06-Preview`、`api-version=2016-09-01-Preview` または `api-version=2017-11-11-Preview` を呼び出している場合、それらの呼び出しはまだ有効です。 ただし、機能強化の更新が行われるのは、最新のプレビュー バージョンのみです。

次のサンプル構文は、API のプレビュー バージョンの呼び出しを示しています。

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure Cognitive Search サービスは複数のバージョンで使用できます。 詳しくは、[API バージョン](search-api-versions.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

Search REST プレビュー API リファレンス ドキュメントを確認します。 問題が発生した場合は、[Stack Overflow](https://stackoverflow.com/) または[サポートへの問い合わせ](https://azure.microsoft.com/support/community/?product=search)でサポートを依頼してください。

> [!div class="nextstepaction"]
> [Search Service REST API リファレンス (プレビュー)](/rest/api/searchservice/index-preview)