---
title: オブジェクトの境界
description: 空間オブジェクトの境界を照会する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021852"
---
# <a name="object-bounds"></a>オブジェクトの境界

オブジェクトの境界は、[エンティティ](entities.md)とその子が占めるボリュームを表します。 Azure Remote Rendering では、オブジェクトの境界は常に*軸平行境界ボックス* (AABB) として与えられます。 オブジェクトの境界は、*ローカル空間*または*ワールド空間*内に指定できます。 どちらの場合でも、これらは常に軸と平行です。つまり、エクステントとボリュームは、ローカルとワールドの空間表現で異なる場合があります。

## <a name="querying-object-bounds"></a>オブジェクトの境界の照会

[メッシュ](meshes.md)のローカル AABB は、メッシュ リソースから直接照会できます。 これらの境界は、エンティティの変換を使用して、エンティティのローカル空間またはワールド空間に変換できます。

このようにオブジェクト階層全体の境界を計算することもできますが、その場合は階層を走査し、各メッシュの境界を照会し、それらを手動で結合する必要があります。 この操作は、煩雑で非効率的です。

より良い方法は、エンティティで `QueryLocalBoundsAsync` または `QueryWorldBoundsAsync` を呼び出すことです。 その後、計算はサーバーにオフロードされ、最小限の遅延で返されます。

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>次のステップ

* [空間クエリ](../overview/features/spatial-queries.md)
