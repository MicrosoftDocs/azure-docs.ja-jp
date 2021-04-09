---
title: クイック スタート:REST API を使用した新しいポリシー割り当て
description: このクイックスタートでは、REST API を使用して、準拠していないリソースを特定するための Azure Policy 割り当てを作成します。
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219979"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>クイック スタート:REST API を使用して準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、ポリシーの割り当てを作成して、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "_準拠していません_"。

REST API を使用して、Azure リソースを作成および管理します。 このガイドでは、REST API を使用してポリシー割り当てを作成し、Azure 環境内の準拠していないリソースを特定します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- [ARMClient](https://github.com/projectkudu/ARMClient) をまだインストールしていない場合はインストールします。 これは、Azure Resource Manager ベースの REST API に HTTP 要求を送信するツールです。 REST ドキュメントの "試してみる" 機能、または PowerShell の [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) や [Postman](https://www.postman.com) などのツールを使用することもできます。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、**マネージド ディスクを使用しない監査 VM** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義を割り当てます。 このポリシー定義では、ポリシー定義で設定されている条件に準拠していないリソースが識別されます。

ポリシーの割り当てを作成するには、次のコマンドを実行します。

   - REST API URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - 要求本文

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

上記のエンドポイントと要求本文では、次の情報を使用します。

REST API URI:
- **Scope** - スコープによって、ポリシーの割り当てを強制するリソースまたはリソースのグループが決まります。 スコープには、管理グループから個々のリソースまで指定できます。 `{scope}` は、次のパターンのいずれかに必ず置き換えてください。
  - 管理グループ: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - サブスクリプション: `/subscriptions/{subscriptionId}`
  - リソース グループ: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - リソース: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Name** - 割り当ての実際の名前。 この例では、_audit-vm-manageddisks_ が使用されました。

要求本文: 
- **DisplayName** - ポリシーの割り当てに使用する表示名。 このケースでは、"_Audit VMs without managed disks Assignment_" を使用します。
- **Description** - ポリシーの機能またはこのスコープに割り当てる理由の詳細な説明。
- **policyDefinitionId** - 割り当てを作成する際に基礎として使用するポリシー定義 ID。 ここでは、"_Managed Disks を使用していない VM の監査_" というポリシー定義の ID です。
- **nonComplianceMessages** - コンプライアンス違反の (非準拠と評価される) ためにリソースが拒否されたときに表示されるメッセージを設定します。 詳細については、[割り当てのコンプライアンス違反メッセージ](./concepts/assignment-structure.md#non-compliance-messages)に関するセクションを参照してください。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

この新しい割り当てで準拠していないリソースを表示するには、次のコマンドを実行して、JSON ファイルに出力される準拠していないリソースのリソース ID を取得します。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

次のような結果が返されます。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

この結果は、普段 Azure Portal ビューの **[準拠していないリソース]** に一覧表示される内容に相当します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次のコマンドを使用します。

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

`{scope}` は、ポリシー割り当てを最初に作成したときに使用したスコープに置き換えます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)
