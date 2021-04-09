---
title: 変換に関する情報を取得する
description: 変換に関する情報を取得する
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91576627"
---
# <a name="get-information-about-conversions"></a>変換に関する情報を取得する

## <a name="information-about-a-conversion-the-result-file"></a>変換に関する情報: 結果ファイル

変換サービスは、資産を変換するときに、問題の概要を "結果ファイル" に書き込みます。 たとえば、ファイル `buggy.gltf` が変換される場合、出力コンテナーには `buggy.result.json` というファイルが格納されます。

結果ファイルには、変換中に発生したエラーと警告が一覧表示され、`succeeded`、`failed`、または `succeeded with warnings` という結果の概要が示されます。
結果ファイルは、オブジェクトの JSON 配列として構成されます。各オブジェクトには、`warning`、`error`、`internal warning`、`internal error`、`result` のいずれかの文字列プロパティが含まれます。 多くても 1 つのエラー (`error` または `internal error`) が発生し、`result` は常に 1 つ存在します。

## <a name="example-result-file"></a>"*結果*" ファイルの例

次の例では、arrAsset が正常に生成された変換について説明します。 ただし、テクスチャが不足していたため、結果として得られる arrAsset は意図したとおりではない可能性があります。

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>変換されたモデルに関する情報: info ファイル

変換サービスによって生成された arrAsset ファイルは、レンダリング サービスによる使用のみを目的としています。 ただし、レンダリング セッションを開始せずにモデルに関する情報にアクセスしたい場合があります。 このワークフローをサポートするために、変換サービスによって、arrAsset ファイルとは別に JSON ファイルが出力コンテナー内に配置されます。 たとえば、ファイル `buggy.gltf` が変換される場合、出力コンテナーには、変換された資産 `buggy.arrAsset` とは別に `buggy.info.json` というファイルが格納されます。 これには、ソース モデル、変換されたモデル、および変換自体に関する情報が含まれています。

## <a name="example-info-file"></a>*info* ファイルの例

次に、`buggy.gltf` というファイルを変換して生成される *info* ファイルの例を示します。

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>info ファイル内の情報

### <a name="the-files-section"></a>*files* セクション

このセクションには、指定されたファイル名が含まれています。

* `input`:ソース ファイルの名前。
* `output`:ユーザーが既定以外の名前を指定した場合の出力ファイルの名前。

### <a name="the-conversionsettings-section"></a>*conversionSettings* セクション

このセクションには、モデルが変換されたときに指定された [ConversionSettings](configure-model-conversion.md#settings-file) のコピーが保持されます。

### <a name="the-inputinfo-section"></a>*inputInfo* セクション

このセクションには、ソース ファイル形式に関する情報が記録されます。

* `sourceAssetExtension`:ソース ファイルのファイル拡張子。
* `sourceAssetFormat`:ソース ファイル形式の説明。
* `sourceAssetFormatVersion`:ソース ファイル形式のバージョン。
* `sourceAssetGenerator`:ソース ファイルを生成したツールの名前 (使用可能な場合)。

### <a name="the-materialoverrides-section"></a>*materialOverrides* セクション

このセクションには、変換サービスに素材のオーバーライド ファイルが提供された場合の[素材のオーバーライド](override-materials.md)に関する情報が示されます。
これには次の情報が含まれています。
* `numOverrides`:素材のオーバーライド ファイルから読み取られたオーバーライド エントリの数。
* `numOverriddenMaterials`:オーバーライドされた素材の数。

### <a name="the-inputstatistics-section"></a>*inputStatistics* セクション

このセクションでは、ソース シーンに関する情報が提供されます。 多くの場合、このセクションの値と、ソース モデルを作成したツールの同等の値には相違点があります。 エクスポートと変換の手順中にモデルは変更されるため、このような違いは想定されます。

* `numMeshes`:各パーツが 1 つの素材を参照できるメッシュ パーツの数。
* `numFaces`:モデル全体の "_三角形_" の合計数。 変換中はメッシュが三角形になることに注意してください。 この数値は、[標準レンダリング サーバー サイズ](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)の多角形の制限に寄与します。
* `numVertices`:モデル全体の頂点の合計数。
* `numMaterial`:モデル全体の素材の合計数。
* `numFacesSmallestMesh`:モデルの最小メッシュの三角形の数。
* `numFacesBiggestMesh`:モデルの最大メッシュの三角形の数。
* `numNodes`:モデルのシーン グラフ内のノードの数。
* `numMeshUsagesInScene`:ノードからメッシュが参照される回数。 複数のノードから同じメッシュが参照される場合があります。
* `maxNodeDepth`:シーン グラフ内のノードの最大深度。

### <a name="the-outputinfo-section"></a>*outputInfo* セクション

このセクションには、生成された出力に関する一般的な情報が記録されます。

* `conversionToolVersion`:モデル コンバーターのバージョン。
* `conversionHash`:レンダリングに寄与できる arrAsset 内のデータのハッシュ。 変換サービスを同じファイルに対して再実行したときに、異なる結果が生成されたかどうかを把握するために使用できます。

### <a name="the-outputstatistics-section"></a>*outputStatistics* セクション

このセクションには、変換された資産から計算された情報が記録されます。

* `numMeshPartsCreated`:arrAsset 内のメッシュの数。 インスタンス化は変換プロセスの影響を受けるため、`inputStatistics` セクションの `numMeshes` とは異なる場合があります。
* `numMeshPartsInstanced`:arrAsset で再利用されるメッシュの数。
* `recenteringOffset`:[ConversionSettings](configure-model-conversion.md) の `recenterToOrigin` オプションが有効な場合、この値は、変換されたモデルを元の位置に戻す変換です。
* `boundingBox`:モデルの境界。

## <a name="deprecated-features"></a>非推奨の機能

変換サービスによって、ファイル `stdout.txt` およびファイル `stderr.txt` が出力コンテナーに書き込まれます。これらが、警告とエラーの唯一のソースとなっていました。
現在これらのファイルは、非推奨となっています。 このため、代わりに[結果ファイル](#information-about-a-conversion-the-result-file)を使用してください。

## <a name="next-steps"></a>次のステップ

* [モデル変換](model-conversion.md)
* [モデルの変換を構成する](configure-model-conversion.md)
