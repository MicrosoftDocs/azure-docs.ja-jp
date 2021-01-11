---
title: 素材
description: Rendering 素材の説明と素材のプロパティ
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c9de26d9a83319eee62cf6d30cf2c83f66446fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020254"
---
# <a name="materials"></a>素材

素材は、[メッシュ](meshes.md)のレンダリング方法を定義する[共有リソース](../concepts/lifetime.md)です。 素材を使用して、適用する[テクスチャ](textures.md)、オブジェクトを透明にするかどうか、およびライティングを計算する方法を指定します。

素材は[モデル変換](../how-tos/conversion/model-conversion.md)中に自動的に作成され、実行時にアクセスできます。 コードからカスタム素材を作成し、既存の素材を置き換えることもできます。 このシナリオは、多くのメッシュで同じ素材を共有する場合に特に意味があります。 素材の変更は、それを参照するすべてのメッシュに表示されるため、この方法を使用して簡単に変更を適用できます。

> [!NOTE]
> 選択したオブジェクトの強調表示などの一部のユース ケースは、素材を変更することによって実行できますが、[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) を使用した方がはるかに簡単です。

## <a name="material-types"></a>素材の種類

Azure Remote Rendering には、次の 2 つの異なる素材の種類があります。

* [PBR 素材](../overview/features/pbr-materials.md) は、可能な限り物理的に正確にレンダリングする必要があるサーフェスに使用されます。 このような素材については、*物理ベース レンダリング* (PBR) を使用して、現実的なライティングが計算されます。 この素材の種類を最大限に活用するには、粗さや法線マップなどの高品質な入力データを提供することが重要です。

* [色素材](../overview/features/color-materials.md)は、追加のライティングが必要ない場合に使用されます。 これらの素材は常に完全に明るく、設定が簡単です。 色素材は、ライティングをまったく持たないデータ、または [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry) で取得したモデルなどの静的なライティングが既に組み込まれているデータに使用されます。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>メッシュとMeshComponent 素材の割り当て

[メッシュ](meshes.md)には、1 つ以上のサブメッシュがあります。 各サブメッシュは 1 つの素材を参照します。 メッシュで直接使用する素材を変更することも、[MeshComponent](meshes.md#meshcomponent) でサブメッシュに使用する素材をオーバーライドすることもできます。

メッシュ リソースで素材を直接変更すると、その変更はそのメッシュのすべてのインスタンスに影響を及ぼします。 ただし、MeshComponent での変更は、1 つのメッシュ インスタンスにのみ影響します。 どちらの方法が適しているかは、目的の動作によって異なりますが、MeshComponent の変更がより一般的な方法です。

## <a name="material-de-duplication"></a>素材の重複除去

同じプロパティやテクスチャを持つ複数のマテリアルは変換中に自動的に重複除去され、1 つの素材になります。 この機能は [[変換設定]](../how-tos/conversion/configure-model-conversion.md) で無効にすることができますが、最適なパフォーマンスを得るには、そのままにしておくことをお勧めします。

## <a name="material-classes"></a>Material クラス

API によって提供されるすべての素材は、基本クラス `Material` から派生します。 それらの型を照会するには `Material.MaterialSubType` を使用するか、直接キャストします。

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>次のステップ

* [PBR 素材](../overview/features/pbr-materials.md)
* [色素材](../overview/features/color-materials.md)
