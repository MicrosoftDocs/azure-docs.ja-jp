---
title: BLOB ストレージに対する要求で暗号化キーを指定する
titleSuffix: Azure Storage
description: Azure Blob Storage に対して要求を行うクライアントには、要求ごとに暗号化キーを指定するオプションがあります。 要求に暗号化キーを含めると、BLOB ストレージ操作の暗号化設定をきめ細かく制御できます。
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97694698"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>BLOB ストレージに対する要求で暗号化キーを指定する

Azure Blob Storage に対して要求を行うクライアントには、要求ごとに AES-256 暗号化キーを指定するオプションがあります。 要求に暗号化キーを含めると、BLOB ストレージ操作の暗号化設定をきめ細かく制御できます。 カスタマー指定のキーは、Azure Key Vault または別のキー ストアに格納できます。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>読み取り操作と書き込み操作の暗号化

クライアント アプリケーションが要求に対して暗号化キーを指定すると、Azure Storage は BLOB データの読み取りおよび書き込み中に暗号化と暗号化解除を透過的に実行します。 Azure Storage は、暗号化キーの SHA-256 ハッシュを BLOB の内容と共に書き込みます。 このハッシュを使用して、BLOB に対する後続のすべての操作で同じ暗号化キーが使用されているか確認されます。

Azure Storage は、クライアントが要求と共に送信する暗号化キーの格納も管理も行いません。 暗号化または暗号化解除のプロセスが完了するとすぐに、キーは安全に破棄されます。

クライアントが、要求のカスタマー指定のキーを使用して BLOB を作成または更新した場合は、その BLOB に対する以降の読み取りと書き込みの要求でもそのキーを指定する必要があります。 カスタマー指定のキーで既に暗号化されている BLOB の要求にキーが指定されていない場合、要求はエラーコード 409 (競合) で失敗します。

クライアント アプリケーションが要求で暗号化キーを送信し、ストレージ アカウントも Microsoft のマネージド キーまたはカスタマー マネージド キーを使用して暗号化されている場合、Azure Storage は、要求で指定されたキーを暗号化と暗号化解除に使用します。

暗号化キーを要求の一部として送信するために、クライアントは HTTPS を使用して Azure Storage へのセキュリティで保護された接続を確立する必要があります。

各 BLOB スナップショットには、独自の暗号化キーを保持できます。

## <a name="request-headers-for-specifying-customer-provided-keys"></a>カスタマー指定のキーを指定するための要求ヘッダー

REST 呼び出しの場合、クライアントは次のヘッダーを使用して、要求の暗号化キー情報を BLOB ストレージに安全に渡すことができます。

|要求ヘッダー | 説明 |
|---------------|-------------|
|`x-ms-encryption-key` |書き込み要求と読み取り要求の両方に必要です。 Base64 でエンコードされた AES-256 暗号化キー値です。 |
|`x-ms-encryption-key-sha256`| 書き込み要求と読み取り要求の両方に必要です。 暗号化キーの Base64 でエンコードされた SHA256 です。 |
|`x-ms-encryption-algorithm` | 書き込み要求の場合は必須、読み取り要求の場合は省略可能です。 指定されたキーを使用してデータを暗号化するときに使用するアルゴリズムを指定します。  このヘッダーの値は `AES256` である必要があります。 |

要求に対する暗号化キーの指定は省略できます。 ただし、上記のいずれかのヘッダーを書き込み操作に指定する場合は、それらのすべてを指定する必要があります。

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>カスタマー指定のキーをサポートしている BLOB ストレージ操作

次の BLOB ストレージ操作では、要求でカスタマー指定の暗号化キーを送信することをサポートしています。

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block from URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>カスタマー指定のキーのローテーション

BLOB の暗号化に使用された暗号化キーをローテーションするには、BLOB をダウンロードし、新しい暗号化キーを使用して再アップロードします。

> [!IMPORTANT]
> Azure portal を使用して、要求で指定されたキーで暗号化されたコンテナーまたは BLOB への読み取りおよび書き込みを行うことはできません。
>
> BLOB ストレージに対する要求で指定した暗号化キーは、Azure Key Vault などのセキュリティで保護されたキー ストア内で必ず保護してください。 暗号化キーを使用せずにコンテナーまたは BLOB に対して書き込み操作を試みると、操作は失敗し、そのオブジェクトにアクセスできなくなります。

## <a name="next-steps"></a>次のステップ

- [.NET での BLOB ストレージの要求時にカスタマー指定のキーを指定する](storage-blob-customer-provided-key.md)
- [保存データに対する Azure Storage 暗号化](../common/storage-service-encryption.md)
