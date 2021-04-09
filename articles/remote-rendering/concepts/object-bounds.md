---
title: オブジェクトの境界
description: 空間オブジェクトの境界を照会する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594486"
---
# <a name="object-bounds"></a>オブジェクトの境界

オブジェクトの境界は、[エンティティ](entities.md)とその子が占めるボリュームを表します。 Azure Remote Rendering では、オブジェクトの境界は常に *軸平行境界ボックス* (AABB) として与えられます。 オブジェクトの境界は、*ローカル空間* または *ワールド空間* 内に指定できます。 どちらの場合でも、これらは常に軸と平行です。つまり、エクステントとボリュームは、ローカルとワールドの空間表現で異なる場合があります。

## <a name="querying-object-bounds"></a>オブジェクトの境界の照会

[メッシュ](meshes.md)のローカル軸配置境界ボックスは、メッシュ リソースから直接クエリで問い合わせられます。 これらの境界は、エンティティの変換を使用して、エンティティのローカル空間またはワールド空間に変換できます。

このようにオブジェクト階層全体の境界を計算することもできますが、その場合は階層を走査し、各メッシュの境界を照会し、それらを手動で結合する必要があります。 この操作は、煩雑で非効率的です。

より良い方法は、エンティティで `QueryLocalBoundsAsync` または `QueryWorldBoundsAsync` を呼び出すことです。 その後、計算はサーバーにオフロードされ、最小限の遅延で返されます。

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# Entity.QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++ Entity::QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>次のステップ

* [空間クエリ](../overview/features/spatial-queries.md)