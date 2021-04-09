---
title: キャッシュとインクリメンタル エンリッチメントの構成 (プレビュー)
titleSuffix: Azure Cognitive Search
description: キャッシュを有効にし、認知スキルセットの制御された処理のためにエンリッチされたコンテンツの状態を保持します。 現在、この機能はパブリック プレビュー段階にあります。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90971607"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search でインクリメンタル エンリッチメントのキャッシュを構成する方法

> [!IMPORTANT] 
> インクリメンタル エンリッチメントは現在、パブリック プレビューの段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 
> [REST API プレビュー バージョン](search-api-preview.md)にはこの機能が用意されています。 現時点で、ポータルまたは .NET SDK はサポートされていません。

この記事では、エンリッチメント パイプラインにキャッシュを追加して、毎回リビルドしなくてもステップを段階的に変更できるようにする方法について説明します。 既定では、スキルセットはステートレスであり、その構成の任意の部分を変更するには、インデクサーを完全に再実行する必要があります。 インクリメンタル エンリッチメントを使用すると、スキルセットまたはインデクサーの定義で検出された変更に基づいて、ドキュメント ツリーのどの部分を更新する必要があるかをインデクサーで判断できます。 既存の処理済みの出力は保持され、可能な限り再利用されます。 

キャッシュされたコンテンツは、指定したアカウント情報を使用して Azure Storage に配置されます。 `ms-az-search-indexercache-<alpha-numerc-string>` という名前のコンテナーは、インデクサーの実行時に作成されます。 これは検索サービスによって管理される内部コンポーネントと見なされ、変更することはできません。

インデクサーの設定に慣れていない場合は、[インデクサーの概要](search-indexer-overview.md)から開始し、[スキルセット](cognitive-search-working-with-skillsets.md)に進んで、エンリッチメント パイプラインについて学習してください。 主要な概念の背景の詳細については、[インクリメンタル エンリッチメント](cognitive-search-incremental-indexing-conceptual.md)に関するページを参照してください。

## <a name="enable-caching-on-an-existing-indexer"></a>既存のインデクサーでキャッシュを有効にする

既にスキルセットがある既存のインデクサーがある場合は、このセクションの手順に従ってキャッシュを追加します。 1 回限りの操作では、増分処理が有効になる前に、インデクサーを完全にリセットして再実行する必要があります。

> [!TIP]
> 概念実証として、この[ポータルのクイックスタート](cognitive-search-quickstart-blob.md)を実行して必要なオブジェクトを作成し、Postman またはポータルを使用して更新を行うことができます。 課金対象の Cognitive Services リソースをアタッチすることもできます。 インデクサーを複数回実行すると、すべての手順を完了する前に、1 日あたりの割り当てが解放されます。

### <a name="step-1-get-the-indexer-definition"></a>手順 1:インデクサーの定義を取得する

コンポーネント (データソース、スキルセット、インデックス) を含む既存の有効なインデクサーを使用して開始します。 インデクサーは実行可能である必要があります。 

API クライアントを使用して、[GET インデクサー要求](/rest/api/searchservice/get-indexer)を作成してインデクサーの現在の構成を取得します。 プレビュー API バージョンを使用してインデクサーを取得すると、null に設定された `cache` プロパティが定義に追加されます。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

応答からインデクサーの定義をコピーします。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>手順 2:インデクサー定義の cache プロパティを変更する

既定では、`cache` プロパティは null です。 API クライアントを使用してキャッシュ構成を設定します (ポータルではこの微小な更新はサポートされていません)。 

キャッシュ オブジェクトを変更して、次の必須プロパティと省略可能なプロパティを含めます。 

+ `storageConnectionString` は必須であり、Azure ストレージ接続文字列に設定する必要があります。 
+ `enableReprocessing` のブール型プロパティは省略可能であり (既定では `true`)、これはインクリメンタル エンリッチメントが有効になっていることを示します。 必要に応じて、これを `false` に設定すると、新しいドキュメントのインデックス作成などのリソースを大量に消費する他の操作の実行中に増分処理を一時停止させ、後で `true` に戻すことができます。

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>手順 3:インデクサーをリセットする

すべてのドキュメントが一貫した状態になるように、既存のインデクサーにインクリメンタル エンリッチメントを設定するときは、インデクサーをリセットする必要があります。 このタスクには、ポータルまたは API クライアントと [Reset Indexer REST API](/rest/api/searchservice/reset-indexer) を使用することができます。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>手順 4:更新された定義を保存する

PUT 要求を使用して[インデクサーを更新](/rest/api/searchservice/preview-api/update-indexer)します。要求の本文には、cache プロパティを含む更新されたインデクサーの定義を含める必要があります。 400 が表示された場合は、インデクサーの定義を確認して、すべての要件が満たされていることを確認します (データ ソース、スキルセット、インデックス)。

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

ここでインデクサーに別の GET 要求を発行した場合、サービスからの応答には、キャッシュ オブジェクトの `ID` プロパティが含まれます。 このインデクサーによって処理される各ドキュメントのキャッシュされたすべての結果と中間状態が格納されているコンテナーの名前に、英数字文字列が追加されます。 ID は、BLOB ストレージでキャッシュの名前を一意に指定するために使用されます。

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>手順 5:インデクサーを実行する

インデクサーを実行するには、ポータルまたは API を使用します。 ポータルでは、インデクサーの一覧からインデクサーを選択し、 **[実行]** をクリックします。 ポータルを使用する利点の 1 つは、インデクサーの状態を監視し、ジョブの実行時間と処理されるドキュメントの数を確認できることです。 ポータルのページは、数分ごとに更新されます。

別の方法として、REST を使用して[インデクサーを実行](/rest/api/searchservice/run-indexer)することもできます。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

インデクサーの実行後、Azure BLOB ストレージでキャッシュを見つけることができます。 コンテナー名の形式は `ms-az-search-indexercache-<YOUR-CACHE-ID>` です

> [!NOTE]
> インデクサーをリセットして再実行すると、コンテンツをキャッシュできるように完全な再構築が実行されます。 すべてのコグニティブ エンリッチメントが、すべてのドキュメントで再実行されます。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>手順 6:スキルセットを変更してインクリメンタル エンリッチメントを確認する

スキルセットを変更するには、ポータルまたは API を使用します。 たとえば、テキスト変換を使用している場合、`en` から `es` または別の言語への単純なインライン変更は、インクリメンタル エンリッチメントの概念実証テストに十分に適しています。

インデクサーを再度実行します。 エンリッチされたドキュメント ツリーの部分だけが更新されます。 [ポータルのクイックスタート](cognitive-search-quickstart-blob.md)を概念実証として使用した場合、テキスト翻訳のスキルを 'es' に変更すると、元の 14 ではなく、8 個のドキュメントのみが更新されることがわかります。 翻訳プロセスによって影響を受けない画像ファイルは、キャッシュから再利用されます。

## <a name="enable-caching-on-new-indexers"></a>新しいインデクサーでキャッシュを有効にする

新しいインデクサーのインクリメンタル エンリッチメントを設定するには、[[インデクサーの作成] (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) を呼び出すときにインデクサー定義のペイロードに `cache` プロパティを含めるだけです。 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>キャッシュされた出力の確認

キャッシュはインデクサーによって作成、使用、および管理され、その内容は人間が判読できる形式では表されません。 キャッシュが使用されるかどうかを判断する最善の方法は、インデクサーを実行し、実行時間とドキュメント数の前後のメトリックを比較することです。 

たとえば、スキャンされたドキュメントの画像分析と光学式文字認識 (OCR) で始まり、結果として得られるテキストのダウンストリーム分析が続くスキルセットがあるとします。 ダウンストリーム テキストのスキルを変更すると、インデクサーは、以前に処理されたすべてのイメージと OCR のコンテンツをキャッシュから取得し、編集によって示されたテキスト関連の変更だけを更新および処理することができます。 ドキュメント数に含まれるドキュメントの数が少なくなり (たとえば、元の実行での 14/14 ではなく 8/8)、実行時間が短縮され、請求書の料金が少なくなることが期待できます。

## <a name="working-with-the-cache"></a>キャッシュの使用

キャッシュが操作可能になると、インデクサーは、[[インデクサーの実行]](/rest/api/searchservice/run-indexer) が呼び出さるたびにキャッシュをチェックして、既存の出力のどの部分を使用できるかを確認します。 

次の表は、さまざまな API がキャッシュにどのように関連しているかをまとめたものです。

| API           | キャッシュの影響     |
|---------------|------------------|
| [インデクサーの作成 (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) | インデクサー定義で指定されている場合は、キャッシュの作成などの最初の使用時にインデクサーを作成して実行します。 |
| [インデクサー実行](/rest/api/searchservice/run-indexer) | 必要に応じてエンリッチメント パイプラインを実行します。 この API は、キャッシュが存在する場合はキャッシュから読み取り、更新されたインデクサー定義にキャッシュを追加した場合はキャッシュを作成します。 キャッシュが有効になっているインデクサーを実行すると、キャッシュされた出力を使用できる場合はインデクサーが手順を省略します。 この API の一般公開版またはプレビュー版 API を使用できます。|
| [インデクサーのリセット](/rest/api/searchservice/reset-indexer)| 増分インデックスの作成情報のインデクサーをクリアします。 次回のインデクサー実行 (オンデマンドまたはスケジュール) は、すべてのスキルの再実行やキャッシュの再構築を含み、最初から完全に再処理されます。 これは、インデクサーを削除して再作成することと機能的には同じです。 この API の一般公開版またはプレビュー版 API を使用できます。|
| [スキルのリセット](/rest/api/searchservice/preview-api/reset-skills) | スキルを変更していない場合でも、次回のインデクサー実行時に再実行するスキルを指定します。 キャッシュは適宜更新されます。 ナレッジ ストアや検索インデックスなどの出力は、キャッシュにある再利用可能なデータと更新されたスキルに基づく新しいコンテンツを使用して更新されます。 |

キャッシュの動作を制御する方法の詳細については、「[キャッシュ管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)」を参照してください。

## <a name="next-steps"></a>次のステップ

インクリメンタル エンリッチメントは、スキルセットを含むインデクサーに適用されます。 次の手順として、スキルセットのドキュメントを参照して、概念と構成を理解してください。 

また、キャッシュを有効にしたら、特定の動作をオーバーライドまたは強制する方法など、キャッシュに関連するパラメーターと API について知る必要があります。 詳細については、次のリンクを参照してください。

+ [スキルセットの概念と構成](cognitive-search-working-with-skillsets.md)
+ [スキルセットを作成する方法](cognitive-search-defining-skillset.md)
+ [インクリメンタル エンリッチメントとキャッシュの概要](cognitive-search-incremental-indexing-conceptual.md)