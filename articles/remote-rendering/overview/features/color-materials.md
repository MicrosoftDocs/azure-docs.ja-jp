---
title: 色素材
description: 色素材の種類について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: fb26a669229ac140aba262032f8ce84ef9f37727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593385"
---
# <a name="color-materials"></a>色素材

*色素材* は、Azure Remote Rendering でサポートされている [素材の種類](../../concepts/materials.md)の 1 つです。 これらは、いかなる照明も当てず、常に明るさが最大である[メッシュ](../../concepts/meshes.md)に使用されます。 車のダッシュボード、電球、静的な光源が既に組み込まれているデータ ([フォトグラメトリ](https://en.wikipedia.org/wiki/Photogrammetry)で取得したモデルなど) については、これが当てはまります。

色素材のシェーディング モデルは [PBR 素材](pbr-materials.md)と比べて単純であるため、より効率的にレンダリングできます。 また、さまざまな透明度モードもサポートしています。

## <a name="common-material-properties"></a>共通の素材のプロパティ

これらのプロパティは、すべての素材に共通です。

* **albedoColor:** この色には、*albedoMap* や " *:::no-loc text="vertex"::: カラー*" など、その他の色が乗算されます。 素材に対して *透明度* が有効な場合、アルファ チャネルを使用して不透明度が調整されます。ここで、`1` は完全に不透明、`0` は完全に透明を意味します。 既定値は白です。

  > [!NOTE]
  > 色素材には環境が反映されないため、完全に透明な色の素材は非表示になります。 これは、[PBR 素材](pbr-materials.md)とは異なります。

* **albedoMap:** ピクセル単位の albedo 値の [2D テクスチャ](../../concepts/textures.md)。

* **alphaClipEnabled** と **alphaClipThreshold:** *alphaClipEnabled* が true の場合、albedo アルファ値が *alphaClipThreshold* よりも小さいすべてのピクセルは描画されません。 アルファ クリッピングは、透明度を有効にしなくても使用でき、レンダリングがはるかに高速になります。 ただし、アルファ クリッピングされた素材は、完全に不透明な素材と比べると、レンダリングは遅くなります。 既定では、アルファ クリッピングは無効になっています。

* **textureCoordinateScale** と **textureCoordinateOffset:** スケールが UV テクスチャ座標に乗算され、オフセットが追加されます。 テクスチャの伸縮とシフトに使用できます。 既定のスケールは (1, 1) で、オフセットは (0, 0) です。

* **useVertexColor:** メッシュに :::no-loc text="vertex"::: カラーが含まれていて、このオプションが有効になっている場合は、メッシュの :::no-loc text="vertex"::: カラーが *albedoColor* と *albedoMap* に乗算されます。 既定では *useVertexColor* は無効になっています。

* **isDoubleSided:** 両面に対する表示が true に設定されている場合、この素材が表示されている三角形は、カメラが背面を見ている場合でもレンダリングされます。 既定では、このオプションは無効になっています。 「[:::no-loc text="Single-sided"::: レンダリング](single-sided-rendering.md)」も参照してください。

* **TransparencyWritesDepth:** 素材に TransparencyWritesDepth フラグが設定されていて、その素材が透明である場合、その素材を使用した物体も、最終的な深度バッファーに寄与します。 次のセクションの色素材プロパティ *transparencyMode* を参照してください。 実際のユース ケースで、完全に透明なシーンの [Late Stage Reprojection](late-stage-reprojection.md) の現実感を高める必要がある場合は、この機能を有効にすることをお勧めします。 不透明と透明が混在するシーンでは、この設定によって、非現実的な再投影動作や再投影アーティファクトが生じることがあります。 このため、一般的なユース ケースにおいて推奨される既定の設定は、このフラグを無効にすることです。 書き込まれる深度値は、カメラに最も近い物体のピクセルごとの深度レイヤーから取得されます。

* **FresnelEffect:** この素材フラグを使用すると、個々のマテリアルに対して付加的な [フレネル効果](../../overview/features/fresnel-effect.md)を有効にすることができます。 この効果の外観は、以下で説明する、他のフレネル パラメーターによって制御されます。 

* **FresnelEffectColor:** この素材に使用されるフレネルの色。 この素材に対してフレネル効果ビットが設定されている場合にのみ重要です (上記を参照)。 このプロパティによって、フレネル光沢の基本色が制御されます (詳細な説明は「[フレネル効果](../../overview/features/fresnel-effect.md)」を参照してください)。 現在は、rgb チャネル値のみが重要であり、アルファ値は無視されます。

* **FresnelEffectExponent:** この素材に使用されるフレネル指数。 この素材に対してフレネル効果ビットが設定されている場合にのみ重要です (上記を参照)。 このプロパティによって、フレネル光沢の拡散が制御されます。 最小値 0.01 の場合、オブジェクト全体にわたって拡散されます。 最大値 10.0 では、光沢が抑制され、最グレージング端にのみ表示されます。

## <a name="color-material-properties"></a>色素材のプロパティ

次のプロパティは、色素材に固有のものです。

* **vertexMix:** `0` と `1` の間のこの値は、[メッシュ](../../concepts/meshes.md)の :::no-loc text="vertex"::: カラーが最終的な色にどれだけ強く影響するかを指定します。 既定値の 1 の場合、:::no-loc text="vertex"::: カラーは albedo の色に完全に乗算されます。 値が 0 の場合、:::no-loc text="vertex"::: カラーは完全に無視されます。

* **transparencyMode:** [PBR 素材](pbr-materials.md)とは対照的に、色素材は異なる透明度モードを区別します。

  1. **Opaque:** 既定のモードでは、透明度が無効になります。 この場合でもアルファ クリッピングは引き続き可能で、これが十分な場合は推奨されます。
  
  1. **AlphaBlended:** このモードは、PBR 素材の透明度モードに似ています。 ガラスなどの透明な素材の表示に使用されます。

  1. **Additive:** このモードは、最もシンプルかつ効率的な透明度モードです。 素材の影響は、レンダリングされるイメージに追加されます。 このモードを使用すると、重要なオブジェクトを強調表示するために使用されるマーカーなど、光彩 (ただし透明) のオブジェクトをシミュレートできます。

## <a name="api-documentation"></a>API のドキュメント

* [C# ColorMaterial クラス](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ ColorMaterial クラス](/cpp/api/remote-rendering/colormaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>次のステップ

* [PBR 素材](pbr-materials.md)
* [テクスチャ](../../concepts/textures.md)
* [メッシュ](../../concepts/meshes.md)