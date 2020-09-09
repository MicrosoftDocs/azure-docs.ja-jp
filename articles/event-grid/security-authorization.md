---
title: Azure Event Grid のセキュリティと認証
description: Azure Event Grid とその概念について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 09317b310a5934d27b82d265ec7f9b276135f882
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119091"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Event Grid リソースへのアクセスの承認
Azure Event Grid を使用すると、イベント サブスクリプションの一覧表示、新しいサブスクリプションの作成、キーの生成など、多様な管理操作を実行する各ユーザーに付与するアクセス レベルを制御できます。 Event Grid は、Azure のロール ベースのアクセス制御 (RBAC) を使います。


## <a name="operation-types"></a>操作の種類

Event Grid では、以下の操作がサポートされています。

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

最後の 3 つの操作は秘密情報を返す可能性がありますが、この情報は通常の読み取り操作からはフィルターで除外されます。 このような操作へのアクセスは制限することをお勧めします。 

## <a name="built-in-roles"></a>組み込みのロール

Event Grid には、イベント サブスクリプションを管理するための組み込みロールが 2 つ用意されています。 これらは、イベント ドメイン内のトピックをサブスクライブするために必要なアクセス許可をユーザーに提供するため、[イベント ドメイン](event-domains.md)を実装するときに重要です。 これらのロールはイベント サブスクリプションだけを対象としたものであり、トピックの作成などのアクションに対するアクセス権は付与されません。

[これらのロールはユーザーまたはグループに割り当てる](../role-based-access-control/quickstart-assign-role-user-portal.md)ことができます。

**EventGrid EventSubscription 共同作成者**: Event Grid のサブスクリプション操作を管理します

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription 閲覧者**: Event Grid のサブスクリプションを読みます

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>カスタム ロール

組み込みロールとは異なるアクセス許可を指定する必要がある場合は、カスタム ロールを作成できます。

さまざまなアクションの実行をユーザーに許可する Event Grid ロール定義の例を以下に示します。 これらのカスタム ロールは、イベント サブスクリプションより広範囲のアクセス権を付与するため、組み込みロールとは異なります。

**EventGridReadOnlyRole.json**:読み取り専用操作のみを許可します。

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**:制限付きの投稿アクションを許可しますが、削除アクションは禁止します。

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**:すべての Event Grid アクションを許可します。

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

カスタム ロールは、[PowerShell](../role-based-access-control/custom-roles-powershell.md)、[Azure CLI](../role-based-access-control/custom-roles-cli.md)、[REST API](../role-based-access-control/custom-roles-rest.md) で作成できます。



### <a name="encryption-at-rest"></a>保存時の暗号化

Event Grid サービスによってディスクに書き込まれるすべてのイベントまたはデータは、保存時に確実に暗号化されるように、Microsoft マネージド キーによって暗号化されます。 また、[Event Grid の再試行ポリシー](delivery-and-retry.md)に従って、イベントまたはデータが保持される最大期間は 24 時間です。 Event Grid では、24時間またはイベントの Time-To-Live のいずれか少ない方が経過すると、すべてのイベントまたはデータが自動的に削除されます。

## <a name="permissions-for-event-subscriptions"></a>イベント サブスクリプション用のアクセス許可
WebHook ではないイベント ハンドラー (イベント ハブ、キュー ストレージなど) を使用している場合は、そのリソースへの書き込みアクセスが必要です。 このアクセス許可のチェックにより、未認証のユーザーはリソースにイベントを送信できなくなります。

イベント ソースであるリソースに対する **Microsoft.EventGrid/EventSubscriptions/Write** アクセス許可を持っている必要があります。 リソースのスコープで新しいサブスクリプションを作成するため、このアクセス許可が必要です。 必要なリソースは、サブスクライブしているのがシステム トピックかカスタム トピックかによって異なります。 ここでは、この 2 つの種類について説明します。

### <a name="system-topics-azure-service-publishers"></a>システム トピック (Azure サービスの発行元)
システム トピックの場合、イベントを発行するリソースのスコープに新しいイベント サブスクリプションを書き込むアクセス許可が必要です。 リソースの形式は次のとおりです。`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

たとえば、**myacct** というストレージ アカウントでイベントにサブスクライブするには、Microsoft.EventGrid/EventSubscriptions/Write アクセス許可が必要です。`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>カスタム トピック
カスタム トピックの場合、Event Grid トピックのスコープに新しいイベント サブスクリプションを書き込むアクセス許可が必要です。 リソースの形式は次のとおりです。`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

たとえば、**mytopic** というカスタム トピックにサブスクライブするには、Microsoft.EventGrid/EventSubscriptions/Write アクセス許可が必要です。`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
