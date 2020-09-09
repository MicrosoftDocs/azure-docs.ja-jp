---
title: Azure Cosmos DB の添付ファイル
description: この記事では Azure Cosmos DB の添付ファイルの概要について説明します。
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 27d9297809d2bd028918885a88faf55e8314b71d
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817255"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB の添付ファイル

Azure Cosmos DB の添付ファイルは、外部 BLOB またはメディア ファイルと関連付けられているメタデータへの参照を含む特殊な項目です。

Azure Cosmos DB では 2 種類の添付ファイルがサポートされます。

* **管理対象外の添付ファイル**は、外部サービス (たとえば、Azure Storage、OneDrive など) に格納されている BLOB への URI 参照のラッパーです。 この方法は、標準の Azure Cosmos DB 項目に URI プロパティを格納することと似ています。
* **管理対象の添付ファイル**は、Azure Cosmos DB によって内部的に管理および格納され、システムによって生成された mediaLink を介して公開される BLOB です。


> [!NOTE]
> 添付ファイルはレガシ機能です。 この機能を既に使用している場合は、機能が引き続きサポートされます。
> 
> 添付ファイルを使用する代わりに、BLOB データを格納するための専用の BLOB ストレージ サービスとして Azure BLOB ストレージを使用することをお勧めします。 BLOB に関連するメタデータは、参照 URI リンクと共に、Azure Cosmos DB に項目プロパティとして引き続き格納できます。 このデータを Azure Cosmos DB に格納することで、メタデータを照会し、Azure BLOB ストレージに格納されている BLOB へのリンクを提供することができます。
> 
> Microsoft は、添付ファイルを完全に非推奨にする前に、36 か月以上の通知期間を提供することを約束しています。これは、今後発表される予定です。

## <a name="known-limitations"></a>既知の制限事項

Azure Cosmos DB の管理対象の添付ファイルは、標準項目のサポートとは異なります。標準項目に対しては、無制限のスケーラビリティ、グローバル配信、および他の Azure サービスとの統合が提供されます。

- Azure Cosmos DB の SDK には、添付ファイルがサポートされていないバージョンもあります。
- 管理対象の添付ファイルは、データベース アカウントごとに 2 GB のストレージに制限されます。
- 管理対象の添付ファイルは、Azure Cosmos DB のグローバル配信と互換性がなく、リージョン間でレプリケートされません。

## <a name="migrating-attachments-to-azure-blob-storage"></a>添付ファイルの Azure BLOB ストレージへの移行

次の手順で Azure Cosmos DB の添付ファイルを Azure BLOB ストレージに移行することをお勧めします。

1. 移行元の Azure Cosmos DB コンテナーから移行先の Azure BLOB ストレージ コンテナーに、添付ファイルのデータをコピーします。
2. アップロードされた BLOB データを移行先の Azure BLOB ストレージ コンテナーで検証します。
3. 必要に応じて、Azure BLOB ストレージに含まれている BLOB への URI 参照を Azure Cosmos DB データセット内の文字列プロパティとして追加します。
4. 新しい Azure BLOB ストレージ コンテナーから BLOB の読み取りと書き込みを行うようにアプリケーション コードをリファクタリングします。

次の dotnet コード サンプルは、Azure Cosmos DB の .NET SDK v2 と Azure Blob Storage .NET SDK v12 を使用して、移行フローの一部として Azure Cosmos DB から Azure BLOB ストレージに添付ファイルをコピーする方法を示しています。 移行元の Azure Cosmos DB アカウントと移行先の Azure BLOB ストレージ コンテナーの `<placeholder values>` を必ず置き換えてください。

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = new PartitionKey(document.Id),
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>次のステップ

- [Azure BLOB ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet)を使用する
- [Azure Cosmos DB の .NET SDK v2](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.attachment?view=azure-dotnet) を用いて添付ファイルを使用するための参照を取得する
- [Azure Cosmos DB の Java SDK v2](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.attachment?view=azure-java-stable) を用いて添付ファイルを使用するための参照を取得する
- [Azure Cosmos DB の REST API](https://docs.microsoft.com/rest/api/cosmos-db/attachments) を用いて添付ファイルを使用するための参照を取得する
