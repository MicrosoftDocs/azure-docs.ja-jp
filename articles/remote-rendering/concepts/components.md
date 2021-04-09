---
title: Components
description: Azure Remote Rendering のスコープ内のコンポーネントの定義
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592196"
---
# <a name="components"></a>Components

Azure Remote Rendering では、[エンティティ コンポーネント システム](https://en.wikipedia.org/wiki/Entity_component_system)のパターンが使用されます。 [エンティティ](entities.md)は、オブジェクトの位置と階層構造を表し、コンポーネントで動作が実装されます。

最も頻繁に使用されるコンポーネントの種類は [:::no-loc text="mesh components":::](meshes.md) であり、これにより、メッシュがレンダリング パイプラインに追加されます。 同様に、[ライト コンポーネント](../overview/features/lights.md)を使用してライティングを追加し、[カット プレーン コンポーネント](../overview/features/cut-planes.md)を使用してオープン メッシュを切り取ります。

これらのコンポーネントはすべて、関連付けられているエンティティの変換 (位置、回転、スケール) を参照ポイントとして使用します。

## <a name="working-with-components"></a>コンポーネントの操作

プログラムを使用して、コンポーネントの追加、削除、および操作を簡単に行うことができます。

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

コンポーネントは、作成時にエンティティに関連付けられます。 後で別のエンティティに移動することはできません。 コンポーネントは `Component.Destroy()` を使用して明示的に削除されるか、またはコンポーネントの所有者エンティティが破棄されたときに自動的に削除されます。

各コンポーネントの種類のインスタンスは、一度に 1 つのみエンティティに追加できます。

## <a name="unity-specific"></a>Unity 固有

Unity 統合には、コンポーネントと対話するための拡張機能が追加されています。 「[Unity のゲーム オブジェクトとコンポーネント](../how-tos/unity/objects-components.md)」を参照してください。

## <a name="api-documentation"></a>API のドキュメント

* [C# ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection.CreateComponent()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C# Entity.FindComponentOfType()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection::CreateComponent()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++ Entity::FindComponentOfType()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>次のステップ

* [オブジェクトの境界](object-bounds.md)
* [メッシュ](meshes.md)