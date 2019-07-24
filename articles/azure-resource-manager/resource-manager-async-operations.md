---
title: Azure Resource Manager の非同期操作の状態
description: Azure での非同期操作を追跡する方法について説明します。 長時間実行の操作の状態を取得するために使用する値が表示されます。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1b05ed50f08ddbf2eb5da8e08f5bf623596e1f9b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135459"
---
# <a name="track-asynchronous-azure-operations"></a>非同期 Azure 操作の追跡
Azure の REST 操作の中には、操作を迅速に完了できないため、非同期的に実行されるものがあります。 この記事では、応答で返される値を通じて非同期操作の状態を追跡する方法について説明します。  

## <a name="status-codes-for-asynchronous-operations"></a>非同期操作の状態コード
非同期操作では、最初に次のどちらかの HTTP 状態コードが返されます。

* 201 (作成済み)
* 202 (承認済み) 

操作が正常に完了すると、次のどちらかが返されます。

* 200 (OK)
* 204 (コンテンツなし) 

実行中の操作の応答については、[REST API のドキュメント](/rest/api/)を参照してください。

## <a name="monitor-status-of-operation"></a>操作の状態を監視する
非同期の REST 操作では、操作の状態を確認するために使用するヘッダーの値が返されます。 確認が必要なヘッダーの値は 3 つ想定されます。

* `Azure-AsyncOperation` - 操作の進行状態をチェックするための URL です。 操作がこの値を返す場合、(Location ではなく) この値を使用して操作の状態を追跡します。
* `Location` - 操作が完了したタイミングを確認するための URL です。 Azure-AsyncOperation が返されない場合にのみ、この値を使用します。
* `Retry-After` - 非同期操作の状態をチェックするまで待機する秒数。

ただし、すべての非同期操作で、これらすべての値が返されるわけではありません。 たとえば、ある操作には Azure-AsyncOperation ヘッダー値を、別の操作には Location ヘッダー値を評価する必要がある場合があります。 

他の要求のヘッダー値を取得するのと同じように、ヘッダーの値を取得します。 たとえば、C# では、次のコードを使用して `response` という名前の `HttpWebResponse` オブジェクトからヘッダー値を取得します。

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation 要求と応答

非同期操作の状態を取得するには、Azure-AsyncOperation ヘッダーの値の URL に GET 要求を送信します。

この操作からの応答の本文には、操作に関する情報が含まれています。 次の例では、操作から返されると想定される値を示します。

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

`status` のみが、すべての応答で返されます。 状態が "失敗" または "取り消し済み" の場合、エラー オブジェクトが返されます。 その他の値はすべて省略可能です。そのため、受信した応答は、例とは外観が異なる場合があります。

## <a name="provisioningstate-values"></a>provisioningState 値

リソースを作成、更新、または削除 (PUT、PATCH、DELETE) する操作は通常、`provisioningState` 値を返します。 操作が完了すると、次の 3 つの値のいずれかが返されます。 

* 成功
* 失敗
* Canceled

その他の値はすべて、操作がまだ実行中であることを示します。 リソース プロバイダーは、その状態を示すカスタマイズされた値を返すことができます。 たとえば、要求が受信され、実行中の場合は **Accepted** が表示されします。

## <a name="example-requests-and-responses"></a>要求と応答の例

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>仮想マシンの起動 (202 と Azure-AsyncOperation)
この例では、仮想マシンの**開始**操作の状態を確認する方法を示します。 最初の要求の形式は次のとおりです。

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

状態コード 202 が返されます。 ヘッダー値の中では、以下が確認できます。

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

非同期操作の状態をチェックするには、その URL に別の要求を送信します。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

応答の本文には、操作の状態が含まれています。

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>リソースのデプロイ (201 と Azure-AsyncOperation)

この例では、リソースを Azure にデプロイするための**展開**操作の状態を確認する方法を示します。 最初の要求の形式は次のとおりです。

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

状態コード 201 が返されます。 応答の本文には、以下が含まれます。

```json
"provisioningState":"Accepted",
```

ヘッダー値の中では、以下が確認できます。

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

非同期操作の状態をチェックするには、その URL に別の要求を送信します。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

応答の本文には、操作の状態が含まれています。

```json
{"status":"Running"}
```

デプロイが完了すると、応答には以下が含まれます。

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>ストレージ アカウントの作成 (202 と Location および Retry-After)

この例では、ストレージ アカウントの**作成**操作の状態を確認する方法を示します。 最初の要求の形式は次のとおりです。

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

要求本文には、ストレージ アカウントのプロパティが含まれています。

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

状態コード 202 が返されます。 ヘッダーの値の中には、次の 2 つの値があります。

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Retry-After で指定されている秒数分待機した後、この URL に別の要求を送信して非同期操作の状態を確認します。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

要求がまだ実行中の場合は、状態コード 202 が表示されます。 要求が完了すると、状態コード 200 が表示され、応答の本文には、作成されたストレージ アカウントのプロパティが含まれます。

## <a name="next-steps"></a>次の手順

* 各 REST 操作の詳細については、[REST API のドキュメント](/rest/api/)を参照してください。
* リソース マネージャー REST API からテンプレートをデプロイする方法の詳細については、「[Deploy resources with Resource Manager templates and Resource Manager REST API (リソース マネージャー テンプレートとリソース マネージャー REST API を使用したリソースのデプロイ)](resource-group-template-deploy-rest.md)」を参照してください。