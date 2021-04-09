---
title: イベント スキーマ - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge のイベント スキーマ。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171569"
---
# <a name="event-schemas"></a>イベント スキーマ

Event Grid モジュールは、JSON 形式でイベントを受け入れて配信します。 現在、Event Grid でサポートされているスキーマには、次の 3 つがあります。

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

トピックの作成中に発行元が準拠する必要があるスキーマを構成できます。 指定しない場合、既定で **EventGridSchema** が使用されます。 予期されるスキーマに準拠していないイベントは拒否されます。

サブスクライバーは、イベントを配信するスキーマを構成することもできます。 指定しない場合、既定でトピックのスキーマが使用されます。
現在、サブスクライバー配信スキーマは、そのトピックの入力スキーマと一致している必要があります。 

## <a name="eventgrid-schema"></a>EventGrid スキーマ

EventGrid スキーマは、発行エンティティが準拠する必要のある一連の必須プロパティで構成されています。 各発行元は、最上位レベルのフィールドに入力する必要があります。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid スキーマのプロパティ

すべてのイベントには、次の同じ最上位レベルのデータが含まれています。

| プロパティ | Type | 必須 | 説明 |
| -------- | ---- | ----------- |-----------
| topic | string | いいえ | 発行されるトピックと一致している必要があります。 指定しない場合、Event Grid によって、発行されるトピックの名前が設定されます。 |
| subject | string | はい | 発行元が定義したイベントの対象のパス。 |
| eventType | string | はい | このイベント ソースのイベントの種類 (たとえば、BlobCreated)。 |
| eventTime | string | はい | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | いいえ | イベントの一意識別子。 |
| data | object | いいえ | 発行エンティティに固有のイベント データをキャプチャするために使用されます。 |
| dataVersion | string | はい | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | いいえ | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

### <a name="example--eventgrid-schema-event"></a>例 - EventGrid スキーマ イベント

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent スキーマ

カスタム スキーマには、EventGrid スキーマのように強制される必須のプロパティはありません。 発行エンティティは、イベント スキーマを完全に制御できます。 最大限の柔軟性が提供され、イベントベースのシステムが既に配置済みのときに既存のイベントを再利用したいシナリオや特定のスキーマに関連付けたくないシナリオにも対応できます。

### <a name="custom-schema-properties"></a>カスタム スキーマのプロパティ

必須のプロパティはありません。 ペイロードを決定するのは発行エンティティです。

### <a name="example--custom-schema-event"></a>例 - カスタム スキーマのイベント

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent スキーマ

Azure Event Grid では、上記のスキーマに加えて、[CloudEvents JSON スキーマ](https://github.com/cloudevents/spec/blob/master/json-format.md)内のイベントをネイティブ サポートしています。 CloudEvents は、イベント データを記述するためのオープンな仕様です。 これを使用すると、イベントを発行したり使用したりするための共通のイベント スキーマを提供して、相互運用性を簡略化することができます。 これは [CNCF](https://www.cncf.io/) の一部であり、現在使用可能なバージョンは 1.0-rc1 です。

### <a name="cloudevent-schema-properties"></a>CloudEvents スキーマのプロパティ

必須のエンベロープ プロパティについては、[CloudEvents の仕様](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)を参照してください。

### <a name="example--cloud-event"></a>例 - クラウド イベント
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
