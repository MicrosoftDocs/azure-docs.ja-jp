---
title: 空の反射
description: 空の反射について環境マップを設定する方法について説明します。
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594182"
---
# <a name="sky-reflections"></a>空の反射

Azure Remote Rendering では、オブジェクトをリアルに照明するために空のテクスチャが使用されます。 拡張現実アプリケーションでは、オブジェクトを説得力のあるものにするために、このテクスチャは現実世界の環境に似ている必要があります。 この記事では、空のテクスチャを変更する方法について説明します。

> [!NOTE]
> 空のテクスチャは、"*環境マップ*" とも呼ばれます。 これらの用語は同じ意味で使用されます。

## <a name="object-lighting"></a>オブジェクトの照明

Azure Remote Rendering では、リアルな照明の計算を行うために "*物理ベース レンダリング*" (PBR) が採用されています。 ご利用のシーンに[光源](lights.md)を追加することはできますが、適切な空のテクスチャを使用すれば、最大の効果を得られます。

次の図に、空のテクスチャのみを使用してさまざまな表面に照明をあてた結果を示します。

| Roughness  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 金属以外  | ![誘電性、Roughness=0](media/dielectric-0.png)   | ![誘電性、Roughness=0.25](media/dielectric-0.25.png)  | ![誘電性、Roughness=0.5](media/dielectric-0.5.png)  | ![誘電性、Roughness=0.75](media/dielectric-0.75.png)  | ![誘電性、Roughness=1](media/dielectric-1.png)  |
| 金属      | ![金属、Roughness=0](media/metallic-0.png)  | ![金属、Roughness=0.25](media/metallic-0.25.png)    | ![金属、Roughness=0.5](media/metallic-0.5.png)    | ![金属、Roughness=0.75](media/metallic-0.75.png)    | ![金属、Roughness=1](media/metallic-1.png)    |

照明モデルの詳細については、「[素材](../../concepts/materials.md)」の章を参照してください。

> [!IMPORTANT]
> Azure Remote Rendering で空のテクスチャが使用されるのは、照明モデルの場合のみです。 拡張現実アプリケーションでは既に適切な背景 (現実世界) が用意されているので、空が背景としてレンダリングされることはありません。

## <a name="changing-the-sky-texture"></a>空のテクスチャの変更

環境マップを変更するには、[テクスチャを読み込んで、](../../concepts/textures.md)セッションの `SkyReflectionSettings` を変更するだけです。

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

組み込みのテクスチャが読み込まれるため、上記の `LoadTextureFromSasAsync` バリアントが使用されることにご注意ください。 [リンクされた BLOB ストレージ](../../how-tos/create-an-account.md#link-storage-accounts)から読み込む場合は、`LoadTextureAsync` バリアントを使用します。

## <a name="sky-texture-types"></a>空のテクスチャの種類

" *[キューブマップ](https://en.wikipedia.org/wiki/Cube_mapping)* " と "*2D テクスチャ*" を環境マップとして使用できます。

テクスチャはすべて、[サポートされているテクスチャ形式](../../concepts/textures.md#supported-texture-formats)とする必要があります。 空のテクスチャに対して mipmap を指定する必要はありません。

### <a name="cube-environment-maps"></a>キューブ環境マップ

参照用に、ラップされていないキューブマップを次に示します。

![ラップ解除したキューブマップ](media/Cubemap-example.png)

キューブマップ テクスチャを読み込むには、`TextureType.CubeMap` を `RenderingSession.Connection.LoadTextureAsync`/ `LoadTextureFromSasAsync` と一緒に使用します。

### <a name="sphere-environment-maps"></a>球環境マップ

2D テクスチャを環境マップとして使用する場合、[球座標空間](https://en.wikipedia.org/wiki/Spherical_coordinate_system)にイメージを置く必要があります。

![球座標での空のイメージ](media/spheremap-example.png)

球環境マップを読み込むには、`TextureType.Texture2D` を `RenderingSession.Connection.LoadTextureAsync` と一緒に使用します。

## <a name="built-in-environment-maps"></a>組み込みの環境マップ

Azure Remote Rendering には、常に使用可能な組み込みの環境マップがいくつか用意されています。 組み込み環境マップはすべてキューブマップです。

|識別子                         | 説明                                              | 図                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | さまざまなストライプ ライト、明るい室内ベース照明    | ![オブジェクトを照らすために使用される Autoshop Skybox](media/autoshop.png)
|builtin://BoilerRoom               | 明るい室内ライト設定、複数のウィンドウ ライト      | ![オブジェクトを照らすために使用される BoilerRoom Skybox](media/boiler-room.png)
|builtin://ColorfulStudio           | 中程度の室内ライト設定でのさまざまな色付きライト  | ![オブジェクトを照らすために使用される ColorfulStudio Skybox](media/colorful-studio.png)
|builtin://Hangar                   | 適度に明るいアンビエント ホール ライト                     | ![オブジェクトを照らすために使用される SmallHangar Skybox](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 明暗のコントラストを使用した薄暗い室内設定              | ![オブジェクトを照らすために使用される IndustrialPipeAndValve Skybox](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 昼間のアンビエント ルーム ライト、明るいウィンドウ エリア ライト     | ![オブジェクトを照らすために使用される Lebombo Skybox](media/lebombo.png)
|builtin://SataraNight              | 暗い夜の空と地面、周囲に多くのライトがある   | ![オブジェクトを照らすために使用される SataraNight Skybox](media/satara-night.png)
|builtin://SunnyVondelpark          | 明るい太陽の光と影のコントラスト                      | ![オブジェクトを照らすために使用される SunnyVondelpark Skybox](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 澄んだスカイ ライトと、適度な地面照明            | ![オブジェクトを照らすために使用される Syferfontein Skybox](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 適度に変化する太陽と日陰                         | ![オブジェクトを照らすために使用される TearsOfSteelBridge Skybox](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 夕暮れに近づく夕日のライト                    | ![オブジェクトを照らすために使用される VeniceSunset Skybox](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明るく、緑がかっている、白色の明るい色調、淡色の地面 | ![オブジェクトを照らすために使用される WhippleCreekRegionalPark Skybox](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 明るい周囲光がある昼間                 | ![オブジェクトを照らすために使用される WinterRiver Skybox](media/winter-river.png)
|builtin://DefaultSky               | TearsOfSteelBridge と同じ                               | ![オブジェクトを照らすために使用される DefaultSky Skybox](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingConnection.SkyReflectionSettings プロパティ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection::SkyReflectionSettings()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>次のステップ

* [照明](../../overview/features/lights.md)
* [素材](../../concepts/materials.md)
* [テクスチャ](../../concepts/textures.md)
