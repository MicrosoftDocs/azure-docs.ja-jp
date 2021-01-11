---
title: 切断面
description: 切断面の概要とその使用方法について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b92bfad99c854f75c945121d352a7122d8c6db89
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011601"
---
# <a name="cut-planes"></a>切断面

*切断面*は、仮想的な平面の片方のピクセルをクリップし、[メッシュの内部](../../concepts/meshes.md)を表示する視覚機能です。
下の図は、効果を示しています。 左側には元のメッシュ、右側にはメッシュの内側が表示されています。

![切断面](./media/cutplane-1.png)

## <a name="limitations"></a>制限事項

* 現時点では、Azure Remote Rendering では、**最大 8 つのアクティブな切断面**がサポートされています。 さらに多くの切断面のコンポーネントを作成することもできますが、より多くを同時に有効にしようとすると、アクティブ化が無視されます。 シーンに影響を与えるコンポーネントを切り替えたい場合は、最初に他のプレーンを無効にします。
* 各切断面は、リモートでレンダリングされるすべてのオブジェクトに影響します。 現在、特定のオブジェクトまたはメッシュ パーツを除外する方法はありません。
* 切断面は純粋に単なる視覚機能であり、[空間クエリ](spatial-queries.md)の結果には影響しません。 切り開かれたメッシュにレイ キャストする場合は、射線の開始点を、切断面上に配置するように調整します。 このようにすることで、射線を表示されている部分にのみ当てることができます。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

アクティブな各切断面により、レンダリング中にわずかなコストが発生します。 不要になった切断面は、無効化または削除します。

## <a name="cutplanecomponent"></a>CutPlaneComponent

*CutPlaneComponent* を作成して、シーンに切断面を追加します。 平面の位置と向きは、コンポーネントの所有者[エンティティ](../../concepts/entities.md)によって決まります。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
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

## <a name="next-steps"></a>次のステップ

* [片面レンダリング](single-sided-rendering.md)
* [空間クエリ](spatial-queries.md)
