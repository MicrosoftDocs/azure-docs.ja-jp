---
title: モデル形式の素材マッピング
description: モデル ソース形式から PBR 素材への既定の変換について説明します
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96024046"
---
# <a name="material-mapping-for-model-formats"></a>モデル形式の素材マッピング

ソース資産が[モデルとして変換される](../how-tos/conversion/model-conversion.md)と、コンバーターによって[メッシュ](../concepts/meshes.md)ごとに[素材](../concepts/materials.md)が作成されます。 素材の作成方法は、[オーバーライド](../how-tos/conversion/override-materials.md)できます。 しかし、既定では、変換によって [PBR 素材](../overview/features/pbr-materials.md)が作成されます。 FBX のようなソース ファイル形式ではすべて、素材を定義するために独自の規則が使用されるため、それらの規則を Azure Remote Rendering の PBR 素材パラメーターにマップする必要があります。 

この記事では、ソース資産の素材を実行時の素材に変換するために使用される正確なマッピングを一覧表示します。

## <a name="gltf"></a>glTF

glTF 2.0 仕様のほぼすべてのものは、*EmissiveFactor* と *EmissiveTexture* を除き、Azure Remote Rendering でサポートされています。

そのマッピングを次の表に示します。

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   roughness                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   occlusion                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode.OPAQUE  |   alphaClipEnabled = false、isTransparent = false |
|   alphaMode.MASK    |   alphaClipEnabled = true、isTransparent = false  |
|   alphaMode.BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

glTF の各テクスチャには `texCoord` 値を指定することができます。これは、Azure Remote Rendering の素材でもサポートされます。

### <a name="embedded-textures"></a>埋め込みテクスチャ

*\*.bin* または *\*.glb* ファイルに埋め込まれているテクスチャがサポートされます。

### <a name="supported-gltf-extension"></a>サポートされている glTF の拡張機能

基本機能セットに加え、Azure Remote Rendering では次の glTF 拡張機能がサポートされます。

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md):[色素材](../overview/features/color-materials.md)に対応します。 "*発色*" 素材の場合は、この拡張機能を使用することをお勧めします。
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md):metallic-roughness テクスチャの代わりに、diffuse-specular-glossiness テクスチャを指定できます。 Azure Remote Rendering 実装では、拡張機能からの変換式に直接従います。

## <a name="fbx"></a>FBX

FBX 形式はクローズドソースであり、FBX 素材は一般に PBR 素材と互換性がありません。 FBX では、一意のパラメーターとプロパティを多く用いた表面の複雑な説明が使用されます。**Azure Remote Rendering パイプラインでは、これらのすべてが使用されるわけではありません**。

> [!IMPORTANT]
> Azure Remote Rendering モデル変換パイプラインでサポートされるのは、**FBX 2011 以降** のみです。

FBX 形式では、素材の保守的なアプローチが定義されます。公式の FBX 仕様には次の 2 種類しかありません。

* *Lambert* - 既にかなり長い間一般的に使用されていませんが、変換時に Phong に変換することでまだサポートされています。
* *Phong* - ほとんどすべての素材とほとんどのコンテンツ ツールで、この種類が使用されます。

この Phong モデルの方が正確であり、FBX 素材の "*唯一*" のモデルとして使用されます。 以下、"*FBX 素材*" と呼びます。

> Maya では、PBR および Stingray という種類の素材に対してカスタム プロパティを定義することで、FBX で 2 つのカスタム拡張機能が使用されます。 これらの詳細は FBX 仕様には含まれていないため、現在、Azure Remote Rendering ではサポートされていません。

FBX 素材では Diffuse-Specular-SpecularLevel 概念が使用されます。したがって、拡散テクスチャから albedo マップに変換するには、拡散から減算するために他のパラメーターを計算する必要があります。

> FBX のすべての色とテクスチャは、sRGB 空間 (ガンマ空間ともいう) にありますが、Azure Remote Rendering は視覚化時に線形空間で動作し、フレームの最後にすべてのものが sRGB 空間に戻されます。 Azure Remote Rendering 資産パイプラインでは、すべてを線形空間に変換し、準備されたデータとしてレンダラーに送信します。

次の表には、テクスチャが FBX 素材から Azure Remote Rendering 素材にどのようにマップされるかが示されています。 これらの一部は直接使用されませんが、式に関与する他のテクスチャ (拡散テクスチャなど) と組み合わせて使用されます。

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | オクルージョン マップ   |
| DiffuseColor | *Albedo、Metalness に使用* |
| TransparentColor | *Albedo のアルファ チャネルに使用* |
| TransparencyFactor | *Albedo のアルファ チャネルに使用* |
| Opacity | *Albedo のアルファ チャネルに使用* |
| SpecularColor | *Albedo、Metalness、Roughness に使用* |
| SpecularFactor| *Albedo、Metalness、Roughness に使用* |
| ShininessExponent | *Albedo、Metalness、Roughness に使用* |
| NormalMap | NormalMap |
| Bump | *NormalMap に変換* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

上記のマッピングは、多くの想定が必要であるため、素材変換の最も複雑な部分です。 以下では、これらの想定について説明します。

以下のいくつかの定義が使用されます。

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 + `Specular`.Green ∗ 0.7154 + `Specular`.Blue ∗ 0.0721
* `DiffuseBrightness` = 0.299 * `Diffuse`.Red<sup>2</sup> + 0.587 * `Diffuse`.Green<sup>2</sup> + 0.114 * `Diffuse`.Blue<sup>2</sup>
* `SpecularBrightness` = 0.299 * `Specular`.Red<sup>2</sup> + 0.587 * `Specular`.Green<sup>2</sup> + 0.114 * `Specular`.Blue<sup>2</sup>
* `SpecularStrength` = max(`Specular`.Red, `Specular`.Green, `Specular`.Blue)

SpecularIntensity 式は、[ここ](https://en.wikipedia.org/wiki/Luma_(video))から取得したものです。
明るさの式については、この[仕様](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)で説明されています。

### <a name="roughness"></a>Roughness

`Roughness` は、[この数式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)を使用して、`Specular` と `ShininessExponent` から計算されます。 この数式は、Phong 反射指数からの粗さの近似値です。

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness` は、この [glTF 仕様の式](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)を使用して、`Diffuse` と `Specular` から計算されます。

ここでの考え方は、次の式を解くことです: Ax<sup>2</sup> + Bx + C = 0。
基本的に、誘電体表面では、反射方向に約 4% の光を反射し、残りは拡散となります。 金属表面では拡散方向に光を反射しませんが、すべて反射方向に反射します。
この数式にはいくつかの欠点があります。光沢のあるプラスチックと光沢のある金属の表面を区別する方法がないためです。 ほとんどの場合、表面には金属プロパティがあり、その結果、光沢のあるプラスチックやゴムの表面は予期したとおりに見えない場合があると想定しています。

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` は、`Diffuse`、`Specular`、および `Metalness` から計算されます。

「Metalness」セクションで説明したように、誘電体表面では約 4% の光を反射します。  
ここでの考え方は、`Metalness` 値を因子として使用し、`Dielectric` と `Metal` の色の間を線形補間することです。 metalness が `0.0` の場合、反射に応じて、濃い色になるか (反射が高い場合)、拡散が変化しません (反射がない場合)。 metalness が大きな値の場合、反射色が優先されるため、拡散色は消えます。

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` は、上記の数式で計算されていますが、アルファ チャネルには追加の計算が必要です。 FBX 形式は透明度についてはあいまいであり、多くの定義方法があります。 コンテンツ ツールによって、使用する方法が異なります。 ここでの考え方は、1 つの数式に統合することです。 一般的な方法で作成されていない場合でも、一部の資産が誤って透明として表示されるようになります。

これは `TransparentColor`、`TransparencyFactor`、`Opacity` から計算されます。

`Opacity` が定義されている場合、`AlbedoAlpha` = `Opacity` を直接使用します。または、  
`TransparencyColor` が定義されている場合は、`AlbedoAlpha` = 1.0 - ((`TransparentColor`.Red + `TransparentColor`.Green + `TransparentColor`.Blue) / 3.0)。あるいは、  
`TransparencyFactor` の場合は、`AlbedoAlpha` = 1.0 - `TransparencyFactor`

最終的な `Albedo` の色のチャネルは 4 つとなり、`AlbedoRGB` と `AlbedoAlpha` が組み合わされます。

### <a name="summary"></a>まとめ

まとめると、`Specular` がゼロに近い場合、`Albedo` は元の `Diffuse` に非常に近くなります。 それ以外の場合、表面は金属表面のように見え、拡散色は失われます。 `ShininessExponent` が十分に大きく、`Specular` が明るい場合、表面はより滑らかに見え、反射性が増します。 それ以外の場合、表面は粗く見え、環境をほとんど反射しません。

### <a name="known-issues"></a>既知の問題

* 現在の数式は、シンプルな色付きジオメトリに対しては適切に機能しません。 `Specular` が十分明るい場合、すべてのジオメトリが色なしの反射する金属表面になります。 これを回避するには、`Specular` を元のものから 30% に下げるか、変換設定の [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model) を使用します。
* `Maya` および `3DS Max` のコンテンツ作成ツールには、最近、PBR 素材が追加されました。 ユーザー定義のカスタム ブラックボックス プロパティを使用して、FBX に渡されます。 Azure Remote Rendering ではこれらの追加のプロパティが読み取られません。これらはドキュメント化されておらず、形式がクローズドソースであるためです。

## <a name="next-steps"></a>次のステップ

* [モデルの変換](../how-tos/conversion/model-conversion.md)
* [モデル変換中の素材のオーバーライド](../how-tos/conversion/override-materials.md)
