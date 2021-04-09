---
title: モデル変換中に素材をオーバーライドする
description: 変換時に素材をオーバーライドするワークフローについて説明します
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91576644"
---
# <a name="override-materials-during-model-conversion"></a>モデル変換中に素材をオーバーライドする

レンダラーに使用される [PBR 素材](../../overview/features/pbr-materials.md)を定義するために、ソース モデルの素材設定が使用されます。
場合によっては、[既定の変換](../../reference/material-mapping.md)では目的の結果が得られず、変更を加える必要があります。
Azure Remote Rendering で使用するためにモデルを変換する場合は、素材のオーバーライド ファイルを用意して、素材変換を行う方法を素材ごとにカスタマイズできます。
入力コンテナーに、入力モデル `<modelName>.<ext>` と共に `<modelName>.MaterialOverrides.json` という名前のファイルがある場合は、それが素材のオーバーライド ファイルとして使用されます。

## <a name="the-override-file-used-during-conversion"></a>変換中に使用されるオーバーライド ファイル

単純な例として、ボックス モデルに "既定値" という 1 つの素材があるとします。
さらに、ARR で使用するために albedo の色を調整する必要があるとします。
この場合、`box.MaterialOverrides.json` ファイルは次のように作成できます。

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

`box.MaterialOverrides.json` ファイルを `box.fbx` と共に入力コンテナーに配置することで、変換サービスに新しい設定を適用するように指示します。

### <a name="color-materials"></a>色素材

[色素材](../../overview/features/color-materials.md)モデルは、照明に関係のない常に影の付いたサーフェスを表します。
色素材は、たとえば写真測量アルゴリズムによって作成された資産に役立ちます。
素材のオーバーライド ファイルでは、`unlit` を `true` に設定することで、素材を色素材として宣言できます。

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>特定のテクスチャ マップを無視する

場合によっては、変換プロセスで特定のテクスチャ マップを無視することがあります。 これは、レンダラーで正しく認識できない特殊なマップを生成するツールによって、モデルが生成された場合に該当する可能性があります。 たとえば、不透明度以外の何かを定義するために使用される "OpacityMap" や、"NormalMap" が "BumpMap" として格納されているモデルなどです (後者の場合は、"NormalMap" を無視します。これにより、コンバーターには "BumpMap" が "NormalMap" として使用されます)。

原理は単純です。 `ignoreTextureMaps` というプロパティを追加し、無視するテクスチャ マップを追加するだけです。

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

無視できるテクスチャ マップの詳細な一覧については、以下の JSON スキーマを参照してください。

### <a name="applying-the-same-overrides-to-multiple-materials"></a>複数の素材に同じオーバーライドを適用する

既定では、素材のオーバーライド ファイルにあるエントリの名前が素材名と完全に一致した場合にそのエントリが適用されます。
同じオーバーライドを複数の素材に適用することがよくあるため、必要に応じて正規表現をエントリ名として指定できます。
フィールド `nameMatching` の既定値は `exact` ですが、`regex` に設定することで、一致するすべての素材にそのエントリが適用されるようになります。
使用する構文は、JavaScript で使用する構文と同じです。 次の例に示すオーバーライドは、"Material2"、"Material01"、"Material999" などの名前の素材に適用されます。

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

1 つの素材に適用される素材のオーバーライド ファイルのエントリは 1 つだけです。
素材名に完全一致が存在する場合 (つまり `nameMatching` がないか `exact` に等しい場合) は、そのエントリが選択されます。
それ以外の場合は、素材名と一致するファイル内の最初の正規表現エントリが選択されます。

### <a name="getting-information-about-which-entries-applied"></a>適用されたエントリに関する情報を取得する

出力コンテナーに書き込まれる [info ファイル](get-information.md#information-about-a-converted-model-the-info-file)には、指定されたオーバーライドの数とオーバーライドされた素材の数に関する情報が記録されています。

## <a name="json-schema"></a>JSON スキーマ

素材ファイルの完全な JSON スキーマを以下に示します。 `unlit` と `ignoreTextureMaps` を例外として、使用できるプロパティは、[色素材](../../overview/features/color-materials.md)モデルと [PBR 素材](../../overview/features/pbr-materials.md)モデルのセクションで説明されているプロパティの一部です。

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>次のステップ

* [色素材](../../overview/features/color-materials.md)
* [PBR 素材](../../overview/features/pbr-materials.md)
