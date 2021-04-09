---
title: REST サンプル
titleSuffix: Azure Cognitive Search
description: Search または Management REST API を使用する Azure Cognitive Search のデモの REST コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98956441"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の REST コード サンプル

Azure Cognitive Search ソリューションの機能とワークフローを示す REST API サンプルについて説明します。 これらのサンプルでは、[**Search REST API**](/rest/api/searchservice) が使用されています。

REST は Azure Cognitive Search 用の信頼性の高いプログラミング インターフェイスであり、プログラムで呼び出すことができるすべての操作は、まず REST で、次に SDK で提供されます。 このため、ドキュメントのほとんどの例では、REST API を使用して重要な概念を例示または説明しています。

REST のサンプルは通常、Postman で開発およびテストされますが、HTTP 呼び出しをサポートする任意のクライアントを使用できます。

+ 最初に、「[クイックスタート: REST API を使用して Azure Cognitive Search インデックスを作成する](search-get-started-rest.md)」を参照してください。HTTP 呼び出しを作成するのに役立ちます。
+ Visual Studio Code で作業する場合は、[Azure Cognitive Search 用の Visual Studio Code 拡張機能](search-get-started-vs-code.md) (現在プレビュー段階) を試してください。

## <a name="doc-samples"></a>ドキュメントのサンプル

Cognitive Search チームのコード サンプルは、機能とワークフローを示しています。 これらのサンプルの多くは、チュートリアル、クイックスタート、操作方法に関する記事で言及されています。 これらのサンプルは、GitHub の [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples) にあります。

| サンプル | 記事 |
|---------|---------|
| [クイックスタート](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | [クイック スタート:REST API を使用して検索インデックスを作成する](search-get-started-rest.md)のソース コード。 この記事では、サンプル データを使用して検索インデックスの作成、読み込み、クエリを行うための基本的なワークフローについて説明します。 |
| [チュートリアル](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | 「[チュートリアル:REST と AI を使用して Azure Blob から検索可能なコンテンツを生成する](cognitive-search-tutorial-blob.md)」のソース コード。 この記事では、Azure Blob を反復処理して情報を抽出し、構造を推論するスキルセットを作成する方法について説明します。|
| [デバッグ セッション](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | 「[チュートリアル:スキルセットに対する診断、修復、および変更のコミットを行う](cognitive-search-tutorial-debug-sessions.md)」のソース コード。 この記事では、Azure portal でスキルセット デバッグ セッションを使用する方法について説明します。 REST は、デバッグ中に使用されるオブジェクトを作成するために使用されます。|
| [カスタム アナライザー](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | 「[チュートリアル:電話番号のカスタム アナライザーを作成する](tutorial-create-custom-analyzer.md)」のソース コード。 この記事では、アナライザーを使用して、検索可能なコンテンツでパターンと特殊文字を保持する方法について説明します。|
| [ナレッジ ストア](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | 「[REST と Postman を使用してナレッジ ストアを作成する](knowledge-store-create-rest.md)」のソース コード。 この記事では、ナレッジ マイニング ワークフローに使用されるナレッジ ストアを設定するために必要な手順について説明します。 |
| [プロジェクション](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | 「[エンリッチメントを整形してエクスポートする方法](knowledge-store-projections-examples.md)」のソース コード。 この記事では、ナレッジ ストアで物理データ構造を指定する方法について説明します。|
| [暗号化された BLOB にインデックスを付ける](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | [BLOB インデクサーとスキルセットを使用して暗号化された BLOB にインデックスを付ける方法](search-howto-index-encrypted-blobs.md)に関する記事のソース コード。 この記事では、以前に Azure Key Vault を使用して暗号化されている Azure Blob Storage 内のドキュメントにインデックスを付ける方法について説明します。 |

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。

## <a name="other-samples"></a>その他のサンプル

次のサンプルも Cognitive Search チームによって公開されていますが、ドキュメントでは参照されていません。 関連する readme ファイルでは、使用方法を説明します。

| サンプル | 説明 |
|---------|-------------|
| [クエリの例](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | あいまい検索、RegEx とワイルドカード検索、オートコンプリートなど、さまざまなクエリ手法を示す Postman コレクション。 |