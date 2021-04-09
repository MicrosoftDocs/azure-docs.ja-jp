---
title: シェル レンダリング
description: シェル レンダリング効果の使用方法について説明します。
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594165"
---
# <a name="shell-rendering"></a>シェル レンダリング

[階層状態のオーバーライド コンポーネント](../../overview/features/override-hierarchical-state.md)のシェルの状態には、透明にする効果があります。 [シースルー](../../overview/features/override-hierarchical-state.md) レンダリングと比較して、オブジェクトの最前面のレイヤーのみを表示し、不透明なレンダリングと似てます。 また、シェルとしてレンダリングした場合、オブジェクトの通常の外観を変更できます。 この効果は、シーン全体は空間的に認識し続ける必要があるが、重要ではない部分からは視覚的にユーザーの目をそらさせたい場合に使用することを意図しています。

シェル レンダリングするオブジェクトの外観は、`ShellRenderingSettings` グローバル状態を使用して構成できます。 シェル レンダリングを使用するすべてのオブジェクトは、同じ設定を使用します。 オブジェクトごとのパラメーターはありません。

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings のパラメーター

グローバルなシェル レンダリング プロパティに関連する設定は、クラス `ShellRenderingSettings` にあります。

| パラメーター      | Type    | 説明                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | 通常の最終オブジェクトの色に適用する彩度を下げる量。範囲は 0 (不飽和度なし) から 1 (完全に不飽和) までです。 |
| `Opacity`      | float   | シェル レンダリングされたオブジェクトの不透明度。範囲は 0 (表示されない) から 1 (完全に不透明) です。 |

シーン全体に適用する場合のパラメーターの効果の例については、次の表も参照してください。

|                | 0 | 0.25 | 0.5 | 0.75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **不飽和度** | ![不飽和度-0.0](./media/shell-desaturation-00.png) | ![不飽和度-0.25](./media/shell-desaturation-025.png) | ![不飽和度-0.5](./media/shell-desaturation-05.png) | ![不飽和度-0.75](./media/shell-desaturation-075.png) | ![不飽和度-1.0](./media/shell-desaturation-10.png) |
| **Opacity**      | ![不透明度-0.0](./media/shell-opacity-00.png) | ![不透明度-0.25](./media/shell-opacity-025.png) | ![不透明度-0.5](./media/shell-opacity-05.png) | ![不透明度-0.75](./media/shell-opacity-075.png) | ![不透明度-1.0](./media/shell-opacity-10.png) |

シェル効果は、それ以外の場合にシーンがレンダリングされる最終的な不透明色に適用されます。 これには、[濃淡の階層状態のオーバーライド](../../overview/features/override-hierarchical-state.md)が含まれます。

## <a name="example"></a>例

次のコードは、API を使用した `ShellRenderingSettings` 状態の使用例を示しています。

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>パフォーマンス

シェルのレンダリング機能は、標準の不透明のレンダリングと比較して、常に一定のオーバーヘッドが少量あります。 これは、オブジェクトに透過的な素材や[シースルー](../../overview/features/override-hierarchical-state.md) レンダリングを使用するよりも、はるかに高速です。 パフォーマンスは、シーンの一部だけをシェル レンダリングに切り替えた場合、さら大幅に低下する可能性があります。 この低下は、レンダリングを必要とするオブジェクトがさらに明らかになり発生します。 この点では、パフォーマンスは[カット プレーン](../../overview/features/cut-planes.md)機能と同様に動作します。

## <a name="next-steps"></a>次のステップ

* [階層状態のオーバーライド コンポーネント](../../overview/features/override-hierarchical-state.md)