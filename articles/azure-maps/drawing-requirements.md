---
title: Azure Maps Creator の Drawing パッケージの要件
description: Azure Maps Conversion サービスを使用して施設のデザイン ファイルをマップ データに変換するための Drawing パッケージの要件について説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: af7238ca4229bac678061c742f13953299a96ba4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290023"
---
# <a name="drawing-package-requirements"></a>Drawing パッケージの要件

[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)を使用すると、アップロードした Drawing パッケージをマップ データに変換できます。 この記事では、Conversion API の Drawing パッケージの要件について説明します。 サンプル パッケージを表示するには、サンプルの [Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をダウンロードします。

## <a name="prerequisites"></a>前提条件

この Drawing パッケージには、DWG 形式で保存された図面が含まれています。これは、Autodesk の AutoCAD® ソフトウェアのネイティブ ファイル形式であり、[Autodesk,Inc の商標](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12)です。

任意の CAD ソフトウェアを選択して、Drawing パッケージで図面を作成することができます。  

[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)を使用すると、Drawing パッケージをマップ データに変換できます。  Conversion サービスは、AutoCAD DWG ファイル形式を使用して開発およびテストされています。 `AC1032` は DWG ファイルの内部形式のバージョンです。 内部 DWG ファイル形式のバージョンとして `AC1032` を選択することをお勧めします。  

このドキュメント内で使用されている用語の用語集です。

| 期間  | 定義 |
|:-------|:------------|
| レイヤー | AutoCAD DWG レイヤー。|
| Level | 設定した高度にある建物の領域。 たとえば、ビルのフロアなどです。 |
| Xref  |外部参照としてプライマリ図面にアタッチされた AutoCAD DWG ファイル形式 (.dwg) のファイル。  |
| 特徴量 | ジオメトリに追加のメタデータ情報を組み合わせたオブジェクト。 |
| 地物クラス | 地物の一般的なブループリント。 たとえば、ユニットは地物クラスであり、オフィスは地物です。 |

## <a name="drawing-package-structure"></a>Drawing パッケージの構造

Drawing パッケージは、次のファイルを含む .zip アーカイブです。

* AutoCAD DWG ファイル形式の DWG ファイル。
* 1 つの施設の _manifest.json_ ファイル。

DWG ファイルはフォルダー内で任意の方法で整理できますが、マニフェスト ファイルはフォルダーのルート ディレクトリに配置する必要があります。 フォルダーは、拡張子が .zip の 1 つのアーカイブ ファイルに圧縮する必要があります。 次のセクションでは、DWG ファイル、マニフェスト ファイル、およびこれらのファイルのコンテンツの要件について詳しく説明します。  

## <a name="dwg-files-requirements"></a>DWG ファイルの要件

施設のレベルごとに 1 つの DWG ファイルが必要です。 そのレベルのデータは、1 つの DWG ファイルに含まれている必要があります。 外部参照 (_xrefs_) は、親図面にバインドする必要があります。 さらに、各 DWG ファイルには次の要件があります。

* _Exterior_ レイヤーと _Unit_ レイヤーを定義する必要があります。 必要に応じて、次の省略可能なレイヤーを定義することもできます。_Wall_、_Door_、_UnitLabel_、_Zone_、および _ZoneLabel_。
* 複数のレベルの地物を含めることはできません。
* 複数の施設の地物を含めることはできません。

[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)を使用すると、DWG ファイルから次の地物クラスを抽出できます。

* Levels
* Units
* ゾーン
* 開口部
* 壁
* 垂直貫入

すべての変換ジョブの結果として、部屋、構造、壁、開口部、ドア、ゾーン、施設などの既定のカテゴリの最小セットが生成されます。 オブジェクトから参照されるカテゴリ名ごとに、追加のカテゴリがあります。  

DWG レイヤーには、1 クラスの地物が含まれている必要があります。 複数のクラスで 1 つのレイヤーを共有しないでください。 たとえば、ユニットと壁でレイヤーを共有することはできません。

DWG レイヤーは、次の条件にも従う必要があります。

* すべての DWG ファイルの図面の原点は、同じ緯度と経度に合わせる必要があります。
* 各レベルは、他のレベルと同じ向きにする必要があります。
* 自己交差する多角形は自動的に修復され、[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)から警告が生成されます。 修復された結果は期待される結果と一致しない可能性があるため、手動で検査することをお勧めします。

すべてのレイヤー エンティティは、次のいずれかの種類である必要があります。線、ポリライン、多角形、円弧、円、テキスト (単一行)。 その他のエンティティの種類は無視されます。

各レイヤーでサポートされているエンティティの種類とサポートされている地物の概要を次の表に示します。 レイヤーにサポートされていないエンティティの種類が含まれている場合、[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)ではそのエンティティは無視されます。  

| レイヤー | エンティティの種類 | 特徴 |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | 多角形、ポリライン (閉)、円 | Levels
| [単位](#unit-layer) |  多角形、ポリライン (閉)、円 | 垂直貫入、ユニット
| [Wall](#wall-layer)  | 多角形、ポリライン (閉)、円 | 適用不可。 詳細については、「[Wall レイヤー](#wall-layer)」を参照してください。
| [Door](#door-layer) | 多角形、ポリライン、線、円弧、円 | 開口部
| [ゾーン](#zone-layer) | 多角形、ポリライン (閉)、円 | ゾーン
| [UnitLabel](#unitlabel-layer) | テキスト (単一行) | 適用不可。 このレイヤーでは、Unit レイヤーのユニット地物にのみプロパティを追加できます。 詳細については、「[UnitLabel レイヤー](#unitlabel-layer)」を参照してください。
| [ZoneLabel](#zonelabel-layer) | テキスト (単一行) | 適用不可。 このレイヤーでは、ZonesLayer のゾーン地物にのみプロパティを追加できます。 詳細については、「[ZoneLabel レイヤー](#zonelabel-layer)」を参照してください。

以下のセクションでは、各レイヤーの要件について詳しく説明します。

### <a name="exterior-layer"></a>Exterior レイヤー

各レベルの DWG ファイルには、そのレベルの境界を定義するレイヤーが含まれている必要があります。 このレイヤーは Exterior レイヤーと呼ばれます。 たとえば、施設に 2 つのレベルが含まれている場合、施設には 2 つの DWG ファイルが必要であり、各ファイルには Exterior レイヤーがあります。

Exterior レイヤーにあるエンティティ図面の数に関係なく、[最終的な施設データセット](tutorial-creator-indoor-maps.md#create-a-feature-stateset)には、各 DWG ファイルの **1** レベルの地物のみが含まれます。 追加として:

* 外装は、多角形、ポリライン (閉)、円として描画する必要があります。

* 外装は重ねることができますが、1 つのジオメトリに分解されます。

レイヤーに複数の重なり合うポリラインが含まれている場合、ポリラインは 1 つのレベル地物に分解されます。 または、レイヤーに複数の重ならないポリラインが含まれている場合、結果のレベル地物は複数の多角形で表現されます。

Exterior レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の OUTLINE レイヤーとして確認することができます。

### <a name="unit-layer"></a>Unit レイヤー

各レベルの DWG ファイルでは、ユニットを含むレイヤーを定義する必要があります。  ユニットは、オフィス、廊下、階段、エレベーターなど、建物内の移動可能なスペースです。 Unit レイヤーは次の要件に従う必要があります。

* ユニットは、多角形、ポリライン (閉)、円として描画する必要があります。
* ユニットは、施設の外装の境界内に収まっている必要があります。
* ユニットの一部を重ねることはできません。
* ユニットに自己交差するジオメトリを含めることはできません。

 _UnitLabel_ レイヤーにテキスト オブジェクトを作成してユニットに名前を付け、ユニットの境界内にオブジェクトを配置します。 詳細については、「[UnitLabel レイヤー](#unitlabel-layer)」を参照してください。

Unit レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の UNITS レイヤーとして確認することができます。

### <a name="wall-layer"></a>Wall レイヤー

各レベルの DWG ファイルには、壁、柱、その他の建築構造の物理的な範囲を定義するレイヤーを含めることができます。

* 壁は、多角形、ポリライン (閉)、円として描画する必要があります。
* Wall レイヤーには、建築構造として解釈されるジオメトリのみを含める必要があります。

Wall レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の WALLS レイヤーとして確認することができます。

### <a name="door-layer"></a>Door レイヤー

ドアを含む DWG レイヤーを含めることができます。 各ドアは、Unit レイヤーのユニットの端と重なる必要があります。

Azure Maps データセットのドアの開口部は、複数のユニットの境界と重なる単一の線分として表されます。 Door レイヤーのジオメトリをデータセットの開口部地物に変換するには、次の手順を実行します。

![開口部を生成する手順](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zone レイヤー

各レベルの DWG ファイルには、ゾーンの物理的な範囲を定義する Zone レイヤーを含めることができます。 Zone は、屋内の空のスペースまたはバック ヤードの場合があります。

* ゾーンは、多角形、ポリライン (閉)、円として描画する必要があります。
* ゾーンは重ねることができます。
* ゾーンは、施設の外装の内側または外側に配置することができます。

_ZoneLabel_ レイヤーにテキスト オブジェクトを作成し、Zone の境界内にテキスト オブジェクトを配置して、Zone に名前を付けます。 詳細については、「[ZoneLabel レイヤー](#zonelabel-layer)」を参照してください。

Zone レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の ZONES レイヤーとして確認することができます。

### <a name="unitlabel-layer"></a>UnitLabel レイヤー

各レベルの DWG ファイルには、ユニット ラベル レイヤーを含めることができます。 ユニット ラベル レイヤーでは、Unit レイヤーから抽出されたユニットに name プロパティを追加します。 name プロパティを持つユニットには、マニフェスト ファイルで追加の詳細を指定できます。

* ユニット ラベルは単一行のテキスト エンティティである必要があります。
* ユニット ラベルは、ユニットの境界内に収まる必要があります。
* ユニット ラベル レイヤーに複数のテキスト エンティティを含めることはできません。

UnitLabel レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の UNITLABELS レイヤーとして確認することができます。

### <a name="zonelabel-layer"></a>ZoneLabel レイヤー

各レベルの DWG ファイルには、ゾーン ラベル レイヤーを含めることができます。 このレイヤーでは、Zone レイヤーから抽出されたゾーンに name プロパティを追加します。 name プロパティを持つゾーンには、マニフェスト ファイルで追加の詳細を指定できます。

* ゾーン ラベルは単一行のテキスト エンティティである必要があります。
* ゾーン ラベルは、ユニットの境界内に収まる必要があります。
* ゾーン ラベル レイヤーのゾーンに複数のテキスト エンティティを含めることはできません。

Zonelabel レイヤーの例は、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)の ZONELABELS レイヤーとして確認することができます。

## <a name="manifest-file-requirements"></a>マニフェスト ファイルの要件

zip フォルダーには、ディレクトリのルート レベルにマニフェスト ファイルが格納されている必要があります。また、ファイル名は **manifest.json** にする必要があります。 これには、[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)でコンテンツを解析できる DWG ファイルについて記述されています。 マニフェストに指定されたファイルのみが取り込まれます。 zip フォルダー内にあっても、マニフェストに適切に登録されていないファイルは無視されます。

マニフェスト ファイルの **buildingLevels** オブジェクト内のファイル パスは、zip フォルダーのルートからの相対パスにする必要があります。 DWG ファイル名は、施設レベルの名前と正確に一致している必要があります。 たとえば、"Basement" レベルの DWG ファイルは "Basement.dwg" にします。 level 2 の DWG ファイルは "level_2.dwg" です。 レベル名にスペースが含まれている場合は、アンダースコアを使用します。

マニフェスト オブジェクトの使用には要件がありますが、すべてのオブジェクトが必要なわけではありません。 [Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion) バージョン 1.1 の必須オブジェクトと省略可能なオブジェクトを次に示します。

| Object | 必須 | 説明 |
| :----- | :------- | :------- |
| version | true |マニフェスト スキーマのバージョン。 現時点では、バージョン 1.1 のみがサポートされています。|
| directoryInfo | true | 施設の地理情報と連絡先情報の概要を示します。 また、居住者の地理情報と連絡先情報の概要を示すためにも使用できます。 |
| buildingLevels | true | 建物のレベルと、レベルの設計を含むファイルを指定します。 |
| georeference | true | 施設の図面の数値的な地理情報が含まれています。 |
| dwgLayers | true | レイヤーの名前が列挙されています。各レイヤーにはその地物の名前が列挙されています。 |
| unitProperties | false | ユニット地物の追加のメタデータを挿入するために使用できます。 |
| zoneProperties | false | ゾーン地物の追加のメタデータを挿入するために使用できます。 |

以下のセクションでは、各オブジェクトの要件について詳しく説明します。

### <a name="directoryinfo"></a>directoryInfo

| プロパティ  | type | 必須 | 説明 |
|-----------|------|----------|-------------|
| name      | string | true   |  建物の名前。 |
| streetAddress|    string |    false    | 建物の住所。 |
|unit     | string    |  false    |  建物内のユニット。 |
| locality |    string |    false |    エリア、近隣、または地域の名前。 たとえば、"オーバーレイク" や "中心地区" などです。 locality は郵送先住所の一部ではありません。 |
| adminDivisions |    文字列の JSON 配列 |    false     | 住所表記 (国、州、市区町村) または (国、都道府県、市区町村、町) を含む配列。 ISO 3166 の国番号と ISO 3166-2 の州または領域番号を使用します。 |
| postalCode |    string    | false    | 郵便物の仕分用番号。 |
| hoursOfOperation |    string |     false | [OSM の Opening Hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) 形式に準拠しています。 |
| phone    | string |    false |    建物に関連付けられている電話番号。 国番号を含める必要があります。 |
| Web サイト (website)    | string |    false    | 建物に関連付けられている Web サイト。 先頭に http または https を付ける必要があります。 |
| nonPublic |    [bool]    | false | 建物が一般公開されているかどうかを指定するフラグ。 |
| anchorLatitude | numeric |    false | 施設アンカー (画鋲) の緯度。 |
| anchorLongitude | numeric |    false | 施設アンカー (画鋲) の経度。 |
| anchorHeightAboveSeaLevel  | numeric | false | 施設の地表レベルを基準とする 1 階の高さ (メートル単位)。 |
| defaultLevelVerticalExtent | numeric | false | レベルの `verticalExtent` が定義されていない場合に使用する、この施設のレベルの既定の高さ (厚さ)。 |

### <a name="buildinglevels"></a>buildingLevels

`buildingLevels` オブジェクトには、建物レベルの JSON 配列が含まれています。

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|levelName    |string    |true |    わかりやすいレベル名。 次に例を示します。フロア 1、ロビー、ブルー パーキング、地下など。|
|ordinal | 整数 (integer) |    true | ordinal は、レベルの垂直順序を決定するために使用されます。 すべての施設には、ordinal 0 のレベルが必要です。 |
|heightAboveFacilityAnchor | numeric | false |    アンカーより上のレベルの高さ (メートル単位)。 |
| verticalExtent | numeric | false | レベルの床から天井までの高さ (厚さ) (メートル単位)。 |
|filename |    string |    true |    建物レベルの CAD 図面のファイル システム パス。 建物の zip ファイルのルートを基準とした相対パスにする必要があります。 |

### <a name="georeference"></a>georeference

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|lat    | numeric |    true |    施設図面の原点の緯度を表す 10 進数表現。 起点の座標は WGS84 Web メルカトル (`EPSG:3857`) 内にある必要があります。|
|lon    |numeric|    true|    施設図面の原点の経度を表す 10 進数表現。 起点の座標は WGS84 Web メルカトル (`EPSG:3857`) 内にある必要があります。 |
|angle|    numeric|    true|   真北と図面の垂直 (Y) 軸との間の時計回りの角度 (度単位)。   |

### <a name="dwglayers"></a>dwgLayers

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|exterior    |文字列の配列|    true|    外装の建物プロファイルを定義するレイヤーの名前。|
|unit|    文字列の配列|    true|    ユニットを定義するレイヤーの名前。|
|wall|    文字列の配列    |false|    壁を定義するレイヤーの名前。|
|door    |文字列の配列|    false   | ドアを定義するレイヤーの名前。|
|unitLabel    |文字列の配列|    false    |ユニットの名前を定義するレイヤーの名前。|
|ゾーン | 文字列の配列    | false    | ゾーンを定義するレイヤーの名前。|
|zoneLabel | 文字列の配列 |     false |    ゾーンの名前を定義するレイヤーの名前。|

### <a name="unitproperties"></a>unitProperties

`unitProperties` オブジェクトには、ユニットのプロパティの JSON 配列が含まれています。

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|unitName    |string    |true    |この `unitProperty` レコードに関連付けるユニットの名前。 このレコードは、`unitName` と一致するラベルが `unitLabel` レイヤーで見つかった場合にのみ有効です。 |
|categoryName|    string|    false    |カテゴリ名。 カテゴリの完全な一覧については、[categories](https://aka.ms/pa-indoor-spacecategories) を参照してください。 |
|navigableBy| 文字列の配列 |    false    |ユニットを横断することができる移動エージェントの種類を示します。 たとえば、"歩道" などです。 このプロパティを使用して、経路探索機能に通知します。  使用できる値は、`pedestrian`、`wheelchair`、`machine`、`bicycle`、`automobile`、`hiredAuto`、`bus`、`railcar`、`emergency`、`ferry`、`boat`、および `disallowed` です。|
|routeThroughBehavior|    string|    false    |ユニットのルート経由動作。 使用できる値は、`disallowed`、`allowed`、および `preferred` です。 既定値は `allowed` です。|
|occupants    |directoryInfo オブジェクトの配列 |false    |ユニットの occupants の一覧。 |
|nameAlt|    string|    false|    ユニットの代替名。 |
|nameSubtitle|    string    |false|    ユニットのサブタイトル。 |
|addressRoomNumber|    string|    false|    ユニットの部屋、ユニット、アパートメント、またはスイートの番号。|
|verticalPenetrationCategory|    string|    false| このプロパティが定義されている場合、結果の地物はユニットではなく垂直貫入 (VRT) になります。 VRT を使用すると、その上のレベルまたは下のレベルにある他の VRT 地物に移動できます。 垂直貫入は[カテゴリ](https://aka.ms/pa-indoor-spacecategories)名です。 このプロパティが定義されている場合、categoryName プロパティは verticalPenetrationCategory でオーバーライドされます。 |
|verticalPenetrationDirection|    string|    false    |`verticalPenetrationCategory` が定義されている場合は、必要に応じて有効な移動方向を定義します。 使用できる値は、`lowToHigh`、`highToLow`、`both`、および `closed` です。 既定値は `both` です。|
| nonPublic | [bool] | false | ユニットが一般公開されているかどうかを示します。 |
| isRoutable | [bool] | false | `false` に設定されている場合は、ユニットを移動したり、経由したりすることはできません。 既定値は `true` です。 |
| isOpenArea | [bool] | false | ユニットに開口部をアタッチすることなく、移動エージェントはユニットに入ることができます。 既定では、この値は開口部がないユニットでは `true` に設定され、開口部があるユニットでは `false` に設定されています。  開口部がないユニットで `isOpenArea` を手動で `false` に設定すると、警告が出されます。 これは、結果として得られるユニットに移動エージェントが到達できないためです。|

### <a name="the-zoneproperties-object"></a>zoneProperties オブジェクト

`zoneProperties` オブジェクトには、ゾーンのプロパティの JSON 配列が含まれています。

| プロパティ  | Type | 必須 | 説明 |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |`zoneProperty` レコードに関連付けるゾーンの名前。 このレコードは、`zoneName` と一致するラベルがゾーンの `zoneLabel` レイヤーで見つかった場合にのみ有効です。  |
|categoryName|    string|    false    |カテゴリ名。 カテゴリの完全な一覧については、[categories](https://aka.ms/pa-indoor-spacecategories) を参照してください。 |
|zoneNameAlt|    string|    false    |ゾーンの代替名。  |
|zoneNameSubtitle|    string |    false    |ゾーンのサブタイトル。 |
|zoneSetId|    string |    false    | 複数のゾーン間の関係を確立して、それらがグループとしてクエリまたは選択できるようにするためのセット ID。 たとえば、複数のレベルにまたがるゾーンです。 |

### <a name="sample-drawing-package-manifest"></a>サンプル Drawing パッケージ マニフェスト

サンプル Drawing パッケージのサンプル マニフェスト ファイルを次に示します。 パッケージ全体をダウンロードするには、[サンプル Drawing パッケージ](https://github.com/Azure-Samples/am-creator-indoor-data-examples)をクリックしてください。

#### <a name="manifest-file"></a>マニフェスト ファイル

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

Drawing パッケージが要件を満たしたら、[Azure Maps Conversion サービス](https://docs.microsoft.com/rest/api/maps/conversion)を使用してパッケージをマップ データセットに変換することができます。 次に、そのデータセットを使用し、屋内マップ モジュールを使用して屋内マップを生成できます。 屋内マップ モジュールの詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
>[屋内マップ用の Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [チュートリアル:Creator 屋内マップを作成する](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [屋内マップの動的スタイル設定](indoor-map-dynamic-styling.md)
