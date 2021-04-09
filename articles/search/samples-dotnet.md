---
title: .NET のサンプル
titleSuffix: Azure Cognitive Search
description: .Net クライアント ライブラリを使用する Azure Cognitive Search デモ C# コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99218164"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の .NET (C#) コード サンプル

Azure Cognitive Search ソリューションの機能とワークフローを示す C# コード サンプルについて説明します。 これらのサンプルでは、次のリンクを使用して調べることができる [**Azure SDK for .NET**](/dotnet/azure/) に対して、[**Azure Cognitive Search クライアント ライブラリ**](/dotnet/api/overview/azure/search)を使用します。

| 移行先 | Link |
|--------|------|
| パッケージのダウンロード | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API リファレンス | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API テスト ケース | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| ソース コード | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK のサンプル

Azure SDK 開発チームのコード サンプルでは、API の使用方法を示します。 これらのサンプルは、GitHub の [**Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) にあります。

| サンプル | 説明 |
|---------|-------------|
| ["Hello world" (同期的に)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 同期メソッドを使用してクライアントの作成、認証、エラー処理を行う方法を示します。|
| ["Hello world" (非同期的に)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 非同期メソッドを使用してクライアントの作成、認証、エラー処理を行う方法を示します。  |
| [サービス レベルの操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | インデックス、インデクサー、データ ソース、スキルセット、および同意語マップを作成する方法を示します。 このサンプルでは、サービス統計情報を取得する方法と、インデックスのクエリを実行する方法についても示されています。  |
| [インデックスの操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 既存のインデックスに対してアクションを実行する方法を示します。この場合、インデックスに格納されているドキュメントの数を取得します。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | サポートされていないデータ型を操作する手法を示します。  |
| [ドキュメントのインデックス作成 (プッシュ モデル)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "プッシュ" モデルのインデックス作成。サービスのインデックスに JSON ペイロードを送信します。   |
| [暗号化キーのサンプル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | カスタマー マネージド暗号化キーを使用して、機密性の高いコンテンツに保護レイヤーを追加する方法を示します。  |

## <a name="doc-samples"></a>ドキュメントのサンプル

Cognitive Search チームのコード サンプルは、機能とワークフローを示しています。 これらのサンプルの多くは、チュートリアル、クイックスタート、および操作方法に関する記事で言及されています。 これらのサンプルは、GitHub の [**Azure-Samples/azure-search-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) と [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) にあります。

| サンプル | 記事  |
|---------|-------------|
| [クイックスタート](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [クイック スタート:検索インデックスの作成](search-get-started-dotnet.md)に関する記事のソース コード。 この記事では、サンプル データを使用して検索インデックスを作成、読み込み、クエリするための基本的なワークフローについて説明します。 |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [.NET クライアント ライブラリの使用方法](search-howto-dotnet-sdk.md)に関する記事のソース コード。 この記事では、基本的なワークフローについて手順を説明しますが、API の使用方法について特に詳しく説明します。  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 「[例:C# での同意語の追加](search-synonyms-tutorial-sdk.md)に関する記事に含まれています。 同意語の一覧は、クエリの拡張に使用され、インデックスの外部にある同等の用語を提供します。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | 「[チュートリアル:.NET SDK を使用して Azure SQL データにインデックスを付ける](search-indexer-tutorial.md)」のソースコード。 この記事では、スケジュール、フィールド マッピング、およびパラメーターを含む Azure SQL インデクサーを構成する方法を示します。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [データの暗号化のためにカスタマー マネージド キーを構成する方法](search-security-manage-encryption-keys.md)に関する記事のソース コード。 |
| [C# での最初のアプリの作成](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [チュートリアル:最初の検索アプリの作成](tutorial-csharp-create-first-app.md)に関する記事のソース コード。 ほとんどのサンプルはコンソール アプリケーションですが、この MVC サンプルでは Web ページを使用してサンプルの Hotels インデックスを配置し、基本的な検索、改ページ位置の自動修正、オートコンプリートおよび提案されたクエリ、ファセット、フィルターを示します。 |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [チュートリアル:複数のデータ ソースからのインデックス作成](tutorial-multiple-data-sources.md)に関する記事のソース コード。 |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [チュートリアル:プッシュ API を使用したインデックス作成の最適化](tutorial-optimize-indexing-push-api.md)に関する記事のソース コード。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 「[チュートリアル:.NET SDK を使用して Azure BLOB から AI で生成する検索可能なコンテンツ](cognitive-search-tutorial-blob-dotnet.md)」のソース コード。  |

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?languages=csharp&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。

## <a name="other-samples"></a>その他のサンプル

次のサンプルも Cognitive Search チームによって公開されていますが、ドキュメントでは参照されていません。 関連する readme ファイルでは、使用方法を説明します。

| サンプル | 説明 |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | 獲得したソリューションに組み込むことができる、使用可能なカスタム スキルのソース コード。  |
| [ナレッジ マイニング ソリューション アクセラレータ](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | エンドツーエンドのナレッジ マイニング ソリューションのプロトタイプ作成に役立つテンプレート、サポート ファイル、および分析レポートが含まれています。  |
| [Covid-19 Search App リポジトリ](https://github.com/liamca/covid19search) | Cognitive Search ベースの [Covid-19 Search App](https://covid19search.azurewebsites.net/) のソース コード リポジトリ |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | [JFK ソリューション](https://www.microsoft.com/ai/ai-lab-jfk-files)の詳細を確認してください。 |
| [Search + QnA Maker アクセラレータ](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Search と QnA Maker の機能を組み合わせた[ソリューション](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381)。 ライブ [デモ サイト](https://aka.ms/qnaWithAzureSearchDemo)を参照してください。 |