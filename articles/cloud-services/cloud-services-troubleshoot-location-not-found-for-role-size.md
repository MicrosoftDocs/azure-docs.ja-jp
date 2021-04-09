---
title: Azure にクラウド サービス (クラシック) をデプロイするときの LocationNotFoundForRoleSize のトラブルシューティング | Microsoft Docs
description: この記事では、Azure にクラウド サービス (クラシック) をデプロイするときの LocationNotFoundForRoleSize 例外を解決する方法について説明します。
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: b11aedb52be3c263c781c2ac68d1d5197ba4def2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743861"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Azure にクラウド サービス (クラシック) をデプロイするときの LocationNotFoundForRoleSize のトラブルシューティング

この記事では、Azure クラウド サービス (クラシック) をデプロイするときに仮想マシン (VM) のサイズを使用できない場合の、割り当てエラーのトラブルシューティングを行います。

インスタンスをクラウド サービス (クラシック) にデプロイした場合や、新しい Web ロール インスタンスまたは worker ロール インスタンスを追加した場合に、Microsoft Azure によってコンピューティング リソースが割り当てられます。

これらの操作を行っているときには、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。

> [!TIP]
> この情報は、サービスのデプロイを計画する場合にも役立ちます。

## <a name="symptom"></a>症状

Azure portal で、ご利用のクラウド サービス (クラシック) に移動し、サイドバーの *[操作ログ (クラシック)]* を選択してログを表示します。

![[操作ログ (クラシック)] ブレードを示す画像。](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

そのクラウド サービス (クラシック) のログを調べると、次の例外が表示されます。

|例外の種類  |エラー メッセージ  |
|---------|---------|
|LocationNotFoundForRoleSize     |操作 '`{Operation ID}`' が失敗しました: '要求された VM 層は、現在、このサブスクリプションのリージョン (`{Region ID}`) では利用できません。 別の層を試すか、別の場所にデプロイしてください。'。|

## <a name="cause"></a>原因

デプロイ先のリージョンまたはクラスターに容量の問題があります。 選択したリソース SKU (VM サイズ) が指定したリージョンで使用できない場合、*LocationNotFoundForRoleSize* 例外が発生します。

## <a name="solution"></a>解決策

このシナリオでは、クラウド サービス (クラシック) のデプロイ先として別のリージョンまたは SKU を選択する必要があります。 クラウド サービス (クラシック) をデプロイまたはアップグレードする前に、リージョンまたは可用性ゾーンで使用可能な SKU を確認できます。 以下に示す [Azure CLI](#list-skus-in-region-using-azure-cli)、[PowerShell](#list-skus-in-region-using-powershell)、または [REST API](#list-skus-in-region-using-rest-api) のプロセスに従ってください。

### <a name="list-skus-in-region-using-azure-cli"></a>Azure CLI を使用してリージョン内の SKU を一覧表示する

[az vm list-skus](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) コマンドを使用できます。

- 出力をフィルター処理で使用中の場所に絞り込むには、`--location` パラメーターを使用します。
- 名前の一部で検索するには、`--size` パラメーターを使用します。
- 詳細については、「[SKU 利用不可エラーを解決する](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli)」のガイドを参照してください。

    **例:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **結果の例:** ![使用可能な SKU を表示する 'az vm list-skus --location southcentralus --size Standard_F --output table' コマンドを Azure CLI で実行した出力。](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>PowerShell を使用してリージョン内の SKU を一覧表示する

[Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) コマンドを使用できます。

- 結果を場所でフィルター処理します。
- このコマンドには、PowerShell の最新バージョンが必要です。
- 詳細については、「[SKU 利用不可エラーを解決する](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell)」のガイドを参照してください。

**例:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**その他の便利なコマンド:**

サイズ (Standard_DS14_v2) を含む場所をフィルターで除外します。

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

サイズ (V3) を含むすべての場所をフィルターで除外します。

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>REST API を使用してリージョン内の SKU を一覧表示する

「[リソース SKU - 一覧](https://docs.microsoft.com/rest/api/compute/resourceskus/list)」の操作を使用できます。 次の形式で利用可能な SKU とリージョンを返します。

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>次のステップ

その他の割り当てエラーの解決策について確認し、それらを導く方法について十分に理解します。

> [!div class="nextstepaction"]
> [割り当てエラー - クラウド サービス (クラシック)](cloud-services-allocation-failures.md)

この記事で Azure の問題に対処できない場合は、[MSDN および Stack Overflow](https://azure.microsoft.com/support/forums/) の Azure 関連フォーラムを参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 Azure サポート要求を送信することもできます。 サポート要求を送信するには、[[Azure サポート]](https://azure.microsoft.com/support/options/) ページで *[サポートを受ける]* を選択します。
