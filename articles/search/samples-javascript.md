---
title: JavaScript のサンプル
titleSuffix: Azure Cognitive Search
description: JavaScript 用の Azure .NET SDK を使用する Azure Cognitive Search デモ JavaScript コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955943"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の JavaScript コード サンプル

Azure Cognitive Search ソリューションの機能とワークフローを示す JavaScript コード サンプルについて説明します。 これらのサンプルでは、次のリンクを使用して調べることができる [**Azure SDK for JavaScript**](/azure/developer/javascript/)に対して、[**Azure Cognitive Search クライアント ライブラリ**](/javascript/api/overview/azure/search-documents-readme)を使用します。

| 移行先 | Link |
|--------|------|
| パッケージのダウンロード | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API リファレンス | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API テスト ケース | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| ソース コード | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK のサンプル

Azure SDK 開発チームのコード サンプルは、API の使用方法を示します。 これらのサンプルは、GitHub の [**azure-sdk-for-js/tree/master/sdk/search/search-documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) にあります。

### <a name="javascript-sdk-samples"></a>JavaScript SDK サンプル

| サンプル | 説明 |
|---------|-------------|
| [インデックス](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | [検索インデックス](search-what-is-an-index.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 このサンプル カテゴリには、サービス統計のサンプルも含まれます。 |
| [dataSourceConnections (インデクサー用)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [インデクサー](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  [インデクサー](search-indexer-overview.md)を作成、更新、取得、一覧表示、リセット、削除する方法を示します。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   アタッチされたインデクサーであり、インデックス作成中に AI ベースの強化を実行する[スキルセット](cognitive-search-working-with-skillsets.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | [シノニム マップ](search-synonyms.md)を作成、更新、取得、一覧表示、削除する方法が示されています。  |

### <a name="typescript-samples"></a>TypeScript のサンプル

| サンプル | 説明 |
|---------|-------------|
| [インデックス](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | [検索インデックス](search-what-is-an-index.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 このサンプル カテゴリには、サービス統計のサンプルも含まれます。 |
| [dataSourceConnections (インデクサー用)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [インデクサー](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  [インデクサー](search-indexer-overview.md)を作成、更新、取得、一覧表示、リセット、削除する方法を示します。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   アタッチされたインデクサーであり、インデックス作成中に AI ベースの強化を実行する[スキルセット](cognitive-search-working-with-skillsets.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | [シノニム マップ](search-synonyms.md)を作成、更新、取得、一覧表示、削除する方法が示されています。  |

## <a name="doc-samples"></a>ドキュメントのサンプル

Cognitive Search チームのコード サンプルは、機能とワークフローを示しています。 これらのサンプルの多くは、チュートリアル、クイックスタート、および操作方法に関する記事で言及されています。 これらのサンプルは、GitHub の [**Azure-Samples/azure-search-javascript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) にあります。

| サンプル | 記事 |
|---------|---------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | 次の記事のソース コード。"[クイック スタート:JavaScript で検索インデックスを作成する](search-get-started-javascript.md)" のような記事。 この記事では、サンプル データを使用して検索インデックスを作成、読み込み、クエリするための基本的なワークフローについて説明します。 |

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?languages=javascript&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。

## <a name="other-samples"></a>その他のサンプル

次のサンプルも Cognitive Search チームによって公開されていますが、ドキュメントでは参照されていません。 関連する readme ファイルでは、使用方法を説明します。

| サンプル | 説明 |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Azure Cognitive Search 用の React テンプレート (github.com) |