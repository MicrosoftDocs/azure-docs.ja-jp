---
title: フレネル効果
description: フレネル素材効果の機能を説明するページ
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594114"
---
# <a name="fresnel-effect"></a>フレネル効果

フレネル効果素材の機能は、アドホック効果であり、物理的に正しいものではありません。 この機能は、オブジェクトの物理的な観察に基づくもので、これらの角度ではさらに反射性が増します。 フレネルの反射率自体は、Azure Remote Rendering で使用される [PBR 素材モデル](../../overview/features/pbr-materials.md)に既に物理的に組み込まれています。 これに対して、フレネル効果素材の機能は、単なる付加的な色彩効果であり、[光](../../overview/features/lights.md)または[空環境](../../overview/features/sky.md)に依存しません。

フレネル効果を使用すると、影響を受けるオブジェクトの端部の周りに色付きの光沢が加えられます。 効果のカスタマイズとレンダリング結果の例については、次のセクションを参照してください。

## <a name="enabling-the-fresnel-effect"></a>フレネル効果を有効にする

フレネル効果の機能を使用するには、対象となる素材でこの機能を有効にする必要があります。 有効にするには、[PBR 素材](../../overview/features/pbr-materials.md)で、[PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) の FresnelEffect ビットを設定します。 [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) と[色素材](../../overview/features/color-materials.md)にも、同じパターンが適用されます。 使用方法のデモについては、「コード サンプル」セクションを参照してください。

有効にすると、フレネル効果がすぐに表示されます。 既定では、光沢は白 (1, 1, 1, 1) で、指数は 1 となります。 次のパラメーター セッターを使用して、これらの設定をカスタマイズできます。

## <a name="customizing-the-effect-appearance"></a>効果の外観のカスタマイズ

現時点では、次のプロパティを使用すると、フレネル効果を素材ごとにカスタマイズできます。

| 素材プロパティ | Type | 説明 |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | フレネル光沢として最も加えられる色。 現在、アルファ チャネルは無視されています。 |
| FresnelEffectExponent | float | フレネル光沢の拡散。 0\.01 (オブジェクト全体に拡散) から 10 (最グレージング角のみ) の範囲です。 |

実際には、異なる色と指数を設定すると、次のように表示されます。

![フレネル効果の例](./media/fresnel-effect-examples.png)

フレネル効果の指数は、各色の行に対し、1 から 10 へと段階的に増加します。 これにより、表示されているオブジェクトの端に、フレネル光沢が徐々に加えられていきます。 フレネル効果は、次の例に示すように、透過性の影響を受けません。

![フレネル効果の透過性の例](./media/fresnel-effect-transparent-examples.png)

ここに示されているように、対角線上のオブジェクトは完全に透明ですが、フレネル光沢は残っています。 その点で、この効果は、これらのスクリーンショットにも示されているように、物理ベースのフレネルを模倣します。

## <a name="code-samples"></a>コード サンプル

次のコード サンプルは、[PBR 素材](../../overview/features/pbr-materials.md)と[色素材](../../overview/features/color-materials.md)の両方のフレネル効果の有効化とカスタマイズを示しています。

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>次のステップ

* [素材](../../concepts/materials.md)
* [PBR 素材](../../overview/features/pbr-materials.md)
* [色素材](../../overview/features/color-materials.md)