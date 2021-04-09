---
title: Azure Notification Hubs の診断ログ | Microsoft Docs
description: この記事では、Azure Notification Hubs で使用可能なすべての操作および診断ログの概要について説明します。
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736238"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Notification Hubs の診断ログを有効にする

Azure Notification Hubs 名前空間の使用を開始するときに、名前空間がいつ、どのように作成、削除、またはアクセスされるかを監視したい場合があります。 この記事では、使用可能なすべての操作と診断ログの概要について説明します。

Azure Notification Hubs では現在、Azure Notification Hubs 名前空間上で実行される "*管理操作*" をキャプチャするアクティビティおよび操作ログがサポートされています。

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ

すべてのログは、以下の 2 つの場所に JavaScript Object Notation (JSON) 形式で格納されます。

- **AzureActivity**:Azure portal 内で、または Azure Resource Manager テンプレートのデプロイを使用して、名前空間に対して実行された操作とアクションのログを表示します。
- **AzureDiagnostics**:API を使用して、または言語 SDK 上の管理クライアントを使用して、名前空間に対して実行された操作とアクションのログを表示します。

診断ログの JSON 文字列には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ------- | ------- |
| time | ログのタイムスタンプ (UTC) |
| resourceId | Azure リソースへの相対パス |
| operationName | 管理操作の名前 |
| category | ログのカテゴリ。 有効な値: `OperationalLogs` |
| callerIdentity | 管理操作を開始した呼び出し元の ID |
| resultType | 管理操作の状態。 有効な値: `Succeeded` または `Failed` |
| resultDescription | 管理操作の説明 |
| correlationId | 管理操作の関連付け ID (指定されている場合) |
| callerIpAddress | 呼び出し元の IP アドレス。 Azure portal から発生した呼び出しの場合は空 |

操作ログの JSON 文字列の例を次に示します。

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

`callerIdentity` フィールドは空にするか、次のいずれかの形式の JSON 文字列にすることができます。

Azure portal から発生した呼び出しの場合、`identity` フィールドは空になります。 このログをアクティビティ ログと関連付けて、ログインしているユーザーを特定できます。
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Azure Resource Manager から行われた呼び出しの場合、`identity` フィールドにはログインしているユーザーのユーザー名が含まれます。
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Notification Hubs の REST API への呼び出しの場合、`identity` フィールドには、SharedAccessSignature トークンの生成に使用されるアクセス ポリシーの名前が含まれます。
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>操作ログでキャプチャされるイベントと操作

操作ログでは、Azure Notification Hubs 名前空間上で実行されるすべての管理操作がキャプチャされます。 Azure Notification Hubs 上では大量のデータ操作が実行されるため、データ操作はキャプチャされません。

操作ログでは、以下の管理操作がキャプチャされます。 

| Scope | 操作の名前 | 操作の説明 |
| :-- | :-- | :-- |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 承認規則を一覧表示します |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 承認規則を削除します |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | キーを一覧表示します。 |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 承認規則を取得します |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | キーの再生成 |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 承認規則を作成または更新します |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/delete | 名前空間の削除 |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/read | 名前空間を取得します |
| 名前空間 | Microsoft.NotificationHubs/Namespaces/write | 名前空間を作成または更新します |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 承認規則を一覧表示します |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 承認規則を削除します |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | キーを一覧表示します。 |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 承認規則を読み取ります |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | キーの再生成 |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 承認規則を作成または更新します |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/delete | 通知ハブの削除 |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | PNS 資格情報を作成、更新、または取得します |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Notification Hub を取得します |
| Notification Hub | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Notification Hub を作成または更新します |

## <a name="enable-operational-logs"></a>操作ログを有効にする

操作ログは、既定では無効になっています。 ログを有効にするには、次のようにします。

1. [Azure portal](https://portal.azure.com) で、Azure Notification Hubs 名前空間に移動した後、 **[監視]** で **[診断設定]** を選択します。

   ![[診断設定] リンク](./media/notification-hubs-diagnostic-logs/image-1.png)

1. **[診断設定]** ウィンドウで、 **[診断設定を追加する]** を選択します。  

   ![[診断設定を追加する] リンク](./media/notification-hubs-diagnostic-logs/image-2.png)

1. 次の手順に従って、診断設定を構成します。

   a. **[名前]** ボックスに、診断設定の名前を入力します。  

   b. 診断ログの送信先として、以下の 3 つのいずれかを選択します。  
   - **[Send to Log Analytics workspace]\(Log Analytics ワークスペースへの送信\)** を選択する場合は、診断の送信先の Log Analytics インスタンスを指定する必要があります。  
   - **[ストレージ アカウントへのアーカイブ]** を選択する場合は、診断ログが格納されるストレージ アカウントを構成する必要があります。  
   - **[イベント ハブへのストリーム]** を選択する場合は、診断ログのストリーム先のイベント ハブを構成する必要があります。

   c. **[OperationalLogs]** チェック ボックスをオンにします。

    ![[診断設定] ウィンドウ](./media/notification-hubs-diagnostic-logs/image-3.png)

1. **[保存]** を選択します。

新しい設定は、10 分ほどで有効になります。 構成されたアーカイブ ターゲットの **[診断ログ]** ペインにログが表示されます。

## <a name="next-steps"></a>次のステップ

診断設定の構成の詳細については、次を参照してください。
* [Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)。

Azure Notification Hubs の詳細については、次を参照してください。
* [Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)