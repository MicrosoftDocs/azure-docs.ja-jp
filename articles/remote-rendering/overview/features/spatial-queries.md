---
title: 空間クエリ
description: シーンで空間クエリを実行する方法
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594046"
---
# <a name="spatial-queries"></a>空間クエリ

空間クエリは、リモート レンダリング サービスに対して、どのオブジェクトが領域内に配置されているかを確認するための操作です。 空間クエリは、ユーザーが指しているオブジェクトを特定するなど、対話処理を実装するために頻繁に使用されます。

すべての空間クエリは、サーバーで評価されます。 そのため、これらは非同期操作であり、結果が届くまでネットワーク待ち時間に応じた遅延が発生します。 すべての空間クエリでネットワーク トラフィックが生成されるため、一度に多くの操作を行わないように注意してください。

## <a name="collision-meshes"></a>衝突メッシュ

空間クエリは [Havok Physics](https://www.havok.com/products/havok-physics) エンジンにより実行され、専用の衝突メッシュが存在する必要があります。 既定では、[モデルの変換](../../how-tos/conversion/model-conversion.md)によって衝突メッシュが生成されます。 複雑なモデルにおいて空間クエリを必要としない場合、複数の面で影響があるため、[変換オプション](../../how-tos/conversion/configure-model-conversion.md)で衝突メッシュの生成を無効にすることを検討してください。

* [モデルの変換](../../how-tos/conversion/model-conversion.md)にはかなり長い時間がかかります。
* 変換されたモデルのファイル サイズは非常に大きくなり、ダウンロード速度に影響します。
* 実行時の読み込み時間が長くなります。
* 実行時の CPU メモリ使用量が増加します。
* すべてのモデル インスタンスについて、実行時のパフォーマンスのオーバーヘッドはわずかです。

## <a name="ray-casts"></a>レイ キャスト

"*レイ キャスト*" は空間クエリです。指定の位置から開始し特定の方向を指すレイが交差するオブジェクトが、ランタイムによってチェックされます。 あまりに遠くにあるオブジェクトを検索しないようにするために、レイの最大距離も最適化のために与えられます。

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


3 種類のヒット コレクション モードがあります。

* **`Closest`:** このモードでは、最も近いヒットだけが報告されます。
* **`Any`:** レイが何かにヒット "*するかどうか*" を知りたいだけで、何がヒットしたのかは知る必要がない場合、このモードをお勧めします。 このクエリはかなりの低コストで評価できますが、アプリケーションはごくわずかです。
* **`All`:** このモードでは、レイに沿ったすべてのヒットが、距離で並べ替えられて報告されます。 最初のヒット以外にも必要な場合を除き、このモードは使用しないでください。 `MaxHits` オプションを使用して、報告されるヒット数を制限します。

レイ キャストの検討対象から除外するオブジェクトを選択するには、[HierarchicalStateOverrideComponent](override-hierarchical-state.md) コンポーネントを使用できます。

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>ヒット結果

レイ キャスト クエリの結果は、ヒットの配列です。 ヒットしたオブジェクトがなかった場合、配列は空になります。

ヒットには次のプロパティがあります。

* **`HitEntity`:** どの [エンティティ](../../concepts/entities.md)がヒットしたか。
* **`SubPartId`:** どの *submesh* が [MeshComponent](../../concepts/meshes.md) でヒットしたか。 `MeshComponent.UsedMaterials` にインデックスを作成し、その時点で[素材](../../concepts/materials.md)を検索するために使用できます。
* **`HitPosition`:** レイがオブジェクトと交差するワールド空間の位置。
* **`HitNormal`:** 交差の位置にあるメッシュのワールド空間表面法線。
* **`DistanceToHit`:** レイの開始位置からヒットまでの距離。

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingConnection.RayCastQueryAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection::RayCastQueryAsync()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>次のステップ

* [オブジェクトの境界](../../concepts/object-bounds.md)
* [階層状態をオーバーライドする](override-hierarchical-state.md)