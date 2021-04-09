---
title: 'クイックスタート: Terraform を使用した新しいポリシーの割り当て'
description: このクイックスタートでは、Terraform と HCL 構文を使用して、ポリシー割り当てを作成し、非準拠リソースを特定します。
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93104923"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>クイックスタート: Terraform を使用してポリシー割り当てを作成し、非準拠リソースを特定する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、ポリシーの割り当てを作成して、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "_準拠していません_"。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- 環境で [Terraform](https://www.terraform.io/) バージョン 0.12.0 以上が構成されていること。
  手順については、「[Azure Cloud Shell を使用して Terraform を構成する](/azure/developer/terraform/get-started-cloud-shell)」を参照してください。
- このクイックスタートでは、Azure CLI バージョン 2.13.0 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Terraform の構成、変数、および出力ファイルを作成する

このクイックスタートでは、ポリシー割り当てを作成し、**マネージド ディスクを使用しない監査 VM** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義を割り当てます。 このポリシー定義では、ポリシー定義で設定されている条件に準拠していないリソースが識別されます。

まず、Terraform の構成、変数、および出力ファイルを作成します。 Azure Policy 用の Terraform リソースでは、[Azure プロバイダー](https://www.terraform.io/docs/providers/azurerm/index.html)を使用します。

1. `policy-assignment` という名前の新しいフォルダーを作成し、ここにディレクトリを変更します。

1. 次のコードを使用して `main.tf` を作成します。

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. 次のコードを使用して `variables.tf` を作成します。

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 スコープには、管理グループから個々のリソースまで指定できます。 `{scope}` は、次のパターンのいずれかに必ず置き換えてください。

   - サブスクリプション: `/subscriptions/{subscriptionId}`
   - リソース グループ: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - リソース: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. 次のコードを使用して `output.tf` を作成します。

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Terraform を初期化してプランを作成する

次に、Terraform を初期化して、必要なプロバイダーをダウンロードしてから、プランを作成します。

1. [terraform init](https://www.terraform.io/docs/commands/init.html) コマンドを実行します。 このコマンドによって、Terraform 構成で Azure リソースを作成するために必要な Azure モジュールがダウンロードされます。

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="terraform init コマンドの実行を示すスクリーンショット。azurerm モジュールのダウンロードと成功メッセージが表示されています。":::

1. [Azure CLI](/cli/azure/) で Terraform に対して認証します。 詳細については、「[Azure プロバイダー: Azure CLI を使用した認証](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html)」を参照してください。

   ```bash
   az login
   ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) コマンドと **out** パラメーターを使用して実行プランを作成します。

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="terraform plan コマンドと out パラメーターの実行を示すスクリーンショット。作成される Azure リソースが表示されています。":::

   > [!NOTE]
   > 実行プランの永続化とセキュリティの詳細については、「[Terraform Plan: セキュリティ警告](https://www.terraform.io/docs/commands/plan.html#security-warning)」を参照してください。

## <a name="apply-the-terraform-execution-plan"></a>Terraform 実行プランを適用する

最後に、実行プランを適用します。

[terraform apply](https://www.terraform.io/docs/commands/apply.html) コマンドを実行し、作成済みの `assignment.tfplan` を指定します。

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="terraform apply コマンドの実行を示すスクリーンショット。結果として作成されるリソースが表示されています。":::

"Apply complete! (適用が完了しました。) リソース: 1 added, 0 changed, 0 destroyed. (追加 1、変更 0、破棄 0。)" というメッセージが表示され、ポリシー割り当てが作成されます。 `outputs.tf` ファイルを定義したため、_assignment\_id_ も返されます。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

この新しい割り当てに準拠していないリソースを表示するには、`terraform apply` によって返される _assignment\_id_ を使用します。 これを指定して次のコマンドを実行し、非準拠リソースのリソース ID を取得します。ID は JSON ファイルに出力されます。

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

作成された割り当てを削除するには、Azure CLI を使用するか、`terraform destroy` を使用して Terraform 実行プランを元に戻します。

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)
