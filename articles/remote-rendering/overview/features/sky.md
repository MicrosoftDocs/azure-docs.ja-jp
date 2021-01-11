---
title: 空の反射
description: 空の反射について環境マップを設定する方法について説明します。
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f2a871e409761116182f67eb877f3727038fe0dc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013641"
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
| 金属以外  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| 金属      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

照明モデルの詳細については、「[素材](../../concepts/materials.md)」の章を参照してください。

> [!IMPORTANT]
> Azure Remote Rendering で空のテクスチャが使用されるのは、照明モデルの場合のみです。 拡張現実アプリケーションでは既に適切な背景 (現実世界) が用意されているので、空が背景としてレンダリングされることはありません。

## <a name="changing-the-sky-texture"></a>空のテクスチャの変更

環境マップを変更するには、[テクスチャを読み込んで、](../../concepts/textures.md)セッションの `SkyReflectionSettings` を変更するだけです。

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!");
            }
        });
}

```

組み込みのテクスチャが読み込まれるため、上記の `LoadTextureFromSASAsync` バリアントが使用されることにご注意ください。 [リンクされた BLOB ストレージ](../../how-tos/create-an-account.md#link-storage-accounts)から読み込む場合は、`LoadTextureAsync` バリアントを使用します。

## <a name="sky-texture-types"></a>空のテクスチャの種類

" *[キューブマップ](https://en.wikipedia.org/wiki/Cube_mapping)* " と "*2D テクスチャ*" を環境マップとして使用できます。

テクスチャはすべて、[サポートされているテクスチャ形式](../../concepts/textures.md#supported-texture-formats)とする必要があります。 空のテクスチャに対して mipmap を指定する必要はありません。

### <a name="cube-environment-maps"></a>キューブ環境マップ

参照用に、ラップされていないキューブマップを次に示します。

![ラップ解除したキューブマップ](media/Cubemap-example.png)

キューブマップ テクスチャを読み込むには、`TextureType.CubeMap` を `AzureSession.Actions.LoadTextureAsync`/ `LoadTextureFromSASAsync` と一緒に使用します。

### <a name="sphere-environment-maps"></a>球環境マップ

2D テクスチャを環境マップとして使用する場合、[球座標空間](https://en.wikipedia.org/wiki/Spherical_coordinate_system)にイメージを置く必要があります。

![球座標での空のイメージ](media/spheremap-example.png)

球環境マップを読み込むには、`TextureType.Texture2D` を `AzureSession.Actions.LoadTextureAsync` と一緒に使用します。

## <a name="built-in-environment-maps"></a>組み込みの環境マップ

Azure Remote Rendering には、常に使用可能な組み込みの環境マップがいくつか用意されています。 組み込み環境マップはすべてキューブマップです。

|識別子                         | 説明                                              | 図                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | さまざまなストライプ ライト、明るい室内ベース照明    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | 明るい室内ライト設定、複数のウィンドウ ライト      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | 中程度の室内ライト設定でのさまざまな色付きライト  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | 適度に明るいアンビエント ホール ライト                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 明暗のコントラストを使用した薄暗い室内設定              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 昼間のアンビエント ルーム ライト、明るいウィンドウ エリア ライト     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | 暗い夜の空と地面、周囲に多くのライトがある   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | 明るい太陽の光と影のコントラスト                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 澄んだスカイ ライトと、適度な地面照明            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 適度に変化する太陽と日陰                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 夕暮れに近づく夕日のライト                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明るく、緑がかっている、白色の明るい色調、淡色の地面 | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 明るい周囲光がある昼間                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | TearsOfSteelBridge と同じ                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>次のステップ

* [照明](../../overview/features/lights.md)
* [素材](../../concepts/materials.md)
* [テクスチャ](../../concepts/textures.md)
* [TexConv コマンドライン ツール](../../resources/tools/tex-conv.md)
