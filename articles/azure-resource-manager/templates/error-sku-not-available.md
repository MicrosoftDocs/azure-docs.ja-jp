---
title: SKU が使用できないエラー
description: Azure Resource Manager でリソースをデプロイするときに、SKU が使用できないことを示すエラーのトラブルシューティングを行う方法について説明します。
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 5b0bbd653907c109eca526af86979013b3137cfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737152"
---
# <a name="resolve-errors-for-sku-not-available"></a>SKU 利用不可エラーを解決する

この記事では、**SkuNotAvailable** エラーを解決する方法について説明します。 そのリージョンまたはゾーン内で、あるいはビジネス ニーズを満たす代替のリージョンまたはゾーン内で適切な SKU が見つからない場合は、Azure サポートに [ SKU 要求](/troubleshoot/azure/general/region-access-request-process)を送信してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

リソース (通常は仮想マシン) をデプロイするときに、次のエラー コードとエラー メッセージが表示されます。

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

このエラーは、選択したリソースの SKU (VM サイズなど) が、選択した場所で利用できない場合に発生します。

Azure スポット VM またはスポット スケール セット インスタンスをデプロイする場合、この場所に Azure スポットの容量はありません。 詳細については、[スポットのエラー メッセージ](../../virtual-machines/error-codes-spot.md)に関する記事を参照してください。

## <a name="solution-1---powershell"></a>解決策 1 - PowerShell

リージョンまたはゾーンで利用可能な SKU を特定するには、[Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) コマンドを使用します。 結果を場所でフィルター処理します。 このコマンドには、PowerShell の最新バージョンが必要です。

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

結果には、目的の場所で利用可能な SKU の一覧とその SKU に関する制約事項が含まれます。 SKU は `NotAvailableForSubscription` として表示される場合があります。

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

いくつかの追加のサンプル:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

末尾に "fc" を追加すると、詳細が返されます。

## <a name="solution-2---azure-cli"></a>解決策 2 - Azure CLI

リージョンで利用可能な SKU を特定するには、`az vm list-skus` コマンドを使用します。 出力をフィルター処理で使用中の場所に絞り込むには、`--location` パラメーターを使用します。 名前の一部で検索するには、`--size` パラメーターを使用します。

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

このコマンドからは、次のような結果が返されます。

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```

## <a name="solution-3---azure-portal"></a>解決策 3 - Azure Portal

リージョンで利用可能な SKU を特定するには、[ポータル](https://portal.azure.com)を使用します。 ポータルにサインインし、インターフェイスからリソースを追加します。 値を設定するときに、そのリソースで利用可能な SKU が表示されます。 デプロイを完了する必要はありません。

たとえば、仮想マシンの作成プロセスを開始できます。 利用可能な他のサイズを表示するには、 **[サイズ変更]** を選択します。

![VM を作成する](./media/error-sku-not-available/create-vm.png)

利用可能なサイズをフィルター処理して、スクロールできます。

![利用可能な SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>解決策 4 - REST

リージョンで利用可能な SKU を特定するには、[[リソース SKU - 一覧]](/rest/api/compute/resourceskus/list) 操作を使用します。

次の形式で利用可能な SKU とリージョンを返します。

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```