---
title: Java のサンプル
titleSuffix: Azure Cognitive Search
description: Java 用の Azure .NET SDK を使用する Azure Cognitive Search デモ Java コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955039"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の Java コード サンプル

Azure Cognitive Search ソリューションの機能とワークフローを示す Java コード サンプルについて説明します。 これらのサンプルでは、次のリンクを使用して調べることができる [**Azure SDK for Java**](/azure/developer/java/sdk) に対して、[**Azure Cognitive Search クライアント ライブラリ**](/java/api/overview/azure/search-documents-readme)を使用します。

| 移行先 | Link |
|--------|------|
| パッケージのダウンロード | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API リファレンス | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API テスト ケース | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| ソース コード | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK のサンプル

Azure SDK 開発チームのコード サンプルでは、API の使用方法を示します。 これらのサンプルは、GitHub の [**Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) にあります。

| サンプル | 説明 |
|---------|-------------|
| [検索インデックスの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | [検索インデックス](search-what-is-an-index.md)の作成方法が示されています。 |
| [シノニムの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | [シノニム マップ](search-synonyms.md)の作成方法が示されています。  |
| [検索インデクサーの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | [インデクサー](search-indexer-overview.md)の作成方法が示されています。 |
| [検索インデクサー データ ソースの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成する方法が示されています。 |
| [スキルセットの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  アタッチされたインデクサーであり、インデックス作成中に AI ベースの強化を実行する[スキルセット](cognitive-search-working-with-skillsets.md)を作成する方法が示されています。 |
| [ドキュメントを読み込む](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | [データ インポート](search-what-is-data-import.md)操作においてインデックスにドキュメントをアップロードまたはマージする方法が示されています。 |
| [クエリ構文](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | [基本的なクエリ](search-query-overview.md)を設定する方法が示されています。 |

## <a name="doc-samples"></a>ドキュメントのサンプル

Cognitive Search チームのコード サンプルは、機能とワークフローを示しています。 これらのサンプルの多くは、チュートリアル、クイックスタート、および操作方法に関する記事で言及されています。 これらのサンプルは、GitHub の [**Azure-Samples/azure-search-java-samples**](https://github.com/Azure-Samples/azure-search-java-samples) にあります。

| サンプル | 記事 | 
|---------|-------------|
| [クイックスタート](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | [クイック スタート:Java と REST で検索インデックスを作成する方法](search-get-started-java.md)に関する記事のソース コード。 このサンプルは、Java SDK 用に更新されていません。 ここでは、REST API を呼び出します。 |

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?languages=java&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。

## <a name="other-samples"></a>その他のサンプル

次のサンプルも Cognitive Search チームによって公開されていますが、ドキュメントでは参照されていません。 関連する readme ファイルでは、使用方法を説明します。

| サンプル | 説明 |
|---------|-------------|
| [search-java-getting-started](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Java SDK クライアント ライブラリを使用して、検索インデックスの作成、読み込み、クエリを実行します。 このサンプルは現在スタンドアロンです。 |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Java での Azure Cosmos DB インデクサーが示されています。 このサンプルは、Java SDK 用に更新されていません。 ここでは、REST API を呼び出します。|