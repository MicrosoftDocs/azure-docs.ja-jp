---
title: クイック スタート:Azure CLI を使用した新しいポリシーの割り当て
description: このクイックスタートでは、Azure CLI を使用して、Azure Policy の割り当てを作成し、準拠していないリソースを特定します。
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9955f911f9a92d7b353a8f3d022af7884b5a6aae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090156"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>クイック スタート:Azure CLI を使用して準拠していないリソースを識別するポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、ポリシーの割り当てを作成して、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "_準拠していません_"。

Azure CLI は、コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- このクイックスタートでは、Azure CLI バージョン 2.0.76 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

- Azure CLI を使用して Azure Policy Insights リソース プロバイダーを登録します。 リソース プロバイダーを登録すると、そのリソース プロバイダーで、ご利用のサブスクリプションを確実に動作させることができます。 リソース プロバイダーを登録するには、リソース プロバイダーの登録操作のためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。 リソース プロバイダーを登録する以下のコマンドを実行します。

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  リソース プロバイダーの登録と表示の詳細については、「[リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

- [ARMClient](https://github.com/projectkudu/ARMClient) をまだインストールしていない場合はインストールします。 Azure Resource Manager ベースの API に HTTP 要求を送信するツールです。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、ポリシーの割り当てを作成し、**マネージド ディスクを使用しない監査 VM** 定義を割り当てます。 このポリシー定義では、ポリシー定義で設定されている条件に準拠していないリソースが識別されます。

ポリシーの割り当てを作成するには、次のコマンドを実行します。

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

上記のコマンドでは次の情報を使用します。

- **Name** - 割り当ての実際の名前。 この例では、_audit-vm-manageddisks_ が使用されました。
- **DisplayName** - ポリシーの割り当てに使用する表示名。 このケースでは、"_Audit VMs without managed disks Assignment_" を使用します。
- **Policy** - 割り当ての作成に使用するポリシー定義 ID。 ここでは、"_Managed Disks を使用していない VM の監査_" というポリシー定義の ID です。 ポリシー定義 ID を取得するには、次のコマンドを実行します。`az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Scope** - スコープによって、ポリシーの割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。 &lt;scope&gt; は、実際のリソース グループの名前に置き換えてください。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

この新しい割り当てに準拠していないリソースを表示するには、次のコマンドを実行してポリシー割り当て ID を取得します。

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

ポリシー割り当て ID の詳細については、「[az policy assignment](/cli/azure/policy/assignment)」を参照してください。

続けて、次のコマンドを実行して、非準拠リソースの ID を取得します。該当するリソース ID が JSON ファイルに出力されます。

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

この結果は、普段 Azure Portal ビューの **[準拠していないリソース]** に一覧表示される内容に相当します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次のコマンドを使用します。

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)
