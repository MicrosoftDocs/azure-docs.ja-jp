---
title: レンダリング モード
description: サーバー側のさまざまなレンダリング モードについて説明します
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594503"
---
# <a name="rendering-modes"></a>レンダリング モード

Remote Rendering には、2 つの操作のメイン モード (**TileBasedComposition** モードと **DepthBasedComposition** モード) が用意されています。 これらのモードにより、ワークロードがサーバー上の複数の GPU 全体に分散される方法が決まります。 モードは接続時に指定する必要があり、実行時に変更することはできません。

どちらのモードにも利点がありますが、固有の機能制限があるため、最適なモードの選択はユース ケースごとに異なります。

## <a name="modes"></a>モード

2 つのモードについて、さらに詳しく説明します。

### <a name="tilebasedcomposition-mode"></a>'TileBasedComposition' モード

**TileBasedComposition** モードでは、関連するすべての GPU により、特定のサブ四角形 (タイル) が画面上にレンダリングされます。 メイン GPU により、タイルから最終的な画像が合成されてから、ビデオ フレームとしてクライアントに送信されます。 したがって、すべての GPU でレンダリングに同じリソース セットが必要になるため、読み込まれたアセットは 1 つの GPU のメモリに収まる必要があります。

MSAA はすべての GPU に対してジオメトリの完全なセットで動作できるため、このモードのレンダリング品質は **DepthBasedComposition** モードよりも多少優れています。 次のスクリーンショットは、アンチエイリアシングが両方のエッジに対して適切に機能することを示しています。

![TileBasedComposition の MSAA](./media/service-render-mode-quality.png)

さらに、このモードでは、各パーツを透明な素材に切り替えたり、[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) を介して **透過** モードに切り替えたりすることができます。

### <a name="depthbasedcomposition-mode"></a>'DepthBasedComposition' モード

**DepthBasedComposition** モードでは、関連するすべての GPU により全画面解像度でレンダリングされますが、メッシュのサブセットのみとなります。 メイン GPU での最終的な画像合成では、パーツが深度情報に従って適切にマージされます。 当然ながら、メモリ ペイロードは GPU 全体に分散されるため、単一の GPU のメモリに収まらないモデルをレンダリングできます。

すべての単一の GPU では、MSAA の使用によりローカル コンテンツがアンチエイリアスされます。 ただし、個別の GPU のエッジ間には固有のエイリアスが存在する場合があります。 この効果は最終的な画像の後処理によって軽減されますが、MSAA の品質は依然として **TileBasedComposition** モードより劣ります。

MSAA アーティファクトを次の図に示します。![DepthBasedComposition の MSAA](./media/service-render-mode-balanced.png)

両方のパーツが同じ GPU 上でレンダリングされるため、彫刻とカーテンの間でアンチエイリアシングが適切に機能します。 一方、カーテンと壁の間のエッジでは、これら 2 つのパーツが異なる GPU から合成されているため、いくつかのエイリアスが表示されます。

このモードの最大の制限として、ジオメトリ パーツを透明な素材に動的に切り替えることも、[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) に対して **透過** モードの動作を行うこともできません。 ただし、その他の状態のオーバーライド機能 (アウトライン、色の濃淡など) は機能します。 また、変換時に透過的とマークされた素材は、このモードで正常に機能します。

### <a name="performance"></a>パフォーマンス

どちらのモードのパフォーマンス特性も、ユース ケースによって異なります。また、一般的な推奨事項を判断したり示したりすることも困難です。 上記の制限 (メモリまたは透明度/透過) による制限を受けていない場合は、両方のモードを試して、さまざまなカメラの位置を使用してパフォーマンスを監視することをお勧めします。

## <a name="setting-the-render-mode"></a>レンダリング モードの設定

Remote Rendering サーバーで使用されるレンダリング モードは、`RenderingSession.ConnectAsync` の間に `RendererInitOptions` を介して指定されます。

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingSession.ConnectAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [C# RendererInitOptions 構造体](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession::ConnectToConnectAsyncRuntime()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [C++ RendererInitOptions 構造体](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>次のステップ

* [セッション](../concepts/sessions.md)
* [階層状態のオーバーライド コンポーネント](../overview/features/override-hierarchical-state.md)