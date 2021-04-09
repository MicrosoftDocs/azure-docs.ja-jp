---
title: メッシュ
description: Azure Remote Rendering のスコープ内のメッシュの定義
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594520"
---
# <a name="meshes"></a>メッシュ

## <a name="mesh-resource"></a>メッシュ リソース

メッシュは、変更できない[共有リソース](../concepts/lifetime.md)であり、[モデル変換](../how-tos/conversion/model-conversion.md)でのみ作成できます。 メッシュには、[レイキャスト クエリ](../overview/features/spatial-queries.md)の物理学表現と共に、1 つまたは複数の "*サブメッシュ*" が含まれています。 各サブメッシュは、既定でレンダリングに使用する必要がある[素材](materials.md)を参照します。 3D 空間にメッシュを配置するには、[エンティティ](entities.md)に [MeshComponent](#meshcomponent) を追加します。

### <a name="mesh-resource-properties"></a>メッシュ リソースのプロパティ

`Mesh` クラスのプロパティは次のとおりです。

* **素材:** 素材の配列。 各素材は、異なるサブメッシュによって使用されます。 配列内の複数のエントリが、同じ[素材](materials.md)を参照している場合があります。 このデータは実行時に変更できません。

* **境界:** メッシュの頂点のローカル空間の軸平行境界ボックス (AABB)。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent` クラスは、メッシュ リソースのインスタンスを配置するために使用されます。 各 MeshComponent は単一のメッシュを参照します。 これは、各サブメッシュのレンダリングに使用される素材をオーバーライドできます。

### <a name="meshcomponent-properties"></a>MeshComponent のプロパティ

* **メッシュ:** このコンポーネントによって使用されるメッシュ リソース。

* **素材:** メッシュ コンポーネント自体に指定されている素材の配列。 配列は、メッシュ リソースの *素材* 配列と常に同じ長さになります。 メッシュの既定値からオーバーライドされない素材は、この配列で *null 値* に設定されます。

* **UsedMaterials:** 各サブメッシュで実際に使用される素材の配列。 null 以外の値の場合、*素材* 配列のデータと同じになります。 それ以外の場合は、メッシュ インスタンス内の *素材* 配列の値を格納します。

### <a name="sharing-of-meshes"></a>メッシュの共有

`Mesh` リソースはメッシュ コンポーネントの複数のインスタンス間で共有できます。 また、メッシュ コンポーネントに割り当てられている `Mesh` リソースはいつでもプログラムを利用して変更できます。 下のコードはメッシュの複製方法を示しています。

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# Mesh クラス](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent クラス](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++ Mesh クラス](/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent クラス](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>次のステップ

* [素材](materials.md)