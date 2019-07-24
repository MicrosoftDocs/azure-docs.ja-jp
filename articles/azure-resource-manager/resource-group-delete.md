---
title: リソース グループとリソースを削除する - Azure Resource Manager
description: リソース グループを削除するときに、Azure Resource Manager によってリソースの削除がどのように並べ替えられるかについて説明します。 応答コードと、削除が成功したかを判断するために Resource Manager によって応答コードが処理される方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: c38b1ccf7f7ccfe57e2b29f236f642238c4706a7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492744"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Azure Resource Manager によるリソース グループの削除

この記事では、リソース グループを削除するときに、Azure Resource Manager によってリソースの削除がどのように並べ替えられるかについて説明します。

## <a name="determine-order-of-deletion"></a>削除の順序を決定する

リソース グループを削除する際に、Resource Manager によってリソースを削除する順序が決定されます。 次の順序が使用されます。

1. すべての子 (入れ子) リソースが削除されます。

2. 次に、他のリソースを管理するリソースが削除されます。 リソースには、別のリソースによって管理されていることを示す `managedBy` プロパティが設定されている場合があります。 このプロパティが設定されている場合、他のリソースを管理しているリソースは、その管理対象リソースより前に削除されます。

3. 残りのリソースは、前の 2 つのカテゴリより後に削除されます。

## <a name="resource-deletion"></a>リソースの削除

順序が決定された後、Resource Manager によって、各リソースに対して DELETE 操作が発行されます。 先に進む前に、すべての依存関係が完了するまで待ちます。

同期操作の場合、期待される正常な応答コードは次のとおりです。

* 200
* 204
* 404

非同期操作の場合、期待される正常な応答は 202 です。 Resource Manager では、location ヘッダーまたは azure-async 操作ヘッダーが追跡されて、非同期の削除操作の状態が確認されます。
  
### <a name="errors"></a>Errors

削除操作からエラーが返された場合、Resource Manager によって DELETE 呼び出しが再試行されます。 再試行は、5xx、429、408 の状態コードに対して行われます。 既定では、再試行の間隔は 15 分です。

## <a name="after-deletion"></a>削除後

Resource Manager では、削除が試行されるリソースごとに GET 呼び出しが発行されます。 この GET 呼び出しの応答として、404 が期待されます。 Resource Manager が 404 を受け取った場合は、削除が正常に完了したと見なされます。 Resource Manager によって、そのキャッシュからリソースが削除されます。

一方、リソースに対する GET 呼び出しで 200 または 201 が返された場合は、Resource Manager によってリソースが再作成されます。

### <a name="errors"></a>Errors

GET 操作からエラーが返された場合、次のエラー コードであれば Resource Manager によって GET が再試行されます。

* 100 未満
* 408
* 429
* 500 より大きい

その他のエラー コードの場合は、Resource Manager によるリソースの削除が失敗します。

## <a name="next-steps"></a>次の手順

* Resource Manager の概念を理解するには、「[Azure Resource Manager の概要](resource-group-overview.md)」をご覧ください。
* 削除コマンドについては、[PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup)、[Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)、[REST API](/rest/api/resources/resourcegroups/delete) をご覧ください。
