---
author: baanders
description: Azure Digital Twins のルート フィルター オプションのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98859870"
---
| フィルター名 | 説明 | フィルター テキスト スキーマ | サポート状況の値 | 
| --- | --- | --- | --- |
| True/False | フィルター処理なしでルートを作成したり、ルートを無効にしてイベントが送信されないようにしたりすることができます | `<true/false>` | `true` = ルートはフィルター処理なしで有効になります。 <br> `false` = ルートは無効になります。 |
| Type | デジタル ツイン インスタンスを通過する[イベントの種類](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | 指定できるイベントの種類の値は次のとおりです。 <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| source | Azure Digital Twins インスタンスの名前 | `source = '<hostname>'`| 指定できるホスト名の値は次のとおりです。 <br> **通知の場合**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **テレメトリの場合**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| サブジェクト | 上記のイベント ソースのコンテキストでのイベントの説明 | `subject = '<subject>'` | 指定できるサブジェクトの値は次のとおりです。 <br>**通知の場合**: サブジェクトは、サブジェクトの `<twinid>` <br> または URI 形式です。これは、次のように複数の部分または ID で一意に識別されます。<br>`<twinid>/relationships/<relationshipid>`<br> **テレメトリの場合**: ツイン コンポーネントからテレメトリが生成された場合、サブジェクトはコンポーネント パス (例: `comp1.comp2`) です。 コンポーネントからテレメトリが生成されていない場合、サブジェクト フィールドは空になります。 |
| データ スキーマ | DTDL モデル ID | `dataschema = '<model-dtmi-ID>'` | **テレメトリの場合**: データ スキーマは、テレメトリを生成するツインまたはコンポーネントのモデル ID です。 たとえば、`dtmi:example:com:floor4;2` のように指定します。 <br>**通知の場合 (作成/削除)** :データ スキーマには、`$body.$metadata.$model` の通知本文でアクセスできます。 <br>**通知の場合 (更新)** :データ スキーマには、`$body.modelId` の通知本文でアクセスできます|
| Content type | データ値のコンテンツ タイプ | `datacontenttype = '<contentType>'` | コンテンツ タイプは`application/json` です。 |
| 仕様バージョン | 使用しているイベント スキーマのバージョン | `specversion = '<version>'` | バージョンは `1.0` である必要があります。 これは、CloudEvents スキーマ バージョン 1.0 を示しています。 |
| 通知本文 | 通知の `data` フィールドの任意のプロパティを参照します | `$body.<property>` | 「[*イベント データについて*](../articles/digital-twins/how-to-interpret-event-data.md)」で通知の例を参照してください。 `data` フィールドの任意のプロパティは、`$body` を使用して参照できます

上記のデータへの参照によって返される値としては、次のデータ型がサポートされています。

| データの種類 | 例 |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**整数**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

ルート フィルターを定義するときは、次の演算子がサポートされます。

|ファミリ|オペレーター|例|
|-|-|-|
|論理|AND、OR、( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|比較|<、<=、>、>=、=、!=|`$body.temperature <= 5.5`

ルート フィルターを定義するときは、次の関数がサポートされます。

|機能|説明|例|
|--|--|--|
|STARTS_WITH(x,y)|値 `x` が文字列 `y` で始まっている場合、true を返します。|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | 値 `x` が文字列 `y` で終わっている場合、true を返します。|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 値 `x` に文字列 `y` が含まれる場合、true を返します。|`CONTAINS(subject, '<twinID>')`|

フィルターを実装または更新したときに、変更がデータ パイプラインに反映されるまでに数分かかることがあります。