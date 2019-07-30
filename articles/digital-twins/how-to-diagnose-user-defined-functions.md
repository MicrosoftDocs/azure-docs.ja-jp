---
title: Azure Digital Twins で UDF をデバッグする方法 | Microsoft Docs
description: Azure Digital Twins で UDF をデバッグする方法に関するガイドライン。
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: stegaw
ms.custom: seodec18
ms.openlocfilehash: 577467a6322b7f6d3cd7f199d80963f2f1a98ed6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849327"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins でユーザー定義関数をデバッグする方法

この記事では、Azure Digital Twins でユーザー定義関数を診断してデバッグする方法について説明します。 次に、それらをデバッグするときの最も一般的なシナリオをいくつか紹介します。

>[!TIP]
> Activity Logs、Diagnostic Logs、Azure Monitor を使用し、Azure Digital Twins にデバッグ ツールを設定する方法の詳細については、[監視とログの構成](./how-to-configure-monitoring.md)方法に関するページを参照してください。

## <a name="debug-issues"></a>問題をデバッグする

Azure Digital Twins 内で問題を診断する方法がわかると、問題を効果的に分析し、問題の原因を特定し、それらに適切な解決策を講じることができるようになります。

そのために、さまざまなログ記録、分析、および診断ツールが用意されています。

### <a name="enable-logging-for-your-instance"></a>インスタンスのログ記録を有効にする

Azure Digital Twins は堅牢なログ記録、監視、分析をサポートします。 ソリューションの開発者は Azure Monitor ログ、診断ログ、アクティビティ ログ、その他のサービスを使用して、IoT アプリの監視の複雑なニーズをサポートします。 ログ記録オプションを組み合わせて使用することで、複数のサービス間でクエリを実行またはレコードを表示できるほか、数多くのサービスにわたって詳細なログを記録する機能を提供します。

* Azure Digital Twins 固有のログ構成については、[監視とログを構成する方法](./how-to-configure-monitoring.md)に関する記事を参照してください。
* Azure Monitor を使って実現できる強力なログ設定については、[Azure Monitor](../azure-monitor/overview.md) の概要を参照してください。
* Azure Portal、Azure CLI、または PowerShell を使用して Azure Digital Twins での診断ログ設定を構成するには、「[Azure リソースからのログ データの収集と使用](../azure-monitor/platform/diagnostic-logs-overview.md)」の記事を参照してください。

一度構成すると、すべてのログ カテゴリ、メトリックを選択し、強力な Azure Monitor ログ分析ワークスペースを使用してデバッグ作業を補助できるようになります。

### <a name="trace-sensor-telemetry"></a>センサー テレメトリのトレース

センサー テレメトリをトレースするには、自分の Azure Digital Twins インスタンスで診断設定が有効になっていることを確認します。 次に、必要なすべてのログ カテゴリが選択されていることを確認します。 最後に、必要なログが Azure Monitor ログに送信されていることを確認します。

センサー テレメトリのメッセージが対応するログと一致するように、送信されるイベント データに相関 ID を指定できます。 これを行うには、`x-ms-client-request-id` プロパティを GUID に設定します。

テレメトリを送信した後は、Log Analytics を開き、設定された相関 ID を使用してログに対してクエリを実行します。

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| クエリの値 | 置換後の文字列 |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | イベント データで指定した相関 ID |

ユーザー定義関数のログ記録を有効にした場合、それらのログがカテゴリ `UserDefinedFunction` で Log Analytics インスタンスに表示されます。 それらを取得するには、Log Analytics で、次のクエリ条件を入力します。

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

高性能なクエリ操作について詳しくは、[クエリの概要](../azure-monitor/log-query/get-started-queries.md)に関するページをご覧ください。

## <a name="identify-common-issues"></a>一般的な問題の識別

ソリューションに対してトラブルシューティングを実行するときは、一般的な問題の診断と識別の両方が重要です。 ユーザー定義関数を開発するときに発生するいくつかの一般的な問題を以下のサブセクションにまとめます。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>ロールの割り当てが作成されていることを確認する

Management API 内でロールの割り当てが作成されないと、ユーザー定義関数に通知の送信、メタデータの取得、トポロジ内での計算された値の設定などのアクションを実行するアクセス権限が付与されません。

次の Management API を使用してユーザー定義関数にロールの割り当てが存在することを確認してください。

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| パラメーター値 | 置換後の文字列 |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | ロールの割り当てを取得するユーザー定義関数の ID|

ロールが割り当てられていない場合、[ユーザー定義関数にロールの割り当てを作成する方法](./how-to-user-defined-functions.md)に関する記事をご覧ください。

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>センサー テレメトリでマッチャーが機能することを確認する

Azure Digital Twins インスタンスの Management API に対して次の呼び出しを実行すると、指定のマッチャーが指定のセンサーに適用されるかどうかを判断できます。

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | 評価するマッチャーの ID |
| *YOUR_SENSOR_IDENTIFIER* | 評価するセンサーの ID |

応答:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>トリガーされるセンサーを確認する

Azure Digital Twins の Management API に対して次の呼び出しを実行すると、指定のセンサーの受信テレメトリによってトリガーされるユーザー定義関数の識別子を判別できます。

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | テレメトリを送信するセンサーの ID |

応答:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>通知の受信に関する問題

トリガーされたユーザー定義関数から通知を受信していない場合は、トポロジのオブジェクト型パラメーターが使用されている識別子の型と一致していることを確認します。

**正しくない**例:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

このシナリオは、使用された識別子がセンサーを参照する一方で、指定されたトポロジのオブジェクト型が `Space` であるため発生します。

**正しい**例:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

この問題に直面しないための最も簡単な方法は、メタデータ オブジェクトに `Notify` メソッドを使用する方法です。

例:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>一般的な診断の例外

診断設定を有効にした場合は、これらの一般的な例外が発生する可能性があります。

1. **調整**: ユーザー定義関数が[サービスの制限](./concepts-service-limits.md)の記事で説明されている実行レート制限を超える場合、調整が行われます。 調整の制限が解除されるまで、それ以上操作を実行しても成功しません。

1. **データが見つからない**: ユーザー定義関数が存在しないメタデータにアクセスを試行すると、操作は失敗します。

1. **承認されていない**: ユーザー定義関数にロールの割り当てが設定されていない、またはトポロジから特定のメタデータにアクセスするための十分なアクセス許可がない場合、操作は失敗します。

## <a name="next-steps"></a>次の手順

- Azure Digital Twins で[監視とログ](./how-to-configure-monitoring.md)を有効にする方法について確認する。

- Azure のログ記録オプションの詳細については、[Azure アクティビティ ログの概要](../azure-monitor/platform/activity-logs-overview.md)の記事を参照してください。
