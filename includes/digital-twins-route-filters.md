---
author: baanders
description: Azure Digital Twins のルート フィルター オプションのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902202"
---
| フィルター名 | 説明 | フィルター テキスト スキーマ | サポート状況の値 | 
| --- | --- | --- | --- |
| True/False | フィルター処理なしでルートを作成したり、ルートを無効にしてイベントが送信されないようにしたりすることができます | `<true/false>` | `true` = ルートはフィルター処理なしで有効になります。 <br> `false` = ルートは無効になります。 |
| Type | デジタル ツイン インスタンスを通過する[イベントの種類](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 指定できるイベントの種類の値は次のとおりです。 <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| source | Azure Digital Twins インスタンスの名前 | `source = '<hostname>'`| 指定できるホスト名の値は次のとおりです。 <br> **通知の場合**: `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **テレメトリの場合**: `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| サブジェクト | 上記のイベント ソースのコンテキストでのイベントの説明 | `subject = '<subject>'` | 指定できるサブジェクトの値は次のとおりです。 <br>**通知の場合**: サブジェクトは、サブジェクトの `<twinid>` <br> または URI 形式です。これは、次のように複数の部分または ID で一意に識別されます。<br>`<twinid>/relationships/<relationshipid>`<br> **テレメトリの場合**: ツイン コンポーネントからテレメトリが生成された場合、サブジェクトはコンポーネント パス (例: `comp1.comp2`) です。 コンポーネントからテレメトリが生成されていない場合、サブジェクト フィールドは空になります。 |
| データ スキーマ | DTDL モデル ID | `dataschema = '<model-dtmi-ID>'` | **テレメトリの場合**: データ スキーマは、テレメトリを生成するツインまたはコンポーネントのモデル ID です。 たとえば、`dtmi:example:com:floor4;2` のように指定します。 <br>**通知の場合**: データ スキーマはサポートされていません。|
| Content type | データ値のコンテンツ タイプ | `datacontenttype = '<contentType>'` | コンテンツ タイプは`application/json` です。 |
| 仕様バージョン | 使用しているイベント スキーマのバージョン | `specversion = '<version>'` | バージョンは `1.0` である必要があります。 これは、CloudEvents スキーマ バージョン 1.0 を示しています。 |

次の操作を使用してフィルターを組み合わせることもできます。

| フィルター名 | フィルター テキスト スキーマ | 例 | 
| --- | --- | --- |
| AND/OR | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| AND/OR | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| 入れ子になった操作 | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> プレビュー期間中は、文字列の等価性 (=、!=) のみがサポートされます。

フィルターを実装または更新したときに、変更がデータ パイプラインに反映されるまでに数分かかることがあります。
