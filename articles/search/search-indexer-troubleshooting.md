---
title: インデクサーの一般的な問題のトラブルシューティング - Azure Search
description: データ ソース接続、ファイアウォール、および不足しているドキュメントなど、Azure Search でのインデクサーのエラーと一般的な問題を修正。
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: b527b759eb9c76ab6289e909001c5f7820f34ef4
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652418"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Azure Search のインデクサーの一般的な問題のトラブルシューティング

Azure Search においてデータにインデックスを付けるとき、インデクサーでいくつかの問題が発生することがあります。 エラーは次のように分類されます。

* [データ ソースへの接続](#data-source-connection-errors)
* [ドキュメントの処理](#document-processing-errors)
* [インデックスへのドキュメントの取り込み](#index-errors)

## <a name="data-source-connection-errors"></a>データ ソースの接続エラー

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>ストレージ アカウント ファイアウォール

Azure Storage では、構成可能なファイアウォールが提供されます。 既定ではファイアウォールは無効になっているため、Azure Search はご使用のストレージ アカウントに接続できます。

ファイアウォールが有効になっているとき、特定のエラー メッセージはありません。 通常、ファイアウォールのエラーは `The remote server returned an error: (403) Forbidden` のようなものです。

ファイアウォールが有効であることは[ポータル](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)で確認できます。 ファイアウォールが有効である場合、この問題を回避するために 2 つの方法があります。

1. [[すべてのネットワーク]](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal) からのアクセスを許可することを選択して、ファイアウォールを無効にします。
1. 検索サービスの IP アドレスを[例外として追加](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules)します。 この IP アドレスを検索するには、次のコマンドを使用します。

`nslookup <service name>.search.windows.net`

例外は[コグニティブ検索](cognitive-search-concept-intro.md)では使用できません。 唯一の回避策はファイアウォールを無効にすることです。

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>インデックス付けが有効でない

Azure Search は、Cosmos DB のインデックス付けに暗黙に依存しています。 Cosmos DB の自動インデックス付けをオフにすると、Azure Search から成功状態が返されますが、コンテナーの内容をインデックス付けすることができません。 設定を確認してインデックス付けをオンにする手順については、[Azure Cosmos DB でのインデックス付けの管理](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#manage-indexing-using-azure-portal)に関する記事をご覧ください。

## <a name="document-processing-errors"></a>ドキュメントの処理エラー

### <a name="unprocessable-or-unsupported-documents"></a>処理できないドキュメントまたはサポートされていないドキュメント

BLOB インデクサーでは、[明示的にサポートされているドキュメント形式が説明](search-howto-indexing-azure-blob-storage.md#supported-document-formats)されています。 場合によっては、サポートされないドキュメントが BLOB ストレージ コンテナーに含まれています。 問題があるドキュメントがあることもあります。 [構成オプションを変更](search-howto-indexing-azure-blob-storage.md#dealing-with-errors)すると、このようなドキュメントに対してインデクサーを停止することを回避できます。

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>ドキュメントの内容の欠落

BLOB インデクサーによって、[コンテナー内の BLOB からテキストが検索されて抽出](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs)されます。 テキストの抽出に関する問題には次のものがあります。

* ドキュメントに、スキャンしたイメージしか含まれていません。 スキャンしたイメージ (JPG) などテキスト以外のコンテンツを含む PDF BLOB では、標準 BLOB インデックス パイプラインで結果が生成されません。 イメージ コンテンツにテキスト要素が含まれる場合は、[コグニティブ検索](cognitive-search-concept-image-scenarios.md)を使用して、テキストを検索して抽出できます。
* BLOB インデクサーは、メタデータのインデックス付けのみを行うように構成されています。 コンテンツを抽出するには、[コンテンツとメタデータの両方を抽出](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)するように BLOB インデクサーを構成する必要があります。

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>インデックスのエラー

### <a name="missing-documents"></a>ドキュメントの欠落

インデクサーによって[データ ソース](https://docs.microsoft.com/rest/api/searchservice/create-data-source)でドキュメントが検索されます。 場合によっては、インデックスが付けられているはずのデータ ソースのドキュメントが、インデックスに存在しないように見えることがあります。 このようなエラーが発生する一般的な理由がいくつかあります。

* ドキュメントがインデックス付けされていません。 インデクサーの実行が成功したかどうかポータルで確認します。
* インデクサーの実行後にドキュメントが更新されませんでした。 インデクサーが[スケジュール](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)設定されている場合は、いずれ再実行されて、ドキュメントを処理します。
* データ ソースに指定されている[クエリ](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax)によって、ドキュメントが除外されています。 インデクサーは、データ ソースに含まれないドキュメントにインデックス付けすることはできません。
* [フィールド マッピング](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings)または[コグニティブ検索](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)によってドキュメントが変更され、予期したドキュメントと違うように見えます。
* [Lookup Document API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) を使用してドキュメントを検索します。
