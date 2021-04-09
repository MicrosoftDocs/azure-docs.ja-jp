---
title: 階層状態のオーバーライド
description: 階層状態のオーバーライド コンポーネントの概念について説明します。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94445156"
---
# <a name="hierarchical-state-override"></a>階層状態のオーバーライド

多くの場合、[モデル](../../concepts/models.md)のパーツの外観を動的に変更する必要があります (サブグラフを非表示にする、パーツを透明なレンダリングに切り替える、など)。 シーン グラフ全体を反復処理し、ノードごとに素材の複製と割り当てを管理する必要があるため、関連する各パーツの素材を変更することは現実的ではありません。

考えられる最小限のオーバーヘッドでこのユース ケースを実現するには、`HierarchicalStateOverrideComponent` を使用します。 このコンポーネントは、シーン グラフの任意の分岐に階層状態の更新を実装します。 つまり、シーン グラフ内のどのレベルにも状態を定義でき、その状態は、新しい状態でオーバーライドされるか、リーフ オブジェクトに適用されるまで、下位の階層へと徐々に伝わっていきます。

例として、自動車のモデルについて考えてみましょう。内部のエンジン部品を除き、車全体が透明になるように切り替える必要があるとします。 このユース ケースには、コンポーネントのインスタンスが 2 つだけ含まれます。

* 最初のコンポーネントは、モデルのルート ノードに割り当てられ、車全体に対して透明なレンダリングを有効にします。
* 2 つ目のコンポーネントは、エンジンのルート ノードに割り当てられ、透過モードを明示的にオフにすることによって、状態を再度オーバーライドします。

## <a name="features"></a>特徴

オーバーライドできる一定の状態は次のとおりです。

* **`Hidden`** :シーン グラフ内の各メッシュが非表示になるか、表示されます。
* **`Tint color`** :レンダリングされたオブジェクトに、個別の濃淡の色と濃淡の重みを使用して色合いを付けることができます。 次の画像は、ホイールの縁への色付けを示しています。
  
  ![オブジェクトを緑色にするために使用される濃淡の色](./media/color-tint.png)

* **`See-through`** :オブジェクトの内部部品を見せるなどのために、ジオメトリが半透明にレンダリングされます。 次の画像は、赤のブレーキ キャリパーを除き、透過モードでレンダリングされている車全体を示しています。

  ![選択したオブジェクトを透明にするために使用される透過モード](./media/see-through.png)

  > [!IMPORTANT]
  > *TileBasedComposition* [レンダリング モード](../../concepts/rendering-modes.md)が使用されている場合にのみ、透過効果が働きます。

* **`Shell`** :ジオメトリは、透明で彩度の低いシェルとしてレンダリングされます。 このモードを使用すると、シェイプの意味と相対的な位置を維持しながら、シーンの重要でない部分をフェードアウトすることができます。 シェル レンダリングの外観を変更するには、[ShellRenderingSettings](shell-effect.md) の状態を使用します。 青いスプリングを除き、完全にシェルレンダリングされている車のモデルについては、次の画像を参照してください。

  ![特定のオブジェクトをフェードアウトするために使用されるシェル モード](./media/shell.png)

  > [!IMPORTANT]
  > *TileBasedComposition* [レンダリング モード](../../concepts/rendering-modes.md)が使用されている場合にのみ、シェルの効果が働きます。

* **`Selected`** :ジオメトリが、[選択アウトライン](outlines.md)でレンダリングされます。

  ![選択したパーツを強調表示するために使用されるアウトライン オプション](./media/selection-outline.png)

* **`DisableCollision`** :ジオメトリには、[空間クエリ](spatial-queries.md)が適用されません。 **`Hidden`** フラグによる競合状態フラグへの影響はないため、これらの 2 つのフラグを同時に設定することがよくあります。

* **`UseCutPlaneFilterMask`** :個々のフィルター ビット マスクを使用して、切断面の選択を制御します。 このフラグによって、個々のフィルター マスクを使用する必要があるか、その親から継承する必要があるかが決まります。 フィルター ビット マスク自体は、`CutPlaneFilterMask` プロパティを使用して設定されます。 フィルター処理のしくみの詳細については、[選択的切断面の段落](cut-planes.md#selective-cut-planes)を参照してください。 次の例を参照してください。タイヤとリムのみが切り取られ、シーンの残りの部分は影響を受けません。
![選択的切断面](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> 完全なサブ グラフの可視性と空間クエリを無効にする代替手段として、game オブジェクトの `enabled` 状態を切り替えることができます。 階層が無効になっている場合、これはすべての `HierarchicalStateOverrideComponent` よりも優先されます。

## <a name="hierarchical-overrides"></a>階層のオーバーライド

`HierarchicalStateOverrideComponent` は、オブジェクト階層の複数のレベルにアタッチできます。 1 つのエンティティには各種類のコンポーネントを 1 つだけ置くことができるため、それぞれの `HierarchicalStateOverrideComponent` で、非表示、透過、選択、色付け、および競合の状態を管理します。

したがって、各状態は次のいずれかに設定できます。

* `ForceOn` - このノード上とその下のすべてのメッシュで状態が有効になっています
* `ForceOff` - このノード上とその下のすべてのメッシュで状態が無効になっています
* `InheritFromParent` - 状態は、このオーバーライド コンポーネントの影響を受けません

状態は、直接または `SetState` 関数を使用して変更できます。

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>濃淡の色

`tint color` オーバーライドは、オン/オフ/継承状態と濃淡の色プロパティの両方があるため、若干特殊です。 濃淡の色のアルファ部分では、色付け効果の重みを定義します。0.0 に設定した場合、濃淡の色は表示されず、1.0 に設定した場合、オブジェクトは純粋な濃淡の色でレンダリングされます。 中間の値の場合、最終的な色は濃淡の色と混合されます。 濃淡の色は、カラー アニメーションを実現するために、フレームごとに変更できます。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

`HierarchicalStateOverrideComponent` 自体のインスタンスで、ランタイム オーバーヘッドが大幅に増えることはありません。 ただし、アクティブなコンポーネントの数は常に少なくしておくことをお勧めします。 たとえば、選択したオブジェクトが強調表示される選択システムを実装している場合、強調表示が取り除かれたときにコンポーネントを削除することをお勧めします。 コンポーネントをニュートラルな機能で持ち続けると、たちまち膨れ上がる可能性があります。

透明なレンダリングでは、標準のレンダリングよりも大きいワークロードがサーバーの GPU にかかります。 ジオメトリの多数のレイヤーが表示されている状態でシーン グラフの大きなパーツを *透過* に切り替えると、パフォーマンスのボトルネックになる可能性があります。 同じことが、[選択アウトライン](../../overview/features/outlines.md#performance)のオブジェクトと[シェル レンダリング](../../overview/features/shell-effect.md#performance)にも当てはまります。 

## <a name="api-documentation"></a>API のドキュメント

* [C# HierarchicalStateOverrideComponent クラス](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C++ HierarchicalStateOverrideComponent クラス](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>次のステップ

* [アウトライン](../../overview/features/outlines.md)
* [レンダリング モード](../../concepts/rendering-modes.md)
* [空間クエリ](../../overview/features/spatial-queries.md)