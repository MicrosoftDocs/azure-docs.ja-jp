---
title: 診断を設定する
titleSuffix: Azure Digital Twins
description: 診断設定を使用してログ記録を有効にする方法について確認します。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: fc397b6d6beb719e11dc3959bbcf4d75c08a8dda
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723930"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins のトラブルシューティング: 診断ログ

Azure Digital Twins では、リソースの状態に関する情報を提供する、サービス インスタンスの[メトリック](troubleshoot-metrics.md)が収集されます。 これらのメトリックを使用すると、Azure Digital Twins サービスやそれに接続されているリソースの全体的な正常性を評価できます。 これらのユーザー向けの統計情報は、Azure Digital Twins で何が起きているかを確認するのに役立ち、Azure サポートに連絡することなく問題の根本原因の分析を実行する助けとなります。

この記事では、Azure Digital Twins インスタンスからメトリック データの**診断ログ**を有効にする方法について説明します。 これらのログを使用して、サービスの問題をトラブルシューティングしたり、Azure Digital Twins のメトリックを異なる宛先に送信するように診断設定を構成したりできます。 これらの設定の詳細については、「[*プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する*](../azure-monitor/platform/diagnostic-settings.md)」を参照してください。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Azure portal 上で診断設定を有効にする

Azure Digital Twins インスタンスの診断設定を有効にする方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[診断設定]** 、 **[診断設定を追加する]** の順に選択します。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

3. 次のページで、以下の値を入力します。
     * **診断設定の名前**:診断設定の名前を付けます。
     * **カテゴリの詳細**:監視する操作を選択し、チェック ボックスをオンにしてそれらの操作の診断を有効にします。 診断設定では、次の操作をレポートできます。
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        これらのオプションの詳細については、下の「[*カテゴリの詳細*](#category-details)」を参照してください。
     * **宛先の詳細**:ログの送信先を選択します。 3 つのオプションを自由に組み合わせて選択できます。
        - Log Analytics への送信
        - ストレージ アカウントへのアーカイブ
        - イベント ハブへのストリーミング

        宛先の選択のために追加の詳細情報が必要な場合に、入力を求められることがあります。  
    
4. 新しい設定を保存します。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="診断設定ページを示すスクリーンショット。ここで、ユーザーは、診断設定の名前を入力し、[カテゴリの詳細] および [宛先の詳細] のチェックボックスをいくつか選択しています。[保存] ボタンが強調表示されています。":::

新しい設定は、10 分ほどで有効になります。 その後、インスタンスの **[診断設定]** ページ上の構成されたターゲットにログが表示されます。 

## <a name="category-details"></a>カテゴリの詳細

診断設定を構成するとき **[カテゴリの詳細]** で選択できるログ カテゴリの詳細を次に示します。

| ログのカテゴリ | 説明 |
| --- | --- |
| ADTModelsOperation | モデルに関連するすべての API 呼び出しをログに記録します |
| ADTQueryOperation | クエリに関連するすべての API 呼び出しをログに記録します |
| ADTEventRoutesOperation | イベント ルートに関連するすべての API 呼び出しに加え、Azure Digital Twins から Event Grid、Event Hubs、Service Bus などのエンドポイント サービスへのイベントの送信をログに記録します |
| ADTDigitalTwinsOperation | Azure Digital Twins に関連するすべての API 呼び出しをログに記録します |

各ログ カテゴリは、書き込み、読み取り、削除、およびアクションの操作で構成されます。  これらは、次のように REST API 呼び出しにマップされます。

| イベントの種類 | REST API の操作 |
| --- | --- |
| Write | PUT と PATCH |
| Read | GET |
| 削除 | DELETE |
| アクション | POST |

各カテゴリに記録される操作と対応する [Azure Digital Twins REST API 呼び出し](https://docs.microsoft.com/rest/api/azure-digitaltwins/)の一覧を次に示します。 

>[!NOTE]
> 各ログ カテゴリには、複数の操作/REST API 呼び出しが含まれています。 次の表では、各ログ カテゴリがすべての操作/REST API 呼び出しにマップされています (次のログ カテゴリが表示されるまで)。 

| ログのカテゴリ | 操作 | REST API 呼び出しとその他のイベント |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | デジタル ツイン モデル更新 API |
|  | Microsoft.DigitalTwins/models/read | デジタル ツイン モデルの ID による取得とリスト API |
|  | Microsoft.DigitalTwins/models/delete | デジタル ツイン モデルの削除 API |
|  | Microsoft.DigitalTwins/models/action | デジタル ツイン モデルの追加 API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | ツインのクエリ API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | イベント ルート追加 API |
|  | Microsoft.DigitalTwins/eventroutes/read | イベント ルートの ID による取得とリスト API |
|  | Microsoft.DigitalTwins/eventroutes/delete | イベント ルートの削除 API |
|  | Microsoft.DigitalTwins/eventroutes/action | エンドポイント サービスにイベントを発行しようとしてエラーが発生した (API 呼び出しではない) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Digital Twins の追加、リレーションシップの追加、更新、コンポーネントの更新 |
|  | Microsoft.DigitalTwins/digitaltwins/read | Digital Twins の ID による取得、コンポーネントの取得、ID によるリレーションシップの取得、受信リレーションシップのリスト、リレーションシップのリスト |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Digital Twins の削除、リレーションシップの削除 |
|  | Microsoft.DigitalTwins/digitaltwins/action | Digital Twins のコンポーネント テレメトリの送信、テレメトリの送信 |

## <a name="log-schemas"></a>ログ スキーマ 

各ログ カテゴリには、そのカテゴリ内のイベントの報告方法を定義するスキーマがあります。 個々のログ エントリはテキストとして保存され、形式は JSON BLOB となります。 ログおよび JSON 本文例内のフィールドは、以下の各種類のログに対して提供されます。 

`ADTDigitalTwinsOperation`、`ADTModelsOperation`、`ADTQueryOperation` では一貫した API ログ スキーマが使用されます。`ADTEventRoutesOperation` は独自に別のスキーマを備えています。

### <a name="api-log-schemas"></a>API ログ スキーマ

このログ スキーマは、`ADTDigitalTwinsOperation`、`ADTModelsOperation`、`ADTQueryOperation` において一貫しています。 これには、Azure Digital Twins インスタンスへの API 呼び出しに関連する情報が含まれています。

API ログのフィールドおよびプロパティの説明を次に示します。

| フィールド名 | データ型 | 説明 |
|-----|------|-------------|
| `Time` | DateTime | このイベントが発生した日時 (UTC) |
| `ResourceID` | String | イベントが発生したリソースの Azure Resource Manager リソース ID |
| `OperationName` | String  | イベント中に実行されるアクションの種類 |
| `OperationVersion` | String | イベント中に使用される API バージョン |
| `Category` | String | 出力されるリソースの種類 |
| `ResultType` | String | イベントの結果 |
| `ResultSignature` | String | イベントの HTTP ステータス コード |
| `ResultDescription` | String | イベントに関する追加情報 |
| `DurationMs` | String | イベントの実行にかかった時間 (ミリ秒) |
| `CallerIpAddress` | String | イベントのマスクされた発信元 IP アドレス |
| `CorrelationId` | Guid | 顧客が指定したイベントの一意識別子 |
| `Level` | String | イベントのログ重大度 |
| `Location` | String | イベントが発生したリージョン |
| `RequestUri` | Uri | イベント中に使用されるエンドポイント |

これらの種類のログの JSON 本文の例を次に示します。

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>エグレス ログ スキーマ

`ADTEventRoutesOperation` ログのスキーマは次のようになります。 これらには、例外に関する詳細と、Azure Digital Twins インスタンスに接続されたエグレス エンドポイントに関する API 操作の詳細が含まれます。

|フィールド名 | データ型 | 説明 |
|-----|------|-------------|
| `Time` | DateTime | このイベントが発生した日時 (UTC) |
| `ResourceId` | String | イベントが発生したリソースの Azure Resource Manager リソース ID |
| `OperationName` | String  | イベント中に実行されるアクションの種類 |
| `Category` | String | 出力されるリソースの種類 |
| `ResultDescription` | String | イベントに関する追加情報 |
| `Level` | String | イベントのログ重大度 |
| `Location` | String | イベントが発生したリージョン |
| `EndpointName` | String | Azure Digital Twins で作成されたエグレス エンドポイントの名前 |

これらの種類のログの JSON 本文の例を次に示します。

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>次のステップ

* 診断の構成に関する詳細については、"[*Azure リソースからのログ データの収集と使用*](../azure-monitor/platform/platform-logs-overview.md)" に関するページを参照してください。
* Azure Digital Twins のメトリックについては、[*トラブルシューティング: Azure Monitor でメトリックを表示する方法*](troubleshoot-metrics.md)に関するページを参照してください。
* メトリックのアラートを有効にする方法については、[*トラブルシューティング:アラートの設定*](troubleshoot-alerts.md)に関するページを参照してください。
