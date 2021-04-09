---
title: Azure DevTest Labs で VM のリソース グループを指定する | Microsoft Docs
description: Azure DevTest Labs でラボの VM に対するリソース グループを指定する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483365"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs でラボの仮想マシンのリソース グループを指定する

ラボの所有者は、ラボの仮想マシンが特定のリソース グループに作成されるように構成することができます。 この機能は、次のシナリオで役立ちます。

- サブスクリプション内のリソース グループのうち、ラボによって作成されたものが少ない。
- 自分で構成した一連の固定のリソース グループ内でラボを運用する。
- Azure サブスクリプション内でリソース グループを作成するために必要な制限および承認を回避する。
- ラボのすべてのリソースを単一のリソース グループ内に統合し、それらのリソースの追跡と[ポリシー](../governance/policy/overview.md)の適用を簡素化して、リソースをリソース グループ レベルで管理する。

この機能では、スクリプトを使用して、すべてのラボ VM に対して、Azure サブスクリプション内の新規または既存のリソース グループを指定できます。 現在、Azure DevTest Labs では、この機能は API を使用することによってサポートされています。

> [!NOTE]
> DevTest Labs でラボを作成する場合、すべてのサブスクリプション制限が適用されます。 ラボは、お使いのサブスクリプション内の他のリソースと考えてください。 リソース グループの場合、制限は、[1 つのサブスクリプションにつき 980 個のリソース グループ](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)になります。 

## <a name="use-azure-portal"></a>Azure Portal の使用
次の手順に従って、ラボで作成されたすべての VM のリソース グループを指定します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション メニューで、 **[すべてのサービス]** を選択します。 
3. 一覧で **[DevTest Labs]** を選択します。
4. ラボの一覧で、目的の **ラボ** を選択します。  
5. 左側のメニューの **[設定]** セクションで、 **[構成とポリシー]** を選択します。 
6. 左側のメニューで **[ラボの設定]** を選択します。 
7. **[1 つのリソース グループ内のすべての仮想マシン]** を選択します。 
8. ドロップダウン リストで既存のリソース グループを選択するか、**[新規作成]** を選択し、リソース グループの **名前** を入力して、**[OK]** をクリックします。 

    ![すべてのラボ VM のリソース グループの選択](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell の使用 
次の例は、PowerShell スクリプトを使用して、すべてのラボ仮想マシンを新しいリソース グループに作成する方法を示しています。

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

次のコマンドを使用してスクリプトを呼び出します。 ResourceGroup.ps1 は、上記のスクリプトが含まれているファイルです。

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
Azure Resource Manager テンプレートを使用してラボを作成する場合は、次の例に示すように、テンプレートのラボ プロパティ セクションで **vmCreationResourceGroupId** プロパティを使用します。

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>ラボ VM 用のリソース グループを構成するための API
ラボ所有者がこの API を使用するときには、次のオプションがあります。

- すべての仮想マシンに対して、**ラボのリソース グループ** を選択する。
- すべての仮想マシンに対して、ラボのリソース グループ以外の **既存のリソース グループ** を選択する。
- すべての仮想マシンの **新しいリソース グループ** の名前を入力する。
- 引き続き既存の動作を使用する (ラボの VM ごとにリソース グループが作成されます)。
 
この設定は、ラボで作成される新しい仮想マシンに適用されます。 独自のリソース グループに作成されたラボの古い VM には影響しません。 ラボで作成された環境は、独自のリソース グループに引き続き維持されます。

この API の使用方法:
- API バージョン **2018_10_15_preview** を使用します。
- 新しいリソース グループを指定する場合は、サブスクリプション内の **リソース グループに対する書き込みアクセス許可** があることを確認します。 書き込みアクセス許可がない場合、指定したリソース グループでの新しい仮想マシンの作成は失敗します。
- API を使用するときは、**完全なリソース グループ ID** を渡します。 たとえば、 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`と指定します。 リソース グループがラボと同じサブスクリプションに含まれていることを確認します。 


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 

- [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
- [よく寄せられる質問](devtest-lab-faq.md)
