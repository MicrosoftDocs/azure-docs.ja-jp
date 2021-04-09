---
title: Azure Maps Drawing Conversion のエラーと警告
description: Azure Maps Conversion サービスの使用中に発生する可能性がある変換エラーと警告について説明します。 エラーと警告の解決方法に関する推奨事項と、いくつかの例を確認してください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906218"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Drawing Conversion のエラーと警告

[Azure Maps Conversion サービス](/rest/api/maps/conversion)を使用すると、アップロードした Drawing パッケージをマップ データに変換できます。 Drawing パッケージは、[Drawing パッケージの要件](drawing-requirements.md)に従っている必要があります。 1 つ以上の要件が満たされていない場合、Conversion サービスからエラーまたは警告が返されます。 この記事では、変換のエラーと警告コードと、その解決方法の推奨事項を示します。 また、Conversion サービスからこれらのコードが返される可能性がある図面の例もいくつか示します。

変換の警告が発生しても、Conversion サービスは成功します。 ただし、すべての警告を確認して解決することをお勧めします。 警告は、変換の一部が無視されたか、自動的に修正されたことを意味します。 警告を解決しないと、後のプロセスでエラーが発生する可能性があります。

## <a name="general-warnings"></a>一般的な警告

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*geometryWarning の説明*

**geometryWarning** は、図面に無効なエンティティが含まれている場合に発生します。 無効なエンティティは、幾何拘束に準拠していないエンティティです。 無効なエンティティの例として、自己交差する多角形、または閉じたジオメトリのみをサポートするレイヤー内の閉じていないポリラインがあります。

Conversion サービスでは、無効なエンティティからマップ地物が作成されず、無視されます。

#### <a name="examples-for-geometrywarning"></a>*geometryWarning の例*

* 以下の 2 つの画像は、自己交差する多角形の例を示しています。

     ![自己交差する多角形の例 1](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![自己交差する多角形の例 2](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* 閉じていないポリラインを示す画像を次に示します。 レイヤーが閉じたジオメトリのみをサポートしているとします。

    ![閉じていないポリラインの例](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*geometryWarning を修正する方法*

各エンティティの **geometryWarning** を調べて、幾何拘束に従っていることを確認します。

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer の説明*

**unexpectedGeometryInLayer** 警告は、特定のレイヤーに想定されているジオメトリの種類と互換性のないジオメトリが図面に含まれている場合に発生します。 Conversion サービスから **unexpectedGeometryInLayer** 警告が返された場合、そのジオメトリは無視されます。

#### <a name="example-for-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer の例*

次の画像は、閉じていないポリラインを示しています。 レイヤーが閉じたジオメトリのみをサポートしているとします。

![閉じていないポリラインの例](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer を修正する方法*

各 **unexpectedGeometryInLayer** 警告を調べて、互換性のないジオメトリを互換性のあるレイヤーに移動します。 他のどのレイヤーとも互換性がない場合は、削除する必要があります。

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation の説明*

**unsupportedFeatureRepresentation** 警告は、サポートされていないエンティティの種類が図面に含まれている場合に発生します。

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation の例*

次の画像は、ラベル レイヤー上の複数行のテキスト オブジェクトとしてサポートされていないエンティティの種類を示しています。
  
![ラベル レイヤー上の複数行テキスト オブジェクトの例](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation を修正する方法*

DWG ファイルにサポートされているエンティティの種類のみが含まれていることを確認します。 サポートされている種類は、[「Drawing パッケージの要件」の記事の Drawing ファイルの要件に関するセクション](drawing-requirements.md#drawing-package-requirements)に記載されています。

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*automaticRepairPerformed の説明*

**automaticRepairPerformed** 警告は、無効なジオメトリが Conversion サービスによって自動的に修復されたときに発生します。

#### <a name="examples-for-automaticrepairperformed"></a>*automaticRepairPerformed の例*

* 次の画像は、自己交差する多角形が、Conversion サービスによって有効なジオメトリにどのように修復されるかを示しています。

    ![自己交差する多角形の修復例](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* 次の画像は、閉じていないポリラインの最初と最後の頂点が Conversion サービスによってどのようにスナップされ、閉じたポリラインが作成されるかを示しています。最初と最後の頂点の間隔は 1 mm 未満でした。  

    ![スナップされたポリラインの例](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* 次の画像は、閉じたポリラインのみをサポートするレイヤーで、複数の閉じていないポリラインが Conversion サービスによってどのように修復されるかを示しています。 閉じていないポリラインが破棄されないように、サービスによって 1 つの閉じたポリラインに結合されました。

    ![閉じていないポリラインが 1 つの閉じたポリラインに結合された例](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*automaticRepairPerformed を修正する方法*

**automaticRepairPerformed** 警告を修正するには、次の操作を実行します。

1. 各警告のジオメトリと具体的な警告テキストを確認します。
2. 自動修復が正しいかどうかを確認します。
3. 修復が正しい場合は、続行します。 それ以外の場合は、設計ファイルにアクセスし、警告を手動で解決します。

>[!TIP]
>この警告が今後表示されないようにするには、元の図面を変更して、元の図面が修復された図面と一致するようにします。

## <a name="manifest-warnings"></a>マニフェストの警告

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*redundantAttribution の説明*

**redundantAttribution** 警告は、冗長な、または競合するオブジェクト プロパティがマニフェストに含まれている場合に発生します。

#### <a name="examples-for-redundantattribution"></a>*redundantAttribution の例*

* 次の JSON スニペットには、同じ `name` を持つ 2 つ以上の `unitProperties` オブジェクトが含まれています。

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* 次の JSON スニペットでは、2 つ以上の `zoneProperties` オブジェクトが同じ `name` を持っています。

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*redundantAttribution を修正する方法*

**redundantAttribution* 警告を修正するには、冗長な、または競合するオブジェクト プロパティを削除します。

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*manifestWarning の説明*

**manifestWarning** は、変換中に使用されていない unitProperties または zoneProperties オブジェクトがマニフェストに含まれている場合に発生します。

#### <a name="examples-for-manifestwarning"></a>*manifestWarning の例*

* マニフェストには、`unitLabel` レイヤーに一致するラベルがない `unitName` の `unitProperties` オブジェクトが含まれています。

* マニフェストには、`zoneLabel` レイヤーに一致するラベルがない `zoneName` の `zoneProperties` オブジェクトが含まれています。

#### <a name="how-to-fix-manifestwarning"></a>*manifestWarning を修正する方法*

**manifestWarning** を修正するには、未使用の `unitProperties` または `zoneProperties` オブジェクトをマニフェストから削除するか、ユニットまたはゾーン ラベルを図面に追加して、変換中にプロパティ オブジェクトが使用されるようにします。

## <a name="wall-warnings"></a>壁の警告

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*wallOutsideLevel の説明*

**wallOutsideLevel** 警告は、レベル アウトラインの境界の外側にある壁ジオメトリが図面に含まれている場合に発生します。

#### <a name="example-for-walloutsidelevel"></a>*wallOutsideLevel の例*

* 次の画像は、黄色のレベル境界の外側に内壁 (赤色) があることを示しています。

    ![レベル境界の外側にある内壁の例](./media/drawing-conversion-error-codes/wall-outside-level.png)

* 次の画像は、黄色のレベル境界の外側に外壁 (赤色) があることを示しています。

    ![レベル境界の外側にある外壁の例](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*wallOutsideLevel を修正する方法*

**wallOutsideLevel** 警告を修正するには、レベル ジオメトリを展開してすべての壁を含めます。 または、レベル境界内に収まるように壁の境界を変更します。

## <a name="unit-warnings"></a>ユニットの警告

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*unitOutsideLevel の説明*

**unitOutsideLevel** 警告は、レベル アウトラインの境界の外側にあるユニット ジオメトリが図面に含まれている場合に発生します。

#### <a name="example-for-unitoutsidelevel"></a>*unitOutsideLevel の例*

 次の画像では、ユニット ジオメトリ (赤色) が黄色のレベル境界の境界を越えています。

 ![レベル境界を越えるユニットの例](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*unitOutsideLevel を修正する方法*

**unitOutsideLevel** 警告を修正するには、レベル境界を展開してすべてのユニットを含めます。 または、レベル境界内に収まるようにユニットのジオメトリを変更します。

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*partiallyOverlappingUnit の説明*

**partiallyOverlappingUnit** 警告は、別のユニット ジオメトリと一部が重なり合うユニット ジオメトリが図面に含まれている場合に発生します。 重なり合うユニットは Conversion サービスによって破棄されます。

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*partiallyOverlappingUnit のシナリオ例*

次の画像では、重なり合うユニットが赤色で強調表示されています。 `UNIT110` と `HALLWAY` は破棄されます。

![重なり合うユニットの例](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*partiallyOverlappingUnit を修正する方法*

**partiallyOverlappingUnit** 警告を修正するには、一部が重なり合う各ユニットを描画し直し、他のユニットと重ならないようにします。

## <a name="door-warnings"></a>ドアの警告

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*doorOutsideLevel の説明*

**doorOutsideLevel** 警告は、レベル ジオメトリの境界の外側にあるドア ジオメトリが図面に含まれている場合に発生します。

#### <a name="example-for-dooroutsidelevel"></a>*doorOutsideLevel の例*

次の画像では、赤色で強調表示されているドア ジオメトリが黄色のレベル境界に重なっています。

![レベル境界に重なっているドアの例](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*doorOutsideLevel を修正する方法*

**doorOutsideLevel** 警告を修正するには、ドア ジオメトリを描画し直して、レベルの境界内に収まるようにします。

## <a name="zone-warnings"></a>ゾーンの警告

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*zoneWarning の説明*

**zoneWarning** は、ゾーンにラベルが含まれていない場合に発生します。 ラベルではないゾーンは、Conversion サービスによって破棄されます。

#### <a name="example-for-zonewarning"></a>*zoneWarning の例*

次の画像は、ラベルが含まれていないゾーンを示しています。

![ゾーンにラベルが含まれていない例](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*zoneWarning を修正する方法*

**zoneWarning** を修正するには、各ゾーンに 1 つのラベルがあることを確認します。

## <a name="label-warnings"></a>ラベルの警告

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*labelWarning の説明*

**labelWarning** は、あいまいな、または矛盾するラベル地物が図面に含まれている場合に発生します。

**labelWarning** は、次の理由の 1 つ以上が原因で発生します。

* どのユニットにもユニット ラベルがない。
* どのゾーンにもゾーン ラベルがない。
* 2 つ以上のゾーンにゾーン ラベルがある。

#### <a name="example-for-labelwarning"></a>*labelWarning の例*

次の画像は、2 つのゾーン内にあるラベルを示しています。

![2 つのゾーン内のラベルの例 ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*labelWarning を修正する方法*

**labelWarning** を修正するには、次のことを確認します。

* すべてのユニット ラベルがユニット内にあります。
* すべてのゾーン ラベルがゾーン内にあります。
* すべてのゾーン ラベルは 1 つのゾーン内にのみあります。

## <a name="drawing-package-errors"></a>Drawing パッケージのエラー

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*invalidArchiveFormat の説明*

**invalidArchiveFormat** エラーは、図面パッケージが GZIP や 7-Zip などの無効なアーカイブ形式である場合に発生します。 ZIP アーカイブ形式のみがサポートされています。

**invalidArchiveFormat** エラーは、ZIP アーカイブが空の場合にも発生します。

#### <a name="how-to-fix-invalidarchiveformat"></a>*invalidArchiveFormat を修正する方法*

**invalidArchiveFormat** エラーを修正するには、次のことを確認します。

* アーカイブ ファイル名の末尾が _.zip_ です。
* ZIP アーカイブにデータが含まれています。
* ZIP アーカイブを開くことができます。

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*invalidUserData の説明*

**invalidUserData** エラーは、Conversion サービスでストレージからユーザー データ オブジェクトを読み取ることができない場合に発生します。

#### <a name="example-scenario-for-invaliduserdata"></a>*invalidUserData のシナリオ例*

正しくない `udid` パラメーターを使用して Drawing パッケージをアップロードしようとしました。

#### <a name="how-to-fix-invaliduserdata"></a>*invalidUserData を修正する方法*

**invalidUserData** エラーを修正するには、次のことを確認します。

* アップロードされたパッケージに正しい `udid` を指定しています。
* Drawing パッケージのアップロードに使用した Azure Maps アカウントで Azure Maps Creator (プレビュー) が有効になっている。
* Conversion サービスに対する API 要求には、Drawing パッケージのアップロードに使用した Azure Maps アカウントのサブスクリプション キーが含まれています。

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*dwgError の説明*

**dwgError** は、アップロードされた ZIP アーカイブ内の Drawing パッケージに、DWG ファイルに関する問題が 1 つ以上含まれている場合に発生します。

**dwgError** は、無効な、または破損しているために開けない DWG ファイルが図面パッケージに含まれている場合に発生します。

* DWG ファイルは、有効な AutoCAD DWG ファイル形式の図面ではありません。
* DWG ファイルは破損しています。
* DWG ファイルは _manifest.json_ ファイルに列挙されていますが、ZIP アーカイブに含まれていません。

#### <a name="how-to-fix-dwgerror"></a>*dwgError を修正する方法*

**dwgError** を修正するには、_manifest.json_ ファイルを調べて次のことを確認します。

* ZIP アーカイブ内のすべての DWG ファイルは有効な AutoCAD DWG 形式の図面です。1 つずつ AutoCAD で開きます。 すべての無効な図面を削除するか修正します。
* _manifest.json_ 内の DWG ファイルの一覧は、ZIP アーカイブの DWG ファイルと一致します。

## <a name="manifest-errors"></a>マニフェストのエラー

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>invalidJsonFormat の説明

**invalidJsonFormat** エラーは、_manifest.json_ ファイルを読み取れない場合に発生します。

JSON の形式または構文エラーのため、_manifest.json_file を読み取ることができません。 JSON の形式と構文の詳細については、「[The JavaScript Object Notation (JSON) Data Interchange Format (JavaScript Object Notation (JSON) データ交換形式)](https://tools.ietf.org/html/rfc7159)」を参照してください。

#### <a name="how-to-fix-invalidjsonformat"></a>*invalidJsonFormat を修正する方法*

**invalidJsonFormat** エラーを修正するには、JSON リンターを使用して JSON エラーを検出し、解決します。

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*missingRequiredField の説明*

**missingRequiredField** エラーは、_manifest.json_ ファイルに必要なデータが不足している場合に発生します。

#### <a name="how-to-fix-missingrequiredfield"></a>*missingRequiredField を修正する方法*

**missingRequiredField** エラーを修正するには、マニフェストに必要なプロパティがすべて含まれていることを確認します。 必要なマニフェスト オブジェクトの完全な一覧については、[「Drawing パッケージの要件」のマニフェストに関するセクション](drawing-requirements.md#manifest-file-requirements)を参照してください  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*missingManifest の説明*

**missingManifest** エラーは、_manifest.json_ ファイルが ZIP アーカイブに存在しない場合に発生します。

**missingManifest** エラーは、次の理由の 1 つ以上が原因で発生します。

* _manifest.json_ ファイルのスペルが間違っています。
* _manifest.json_ がありません。
* _manifest.json_ が ZIP アーカイブのルート ディレクトリにありません。

#### <a name="how-to-fix-missingmanifest"></a>*missingManifest を修正する方法*

**missingManifest** エラーを修正するには、ZIP アーカイブのルート レベルにアーカイブに _manifest.json_ という名前のファイルがあることを確認します。

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*conflict の説明*

**conflict** エラーは、_manifest.json_ ファイルに競合する情報が含まれている場合に発生します。

#### <a name="example-scenario-for-conflict"></a>*conflict のシナリオ例*

同じレベルの ordinal で複数のレベルが定義されている場合、Conversion サービスから **conflict** エラーが返されます。 次の JSON スニペットは、同じ ordinal で定義された 2 つのレベルを示しています。

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*conflict を修正する方法*

**conflict** エラーを修正するには、_manifest.json_ を調べて競合する情報を削除します。

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*invalidGeoreference の説明*

**invalidGeoreference** エラーは、_manifest.json_ ファイルに無効なジオリファレンスが含まれている場合に発生します。

**invalidGeoreference** エラーは、次の理由の 1 つ以上が原因で発生します。

* ユーザーが範囲外の緯度値または経度値をジオリファレンスしています。
* ユーザーが範囲外の回転値をジオリファレンスしています。

#### <a name="example-scenario-for-invalidgeoreference"></a>*invalidGeoreference のシナリオ例*

次の JSON スニペットでは、緯度が上限を超えています。

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*invalidGeoreference を修正する方法*

**invalidGeoreference** エラーを修正するには、ジオリファレンスされた値が範囲内にあることを確認します。

>[!IMPORTANT]
>GeoJSON では、座標は経度、緯度の順です。 正しい順序を使用しないと、範囲外の緯度値または経度値を誤って参照する可能性があります。

## <a name="wall-errors"></a>壁のエラー

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*wallError の説明*

**wallError** は、壁地物を作成しようとしたときに図面にエラーが含まれている場合に発生します。

#### <a name="example-scenario-for-wallerror"></a>*wallError のシナリオ例*

次の画像は、どのユニットにも重なっていない壁地物を示しています。

![どのユニットにも重なっていない壁地物の例](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*wallError を修正する方法*

**wallError** エラーを修正するには、少なくとも 1 つのユニットと重なるように壁を描画し直します。 または、壁と重なる新しいユニットを作成します。

## <a name="vertical-penetration-errors"></a>垂直貫入のエラー

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*verticalPenetrationError の説明*

**verticalPenetrationError** は、曖昧な垂直貫入地物が図面に含まれている場合に発生します。

**verticalPenetrationError** は、次の理由の 1 つ以上が原因で発生します。

* 図面に垂直貫入領域が含まれており、その上または下のレベルに垂直貫入領域が重なっていません。
* 図面パッケージには 2 つ以上の垂直貫入地物を含むレベルがあり、その両方が、そのすぐ上またはすぐ下の別のレベルにある 1 つの垂直貫入地物と重なっています。

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*verticalPenetrationError のシナリオ例*

次の画像は、上のレベルまたは下のレベルに重なっている垂直貫入領域がない垂直貫入領域を示しています。

![垂直貫入の例 1](./media/drawing-conversion-error-codes/vrt-2.png)

次の画像は、隣接するレベル上の複数の垂直貫入領域と重なる垂直貫入領域を示しています。

![垂直貫入の例 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>verticalPenetrationError を修正する方法

**verticalPenetrationError** エラーを修正するには、「[Drawing パッケージの要件](drawing-requirements.md)」の記事の垂直貫入地物の使用方法を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Maps Drawing Error Visualizer を使用する方法](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [室内マッピング用の Creator (プレビュー)](creator-indoor-maps.md)