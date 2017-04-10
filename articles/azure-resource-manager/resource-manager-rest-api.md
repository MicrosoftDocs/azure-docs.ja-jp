---
title: Resource Manager REST API| Microsoft Docs
description: "Resource Manager REST API の認証の概要と使用例"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
translationtype: Human Translation
ms.sourcegitcommit: 76864bfc1b59cfc4e6f39094c08394fe32482d17
ms.openlocfilehash: b7957c52877b262506013a422cd1511dd0ee79a4
ms.lasthandoff: 01/14/2017


---
# <a name="resource-manager-rest-apis"></a>Resource Manager REST API
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [ポータル](resource-group-portal.md) 
> * [REST API](resource-manager-rest-api.md)
> 
> 

Azure Resource Manager、デプロイ済みのテンプレート、構成済みのストレージ アカウントを呼び出す裏では、必ず Azure Resource Manager の RESTful API が&1; 回以上呼び出されます。 このトピックでは、これらの API と、SDK を一切使わずにこれらの API を呼び出す方法について説明します。 この方法は、Azure への要求を完全に制御したい場合や、優先言語の SDK が利用できないか、必要な操作がその SDK でサポートされていない場合に、役に立ちます。

この記事では Azure で公開されているすべての API については説明しませんが、一部の操作を接続例として取り上げます。 基本を理解できたら、[Azure Resource Manager REST API リファレンス](https://docs.microsoft.com/rest/api/resources/)で残りの API の使い方を調べることができます。

## <a name="authentication"></a>認証
Resource Manager の認証は、Azure Active Directory (AD) によって処理されます。 任意の API に接続するには、最初に Azure AD で認証を行って認証トークンを受信する必要があります。この認証トークンは、すべての要求に対して渡すことができます。 ここでは REST API の直接的で純粋な呼び出しについて説明するため、ユーザー名やパスワードの入力を求める画面が表示される認証を望んでいないと想定しています。 また、2 要素認証のメカニズムを使用していないということも想定しています。 したがって、Azure AD アプリケーションと、ログインに使用するサービス プリンシパルを作成します。 ただし、Azure AD は複数の認証手順をサポートしており、そのどれを使っても、後続の API 要求に必要な認証トークンを取得できることに注意してください。
詳しい手順については、[Azure AD アプリケーションとサービス プリンシパルの作成](resource-group-create-service-principal-portal.md)に関する記事をご覧ください。

### <a name="generating-an-access-token"></a>アクセス トークンの生成
Azure AD に対する認証は、login.microsoftonline.com にある Azure AD を呼び出すことで行われます。 認証のためには次の情報が必要です。

* Azure AD テナント ID (ログインに使用している Azure AD の名前。会社名と同じである場合が多いですが、必ずしもそうとは限りません)
* アプリケーション ID (Azure AD アプリケーションの作成中に取得したもの)
* パスワード (Azure AD アプリケーションの作成中に選択したもの)

次の HTTP 要求で、"Azure AD Tenant ID"、"Application ID"、"Password" を適切な値に置き換えてください。

**一般的な HTTP 要求:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

この要求により、(認証に成功した場合は) 次のような応答が返されます。

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(読みやすくするために、上記の応答の access_token は短縮されています)

**Bash を使ったアクセス トークンの生成:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**PowerShell を使ったアクセス トークンの生成:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

応答には、アクセス トークン、そのトークンの有効期限に関する情報、そのトークンを使えるリソースに関する情報が含まれます。
前の HTTP 呼び出しで受信したアクセス トークンは、Resource Manager API に対するすべての要求で、 "Bearer YOUR_ACCESS_TOKEN" 値を含む "Authorization" というヘッダー値として渡す必要があります。 "Bearer" とアクセス トークンの間のスペースに注意してください。

上記の HTTP の結果からわかるとおり、トークンは特定の期間、有効です。その期間中は、トークンをキャッシュし、同じトークンを再利用する必要があります。 API 呼び出しごとに Azure AD に対して認証を行うことができたとしても、著しく非効率です。

## <a name="calling-resource-manager-rest-apis"></a>Resource Manager REST API の呼び出し
このトピックでは、一部の API のみを使用して、REST 操作の基本的な使用方法を説明します。 すべての操作の詳細については、[Azure リソース マネージャー REST API](https://docs.microsoft.com/rest/api/resources/) に関する記事を参照してください。

### <a name="list-all-subscriptions"></a>すべてのサブスクリプションの一覧表示
実行できる最も簡単な操作の&1; つに、アクセスできる利用可能なサブスクリプションの一覧表示があります。 次の要求には、アクセス トークンをヘッダーとして渡す方法が示されています。

(YOUR_ACCESS_TOKEN を実際のアクセス トークンに置き換えてください)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

この要求の結果、このサービス プリンシパルでアクセスできるサブスクリプションの一覧が返されます。

(サブスクリプション ID は読みやすいように短縮されています)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>特定のサブスクリプションに含まれる全リソース グループの一覧表示
Resource Manager API で使用できるリソースは、いずれもリソース グループの入れ子になっています。 次の HTTP GET 要求を使って、サブスクリプションに含まれる既存のリソース グループを Resource Manager に対して照会します。 ここではどのような形でサブスクリプション ID が URL の一部として渡されているか、注目してください。

(YOUR_ACCESS_TOKEN と SUBSCRIPTION_ID を実際のアクセス トークンとサブスクリプション ID に置き換えてください)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

返される応答は、定義済みのリソース グループの有無によって変わります。定義済みのリソース グループがある場合は、その数によって変わります。

(サブスクリプション ID は読みやすいように短縮されています)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>リソース グループの作成
ここまでは、Resource Manager API に対して情報を照会するのみでしたが、 ここからは、いくつかリソースを作成してみます。その中でも最もシンプルな、リソース グループから始めましょう。 次の HTTP 要求は指定したリージョン/場所にリソース グループを作成し、それにタグを追加します。

(YOUR_ACCESS_TOKEN、SUBSCRIPTION_ID、RESOURCE_GROUP_NAME を実際のアクセス トークン、サブスクリプション ID、作成するリソース グループの名前に置き換えてください)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

成功した場合、次のような応答を取得します。

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

これでリソース グループを Azure で正常に作成できました。 お疲れさまでした。

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Resource Manager テンプレートを使ってリソース グループにリソースをデプロイする
Resource Manager では、テンプレートを使ってリソースをデプロイできます。 テンプレートには、いくつかのリソースとその依存関係が定義されます。 このセクションは Resource Manager テンプレートに精通している読者を想定しているため、API 呼び出しによりそのデプロイを始める方法のみを紹介します。 テンプレートの作成の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

テンプレートのデプロイは、他の API を呼び出す方法とそれほど違いはありません。 重要な点は、テンプレートのデプロイにはかなりの時間がかかる可能性があるという点です。 API の呼び出しは単に返るだけなので、デプロイが完了したタイミングを把握するためには、開発者がデプロイの状態を照会する必要があります。 詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。

この例では、[GitHub](https://github.com/Azure/azure-quickstart-templates) で一般公開されているテンプレートを使います。 ここで使用するテンプレートは、Linux VM を米国西部のリージョンに配置します。 この例では GitHub などのパブリック リポジトリで公開されているテンプレートを使用しますが、代わりに要求の一部で完全なテンプレートを渡すこともできます。 デプロイするテンプレート内で使用される要求で、パラメーター値を指定することに注意してください。

(SUBSCRIPTION_ID、RESOURCE_GROUP_NAME、DEPLOYMENT_NAME、YOUR_ACCESS_TOKEN、GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME、ADMIN_USER_NAME、ADMIN_PASSWORD、DNS_NAME_FOR_PUBLIC_IP を独自の要求に適した値に置き換えてください)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

このドキュメントを読みやすくするために、この要求に対する長い JSON の応答は省略されています。 応答には、先ほど作成したテンプレート化されたデプロイに関する情報が含まれます。

## <a name="next-steps"></a>次のステップ

- 非同期の REST 操作の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。

