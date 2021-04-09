---
title: 切断面
description: 切断面の概要とその使用方法について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594374"
---
# <a name="cut-planes"></a>切断面

*切断面* は、仮想的な平面の片方のピクセルをクリップし、[メッシュの内部](../../concepts/meshes.md)を表示する視覚機能です。
下の図は、効果を示しています。 左側には元のメッシュ、右側にはメッシュの内側が表示されています。

![切断面](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

*CutPlaneComponent* を作成して、シーンに切断面を追加します。 平面の位置と向きは、コンポーネントの所有者[エンティティ](../../concepts/entities.md)によって決まります。

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent のプロパティ

次のプロパティは、切断面コンポーネントで公開されます。

* `Enabled`:コンポーネントを無効にすることで、切断面を一時的にオフにすることができます。 無効化された切断面は、レンダリング オーバーヘッドを発生させず、グローバルな切断面の制限にもカウントされません。

* `Normal`:面法線として使用される方向 (+X、-X、+Y、-Y、+Z、-Z) を指定します。 この方向は、所有者のエンティティの向きに対して相対的です。 正確に配置するため、所有者エンティティを移動および回転します。

* `FadeColor` および `FadeLength`:

  *FadeColor* のアルファ値が 0 以外の場合、切断面に近いピクセルは、FadeColor の RGB 部分の色へフェードします。 アルファ チャネルの強度によって、フェードの色に完全にフェードするか、部分的にフェードするかが決まります。 このフェードが行われる距離は、*FadeLength* によって定義されます。

* `ObjectFilterMask`:切断面の影響を受けるジオメトリを決定するフィルター ビットマスク。 詳細については、次の段落を参照してください。

### <a name="selective-cut-planes"></a>選択的切断面

特定のジオメトリにのみ影響するように、個々の切断面を構成することができます。 次の図は、この設定が実際にどのように見えるかを示しています。

![選択的切断面](./media/selective-cut-planes.png)

フィルター処理は、切断面側のビット マスクとジオメトリに設定されている 2 番目のビット マスク間の **論理ビット マスク比較** を通じて行われます。 マスク間の論理 `AND` 演算の結果がゼロでない場合、切断面はジオメトリに影響します。

* 切断面コンポーネントのビット マスクは、その `ObjectFilterMask` プロパティを介して設定されます
* ジオメトリのサブ階層のビット マスクは、[HierarchicalStateOverrideComponent](override-hierarchical-state.md#features) を介して設定されます。

例 :

| 切断面フィルター マスク | ジオメトリ フィルター マスク  | 論理 `AND` の結果 | 切断面はジオメトリに影響するか  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) == 1   | (0000 0001) == 1  | (0000 0001) == 1  | Yes |
| (1111 0000) == 240 | (0001 0001) == 17 | (0001 0000) == 16 | Yes |
| (0000 0001) == 1   | (0000 0010) == 2  | (0000 0000) == 0  | No |
| (0000 0011) == 3   | (0000 1000) == 8  | (0000 0000) == 0  | No |

>[!TIP]
> 切断面の `ObjectFilterMask` を 0 に設定すると、論理 `AND` の結果が null 以外になることはないため、どのジオメトリにも影響しません。 レンダリング システムでは、そもそもこのようなプレーンは考慮されないため、個々の切断面を無効にするには、これが簡単な方法です。 このような切断面は、8 個というアクティブなプレーンの制限にもカウントされません。

## <a name="limitations"></a>制限事項

* Azure Remote Rendering では、**最大 8 個のアクティブな切断面** がサポートされています。 さらに多くの切断面のコンポーネントを作成することもできますが、より多くを同時に有効にしようとすると、アクティブ化が無視されます。 シーンに影響を与えるコンポーネントを切り替えたい場合は、最初に他のプレーンを無効にします。
* 切断面は純粋に単なる視覚機能であり、[空間クエリ](spatial-queries.md)の結果には影響しません。 切り開かれたメッシュにレイ キャストする場合は、射線の開始点を、切断面上に配置するように調整します。 このようにすることで、射線を表示されている部分にのみ当てることができます。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

アクティブな各切断面により、レンダリング中にわずかなコストが発生します。 不要になった切断面は、無効化または削除します。

## <a name="api-documentation"></a>API のドキュメント

* [C# CutPlaneComponent クラス](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent クラス](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>次のステップ

* [片面レンダリング](single-sided-rendering.md)
* [空間クエリ](spatial-queries.md)