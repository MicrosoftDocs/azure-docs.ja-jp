---
title: 変更および削除された BLOB
titleSuffix: Azure Cognitive Search
description: Azure Blob Storage からインポートする最初の検索インデックスの構築後、後続のインデックス作成では、変更または削除された BLOB のみを取得できます。 この記事では詳細について説明します。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383536"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>BLOB インデックス作成での変更と削除の検出 (Azure Cognitive Search)

最初の検索インデックスが作成された後、以降のインデクサー ジョブでは、新規および変更されたドキュメントのみを取得することができます。 Azure Blob Storage のものである検索コンテンツの場合、スケジュールを使用してインデックス作成をトリガーすると、変更の検出が自動的に行われます。 既定では、サービスによって、BLOB の `LastModified` タイムスタンプの指定に従い、変更された BLOB のみのインデックスが再作成されます。 検索インデクサーでサポートされている他のデータ ソースとは対照的に、BLOB には常にタイムスタンプがあるため、変更検出ポリシーを手動で設定する必要がなくなります。

変更の検出は指定されていますが、削除検出は指定されていません。 削除されたドキュメントを検出する場合は、必ず "論理的な削除" アプローチを使用してください。 BLOB を完全に削除すると、対応するドキュメントが Search インデックスから削除されません。

論理的な削除方法を実装するには、2 つの方法があります。

+ 次に説明する、ネイティブ BLOB の論理的な削除 (プレビュー)
+ [カスタム メタデータを使用した論理的な削除](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>ネイティブ BLOB の論理的な削除 (プレビュー)

この削除検出方法の場合、Cognitive Search で Azure BLOB ストレージの[ネイティブ BLOB の論理的な削除](../storage/blobs/soft-delete-blob-overview.md)機能を使用して、BLOB が論理的に削除された状態に移行したかどうかを判断します。 この状態の BLOB が検出されると、この情報が検索インデクサーで使用され、対応するドキュメントがインデックスから削除されます。

> [!IMPORTANT]
> ネイティブ BLOB の論理的な削除のサポートはプレビュー段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 [REST API バージョン 2020-06-30-Preview](./search-api-preview.md) で、この機能を提供しています。 現時点では、ポータルと .NET SDK によるサポートはありません。

### <a name="prerequisites"></a>前提条件

+ [BLOB の論理的な削除を有効にします](../storage/blobs/soft-delete-blob-enable.md)。
+ BLOB は、Azure BLOB ストレージ コンテナー内に存在する必要があります。 Cognitive Search のネイティブ BLOB の論理的な削除ポリシーは、Azure Data Lake Storage Gen2 の BLOB に対してはサポートされていません。
+ インデックス内のドキュメントのドキュメント キーは、BLOB プロパティと BLOB メタデータのどちらかにマップされている必要があります。
+ 論理的な削除のサポートを構成するには、プレビュー REST API (`api-version=2020-06-30-Preview`) を使用する必要があります。

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>ネイティブの論理的な削除を使用して削除検出を構成する方法

1. BLOB ストレージで論理的な削除を有効にするとき、保持ポリシーをインデクサー間隔スケジュールよりも大幅に高い値に設定することをお勧めします。 このようにすると、インデクサーの実行で問題が発生した場合、またはインデックスを作成するドキュメントの数が多い場合に、インデクサーが論理的に削除された BLOB を最終的に処理するのに十分な時間があります。 Azure Cognitive Search インデクサーでは、論理的に削除された状態の BLOB を処理する場合にのみ、インデックスからドキュメントが削除されます。

1. Cognitive Search で、データ ソースに対してネイティブ BLOB の論理的な削除の検出ポリシーを設定します。 次に例を示します。 この機能はプレビュー段階であるため、プレビュー REST API を使用する必要があります。

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [インデクサーを実行する](/rest/api/searchservice/run-indexer)か、または[スケジュールに基づいて](search-howto-schedule-indexers.md)実行するようにインデクサーを設定します。 インデクサーが実行され、論理的な削除状態の BLOB が処理されると、対応する検索ドキュメントがインデックスから削除されます。

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>削除が取り消された BLOB のインデックスの再作成 (ネイティブの論理的な削除ポリシーを使用)

論理的に削除された BLOB を BLOB ストレージに復元した場合、インデクサーでインデックスが常に再作成されるとは限りません。 これは、インデクサーで BLOB の `LastModified` タイムスタンプを使用してインデックス作成が必要かどうかが判断されるためです。 論理的に削除された BLOB の削除が取り消されたとき、その `LastModified` タイムスタンプは更新されないため、インデクサーによって最新の `LastModified` タイムスタンプを持つ BLOB が既に処理されている場合、削除が取り消された BLOB のインデックスは再作成されません。 

削除が取り消された BLOB のインデックス再作成が確実に行われるようにするには、BLOB の `LastModified` タイムスタンプを更新する必要があります。 これを行う 1 つの方法は、その BLOB のメタデータを保存することです。 メタデータを変更する必要はありませんが、メタデータを再保存すると BLOB の `LastModified` タイムスタンプが更新され、インデクサーでそれを選択する必要があることが認識されます。

## <a name="soft-delete-using-custom-metadata"></a>カスタム メタデータを使用した論理的な削除

この方法は、BLOB のメタデータを使用して、検索ドキュメントをインデックスから削除する必要があるかどうかを判断するというものです。 この方法には、2 つの個別のアクションが必要です。インデックスから検索ドキュメントを削除した後、Azure Storage で BLOB を削除します。

BLOB ストレージと Cognitive Search の両方で実行する手順はありますが、その他の機能の依存関係はありません。 この機能は、一般公開されている API でサポートされています。

1. カスタム メタデータのキーと値のペアを BLOB に追加して、これが論理的に削除されていることを Azure Cognitive Search に示します。

1. データ ソースで論理的な削除の列の検出ポリシーを構成します。 たとえば、次のポリシーでは、BLOB のメタデータ プロパティ `IsDeleted` の値が `true` のときに、その BLOB が削除されるものと見なされます。

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. インデクサーによって BLOB が処理され、インデックスからドキュメントが削除されると、Azure Blob Storage の BLOB を削除できます。

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>削除が取り消された BLOB のインデックスの再作成 (カスタム メタデータを使用)

削除された BLOB がインデクサーによって処理され、対応する検索ドキュメントがインデックスから削除された後、BLOB の `LastModified` タイムスタンプが最後に実行されたインデクサーよりも古い場合、後でそれを復元しても、その BLOB に再度アクセスすることはありません。

そのドキュメントのインデックスを再作成する場合は、その BLOB の `"softDeleteMarkerValue" : "false"` を変更して、インデクサーを再実行します。

## <a name="next-steps"></a>次のステップ

+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [BLOB インデクサーを構成する方法](search-howto-indexing-azure-blob-storage.md)
+ [BLOB のインデックス作成の概要](search-blob-storage-integration.md)