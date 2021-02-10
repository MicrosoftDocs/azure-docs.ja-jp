---
title: Event Grid ソースとしての Azure Cache for Redis
description: Azure Event Grid の Azure Cache for Redis イベントに対して用意されているプロパティについて説明します
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056931"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Event Grid ソースとしての Azure Cache for Redis

この記事では、Azure Cache for Redis のイベントのプロパティとスキーマについて説明します。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 

## <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Azure Cache for Redis REST API のイベントの一覧

これらのイベントは、クライアントで Azure Cache for Redis REST API を呼び出してエクスポート、インポート、またはスケーリングを行うとトリガーされます。 ファイルの部分置換イベントは Redis 更新プログラムによってトリガーされます。

 |イベント名 |説明|
 |----------|-----------|
 |**Microsoft.Cache.ExportRDBCompleted** |キャッシュ データがエクスポートされるとトリガーされます。 |
 |**Microsoft.Cache.ImportRDBCompleted** |キャッシュ データがインポートされるとトリガーされます。 |
 |**Microsoft.Cache.PatchingCompleted** |ファイルの部分置換が完了するとトリガーされます。 |
 |**Microsoft.Cache.ScalingCompleted** |スケーリングが完了するとトリガーされます。 |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>イベント応答の内容

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。

このセクションには、各 Azure Cache for Redis イベントでそのデータがどのように見えるかの例が含まれています。

### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft.Cache.PatchingCompleted イベント

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft.Cache.ImportRDBCompleted イベント

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft.Cache.ExportRDBCompleted イベント

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft.Cache.ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| topic | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | object | Azure Cache for Redis イベント データ。 |
| dataVersion | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| timestamp | string | イベントが発生した時刻。 |
| name | string | イベントの名前です。 |
| status | string | イベントの状態。 失敗または成功。 |


## <a name="quickstarts"></a>クイックスタート

Azure Cache for Redis のイベントを試す場合は、次のクイックスタート記事のいずれかを参照してください。

|使うツール:    |参照する記事: |
|--|-|
|Azure portal    |[クイックスタート:Azure portal を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[クイックスタート:PowerShell を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md) (英語)|
|Azure CLI    |[クイックスタート:Azure CLI を使用して Azure Cache for Redis のイベントを Web エンドポイントにルーティングする](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md) (英語)|

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。

