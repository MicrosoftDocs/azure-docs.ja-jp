---
title: BLOB ストレージ内でのコンカレンシーの管理
titleSuffix: Azure Storage
description: アプリケーションでオプティミスティックまたはペシミスティック コンカレンシーを実装することによって、BLOB への複数のライターを管理する方法について説明します。 オプティミスティック コンカレンシーは、BLOB の ETag 値をチェックし、指定された ETag と比較します。 ペシミスティック コンカレンシーは、排他的リースを使用して、他のライターに対して BLOB をロックします。
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96523301"
---
# <a name="managing-concurrency-in-blob-storage"></a>BLOB ストレージ内でのコンカレンシーの管理

最新のアプリケーションでは、複数のユーザーが同時にデータを表示し、更新することがよくあります。 アプリケーション開発者は、予測可能なエクスペリエンスをエンド ユーザーに提供する方法を注意深く検討する必要があります。特に、複数のユーザーが同じデータを更新できる場合はこれが重要になります。 開発者が通常検討するデータのコンカレンシー制御戦略には、主に次の 3 つがあります。  

- **オプティミスティック コンカレンシー**: アプリケーションがデータを更新する場合、更新処理の一部として、データがそのアプリケーションに最後に読み込まれた後に更新されていないか確認します。 たとえば、wiki のページを表示している 2 人のユーザーが同じページを更新しようとしている場合、wiki のプラットフォームは、2 番目の更新が最初の更新を上書きしないようにする必要があります。 また、両方のユーザーが自身の更新の成否を把握できるようにする必要もあります。 この戦略は、Web アプリケーションで最も広く使用されています。

- **ペシミスティック コンカレンシー**: 更新を実行しようとするアプリケーションがオブジェクトをロックし、ロックが解除されるまで他のユーザーがデータを更新できないようにします。 たとえば、プライマリとセカンダリの間でデータを複製する場合、プライマリのみが更新を実行するため、通常は、プライマリがデータを一定期間、排他的にロックして、該当するデータを他から更新できないようにします。

- **最終書き込み者優先**: データの読み取り後に別のアプリケーションがそのデータを更新したかどうかを最初に確認せずに、更新操作を続行できるようにする方法です。 この方法は、通常、複数のユーザーが同時に同じデータにアクセスしないように、データをパーティション分割する場合に使用します。 また、有効期限が短いデータ ストリームの処理にも有効です。

Azure Storage では 3 つの方法すべてがサポートされていますが、オプティミスティックとペシミスティックのコンカレンシーの完全なサポートを提供できるという特徴があります。 Azure Storage は、サービスが挿入または更新操作を実行した後、後続の読み取り操作が最新の更新を返すことを保証する、強力な整合性モデルを採用して設計されています。

また、開発者は、適切なコンカレンシー戦略を選択する以外にも、特に複数のトランザクションで同一のオブジェクトを変更する場合に、ストレージ プラットフォームで変更がどのように分離されるかについて把握しておく必要があります。 Azure Storage にはスナップショット分離が使用されており、同一パーティション内での読み取り処理と書き込み処理を同時に実行できるようになっています。 スナップショット分離では、更新が行われている間でも、すべての読み取り操作がデータの一貫したスナップショットを返すことが保証されます。

BLOB およびコンテナーへのアクセスを管理する場合、オプティミスティック コンカレンシーとペシミスティック コンカレンシーのいずれかのモデルを使用できます。 明示的に戦略を指定しない場合は、最終書き込み者優先が既定となります。  

## <a name="optimistic-concurrency"></a>オプティミスティック コンカレンシー

Azure Storage では、格納されているすべてのオブジェクトに識別子が割り当てられます。 オブジェクトで書き込み処理が実行されるたびに、この識別子が更新されます。 この識別子は、HTTP プロトコルで定義されている ETag ヘッダーを使用して、HTTP GET 応答の一部としてクライアントに返されます。

更新を実行しているクライアントは、元の ETag を条件ヘッダーとともに送信して、特定の条件が満たされた場合にのみ更新が行われるようにすることができます。 たとえば、**If-Match** ヘッダーが指定されている場合は、更新要求で指定された ETag の値が更新されるオブジェクトの ETag と同じであることを、Azure Storage で検証します。 条件ヘッダーの詳細については、「[Blob service 操作の条件ヘッダーの指定](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)」を参照してください。

このプロセスの概要は次のとおりです。  

1. Azure Storage から BLOB を取得します。 この応答に含まれる HTTP ETag ヘッダーの値が、オブジェクトの現在のバージョンを示します。
1. BLOB を更新するときに、手順 1. で受け取った、書き込み要求の **If-Match** 条件ヘッダーで返された ETag の値を含めます。 Azure Storage によって、要求内の ETag の値と BLOB の現在の ETag の値が比較されます。
1. BLOB の現在の ETag 値が、要求で指定された **If-Match** 条件ヘッダーで指定された値と異なる場合、Azure Storage は HTTP 状態コード 412 (必須条件に失敗) を返します。 このエラーは、クライアントが最初にこの BLOB を取得した後に、別のプロセスが更新したことを示しています。
1. BLOB の現在の ETag の値が、要求の **If-Match** 条件ヘッダーの ETag と同じバージョンである場合、Azure Storage は要求された処理を実行し、この BLOB の現在の ETag の値を更新します。  

次のコード例は、BLOB の ETag 値をチェックする書き込み要求に対して **If-Match** 条件を構築する方法を示しています。 Azure Storage は、BLOB の現在の ETag が要求で提供される ETag と同じであるかどうかを評価し、2 つの ETag 値が一致する場合にのみ書き込み操作を実行します。 その間に別のプロセスが BLOB を更新した場合、Azure Storage によって HTTP 412 (必須条件に失敗) のステータス メッセージが返されます。  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure Storage は、**If-Modified-Since**、**If-Unmodified-Since**、**If-None-Match** など、他の条件ヘッダーもサポートします。 詳細については、「[BLOB サービス操作の条件ヘッダーの指定](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)」を参照してください。  

## <a name="pessimistic-concurrency-for-blobs"></a>BLOB でのペシミスティック コンカレンシー制御

BLOB をロックして排他的に使用する場合は、リースを取得します。 リースを取得するときは、リース期間を指定します。 有限リースは、15 - 60 秒の範囲で有効な場合があります。 リース期間は無限の場合もあり、その場合は排他ロックになります。 リース期間が有限の場合、延長することができます。また、完了したリースは解放できます。 期限が切れた有限のリースは、Azure Storage によって自動的に解放されます。  

リースでは、排他的書き込みと共有読み取りの操作、排他的書き込みと排他的読み取りの操作、共有書き込みと排他的読み取りの操作など、さまざまな同期戦略がサポートされています。 リースが存在する場合、Azure Storage はリース所有者の書き込み操作に排他的アクセスを適用します。 ただし、読み込み操作の排他性を保証するために、開発者は、すべてのクライアント アプリケーションがリース ID を使用し、また有効なリース ID は同時に 1 つのクライアントのみが保持するようにする必要があります。 読み込み操作にリース ID を使用しない場合、共有読み取りになります。  

次のコード例では、BLOB に対して排他的リースを取得し、リース ID を指定して BLOB の内容を更新してから、リースを解放する方法を示します。 リースがアクティブで、書き込み要求でリース ID が指定されていない場合、その書き込み操作は失敗し、エラーコード 412 (必須条件に失敗) が表示されます。  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>コンテナーでのペシミスティック コンカレンシー制御

コンテナーのリースでは、排他的書き込みと共有読み取り、排他的書き込みと排他的読み取り、共有書き込みと排他的読み取りなど、BLOB でサポートされるのと同じ同期戦略がサポートされます。 ただし、コンテナーの場合、排他的ロックは削除操作にのみ適用されます。 アクティブなリースを使用してコンテナーを削除するには、クライアントが削除要求に有効なリース ID を含める必要があります。 他のコンテナー操作については、リース ID なしでも、リースされたコンテナーで成功します。  

## <a name="next-steps"></a>次のステップ

* [Blob service 操作の条件ヘッダーの指定](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease Container](/rest/api/storageservices/lease-container)
* [Lease Blob](/rest/api/storageservices/lease-blob)
