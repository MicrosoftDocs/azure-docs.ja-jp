---
title: 切断された Azure Automanage アカウントの修復
description: 最近、Automanage アカウントを含むサブスクリプションを新しいテナントに移動した場合は、そのアカウントを再構成する必要があります。 この記事では、その方法について説明します。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183704"
---
# <a name="repair-an-automanage-account"></a>Automanage アカウントの修復
[Azure Automanage アカウント](./automanage-virtual-machines.md#automanage-account)は、自動化された操作が行われるセキュリティ コンテキストまたは ID です。 最近、Automanage アカウントを含むサブスクリプションを新しいテナントに移動した場合は、そのアカウントを再構成する必要があります。 再構成するには、ID の種類をリセットし、アカウントに適切なロールを割り当てる必要があります。

## <a name="step-1-reset-the-automanage-account-identity-type"></a>手順 1:Automanage アカウントの ID の種類をリセットする
次の Azure Resource Manager (ARM) テンプレートを使用して、Automanage アカウント ID の種類をリセットします。 ファイルを armdeploy.json または同様の名前でローカルに保存します。 Automanage アカウントの名前と場所は、ARM テンプレートに必須のパラメーターです。

1. 次のテンプレートを使用して、Resource Manager デプロイを作成します。 `identityType = None` を使用してください。
    * `az deployment sub create` を使用して、Azure CLI でデプロイを作成できます。 詳細については、「[az deployment sub](/cli/azure/deployment/sub)」を参照してください。
    * `New-AzDeployment` モジュールを使用して、PowerShell でデプロイを作成できます。 詳細については、「[New-AzDeployment](/powershell/module/az.resources/new-azdeployment)」を参照してください。

1. `identityType = SystemAssigned` を使用して、同じ ARM テンプレートをもう一度実行します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>手順 2:Automanage アカウントに適切なロールを割り当てる
Automanage アカウントには、Automanage で管理されている VM が含まれているサブスクリプションに対する共同作成者ロールとリソース ポリシーの共同作成者ロールが必要です。 これらのロールは、Azure portal、ARM テンプレート、または Azure CLI を使用して割り当てることができます。

ARM テンプレートまたは Azure CLI を使用している場合は、Automanage アカウントのプリンシパル ID (オブジェクト ID とも呼ばれます) が必要になります (Azure portal を使用している場合は、ID は必要ありません)。この ID は、次の方法を使用して見つけることができます。

- [Azure CLI](/cli/azure/ad/sp):コマンド `az ad sp list --display-name <name of your Automanage Account>` を使用します。

- Azure portal:**Azure Active Directory** にアクセスし、Automanage アカウントを名前で検索します。 **[エンタープライズ アプリケーション]** で、Automanage アカウント名が表示されたらそれを選択します。

### <a name="azure-portal"></a>Azure portal
1. **[サブスクリプション]** で、自動管理 VM が含まれているサブスクリプションにアクセスします。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当ての追加]** を選択します。
1. **[共同作成者]** ロールを選択し、Automanage アカウントの名前を入力します。
1. **[保存]** を選択します。
1. この時点で、 **[リソース ポリシーの共同作成者]** ロールを使用して、手順 3 から 5 を繰り返します。

### <a name="arm-template"></a>ARM テンプレート
次の ARM テンプレートを実行します。 Automanage アカウントのプリンシパル ID が必要になります。 これを取得する手順は、このセクションの先頭に記載されています。 プロンプトが表示されたら、ID を入力します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
これらのコマンドを実行します。

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>次のステップ
[Azure Automanage の詳細情報](./automanage-virtual-machines.md)