---
title: .NET での BLOB ストレージの要求時にカスタマー指定のキーを指定する - Azure Storage
description: .NET を使用した BLOB ストレージの要求時に、カスタマー指定のキーを指定する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693508"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>.NET での BLOB ストレージの要求時にカスタマー指定のキーを指定する

Azure Blob Storage に対して要求を行うクライアントには、個々の要求に対して AES-256 暗号化キーを指定するオプションがあります。 要求に暗号化キーを含めると、BLOB ストレージ操作の暗号化設定をきめ細かく制御できます。 カスタマー指定のキーは、Azure Key Vault または別のキー ストアに格納できます。

この記事では、.NET での BLOB ストレージの要求時にカスタマー指定のキーを指定する方法を示します。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure ID クライアント ライブラリによる認証方法の詳細については、「[.NET 用 Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)」を参照してください。

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>カスタマー指定のキーを使用して BLOB に書き込む

次の例では、BLOB ストレージの v12 クライアント ライブラリを使用して BLOB をアップロードするときに、AES-256 キーを指定します。 この例では、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) オブジェクトを使用して Azure AD で書き込み要求を認可しますが、共有キー資格情報で要求を認可することもできます。

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>次のステップ

- [BLOB ストレージに対する要求で暗号化キーを指定する](encryption-customer-provided-keys.md)
- [保存データに対する Azure Storage 暗号化](../common/storage-service-encryption.md)
