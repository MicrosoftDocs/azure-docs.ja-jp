---
title: PBR 素材
description: PBR 素材の種類について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591941"
---
# <a name="pbr-materials"></a>PBR 素材

"*PBR 素材*" は、Azure Remote Rendering でサポートされている [素材の種類](../../concepts/materials.md)の 1 つです。 これらは、リアルな照明を受ける必要がある[メッシュ](../../concepts/meshes.md)に使用されます。

PBR は、**P** hysically **B** ased **R** endering (物理ベース レンダリング) の頭文字であり、すべての照明条件下で現実的な結果が得られるように、物理的に適切な方法で表面のビジュアル特性が素材において記述されることを意味します。 リアルタイム レンダリング用に現実世界を近似する手段としては PBR が最適であると考えられているため、最新のゲーム エンジンやコンテンツ作成ツールでは、PBR 素材がサポートされています。

![ARR によってレンダリングされたヘルメットの glTF サンプル モデル](media/helmet.png)

ただし、PBR 素材は万能のソリューションではありません。 見る角度によって色の反射が異なる素材があります。 たとえば、一部の織物や自動車の塗装などです。 この種の素材は、標準的な PBR モデルでは処理されず、現状 Azure Remote Rendering ではサポートされていません。 これには、*Thin-Film* (多層表面) や *Clear-Coat* (自動車の塗装用) などの、PBR 拡張機能が含まれます。

## <a name="common-material-properties"></a>素材の共通プロパティ

これらのプロパティは、すべての素材に共通です。

* **albedoColor:** この色には、*albedoMap* や " *:::no-loc text="vertex "::: カラー*" など、その他の色が乗算されます。 素材に対して *透明度* が有効な場合、アルファ チャネルを使用して不透明度が調整されます。ここで、`1` は完全に不透明、`0` は完全に透明を意味します。 既定値は白です。

  > [!NOTE]
  > まったく汚れのないガラスのように、PBR 素材が完全に透明な場合でも、環境が反射されます。 太陽のような明るいスポットは、反射でもやはり見えます。 これは、[色素材](color-materials.md)とは異なります。

* **albedoMap:** ピクセル単位の albedo 値の [2D テクスチャ](../../concepts/textures.md)。

* **alphaClipEnabled** と **alphaClipThreshold:** *alphaClipEnabled* が true の場合、albedo アルファ値が *alphaClipThreshold* よりも小さいすべてのピクセルは描画されません。 アルファ クリッピングは、透明度を有効にしなくても使用でき、レンダリングがはるかに高速になります。 ただし、アルファ クリッピングされた素材は、完全に不透明な素材と比べると、レンダリングは遅くなります。 既定では、アルファ クリッピングは無効になっています。

* **textureCoordinateScale** と **textureCoordinateOffset:** スケールが UV テクスチャ座標に乗算され、オフセットが追加されます。 テクスチャの伸縮とシフトに使用できます。 既定のスケールは (1, 1) で、オフセットは (0, 0) です。

* **useVertexColor:** メッシュに :::no-loc text="vertex"::: カラーが含まれていて、このオプションが有効になっている場合は、メッシュの :::no-loc text="vertex"::: カラーが *albedoColor* と *albedoMap* に乗算されます。 既定では *useVertexColor* は無効になっています。

* **isDoubleSided:** 両面に対する表示が true に設定されている場合、この素材が表示されている三角形は、カメラが背面を見ている場合でもレンダリングされます。 PBR 素材の場合、照明も背面に対して適切に計算されます。 既定では、このオプションは無効になっています。 「[:::no-loc text="Single-sided"::: レンダリング](single-sided-rendering.md)」も参照してください。

* **TransparencyWritesDepth:** 素材に TransparencyWritesDepth フラグが設定されていて、その素材が透明である場合、その素材を使用した物体も、最終的な深度バッファーに寄与します。 次のセクションの PBR 素材フラグ *transparent* を参照してください。 実際のユース ケースで、完全に透明なシーンの [Late Stage Reprojection](late-stage-reprojection.md) の現実感を高める必要がある場合は、この機能を有効にすることをお勧めします。 不透明と透明が混在するシーンでは、この設定によって、非現実的な再投影動作や再投影アーティファクトが生じることがあります。 このため、一般的なユース ケースにおいて推奨される既定の設定は、このフラグを無効にすることです。 書き込まれる深度値は、カメラに最も近い物体のピクセルごとの深度レイヤーから取得されます。

* **FresnelEffect:** この素材フラグを使用すると、個々のマテリアルに対して付加的な [フレネル効果](../../overview/features/fresnel-effect.md)を有効にすることができます。 この効果の外観は、以下で説明する、他のフレネル パラメーターによって制御されます。 

* **FresnelEffectColor:** この素材に使用されるフレネルの色。 この素材に対してフレネル効果ビットが設定されている場合にのみ重要です (上記を参照)。 このプロパティによって、フレネル光沢の基本色が制御されます (詳細な説明は「[フレネル効果](../../overview/features/fresnel-effect.md)」を参照してください)。 現在は、rgb チャネル値のみが重要であり、アルファ値は無視されます。

* **FresnelEffectExponent:** この素材に使用されるフレネル指数。 この素材に対してフレネル効果ビットが設定されている場合にのみ重要です (上記を参照)。 このプロパティによって、フレネル光沢の拡散が制御されます。 最小値 0.01 の場合、オブジェクト全体にわたって拡散されます。 最大値 10.0 では、光沢が抑制され、最グレージング端にのみ表示されます。

## <a name="pbr-material-properties"></a>PBR 素材のプロパティ

物理ベース レンダリングの中心になる概念は、*BaseColor*、*Metalness*、*Roughness* プロパティを使用して、さまざまな現実世界の素材をエミュレートすることです。 PBR の詳細については、この記事では説明しません。 PBR について詳しくは、[他のソース](http://www.pbr-book.org)をご覧ください。 次のプロパティは、PBR 素材に固有のものです。

* **baseColor:** PBR 素材では、"*アルベド カラー*" は "*ベース カラー*" と呼ばれます。 Azure Remote Rendering では、"*アルベド カラー*" プロパティは素材の共通プロパティに既に存在するため、追加のベース カラー プロパティはありません。

* **roughness** と **roughnessMap:** roughness では、表面の粗さまたは滑らかさを定義します。 粗い表面では滑らかな表面より多くの方向に光が散乱するため、反射がシャープではなくぼやけます。 値の範囲は `0.0` から `1.0` までです。 `roughness` が `0.0` の場合、反射はシャープになります。 `roughness` が `0.5` の場合、反射はぼやけるようになります。

  粗さの値と粗さのマップの両方を指定した場合、最終的な値は両方の積になります。

* **metalness** と **metalnessMap:** 物理的には、このプロパティは表面が導電性か誘電性かに対応します。 導電性の素材にはさまざまな反射特性があり、アルベド カラーなしで反射される傾向があります。 PBR 素材では、このプロパティによって、周囲の環境がどの程度反射されるかが決まります。 値の範囲は `0.0` から `1.0` です。 metalness を `0.0` にすると、アルベド カラーが完全に見えるようになり、素材はプラスチックやセラミックのようになります。 metalness を `0.5` にすると、塗装された金属のように見えます。 metalness を `1.0` にすると、表面はアルベド カラーをほぼ完全に失い、周囲の反射だけになります。 たとえば、`metalness` を `1.0` にして、`roughness` を `0.0` にした場合、表面は実際の鏡のように見えます。

  metalness の値と metalnessMap の両方を指定した場合、最終的な値は両方の積になります。

  ![さまざまな metalness と roughness の値を使用してレンダリングされた球](./media/metalness-roughness.png)

  上の図では、右下隅の球は実際の金属素材のように見え、左下はセラミックまたはプラスチックのように見えます。 アルベド カラーも、物理的なプロパティに応じて変化します。 粗さが増すと、素材の反射のシャープさが失われます。

* **normalMap:** 微細なディテールをシミュレートするには、[法線マップ](https://en.wikipedia.org/wiki/Normal_mapping)を提供できます。

* **occlusionMap** と **aoScale:** [アンビエント オクルージョン](https://en.wikipedia.org/wiki/Ambient_occlusion)を使用すると、間に隙間があるオブジェクトの隠されている領域にシャドウが追加されて、外観がいっそう現実的になります。 オクルージョン値の範囲は `0.0` から `1.0` です。`0.0` は暗い (隠されている) ことを意味し、`1.0` は隠されていないことを意味します。 2D テクスチャをオクルージョン マップとして提供した場合、効果が有効になり、*aoScale* は乗数として機能します。

  ![アンビエント オクルージョンあり/なしでレンダリングされたオブジェクト](./media/boom-box-ao2.gif)

* **transparent:** PBR 素材の場合、透過性の設定は、有効か否かの 1 つだけです。 不透明度は、アルベド カラーのアルファ チャネルによって定義されます。 有効にすると、より複雑なレンダリング パイプラインが呼び出されて、半透明の表面が描画されます。 Azure Remote Rendering では、真の[順序に依存しない透明度](https://en.wikipedia.org/wiki/Order-independent_transparency) (Order Independent Transparency: OIT) が実装されています。

  透明なジオメトリは、レンダリングの負荷が大きくなます。 表面に穴が必要なだけの場合は (木の葉など)、代わりにアルファ クリッピングを使用することをお勧めします。

  ![ゼロから完全までの透明度を使用してレンダリングされた球](./media/transparency.png)上の図では、右端の球は完全に透明ですが、反射がまだ表示されていることに注意してください。

  > [!IMPORTANT]
  > 実行時に素材が不透明から透明に切り替わることが想定される場合は、レンダラーで *TileBasedComposition* [レンダリング モード](../../concepts/rendering-modes.md)を使用する必要があります。 この制限は、最初から透明な素材として変換される素材には適用されません。

## <a name="technical-details"></a>技術的な詳細

Azure Remote Rendering では、Cook-Torrance マイクロファセット BRDF と GGX NDF、Schlick フレネル、GGX Smith で相関された可視性項と Lambert 拡散項が使用されています。 このモデルは、現時点では事実上の業界標準です。 詳細については、次の記事をご覧ください: 「[Physically based Rendering - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)」(物理ベース レンダリング - Cook Torrance)

 Azure Remote Rendering で使用されている *Metalness-Roughness* PBR に代わるものとして、*Specular-Glossiness* PBR モデルがあります。 このモデルでは、より広い範囲の素材を表すことができます。 ただし、コストが高く、通常、リアルタイムでは適切に機能しません。
*(BaseColor、Metalness)* に変換できない " *(拡散、反射)* " の値のペアがあるため、*Specular-Glossiness* から *Metalness-Roughness* に常に変換できるとは限りません。 *(BaseColor、Metalness)* のすべてのペアは明確に定義された " *(拡散、反射)* " のペアに対応するため、反対方向への変換は、より簡単で正確です。

## <a name="api-documentation"></a>API のドキュメント

* [C# PbrMaterial class](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ PbrMaterial class](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>次のステップ

* [色素材](color-materials.md)
* [テクスチャ](../../concepts/textures.md)
* [メッシュ](../../concepts/meshes.md)