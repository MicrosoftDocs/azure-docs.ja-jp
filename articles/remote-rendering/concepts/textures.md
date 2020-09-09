---
title: テクスチャ
description: テクスチャ リソースのワークフロー
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c2951b601690beb9ec64040cf650bb5208d0e4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997712"
---
# <a name="textures"></a>テクスチャ

テクスチャは、変更できない[共有リソース](../concepts/lifetime.md)です。 テクスチャは、「[チュートリアル: 環境と素材を変更する](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)」に示されているように、[BLOB ストレージ](../how-tos/conversion/blob-storage.md)から読み込んで、モデルに直接適用することができます。 ただし通常は、テクスチャは[変換されたモデル](../how-tos/conversion/model-conversion.md)の一部になり、ここでその[素材](materials.md)によって参照されます。

## <a name="texture-types"></a>テクスチャの種類

テクスチャの種類によってユース ケースも異なります。

* **2D テクスチャ**は、主に[素材](materials.md)で使用されます。
* **キューブマップ**は、[スカイ](../overview/features/sky.md)に使用できます。

## <a name="supported-texture-formats"></a>サポートされているテクスチャ形式

ARR に提供されるすべてのテクスチャは、[DDS 形式](https://en.wikipedia.org/wiki/DirectDraw_Surface)である必要があります。 可能であれば、ミップマップとテクスチャの圧縮を使用します。 変換プロセスを自動化する場合は、[TexConv コマンドライン ツール](../resources/tools/tex-conv.md)に関する記事を参照してください。

## <a name="loading-textures"></a>テクスチャの読み込み

テクスチャを読み込むときは、必要な種類を指定する必要があります。 種類が一致しない場合、テクスチャの読み込みが失敗します。
同じ URI のテクスチャを 2 回読み込むと、[共有リソース](../concepts/lifetime.md)なので、同じテクスチャ オブジェクトが返されます。

モデルの読み込みと同様に、ソース BLOB ストレージにテクスチャ資産をアドレス指定するには、次の 2 つのバリアントがあります。

* テクスチャ資産は、その SAS URI でアドレス指定できます。 関連する読み込み関数は、`LoadTextureFromSASAsync` とパラメーター `LoadTextureFromSASParams` です。 [組み込みのテクスチャ](../overview/features/sky.md#built-in-environment-maps)を読み込む場合にもこのバリアントを使用します。
* [BLOB ストレージがアカウントにリンクされている](../how-tos/create-an-account.md#link-storage-accounts)場合、BLOB ストレージ パラメーターによってテクスチャを直接アドレス指定することができます。 この場合、関連する読み込み関数は、`LoadTextureAsync` とパラメーター `LoadTextureParams` です。

次のサンプル コードは、SAS URI (または組み込みテクスチャ) を使用してテクスチャを読み込む方法を示しています。他のケースでは、読み込む関数とパラメーターだけが異なることに注意してください。

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```


用途に対してサポートされるテクスチャの種類によっては、テクスチャの種類とコンテンツに制限がある場合があります。 たとえば、[PBR 素材](../overview/features/pbr-materials.md)の粗さマップはグレースケールである必要があります。

> [!CAUTION]
> ARR ではすべての *Async* 関数が非同期操作オブジェクトを返します。 操作が完了するまで、これらのオブジェクトへの参照を保存する必要があります。 そうしないと、C# ガベージ コレクターによって操作が早期に削除されて、完了できなくなる場合があります。 上のサンプル コードでは、*Completed* イベントが到着するまで参照を保持するために、メンバー変数 ' _textureLoad ' が使用されています。

## <a name="next-steps"></a>次のステップ

* [素材](materials.md)
* [スカイ](../overview/features/sky.md)
