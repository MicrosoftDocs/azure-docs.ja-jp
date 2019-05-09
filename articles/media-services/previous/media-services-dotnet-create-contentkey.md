---
title: .NET を使用したコンテンツ キーの作成
description: アセットに安全にアクセスできる ContentKey を作成する方法について学習します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab26be3b9ac5d209cfe8117bdf9e87e0c7e74188
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183086"
---
# <a name="create-contentkeys-with-net"></a>.NET を使用したコンテンツ キーの作成 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services では、暗号化されたアセットを作成して送信できます。 **ContentKey** により、**アセット**に安全にアクセスすることができます。 

新しいアセットを作成するときは ([ファイルをアップロード](media-services-dotnet-upload-files.md)する前など)、次の暗号化オプションを指定できます:**StorageEncrypted**、**CommonEncryptionProtected**、または **EnvelopeEncryptionProtected**。 

クライアントにアセットを配信するときは、次の 2 つの暗号化のどちらかを使用して[アセットが動的に暗号化されるように構成できます](media-services-dotnet-configure-asset-delivery-policy.md):**DynamicEnvelopeEncryption** または **DynamicCommonEncryption**。

暗号化されたアセットには、 **ContentKey**を関連付ける必要があります。 この記事では、コンテンツ キーの作成方法について説明します。

> [!NOTE]
> Media Services .NET SDK を使用して新しい **StorageEncrypted** のアセットを作成する場合、**ContentKey** が自動的に作成され、アセットにリンクします。
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
コンテンツ キーの作成時には、タイプを設定する必要があります。 次のいずれかの値を選択します。 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a id="envelope_contentkey"></a>エンベロープ タイプの ContentKey の作成
次のコード スニペットでは、エンベロープ暗号化タイプのコンテンツ キーを作成します。 その後、キーを指定されたアセットに関連付けます。

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a id="common_contentkey"></a>共通タイプの ContentKey の作成
次のコード スニペットでは、共通暗号化タイプのコンテンツ キーを作成します。 その後、キーを指定されたアセットに関連付けます。

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

