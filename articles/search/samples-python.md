---
title: Python のサンプル
titleSuffix: Azure Cognitive Search
description: Python または REST 用の Azure .NET SDK を使用する Azure Cognitive Search デモ Python コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955124"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の Python コード サンプル

Azure Cognitive Search ソリューションの機能とワークフローを示す Python コード サンプルについて説明します。 これらのサンプルでは、[**Azure SDK for Python**](/azure/developer/python/) に対して [**Azure Cognitive Search クライアント ライブラリ**](/python/api/overview/azure/search-documents-readme)を使用しています。これは、次のリンクを使用して調べることができます。

| 移行先 | Link |
|--------|------|
| パッケージのダウンロード | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API リファレンス | [azure-search-documents](/python/api/azure-search-documents)  |
| API テスト ケース | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| ソース コード | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK のサンプル

Azure SDK 開発チームのコード サンプルは、API の使用方法を示しています。 これらのサンプルは、GitHub の [**azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) にあります。

| サンプル | 説明 |
|---------|-------------|
| [認証](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | クライアントを構成し、サービスに対して認証する方法を示します。 | 
| [インデックスの作成、読み取り、更新、削除の操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | [検索インデックス](search-what-is-an-index.md)を作成、更新、取得、一覧表示、削除する方法を示します。 |
| [インデクサーの作成、読み取り、更新、削除の操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | [インデクサー](search-indexer-overview.md)を作成、更新、取得、一覧表示、リセット、削除する方法を示します。 |
| [検索インデクサー データ ソース](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成、更新、取得、一覧表示、削除する方法を示します。 |
| [シノニム](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | [同意語マップ](search-synonyms.md)を作成、更新、取得、一覧表示、削除する方法を示します。  |
| [ドキュメントを読み込む](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | [データ インポート](search-what-is-data-import.md)操作においてインデックスにドキュメントをアップロードまたはマージする方法を示します。 |
| [単純なクエリ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | [基本的なクエリ](search-query-overview.md)を設定する方法を示します。 |
| [フィルター クエリ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | [フィルター式](search-filters.md)の設定方法を示します。 |
| [ファセット クエリ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | [ファセット](search-filters-facets.md)の使用方法を示します。 |

## <a name="doc-samples"></a>ドキュメントのサンプル

Cognitive Search チームのコード サンプルは、機能とワークフローを示しています。 これらのサンプルの多くは、チュートリアル、クイックスタート、操作方法に関する記事で言及されています。 これらのサンプルは、GitHub の [**Azure-Samples/azure-search-python-samples**](https://github.com/Azure-Samples/azure-search-python-samples) にあります。

| サンプル | 記事 |
|---------|---------|
| [クイックスタート](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [クイック スタート:.Python で検索インデックスを作成する](search-get-started-python.md)に関する記事のソース コード。 この記事では、サンプル データを使用して検索インデックスを作成、読み込み、クエリを実行するための基本的なワークフローについて説明します。 |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 「[チュートリアル:Python と AI を使用して Azure Blob から検索可能なコンテンツを生成する](cognitive-search-tutorial-blob-python.md)」のソース コード。 この記事では、コグニティブ スキルセットを使用して BLOB インデクサーを作成する方法について説明します。このスキルセットにより、生コンテンツを作成および変換して、検索したり利用したりできるようにします。 |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 「[例:Python を使用してカスタム スキルを作成する](cognitive-search-custom-skill-python.md)」のソース コード。 この記事では、インデクサーおよびスキルセットと Azure Machine Learning のディープ ラーニング モデルの統合について説明します。 |

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?languages=python&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。