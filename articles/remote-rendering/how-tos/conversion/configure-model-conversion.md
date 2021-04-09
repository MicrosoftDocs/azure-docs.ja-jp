---
title: モデルの変換を構成する
description: モデル変換のすべてのパラメーターについての説明
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 1cb5312e164bac09930497c377f1590b6a77ca05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205321"
---
# <a name="configure-the-model-conversion"></a>モデルの変換を構成する

この章では、モデル変換のオプションについて説明します。

## <a name="settings-file"></a>設定ファイル

入力コンテナー内に、入力モデル `<modelName>.<ext>` と共に `<modelName>.ConversionSettings.json` という名前のファイルがある場合、そのファイルを使用して、モデル変換プロセスに追加の構成が提供されます。
たとえば、`box.gltf` の変換時に `box.ConversionSettings.json` が使用されます。

ファイルの内容は、次の JSON スキーマに従っている必要があります。

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

ファイルの例 `box.ConversionSettings.json` は、次のようになることがあります。

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>ジオメトリ パラメーター

* `scaling` - このパラメーターを指定すると、モデルは一様にスケーリングされます。 スケーリングを使用すると、モデルを拡大したり縮小したりすることができます (たとえば、テーブル トップに建築モデルを表示する場合)。
スケーリングは、モデルがメートル以外の単位で定義されている場合にも重要です。これは、レンダリング エンジンではメートルが予期されているためです。
たとえば、モデルがセンチメートルで定義されている場合、0.01 のスケールを適用すると、モデルが適切なサイズで表示されます。
一部のソース データ形式 (.fbx など) では、単位スケーリング ヒントが提供されます。その場合は、変換によってモデルがメートル単位に暗黙的にスケーリングされます。 ソース形式によって提供される暗黙的なスケーリングは、スケーリング パラメーターに加えて適用されます。
最終的なスケール ファクターは、ジオメトリ頂点と、シーン グラフ ノードのローカル変換に対して適用されます。 ルート エンティティの変換に対するスケーリングは変更されません。

* `recenterToOrigin` - 境界ボックスの中心が原点に配置されるようにモデルを変換する必要があることを示します。
ソース モデルが原点から遠く外れている場合、浮動小数点の精度の問題によってレンダリング アーティファクトが発生する可能性があります。
モデルをセンタリングすることは、この状況で役立ちます。

* `opaqueMaterialDefaultSidedness` - レンダリング エンジンでは、不透明な素材は両面であると想定されます。
その想定が特定のモデルでは正しくない場合は、このパラメーターを "SingleSided" に設定する必要があります。 詳細については、「[:::no-loc text="single sided":::レンダリング](../../overview/features/single-sided-rendering.md)」を参照してください。

### <a name="material-de-duplication"></a>素材の重複除去

* `deduplicateMaterials` - このパラメーターは、同じプロパティとテクスチャを共有する素材の自動重複除去を有効または無効にします。 重複除去は、素材のオーバーライドが処理された後に実行されます。 既定で有効です。

* 重複除去後でも、モデルのマテリアルが 65,535 個を超える場合、サービスによって、同様のプロパティを持つマテリアルのマージが試行されます。 最後の手段として、制限を超えるマテリアルはすべて赤色のエラー マテリアルに置き換えられます。

![画像は 68,921 色の三角形の 2 つの立方体を示しています。](media/mat-dedup.png?raw=true)

68,921 色の三角形の 2 つの立方体。 左:68,921 色のマテリアルを使用して重複除去を行う前。 右:64,000 色のマテリアルを使用して重複除去を行った後。 上限は 65,535 個のマテリアルです ([制限](../../reference/limits.md)に関するページを参照してください)。

### <a name="color-space-parameters"></a>色空間のパラメーター

レンダリング エンジンでは、色の値は線形空間内であるものと想定されています。
モデルがガンマ空間を使用して定義されている場合は、これらのオプションを true に設定する必要があります。

* `gammaToLinearMaterial` - 素材の色をガンマ空間から線形空間に変換します
* `gammaToLinearVertex` - :::no-loc text="vertex":::の色をガンマ空間から線形空間に変換します

> [!NOTE]
> FBX ファイルの場合、これらの設定は既定で `true` に設定されます。 その他のすべてのファイルの種類では、既定値は `false` です。

### <a name="scene-parameters"></a>シーンのパラメーター

* `sceneGraphMode` - ソース ファイルのシーン グラフの変換方法を定義します。
  * `dynamic` (既定値): ファイル内のすべてのオブジェクトが API で[エンティティ](../../concepts/entities.md)として公開され、個別に変換できます。 実行時のノード階層は、ソース ファイルでの構造と同じです。
  * `static`:すべてのオブジェクトは API で公開されますが、それらを個別に変換することはできません。
  * `none`:シーン グラフは、1 つのオブジェクトに折りたたまれます。

モードによって実行時のパフォーマンスが異なります。 `dynamic` モードでは、パーツが移動されない場合でも、パフォーマンス コストはグラフ内の[エンティティ](../../concepts/entities.md)の数に比例して増減します。 `dynamic` モードは、"爆発の光景" のアニメーションなど、パーツを個々に移動する必要がある場合にのみ使用します。

`static` モードでは、完全なシーン グラフがエクスポートされますが、このグラフ内のパーツの、ルート パーツを基準とする変換は一定です。 ただし、それでも、大きなパフォーマンス コストを発生させずに、オブジェクトのルート ノードを移動、回転、スケーリングできます。 さらに、[空間クエリ](../../overview/features/spatial-queries.md)では個々のパーツが返され、[状態のオーバーライド](../../overview/features/override-hierarchical-state.md)を使用して各パーツを変更できます。 このモードでは、オブジェクトごとの実行時のオーバーヘッドはごくわずかです。 オブジェクトごとの検査は必要でも、オブジェクトごとに変換を変更する必要はない、大規模なシーンに最適です。

`none` モードでは、実行時のオーバーヘッドが最小限に抑えられ、読み込み時間も若干向上します。 このモードでは、単一のオブジェクトを検査または変換することはできません。 ユース ケースとしては、たとえば、最初は意味のあるシーン グラフがない写真測量モデルなどがあります。

> [!TIP]
> 多くのアプリケーションでは、複数のモデルが読み込まれます。 モデルごとに、その使用方法に応じて、変換パラメーターを最適化する必要があります。 たとえば、ユーザーが分解して詳しく調べられるように車のモデルを表示する場合は、`dynamic` モードで変換する必要があります。 一方、ショー ルームの環境に車を追加して配置したい場合は、`static` または `none` に設定した `sceneGraphMode` でそのモデルを変換することもできます。

### <a name="physics-parameters"></a>物理パラメーター

* `generateCollisionMesh` - モデルで[空間クエリ](../../overview/features/spatial-queries.md)をサポートする必要がある場合は、このオプションを有効にする必要があります。 衝突メッシュを作成すると、最悪の場合、変換時間が 2 倍になる可能性があります。 衝突メッシュが含まれるモデルは読み込みに時間がかかり、`dynamic` シーン グラフを使用すると、実行時のパフォーマンスのオーバーヘッドも高くなります。 全体的なパフォーマンスを最適にするには、空間クエリを必要としないすべてのモデルで、このオプションを無効にする必要があります。

### <a name="unlit-materials"></a>照明なしの素材

* `unlitMaterials` - 既定の変換では、[PBR 素材](../../overview/features/pbr-materials.md)が優先的に作成されます。 このオプションを指定すると、すべての素材を[色素材](../../overview/features/color-materials.md)として扱うようにコンバーターに指示します。 写真測量によって作成されたモデルなど、既に照明が組み込まれているデータがある場合、このオプションを使用すると、[各素材を個別にオーバーライドする](override-materials.md)必要なしに、すべての素材に対して正しい変換を迅速に適用できます。

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Phong 素材モデルが使用されている古い FBX 形式から変換する

* `fbxAssumeMetallic` - 古いバージョンの FBX 形式では、Phong 素材モデルを使用して素材が定義されています。 変換プロセスでは、これらの素材をレンダラーの [PBR モデル](../../overview/features/pbr-materials.md)にマップする方法を推定する必要があります。 通常は問題なく機能しますが、素材にテクスチャがなく、反射値が高く、アルベド カラーがグレーでない場合は、あいまいさが発生する可能性があります。 そのような場合は、変換において、高い反射値を優先し、アルベド カラーがディゾルブする場所で反射率の高いメタリック素材を定義するか、またはアルベド カラーを優先し、光沢のあるカラフルなプラスチックのようなものを定義するかを選択する必要があります。 既定の変換プロセスでは、あいまいさが適用される場合、高い反射値はメタリック素材を意味することが想定されています。 このパラメーターを `false` に設定すると、逆に切り替えることができます。

### <a name="coordinate-system-overriding"></a>座標系のオーバーライド

* `axis` - 座標系の単位ベクトルをオーバーライドします。 既定値は `["+x", "+y", "+z"]` です。 理論的には、FBX 形式にはこれらのベクトルが定義されているヘッダーがあり、変換ではその情報を使用してシーンが変換されます。 glTF 形式では、固定座標系も定義されています。 実際には、ヘッダーの情報が正しくない資産や、別の座標系規則を使用して保存されている資産もあります。 このオプションを使用すると、座標系をオーバーライドして補正できます。 たとえば、`"axis" : ["+x", "+z", "-y"]` では、Z 軸と Y 軸が入れ替えられ、Y 軸の方向を反転することによって座標系の掌性が維持されます。

### <a name="node-meta-data"></a>ノードのメタデータ

* `metadataKeys` - 変換結果に保持するノードのメタデータ プロパティのキーを指定できます。 正確なキーまたはワイルドカードのキーを指定できます。 ワイルドカードのキーは "ABC*" の形式であり、"ABC" で始まるすべてのキーと一致します。 サポートされているメタデータ値の型は、`bool`、`int`、`float`、`string` です。

    GLTF ファイルの場合、このデータは[ノードの extras オブジェクト](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras)から取得されます。 FBX ファイルの場合、このデータは `Model nodes` の `Properties70` データから取得されます。 詳細については、3D アセット ツールのドキュメントを参照してください。

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex":::形式

メッシュの:::no-loc text="vertex":::形式を調整し、精度を低くする代わりにメモリを節約することができます。 メモリ占有領域を小さくすると、より大きいモデルを読み込んだり、パフォーマンスを向上させたりすることができます。 ただし、データによっては、形式を誤るとレンダリング品質に大きく影響する可能性があります。

> [!CAUTION]
> :::no-loc text="vertex":::形式の変更は、モデルがメモリに収まらなくなった場合や、パフォーマンスを可能な限り最適化する場合の最後の手段として使用する必要があります。 変更により、明らかなものと微妙なもの両方のレンダリング アーティファクトが簡単に発生する可能性があります。 気を付ける点が不明な場合は、既定値を変更しないでください。

次のような調整が可能です。

* 特定のデータ ストリームを明示的に含めたり除外したりできます。
* データ ストリームの精度を下げて、メモリ占有領域を減らすことができます。

`.json` ファイルの次の `vertex` セクションは省略可能です。 明示的に指定されていない部分については、変換サービスの既定の設定が使用されます。

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

コンポーネントを強制的に `NONE` にすることにより、出力メッシュに対応するストリームが含まれないことが保証されます。

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>:::no-loc text="vertex":::ストリームごとのコンポーネントの形式

それぞれのコンポーネントでは以下の形式を使用できます。

| :::no-loc text="Vertex"::: コンポーネント | サポートされている形式 (太字 = 既定値) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**、16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**、NONE |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED、**NONE**|
|普通| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT、NONE |
|tangent| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT、NONE |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**、16_16_16_16_FLOAT、NONE |
|texcoord0| **32_32_FLOAT**、16_16_FLOAT、NONE |
|texcoord1| **32_32_FLOAT**、16_16_FLOAT、NONE |

#### <a name="supported-component-formats"></a>サポートされているコンポーネントの形式

各形式のメモリ占有領域は次のとおりです。

| Format | 説明 | :::no-loc text="vertex":::ごとのバイト数 |
|:-------|:------------|:---------------|
|32_32_FLOAT|2 コンポーネントの完全浮動小数点数精度|8
|16_16_FLOAT|2 コンポーネントの半浮動小数点数精度|4
|32_32_32_FLOAT|3 コンポーネントの完全浮動小数点数精度|12
|16_16_16_16_FLOAT|3 コンポーネントの半浮動小数点数精度|8
|8_8_8_8_UNSIGNED_NORMALIZED|`[0; 1]` の範囲に正規化された 4 コンポーネントのバイト|4
|8_8_8_8_SIGNED_NORMALIZED|`[-1; 1]` の範囲に正規化された 4 コンポーネントのバイト|4

#### <a name="best-practices-for-component-format-changes"></a>コンポーネント形式の変更についてのベスト プラクティス

* `position`:低下した精度でも十分であることはほとんどありません。 **16_16_16_16_FLOAT** では、小さなモデルでも、顕著な量子化アーティファクトが発生します。
* `normal`、`tangent`、`binormal`:通常、これらの値はまとめて変更されます。 通常の量子化の結果として顕著な照明アーティファクトが発生するのでない限り、精度を上げる理由はありません。 ただし、場合によっては、これらのコンポーネントを **NONE** に設定できます。
  * `normal`、`tangent`、`binormal` は、モデル内の少なくとも 1 つの素材に照明を当てる必要がある場合にのみ必要です。 ARR では、これは常にモデルで [PBR 素材](../../overview/features/pbr-materials.md)が使用されている場合です。
  * `tangent` と `binormal` は、照明ありの素材のいずれかで通常のマップ テクスチャが使用されている場合にのみ必要です。
* `texcoord0`、`texcoord1`: テクスチャ座標では、これらの値が `[0; 1]` の範囲内にあり、指定されたテクスチャの最大サイズが 2048 x 2048 ピクセルの場合に、低い精度 (**16_16_FLOAT**) を使用できます。 これらの制限を超えた場合、テクスチャ マッピングの品質が低下します。

#### <a name="example"></a>例

テクスチャに照明が組み込まれている写真測量モデルを使用しているものとします。 モデルをレンダリングするために必要なのは、:::no-loc text="vertex":::の位置とテクスチャ座標だけです。

既定では、コンバーターによりある時点でモデルに対して PBR 素材を使用する必要があると想定されて、`normal`、`tangent`、`binormal` データが自動的に生成されます。 その結果、頂点ごとのメモリ使用量は `position` (12 バイト) + `texcoord0` (8 バイト) + `normal` (4 バイト) + `tangent` (4 バイト) + `binormal` (4 バイト) = 32 バイトになります。 この種類のモデルがさらに大きくなると、:::no-loc text="vertices":::の数は簡単に数百万になり、モデルで複数ギガバイトのメモリが使用される可能性があります。 そのような大量のデータは、パフォーマンスに影響を与え、メモリが不足する可能性もあります。

モデルで動的な照明が必要がなく、すべてのテクスチャ座標が `[0; 1]` の範囲内にあることがわかっている場合は、`normal`、`tangent`、`binormal` を `NONE` に設定し、`texcoord0` を半精度 (`16_16_FLOAT`) に設定することで、:::no-loc text="vertex":::あたり 16 バイトだけにすることができます。 メッシュ データを半分に減らすことで、より大きなモデルを読み込むことができ、パフォーマンスが向上する可能性があります。

## <a name="memory-optimizations"></a>メモリの最適化

読み込まれるコンテンツのメモリ消費が、レンダリング システムのボトルネックになる場合があります。 メモリ ペイロードが大きくなりすぎると、レンダリングのパフォーマンスが低下したり、モデルが完全に読み込まれなくなる可能性があります。 この段落では、メモリ占有領域を削減するためのいくつかの重要な戦略について説明します。

### <a name="instancing"></a>インスタンス化

インスタンス化は、すべてのパーツで独自のジオメトリを参照するのではなく、個別の空間変換を使用するパーツに対してメッシュを再利用する概念です。 インスタンス化は、メモリ占有領域に大きく影響します。
インスタンス化のユースケースの例には、エンジン モデルのネジや建築モデルの椅子があります。

> [!NOTE]
> インスタンス化によって、メモリ消費量 (および読み込み回数) を大幅に減らすことができます。ただし、レンダリングのパフォーマンスが大幅に向上することはありません。

変換サービスでは、パーツをインスタンス化するようにソース ファイルでマークアップされている場合は、それに従います。 ただし、変換では、再利用可能なパーツを識別するためのメッシュ データの追加の詳細分析は実行されません。 したがって、インスタンス化を適切に設定するための決定的な条件は、コンテンツ作成ツールとそのエクスポート パイプラインになります。

変換中にインスタンス化情報が保持されるかどうかをテストする簡単な方法は、[出力統計](get-information.md#example-info-file) (具体的には `numMeshPartsInstanced` メンバー) を見ることです。 `numMeshPartsInstanced` の値が 0 より大きい場合は、メッシュがインスタンス間で共有されていることを示しています。

#### <a name="example-instancing-setup-in-3ds-max"></a>例:3ds Max でのインスタンス化の設定

[Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) には、エクスポートされた `.fbx` ファイルでのインスタンス化に関して異なる動作をする **`Copy`** 、 **`Instance`** 、および **`Reference`** と呼ばれる個別のオブジェクト複製モードがあります。

![3ds Max での複製](./media/3dsmax-clone-object.png)

* **`Copy`** :このモードではメッシュが複製されるため、インスタンス化は使用されません (`numMeshPartsInstanced` = 0)。
* **`Instance`** :2 つのオブジェクトで同じメッシュが共有されるため、インスタンス化が使用されます (`numMeshPartsInstanced` = 1)。
* **`Reference`** :ジオメトリに Distinct 修飾子を適用できるため、エクスポーターでは保守的なアプローチが選択され、インスタンス化は使用されません (`numMeshPartsInstanced` = 0)。


### <a name="depth-based-composition-mode"></a>深度ベースのコンポジション モード

メモリが重要な場合は、[深度ベースのコンポジション モード](../../concepts/rendering-modes.md#depthbasedcomposition-mode)でレンダラーを構成します。 このモードでは、複数の GPU に GPU ペイロードが分散されます。

### <a name="decrease-vertex-size"></a>頂点サイズを縮小する

「[コンポーネント形式の変更についてのベスト プラクティス](configure-model-conversion.md#best-practices-for-component-format-changes)」セクションで説明したように、頂点形式の調整によってメモリ占有領域を削減することができます。 ただし、このオプションは、最後の手段にする必要があります。

### <a name="texture-sizes"></a>テクスチャのサイズ

シナリオの種類によっては、テクスチャ データの量がメッシュ データのために使用されるメモリを上回る場合があります。 写真測量モデルにその可能性があります。
変換構成には、テクスチャを自動的にスケールダウンする方法は用意されていません。 必要であれば、クライアント側の処理前の手順として、テクスチャのスケーリングを実行する必要があります。 ただし、変換手順では、適切な[テクスチャ圧縮形式](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)の選択は実行されます。

* 不透明の色テクスチャでは `BC1`
* アルファ チャネル付きのソースの色テクスチャでは `BC7`

`BC7` 形式は `BC1` に比べてメモリ占有領域が 2 倍になるため、入力テクスチャで不必要にアルファ チャネルを提供しないようにすることが重要です。

## <a name="typical-use-cases"></a>一般的なユース ケース

特定のユース ケースに対して適切なインポート設定を見つけることは、面倒なプロセスである場合があります。 一方で、変換の設定は、実行時のパフォーマンスに大きな影響を与える可能性があります。

特定の最適化に適している、ユース ケースの特定のクラスがあります。 いくつかの例を次に示します。

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>ユース ケース:建築の視覚化、大規模な屋外マップ

* これらの種類のシーンは静的である傾向があり、移動可能なパーツは必要ありません。 そのため、`sceneGraphMode` を `static` または `none` に設定することもでき、実行時のパフォーマンスを向上させることができます。 `static` モードでは、シーンのルート ノードを移動、回転、拡大縮小できます。たとえば、1:1 のスケール (一人称視点の場合) とテーブル トップ ビューを動的に切り替えることができます。

* パーツを移動する必要がある場合は、通常、それらのパーツを最初の場所で選択できるように、レイキャストや他の[空間クエリ](../../overview/features/spatial-queries.md)をサポートする必要があることも意味します。 一方、何も移動する予定がない場合は、それを空間クエリに参加させる必要がない可能性も高いので、`generateCollisionMesh` フラグをオフにできます。 このスイッチは、変換時間、読み込み時間、および実行時のフレームごとの更新コストに大きな影響を与えます。

* アプリケーションで[切断面](../../overview/features/cut-planes.md)が使用されていない場合は、`opaqueMaterialDefaultSidedness` フラグをオフにする必要があります。 通常、パフォーマンスが 20% から 30% 向上します。 切断面は引き続き使用できますが、オブジェクトの内部を見ると背面がなく、直感に反します。 詳細については、「[:::no-loc text="single sided":::レンダリング](../../overview/features/single-sided-rendering.md)」を参照してください。

### <a name="use-case-photogrammetry-models"></a>ユース ケース:写真測量モデル

写真測量モデルをレンダリングする場合、通常はシーン グラフを必要としないため、`sceneGraphMode` を `none` に設定できます。 それらのモデルでは、最初から複雑なシーン グラフが含まれることはほとんどないため、このオプションの影響はあまりありません。

照明はテクスチャに既に組み込まれているため、動的な照明は必要ありません。 そのため、次のようになります。

* `unlitMaterials` フラグを `true` に設定して、すべての素材を照明なしの[カラー素材](../../overview/features/color-materials.md)に変換します。
* 頂点形式から不要なデータを削除します。 上の[例](#example)をご覧ください。

### <a name="use-case-visualization-of-compact-machines-etc"></a>ユース ケース:コンパクトなマシンの視覚化など

これらのユース ケースのモデルでは、多くの場合、小さいボリュームに非常に詳細な情報が含まれます。 レンダラーは、このようなケースの処理についても非常に最適化されています。 ただし、前のユース ケースで説明した最適化のほとんどは、ここでは適用されません。

* 個々のパーツは選択可能かつ移動可能でなければならないため、`sceneGraphMode` を `dynamic` のままにしておく必要があります。
* 通常、アプリケーションにはレイ キャストが不可欠であるため、衝突メッシュを生成する必要があります。
* `opaqueMaterialDefaultSidedness` フラグを有効にすると、切断面の見栄えがよくなります。

## <a name="deprecated-features"></a>非推奨の機能

モデル固有ではないファイル名 `conversionSettings.json` を使用して設定を指定することは、引き続きサポートされますが、非推奨となっています。
代わりに、モデル固有のファイル名 `<modelName>.ConversionSettings.json` を使用します。

変換設定ファイルでの[素材のオーバーライド ファイル](override-materials.md)を識別するための `material-override` 設定の使用は引き続きサポートされていますが、非推奨です。 代わりに、モデル固有のファイル名 `<modelName>.MaterialOverrides.json` を使用します。

## <a name="next-steps"></a>次のステップ

* [モデル変換](model-conversion.md)
* [色素材](../../overview/features/color-materials.md)
* [PBR 素材](../../overview/features/pbr-materials.md)
* [モデル変換中に素材をオーバーライドする](override-materials.md)