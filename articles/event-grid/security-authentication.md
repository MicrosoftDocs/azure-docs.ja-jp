---
title: "Azure Event Grid のセキュリティと認証"
description: "Azure Event Grid とその概念について説明します。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/14/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ccef224ef1c2919a3e5469c1bbe0980c6963705b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---

# <a name="event-grid-security-and-authentication"></a>Event Grid のセキュリティと認証 

Azure Event Grid には、3 種類の認証があります。

* イベントのサブスクリプション
* イベントの発行
* webhook のイベント配信

## <a name="webhook-event-delivery"></a>webHook のイベント配信

webhook は、Azure Event Grid からリアルタイムでイベントを受信する多数ある方法の 1 つです。

新しいイベントを配信できる状態になるたびに、Event Grid から、本文にイベントが含まれる HTTP 要求が webhook に送信されます。

Event Grid に独自の webhook エンドポイントを登録すると、エンドポイントの所有権を証明するために、単純な検証コードを含む POST 要求が送信されます。 アプリからは検証コードをエコーで応答する必要があります。 Event Grid は、検証に合格していない webhook イベントにイベントを配信しません。
 
### <a name="validation-details"></a>検証の詳細:

* イベント サブスクリプションの作成時または更新時に、Event Grid は "SubscriptionValidationEvent" イベントをターゲット エンドポイントに投稿します。
* このイベントには、ヘッダー値 "Event-Type: Validation" が含まれています。
* イベント本文のスキーマは、他の Event Grid イベントと同じです。
* イベント データには、ランダムに生成された次のような文字列が指定された "ValidationCode" プロパティが含まれています。"ValidationCode: acb13…"。

SubscriptionValidationEvent の例を次に示します。
```json
[{
  "Id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "Topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "Subject": "",
  "Data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "EventType": "Microsoft.EventGrid/SubscriptionValidationEvent",
  "EventTime": "2017-08-06T22:09:30.740323Z"
}]
```

エンドポイントの所有権を証明するために、検証コード ("validation_response: acb13…" など) をエコーで返します。例を次に示します。

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

エンドポイントの所有権を証明するために、検証コード ("ValidationResponse: acb13…" など) をエコーで返します。

最後に、Azure Event Grid がサポートするのは HTTPS webhook エンドポイントのみであることに中尉してください。
## <a name="event-subscription"></a>イベント サブスクリプション

イベントにサブスクライブするには、必要なリソースに **Microsoft.EventGrid/EventSubscriptions/Write** アクセス許可を持っている必要があります。 リソースのスコープで新しいサブスクリプションを作成するため、このアクセス許可が必要です。 必要なリソースは、サブスクライブしているのがシステム トピックかカスタム トピックかによって異なります。 ここでは、この 2 つの種類について説明します。

### <a name="system-topics-azure-service-publishers"></a>システム トピック (Azure サービスの発行元)

システム トピックの場合、イベントを発行するリソースのスコープに新しいイベント サブスクリプションを書き込むアクセス許可が必要です。 リソースの形式は次のとおりです。`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

たとえば、**myacct** というストレージ アカウントでイベントにサブスクライブするには、Microsoft.EventGrid/EventSubscriptions/Write アクセス許可が必要です。`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>カスタム トピック

カスタム トピックの場合、Event Grid トピックのスコープに新しいイベント サブスクリプションを書き込むアクセス許可が必要です。 リソースの形式は次のとおりです。`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

たとえば、**mytopic** というカスタム トピックにサブスクライブするには、Microsoft.EventGrid/EventSubscriptions/Write アクセス許可が必要です。`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>トピックの発行

トピックは、Shared Access Signature (SAS) またはキー認証を使用します。 SAS が推奨されますが、キー認証はプログラミングが簡単で、既存の多くの webhook 発行元と互換性があります。 

認証値は、HTTP ヘッダーに含めます。 SAS の場合は、ヘッダー値に **aeg-sas-token** を使用します。 キー認証の場合は、ヘッダー値に **aeg-sas-key** を使用します。

### <a name="key-authentication"></a>キー認証

キー認証は、最も単純な形式の認証です。 次の形式を使用します。`aeg-sas-key: <your key>`

たとえば、次のようにキーを渡します。 

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS トークン

Event Grid の SAS トークンには、リソース、有効期限、および署名が含まれます。 SAS トークンの形式は `r={resource}&e={expiration}&s={signature}` です。

リソースは、イベントを送信するトピックのパスです。 たとえば、有効なリソース パスは次のとおりです。`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

キーから署名を生成します。

たとえば、有効な **aeg-sas-token** 値は次のとおりです。

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
``` 

次の例では、Event Grid で使用する SAS トークンを作成します。

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    string encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString());

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>管理アクセス制御

Azure Event Grid を使用すると、イベント サブスクリプションの一覧表示、新しいサブスクリプションの作成、キーの生成など、多様な管理操作を実行する各ユーザーに付与するアクセス レベルを制御できます。 Event Grid は、この処理に Azure のロール ベースのアクセス確認 (RBAC) を利用します。

### <a name="operation-types"></a>操作の種類

Azure Event Grid では、次の操作がサポートされています。

* Microsoft.EventGrid/*/read 
* Microsoft.EventGrid/*/write 
* Microsoft.EventGrid/*/delete 
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action 
* Microsoft.EventGrid/topics/listKeys/action 
* Microsoft.EventGrid/topics/regenerateKey/action

最後の 3 つの操作は、通常の読み取り操作ではフィルターで除外される秘密情報を返す可能性があります。 このような操作へのアクセスは制限することをお勧めします。 カスタム ロールは、[Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)、[Azure コマンド ライン インターフェイス (CLI)](../active-directory/role-based-access-control-manage-access-azure-cli.md)、および [REST API](../active-directory/role-based-access-control-manage-access-rest.md) で作成することができます。

### <a name="enforcing-role-based-access-check-rbac"></a>ロール ベースのアクセス確認 (RBAC) の適用

次の手順を使用して、ユーザーごとに RBAC を適用します。

#### <a name="create-a-custom-role-definition-file-json"></a>カスタム ロールの定義ファイル (.json) を作成する

さまざまなアクションの実行をユーザーに許可する Event Grid ロールの定義例を次に示します。

**EventGridReadOnlyRole.json**: 読み取り専用操作のみを許可します。
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

**EventGridNoDeleteListKeysRole.json**: 制限付きの投稿アクションを許可しますが、削除アクションは禁止します。
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

**EventGridContributorRole.json**: すべての Event Grid アクションを許可します。  
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
    "/subscriptions/d48566a8-2428-4a6c-8347-9675d09fb851" 
  ] 
} 
```

#### <a name="install-and-login-to-azure-cli"></a>Azure CLI のインストールとログイン

* Azure CLI バージョン 0.8.8 以降。 最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「 [Azure CLI のインストールと構成](../cli-install-nodejs.md)」を参照してください。
* Azure CLI の Azure Resource Manager。 詳細については、 [Resource Manager での Azure CLI の使用](../xplat-cli-azure-resource-manager.md) に関するページをご覧ください。

#### <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、次のコマンドを使用します。

    azure role create --inputfile <file path>

#### <a name="assign-the-role-to-a-user"></a>ユーザーにロールを割り当てる


    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>


## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。

