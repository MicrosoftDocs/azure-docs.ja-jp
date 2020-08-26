---
title: 資産変換 REST API
description: REST API を介して資産を変換する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 5c638b434ceb31b57689b11971f48eb322b94726
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985616"
---
# <a name="use-the-model-conversion-rest-api"></a>モデル変換 REST API を使用する

[モデル変換](model-conversion.md)サービスでは、[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer) を使用して制御を行います。 この記事では、変換サービス API の詳細について説明します。

## <a name="regions"></a>リージョン

ベース URL からの要求の送信先として[使用できるリージョンの一覧](../../reference/regions.md)を参照してください。

## <a name="common-headers"></a>共通のヘッダー

### <a name="common-request-headers"></a>共通の要求ヘッダー

これらのヘッダーは、すべての要求に対して指定する必要があります。

- **Authorization** ヘッダーの値は、"ベアラー *TOKEN*" である必要があります。ここでは、*TOKEN* は、[サービス アクセス トークン](../tokens.md)です。

### <a name="common-response-headers"></a>共通の応答ヘッダー

すべての応答には、次のヘッダーが含まれます。

- **MS-CV** ヘッダーには、サービス内での呼び出しをトレースするのに使用できる、一意の文字列が含まれています。

## <a name="endpoints"></a>エンドポイント

変換サービスでは、3 つの REST API エンドポイントを指定して、次のことを行います。

- Azure Remote Rendering アカウントにリンクされたストレージ アカウントを使用して、モデルの変換を開始します。 
- 指定された *Shared Access Signature (SAS)* を使用して、モデル変換を開始します。
- 変換の状態をクエリします。

### <a name="start-conversion-using-a-linked-storage-account"></a>リンクされたストレージ アカウントを使用して変換を開始する
Azure Remote Rendering アカウントは、[ストレージ アカウントにリンクする](../create-an-account.md#link-storage-accounts)方法に関するステップに従い、指定されたストレージ アカウントにアクセス可能である必要があります。

| エンドポイント | Method |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/create | POST |

JSON ドキュメントにラップされている実行中の変換の ID を返します。 フィールド名は "conversionId" です。

#### <a name="request-body"></a>要求本文

> [!NOTE]
> Azure で変換を実行するために、`input.folderPath` 配下のものがすべて取得されます。 `input.folderPath` が指定されていない場合、コンテナーのコンテンツ全体が取得されます。 取得されるすべての BLOB およびフォルダーには、[有効な Windows ファイル名](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions)が指定されている必要があります。

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>指定された Shared Access Signature を使用して変換を開始する
ARR アカウントがストレージ アカウントにリンクされていない場合、この REST インターフェイスを使用すると、*Shared Access Signature (SAS)* を使用してアクセスを提供できます。

| エンドポイント | Method |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

JSON ドキュメントにラップされている実行中の変換の ID を返します。 フィールド名は `conversionId` です。

#### <a name="request-body"></a>要求本文

要求本文は上記の REST 呼び出しの作成と同じですが、入力と出力には "*Shared Access Signature (SAS) トークン*" が含まれています。 これらのトークンは、入力を読み取り、変換結果を書き込むための、ストレージ アカウントへのアクセスを提供します。

> [!NOTE]
> これらの SAS URI トークンは、クエリ文字列であり、完全な URI ではありません。 

> [!NOTE]
> Azure で変換を実行するために、`input.folderPath` 配下のものがすべて取得されます。 `input.folderPath` が指定されていない場合、コンテナーのコンテンツ全体が取得されます。 取得されるすべての BLOB およびフォルダーには、[有効な Windows ファイル名](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions)が指定されている必要があります。

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>ポーリング変換の状態
上記のいずれかの REST 呼び出しで開始された実行中の変換の状態は、次のインターフェイスを使用して照会できます。


| エンドポイント | Method |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/**conversionId** | GET |

次の値を持つことができる "status" フィールドを含む JSON ドキュメントを返します。

- "作成済み"
- "Running"
- "Success"
- "Failure"

状態が "Failure" の場合は、エラー情報を含む "message" サブフィールドを含む "error" フィールドが追加されます。 追加のログは、出力コンテナーにアップロードされます。

## <a name="next-steps"></a>次のステップ

- [モデル変換に Azure Blob Storage を使用する](blob-storage.md)
- [モデル変換](model-conversion.md)
