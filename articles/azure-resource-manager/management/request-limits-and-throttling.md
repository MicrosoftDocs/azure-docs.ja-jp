---
title: 要求の制限と調整
description: サブスクリプションの上限に達したときに、Azure Resource Manager の要求をスロットルする方法について説明します。
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: 181ed1a3059d86f78e40a9949448af77a551efbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563128"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager の要求のスロットル

この記事では、Azure Resource Manager を使って要求を調整する方法について説明します。 制限に達する前に残っている要求の数を追跡する方法と、制限に達したときに対応する方法を示します。

調整は 2 つのレベルで行われます。 Azure Resource Manager を使って、サブスクリプションとテナントの要求を調整します。 要求がサブスクリプションとテナントの調整制限内に収まる場合、Resource Manager によって要求はリソース プロバイダーにルーティングされます。 リソース プロバイダーでは、その操作に合わせた調整制限が適用されます。 次の図は、要求がユーザーから Azure Resource Manager とリソース プロバイダーに送信されるときに調整がどのように適用されるかを示しています。

![要求の調整](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>サブスクリプションとテナントの制限

すべてのサブスクリプションレベルとテナントレベルの操作には、調整制限が適用されます。 サブスクリプション要求 (サブスクリプション内のリソース グループの取得など) では、サブスクリプション ID を渡す必要があります。 テナント要求 (有効な Azure の場所の取得など) には、サブスクリプション ID は含まれません。

次の表は、1 時間あたりの既定の調整制限を示しています。

| スコープ | 操作 | 制限 |
| ----- | ---------- | ------- |
| サブスクリプション | 読み取り | 12000 |
| サブスクリプション | 削除 | 15000 |
| サブスクリプション | 書き込み | 1200 |
| Tenant | 読み取り | 12000 |
| Tenant | 書き込み | 1200 |

これらの制限は、要求を行うセキュリティ プリンシパル (ユーザーまたはアプリケーション) と、サブスクリプション ID またはテナント ID の範囲に設定されます。 複数のセキュリティ プリンシパルから要求が発信されると、サブスクリプションまたはテナント全体の制限は、1 時間あたり 12,000 件および 1,200 件を超えます。

これらの制限は、各 Azure Resource Manager インスタンスに適用されます。 すべての Azure リージョンに複数のインスタンスがあり、Azure Resource Manager はすべての Azure リージョンにデプロイされます。  したがって、実際の制限はこれらの制限よりも高くなります。 通常、ユーザーからの要求は、Azure Resource Manager の異なるインスタンスによって処理されます。

## <a name="resource-provider-limits"></a>リソース プロバイダーの制限

リソース プロバイダーでは、独自の調整制限が適用されます。 Resource Manager では、プリンシパル ID と Resource Manager のインスタンスによって調整を行うため、リソース プロバイダーには前のセクションの既定の制限よりも多くの要求が送信される可能性があります。

このセクションでは、広く使用されているいくつかのリソース プロバイダーの調整制限について説明します。

### <a name="storage-throttling"></a>ストレージの調整

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Network throttling

Microsoft.Network リソース プロバイダーでは、次の調整制限が適用されます。

| 操作 | 制限 |
| --------- | ----- |
| 書き込み/削除 (PUT) | 5 分あたり 1000 |
| 読み取り (GET) | 5 分あたり 10000 |

### <a name="compute-throttling"></a>コンピューティング調整

コンピューティング操作の調整制限の詳細については、[「API の調整エラーのトラブルシューティング」のコンピューティング](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)に関する説明を参照してください。

仮想マシン スケール セット内の仮想マシン インスタンスを確認するには、[仮想マシン スケール セットの操作](/rest/api/compute/virtualmachinescalesetvms)を使用します。 たとえば、[仮想マシン スケール セット VM - 一覧](/rest/api/compute/virtualmachinescalesetvms/list)をパラメーターと共に使用して、仮想マシン インスタンスの電源状態を確認します。 この API を使うと、要求の数を減らすことができます。

### <a name="azure-resource-graph-throttling"></a>Azure Resource Graph の調整

[Azure Resource Graph](../../governance/resource-graph/overview.md) では、その操作に対する要求数が制限されます。 この記事内の、残りの要求数を確認する方法と、上限に達したときの対処方法の手順は、Resource Graph にも該当します。 ただし、Resource Graph は独自の制限とリセット レートを設定します。 詳細については、[Resource Graph スロットル ヘッダー](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)に関する記事をご覧ください。

### <a name="other-resource-providers"></a>他のリソース プロバイダー

他のリソース プロバイダーでの帯域幅調整の詳細については、以下を参照してください。

* [Azure Key Vault のスロットル ガイダンス](../../key-vault/general/overview-throttling.md)
* [AKS のトラブルシューティング](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>エラー コード

上限に達すると、HTTP 状態コード **429 Too many requests** が返されます。 応答には **Retry-After** 値が含まれます。これは、次の要求を送信する前にアプリケーションの待機 (またはスリープ) が必要な秒数を示します。 この再試行値が経過する前に要求を送信した場合、要求は処理されず、新しい再試行値が返されます。

指定された時間待機した後、Azure への接続を閉じてから開き直すこともできます。 接続をリセットすることで、Azure Resource Manager の別のインスタンスに接続できます。

Azure SDK を使用している場合は、SDK に自動再試行構成が含まれている可能性があります。 詳細については、「[特定のサービスの再試行ガイダンス](/azure/architecture/best-practices/retry-service-specific)」を参照してください。

リソース プロバイダーによっては、一時的な問題を報告するために 429 を返します。 この問題は、要求が直接の原因ではないオーバーロード条件の可能性があります。 または、ターゲット リソースまたは依存リソースの状態に関する一時的なエラーを表す可能性があります。 たとえば、ターゲット リソースが別の操作によってロックされている場合、ネットワーク リソース プロバイダーからは **RetryableErrorDueToAnotherOperation** エラー コードと共に 429 が返されます。 調整と一時的な状態のどちらによるエラーかを判断するには、応答のエラーの詳細を確認します。

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

リソース プロバイダーからは、残りの要求に関する情報と共に応答ヘッダーが返されることもあります。 コンピューティング リソース プロバイダーから返される応答ヘッダーの詳細については、「[呼び出しレートの情報提供応答ヘッダー](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)」を参照してください。

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

```output
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

```output
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

```output
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

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>次の手順

* 詳細な PowerShell の例については、[サブスクリプションの Resource Manager の制限を確認する方法](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)に関するページを参照してください。
* 制限とクォータの詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。
* 非同期の REST 要求の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](async-operations.md)」を参照してください。
