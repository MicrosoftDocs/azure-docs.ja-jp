---
title: テクスチャ
description: テクスチャ リソースのワークフロー
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594419"
---
# <a name="textures"></a>テクスチャ

テクスチャは、変更できない[共有リソース](../concepts/lifetime.md)です。 テクスチャは、「[チュートリアル: 環境と素材を変更する](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)」に示されているように、[BLOB ストレージ](../how-tos/conversion/blob-storage.md)から読み込んで、モデルに直接適用することができます。 ただし通常は、テクスチャは[変換されたモデル](../how-tos/conversion/model-conversion.md)の一部になり、ここでその[素材](materials.md)によって参照されます。

## <a name="texture-types"></a>テクスチャの種類

テクスチャの種類によってユース ケースも異なります。

* **2D テクスチャ** は、主に [素材](materials.md)で使用されます。
* **キューブマップ** は、[スカイ](../overview/features/sky.md)に使用できます。

## <a name="supported-texture-formats"></a>サポートされているテクスチャ形式

ARR に提供されるすべてのテクスチャは、[DDS 形式](https://en.wikipedia.org/wiki/DirectDraw_Surface)である必要があります。 可能であれば、ミップマップとテクスチャの圧縮を使用します。

## <a name="loading-textures"></a>テクスチャの読み込み

テクスチャを読み込むときは、必要な種類を指定する必要があります。 種類が一致しない場合、テクスチャの読み込みが失敗します。
同じ URI のテクスチャを 2 回読み込むと、[共有リソース](../concepts/lifetime.md)なので、同じテクスチャ オブジェクトが返されます。

モデルの読み込みと同様に、ソース BLOB ストレージにテクスチャ資産をアドレス指定するには、次の 2 つのバリアントがあります。

* [BLOB ストレージがアカウントにリンクされている](../how-tos/create-an-account.md#link-storage-accounts)場合、BLOB ストレージ パラメーターによってテクスチャを直接アドレス指定することができます。 この場合、関連する読み込み関数は、`LoadTextureAsync` とパラメーター `LoadTextureOptions` です。
* テクスチャ資産は、その SAS URI でアドレス指定できます。 関連する読み込み関数は、`LoadTextureFromSasAsync` とパラメーター `LoadTextureFromSasOptions` です。 [組み込みのテクスチャ](../overview/features/sky.md#built-in-environment-maps)を読み込む場合にもこのバリアントを使用します。

次のサンプル コードは、テクスチャを読み込む方法を示しています。

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

SAS バリアントを使用する場合は、読み込み関数またはパラメーターのみが異なることに注意してください。

用途に対してサポートされるテクスチャの種類によっては、テクスチャの種類とコンテンツに制限がある場合があります。 たとえば、[PBR 素材](../overview/features/pbr-materials.md)の粗さマップはグレースケールである必要があります。

## <a name="api-documentation"></a>API のドキュメント

* [C# Texture クラス](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection.LoadTextureAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection.LoadTextureFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [C++ Texture クラス](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection::LoadTextureAsync()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection::LoadTextureFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>次のステップ

* [素材](materials.md)
* [スカイ](../overview/features/sky.md)