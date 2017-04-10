---
title: "Azure Resource Manager の要求の制限 | Microsoft Docs"
description: "サブスクリプションの上限に達したときに、Azure Resource Manager の要求をスロットルする方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4029b699b59bb12eaa9e24b487d2829b5fb26daf
ms.openlocfilehash: 6780b422138fbe18adfe256e9f7aa279dfed1cd9


---
# <a name="throttling-resource-manager-requests"></a>Resource Manager の要求のスロットル
Resource Manager では、サブスクリプションおよびテナントごとに、読み取り要求が 1 時間あたり 15,000 に制限され、書き込み要求が 1 時間あたり 1,200 に制限されています。 アプリケーションまたはスクリプトがこれらの上限に達した場合、要求をスロットルする必要があります。 このトピックでは、上限に達する前に残りの要求数を確認する方法と、上限に達したときの対処方法について説明します。

上限に達すると、HTTP 状態コード **429 Too many requests** が返されます。

要求数のスコープは、サブスクリプションまたはテナントです。 サブスクリプションで複数の同時実行アプリケーションが要求を行っている場合、それらのアプリケーションからの要求が合計されて残りの要求数が算出されます。

サブスクリプション スコープの要求 (サブスクリプション内のリソース グループの取得など) では、サブスクリプション ID を渡す必要があります。 テナント スコープの要求 (有効な Azure の場所の取得など) には、サブスクリプション ID は含まれません。

## <a name="remaining-requests"></a>残りの要求数
残りの要求数を確認するには、応答ヘッダーを調べます。 各要求には、残りの読み取り要求と書き込み要求の数を示す値が含まれています。 これらの値を確認できる応答ヘッダーを次の表に示します。

| 応答ヘッダー | 説明 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |サブスクリプション スコープの残りの読み取り要求数。 |
| x-ms-ratelimit-remaining-subscription-writes |サブスクリプション スコープの残りの書き込み要求数。 |
| x-ms-ratelimit-remaining-tenant-reads |テナント スコープの残りの読み取り要求数。 |
| x-ms-ratelimit-remaining-tenant-writes |テナント スコープの残りの書き込み要求数。 |
| x-ms-ratelimit-remaining-subscription-resource-requests |サブスクリプション スコープの残りのリソースの種類の要求数。<br /><br />このヘッダー値は、サービスが既定の上限を無効にした場合にのみ返されます。 Resource Manager は、サブスクリプションの読み取り要求数または書き込み要求数の代わりにこの値を追加します。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |サブスクリプション スコープの残りのリソースの種類収集要求数。<br /><br />このヘッダー値は、サービスが既定の上限を無効にした場合にのみ返されます。 この値は、残りの収集要求数 (リソースのリストの取得) を示します。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |テナント スコープの残りのリソースの種類の要求数。<br /><br />このヘッダーはテナント レベルの要求専用であり、サービスが既定の上限を無効にした場合にのみ追加されます。 Resource Manager は、テナントの読み取り要求数または書き込み要求数の代わりにこの値を追加します。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |テナント スコープの残りのリソースの種類収集要求数。<br /><br />このヘッダーはテナント レベルの要求専用であり、サービスが既定の上限を無効にした場合にのみ追加されます。 |

## <a name="retrieving-the-header-values"></a>ヘッダー値の取得
コードまたはスクリプトでこれらのヘッダー値を取得する方法は、任意のヘッダー値を取得する方法と変わりはありません。 

たとえば、**C#** では、次のコードを使用して **response** という名前の **HttpWebResponse** オブジェクトからヘッダー値を取得します。

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

**PowerShell** では、Invoke-WebRequest 操作からヘッダー値を取得します。

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

デバッグのために残りの要求数を確認する場合は、**PowerShell** コマンドレットで **-Debug** パラメーターを指定します。

```powershell
Get-AzureRmResourceGroup -Debug
```

次の応答値を含む多くの値が返されます。

```powershell
...
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
...
```

**Azure CLI** では、より詳細なオプションを使用してヘッダー値を取得します。

```azurecli
azure group list -vv --json
```

次のオブジェクトを含む多くの値が返されます。

```azurecli
...
silly: returnObject
{
  "statusCode": 200,
  "header": {
    "cache-control": "no-cache",
    "pragma": "no-cache",
    "content-type": "application/json; charset=utf-8",
    "expires": "-1",
    "x-ms-ratelimit-remaining-subscription-reads": "14998",
    ...
```

## <a name="waiting-before-sending-next-request"></a>次の要求を送信するまでの待機
要求の上限に達すると、Resource Manager は HTTP 状態コード **429** とヘッダー値 **Retry-After** を返します。 **Retry-After** 値は、アプリケーションが次の要求を送信するまでに待機 (またはスリープ) する必要がある時間 (秒数) を示します。 この再試行値が経過する前に要求を送信した場合、要求は処理されず、新しい再試行値が返されます。

## <a name="next-steps"></a>次のステップ

* 制限とクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。
* 非同期の REST 要求の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。



<!--HONumber=Jan17_HO2-->


