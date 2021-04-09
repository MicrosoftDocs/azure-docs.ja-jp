---
title: アウトライン レンダリング
description: 選択アウトライン レンダリングを実行する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592009"
---
# <a name="outline-rendering"></a>アウトライン レンダリング

選択したオブジェクトは、[階層状態のオーバーライド コンポーネント](../../overview/features/override-hierarchical-state.md)を使用してアウトライン レンダリングを追加することで、視覚的に強調表示できます。 この章では、クライアント API を使用してアウトライン レンダリングのグローバル パラメーターを変更する方法について説明します。

アウトラインのプロパティは、グローバル設定です。 アウトライン レンダリングを使用するすべてのオブジェクトは同じ設定を使用します。オブジェクトごとに異なるアウトラインの色を使用することはできません。

## <a name="parameters-for-outlinesettings"></a>`OutlineSettings` のパラメーター

クラス `OutlineSettings` は、グローバルなアウトラインのプロパティに関連する設定を保持します。 公開されるメンバーは以下のとおりです。

| パラメーター      | Type    | 説明                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | アウトラインの描画に使用される色。 アルファ部分は無視されます。         |
| `PulseRateHz`    | float   | アウトラインが 1 秒あたりに振動する速度|
| `PulseIntensity` | float   | アウトラインのパルス効果の強度。 パルスを止める場合は 0.0、最大限に強める場合は 1.0 の間である必要があります。 強度では、アウトラインの最小不透明度を `MinOpacity = 1.0 - PulseIntensity` として暗黙的に設定します。 |

![さまざまなアウトライン パラメーターを使用して 3 回レンダリングされたオブジェクト](./media/outlines.png) `color` パラメーターを黄色 (左) からマゼンタ (中央) に、`pulseIntensity` を 0 から 0.8 (右) に変更した場合の効果。

## <a name="example"></a>例

次のコードは、API を使用してアウトラインのパラメーターを設定する例を示しています。

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>パフォーマンス

アウトライン レンダリングは、レンダリングのパフォーマンスに大きな影響を与える可能性があります。 この影響は、特定のフレームに対する、選択されたオブジェクトと選択されていないオブジェクトの間の画面領域の空間的な比率によって異なります。

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingConnection.OutlineSettings property](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C++ RenderingConnection::OutlineSettings()](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>次のステップ

* [階層状態のオーバーライド コンポーネント](../../overview/features/override-hierarchical-state.md)