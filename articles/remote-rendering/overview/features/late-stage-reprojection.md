---
title: Late Stage Reprojection
description: Late Stage Reprojection とその使用方法に関する情報。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022182"
---
# <a name="late-stage-reprojection"></a>Late Stage Reprojection

*Late Stage Reprojection* (LSR) は、ユーザーが移動したときにホログラムを安定化するのに役立つハードウェア機能です。

静的モデルは、移動時に位置を視覚的に維持する必要があります。 これが不安定であるように見える場合、この動作は LSR の問題を示唆している可能性があります。 アニメーションや分解図などの追加の動的な変形によって、この動作がマスクされる可能性があることに注意してください。

2つの異なる LSR モード (**Planar LSR** または **Depth LSR**) を選択できます。 どれがアクティブになるかは、クライアント アプリケーションが深度バッファーを送信するかどうかによって決まります。

どちらの LSR モードでも、ホログラムの安定性が向上しますが、それぞれに独自の制限があります。 まず、Depth LSR を試してみてください。ほとんどの場合、より良い結果が得られます。

## <a name="choose-lsr-mode-in-unity"></a>Unity で LSR モードを選択する

Unity エディターの *:::no-loc text="File > Build Settings":::* に移動します。 左下にある *:::no-loc text="Player Settings":::* を選択し、 *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* で **:::no-loc text="Enable Depth Buffer Sharing":::** がオンになっているかどうかを確認します。

![深度バッファーの共有を有効にするフラグ](./media/unity-depth-buffer-sharing-enabled.png)

オンである場合、アプリでは Depth LSR が使用されます。それ以外の場合は、Planar LSR が使用されます。

## <a name="depth-lsr"></a>Depth LSR

Depth LSR を機能させるには、クライアント アプリケーションが、LSR 中に考慮する必要があるすべての関連ジオメトリを含む有効な深度バッファーを指定する必要があります。

Depth LSR は、指定された深度バッファーの内容に基づいて、ビデオ フレームの安定化を試みます。 結果として、透明なオブジェクトなど、レンダリングされていないコンテンツは、LSR によって調整することはできず、不安定で再投影される可能性があります。

## <a name="planar-lsr"></a>Planar LSR

Planar LSR には、Depth LSR のように、ピクセルごとの深度情報は含まれません。 代わりに、各フレームで提供する必要がある平面に基づいてすべてのコンテンツを再投影します。

Planar LSR は、指定された平面に近いオブジェクトを適切に再投影します。 オブジェクトが離れていればいるほど、不安定になります。 Depth LSR の方が、異なる深さでオブジェクトを再投影することに優れていますが、Planar LSR は、平面と整列しているコンテンツに適しています。

### <a name="configure-planar-lsr-in-unity"></a>Unity で Planar LSR を構成する

プレーン パラメーターは、`UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` を使用してすべてのフレームに設定する必要がある*フォーカス ポイント*と呼ばれるものから派生しています。 詳細については、[Unity のフォーカス ポイント API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) に関するページを参照してください。 フォーカス ポイントを設定しない場合は、フォールバックが自動で選択されます。 ただし多くの場合、自動フォールバックの結果は最適ではありません。

フォーカス ポイントは自分で計算できますが、Remote Rendering ホストによって計算されたものを基にした方が適切な場合もあります。 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` を呼び出してこれを取得します。 フォーカス ポイントを表す座標フレームを指定するように求められます。 ほとんどの場合、`UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` の結果をここに入力するだけです。

通常、クライアントとホストの両方が、もう片方が認識していないコンテンツ (クライアント上の UI 要素など) を表示します。 そのため、リモートのフォーカス ポイントをローカルで計算されたフォーカス ポイントと結合することは理にかなっていると考えられます。

2 つの連続するフレームで計算されたフォーカス ポイントは、大きく異なる場合があります。 単にそのまま使用すると、ホログラムがあちこち飛び回って表示される可能性があります。 この動作を回避するには、前のフォーカス ポイントと現在のフォーカス ポイントを補間することをお勧めします。

## <a name="next-steps"></a>次のステップ

* [サーバー側のパフォーマンス クエリ](performance-queries.md)
