---
title: モデル変換中に素材をオーバーライドする
description: 変換時に素材をオーバーライドするワークフローについて説明します
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 2e9cb216c100f1732230a90572284bd3f8462584
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433141"
---
# <a name="override-materials-during-model-conversion"></a>モデル変換中に素材をオーバーライドする

レンダラーに使用される [PBR 素材](../../overview/features/pbr-materials.md)を定義するために、ソース モデルの素材設定が使用されます。
場合によっては、[既定の変換](../../reference/material-mapping.md)では目的の結果が得られず、変更を加える必要があります。
Azure Remote Rendering で使用するためにモデルを変換する場合、素材のオーバーライド ファイルを用意し、素材ごとに素材変換を行う方法をカスタマイズできます。
[モデル変換の構成](configure-model-conversion.md)に関するセクションでは、素材のオーバーライド ファイル名を宣言する手順が説明されています。

## <a name="the-override-file-used-during-conversion"></a>変換中に使用されるオーバーライド ファイル

単純な例として、ボックス モデルに "既定値" という 1 つの素材があるとします。
さらに、ARR で使用するために albedo の色を調整する必要があるとします。
この場合、`box_materials_override.json` ファイルは次のように作成できます。

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

`box_materials_override.json` ファイルが入力コンテナーに配置され、`box.fbx` とは別に `box.ConversionSettings.json` が追加されます。これにより、オーバーライド ファイルの位置が変換に指示されます ([モデル変換の構成](configure-model-conversion.md)に関するページを参照してください)。

```json
{
    "material-override" : "box_materials_override.json"
}
```

モデルが変換されると、新しい設定が適用されます。

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