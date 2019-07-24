---
title: 要求の制限と調整 - Azure Resource Manager
description: サブスクリプションの上限に達したときに、Azure Resource Manager の要求をスロットルする方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 91a776ba13ffaeeb4f8184371ae45a80d829ae46
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550645"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager の要求のスロットル

Resource Manager では、Azure のサブスクリプションおよびテナントごとに、最大で 1 時間あたり 12,000 件の読み取り要求と 1 時間あたり 1,200 件の書き込み要求が許可されています。 これらの制限は、要求を行うプリンシパル ID と、サブスクリプション ID またはテナント ID の範囲に設定されます。 複数のプリンシパル ID から要求が発信されると、サブスクリプションまたはテナント全体の制限は、1 時間あたり 12,000 件および 1,200 件を超えます。

要求は、サブスクリプションまたはテナントに適用されます。 サブスクリプション要求 (サブスクリプション内のリソース グループの取得など) では、サブスクリプション ID を渡す必要があります。 テナント要求 (有効な Azure の場所の取得など) には、サブスクリプション ID は含まれません。

これらの制限は、各 Azure Resource Manager インスタンスに適用されます。 すべての Azure リージョンに複数のインスタンスがあり、Azure Resource Manager はすべての Azure リージョンにデプロイされます。  このため、ユーザーの要求は、通常は多数の異なるインスタンスによって処理されるため、実際の上限はこれらの制限よりも大幅に高くなります。

アプリケーションまたはスクリプトがこれらの上限に達した場合、要求をスロットルする必要があります。 この記事では、上限に達する前に残りの要求数を確認する方法と、上限に達したときの対処方法について説明します。

上限に達すると、HTTP 状態コード **429 Too many requests** が返されます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="remaining-requests"></a>残りの要求数
残りの要求数を確認するには、応答ヘッダーを調べます。 読み取り要求では、残りの読み取り要求数の値がヘッダーに返されます。 書き込み要求には、残りの書き込み要求数の値が含まれます。 これらの値を確認できる応答ヘッダーを次の表に示します。

| 応答ヘッダー | 説明 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |サブスクリプション スコープの残りの読み取り要求数。 この値は読み取り操作で返されます。 |
| x-ms-ratelimit-remaining-subscription-writes |サブスクリプション スコープの残りの書き込み要求数。 この値は書き込み操作で返されます。 |
| x-ms-ratelimit-remaining-tenant-reads |テナント スコープの残りの読み取り要求数。 |
| x-ms-ratelimit-remaining-tenant-writes |テナント スコープの残りの書き込み要求数。 |
| x-ms-ratelimit-remaining-subscription-resource-requests |サブスクリプション スコープの残りのリソースの種類の要求数。<br /><br />このヘッダー値は、サービスが既定の上限をオーバーライドした場合にのみ返されます。 Resource Manager は、サブスクリプションの読み取り要求数または書き込み要求数の代わりにこの値を追加します。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |サブスクリプション スコープの残りのリソースの種類収集要求数。<br /><br />このヘッダー値は、サービスが既定の上限をオーバーライドした場合にのみ返されます。 この値は、残りの収集要求数 (リソースのリストの取得) を示します。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |テナント スコープの残りのリソースの種類の要求数。<br /><br />このヘッダーはテナント レベルの要求専用であり、サービスが既定の上限をオーバーライドした場合にのみ追加されます。 Resource Manager は、テナントの読み取り要求数または書き込み要求数の代わりにこの値を追加します。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |テナント スコープの残りのリソースの種類収集要求数。<br /><br />このヘッダーはテナント レベルの要求専用であり、サービスが既定の上限をオーバーライドした場合にのみ追加されます。 |

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

詳細な PowerShell の例については、[サブスクリプションの Resource Manager の制限を確認する方法](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)に関するページを参照してください。

デバッグのために残りの要求数を確認する場合は、**PowerShell** コマンドレットで **-Debug** パラメーターを指定します。

```powershell
Get-AzResourceGroup -Debug
```

次の応答値を含む多くの値が返されます。

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

書き込み制限数を取得するには、書き込み操作を使用します。 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

次の値を含む多くの値が返されます。

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

**Azure CLI** では、より詳細なオプションを使用してヘッダー値を取得します。

```azurecli
az group list --verbose --debug
```

次の値を含む多くの値が返されます。

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

書き込み制限数を取得するには、書き込み操作を使用します。 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

次の値を含む多くの値が返されます。

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>次の要求を送信するまでの待機
要求の上限に達すると、Resource Manager は HTTP 状態コード **429** とヘッダー値 **Retry-After** を返します。 **Retry-After** 値は、アプリケーションが次の要求を送信するまでに待機 (またはスリープ) する必要がある時間 (秒数) を示します。 この再試行値が経過する前に要求を送信した場合、要求は処理されず、新しい再試行値が返されます。

## <a name="next-steps"></a>次の手順

* 詳細な PowerShell の例については、[サブスクリプションの Resource Manager の制限を確認する方法](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)に関するページを参照してください。
* 制限とクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。
* 非同期の REST 要求の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。
