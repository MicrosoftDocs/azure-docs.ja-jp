---
title: テンプレート デプロイの what-if
description: Azure Resource Manager テンプレートをデプロイする前に、リソースがどのような変更されるかを確認します。
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 3f35f00e5fcd26c7aa4bb685dc80f48eabb88b6d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547936"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM テンプレート デプロイの What-If 操作

Azure Resource Manager テンプレート (ARM テンプレート) をデプロイする前に、発生する変更をプレビューすることができます。 Azure Resource Manager の what-if 操作を使うと、テンプレートをデプロイした場合にリソースがどのように変更されるかを確認できます。 what-if 操作では、既存のリソースに対していかなる変更も行われません。 代わりに、指定したテンプレートがデプロイされた場合の変更が予測されます。

what-if 操作は Azure PowerShell、Azure CLI、または REST API 操作で使用できます。 What-if は、リソース グループ、サブスクリプション、管理グループ、テナント レベルのデプロイでサポートされています。

## <a name="install-azure-powershell-module"></a>Azure PowerShell モジュールをインストールする

PowerShell で What-if を使用するには、**Az モジュールの 4.2 以降** のバージョンが必要です。

モジュールをインストールするには、次を使用します。

```powershell
Install-Module -Name Az -Force
```

モジュールのインストールの詳細については、「[Azure PowerShell をインストールする](/powershell/azure/install-az-ps)」を参照してください。

## <a name="install-azure-cli-module"></a>Azure CLI モジュールのインストール

Azure CLI で what-if を使用するには、Azure CLI 2.5.0 以降である必要があります。 必要であれば、[Azure CLI の最新バージョンをインストールします](/cli/azure/install-azure-cli)。

## <a name="see-results"></a>結果を表示する

what-if を PowerShell または Azure CLI で使用する際、さまざまな種類の変更を確認するのに役立つ、色分けされた結果が出力されます。

![Resource Manager テンプレートのデプロイの what-if 操作の fullresourcepayload と変更の種類](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

テキスト出力は次のとおりです。

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> What-if 操作では、[reference 関数](template-functions-resource.md#reference)を解決できません。 reference 関数を含むテンプレート式にプロパティを設定するたびに、What-if は、そのプロパティが変更されることを報告します。 この動作は、What-if がそのプロパティの現在の値 (ブール値の場合は `true` や `false` など) を未解決のテンプレート式と比較するために発生します。 当然、これらの値は一致しません。 テンプレートをデプロイすると、このプロパティは、テンプレート式が別の値に解決される場合にのみ変更されます。

## <a name="what-if-commands"></a>What-if コマンド

### <a name="azure-powershell"></a>Azure PowerShell

テンプレートをデプロイする前に変更をプレビューするには、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) または [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) を使用します。 デプロイ コマンドに `-Whatif` スイッチ パラメーターを追加します。

* リソース グループ デプロイの場合は `New-AzResourceGroupDeployment -Whatif`
* サブスクリプション レベルのデプロイの場合は `New-AzSubscriptionDeployment -Whatif` および `New-AzDeployment -Whatif`

`-Confirm` スイッチ パラメーターを使用して、変更をプレビューし、デプロイを続行するかどうかを確認するプロンプトを表示することもできます。

* リソース グループ デプロイの場合は `New-AzResourceGroupDeployment -Confirm`
* サブスクリプション レベルのデプロイの場合は `New-AzSubscriptionDeployment -Confirm` および `New-AzDeployment -Confirm`

上記のコマンドは、手動で検査できるテキストの概要を返します。 プログラムで変更を検査できるオブジェクトを取得するには、[Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) または [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult) を使用します。

* リソース グループ デプロイの場合は `$results = Get-AzResourceGroupDeploymentWhatIfResult`
* サブスクリプション レベルのデプロイの場合は `$results = Get-AzSubscriptionDeploymentWhatIfResult` または `$results = Get-AzDeploymentWhatIfResult`

### <a name="azure-cli"></a>Azure CLI

テンプレートをデプロイする前に変更のプレビューを表示するには、次のコマンドを使用します。

* [az deployment group what-if](/cli/azure/deployment/group#az-deployment-group-what-if) (リソース グループのデプロイの場合)
* [az deployment sub what-if](/cli/azure/deployment/sub#az-deployment-sub-what-if) (サブスクリプション レベルのデプロイの場合)
* [az deployment mg what-if](/cli/azure/deployment/mg#az-deployment-mg-what-if) (管理グループのデプロイの場合)
* [az deployment tenant what-if](/cli/azure/deployment/tenant#az-deployment-tenant-what-if) (テナントのデプロイの場合)

`--confirm-with-what-if` (または短縮形式 `-c`) を使用して、変更をプレビューし、デプロイを続行するかどうかを確認するプロンプトを表示することもできます。 このスイッチを次のコマンドに追加します。

* [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create)
* [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create)
* [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

たとえば、リソース グループのデプロイの場合は `az deployment group create --confirm-with-what-if` または `-c` を使用します。

上記のコマンドは、手動で検査できるテキストの概要を返します。 プログラムによって変更を検査できる JSON オブジェクトを取得するには、`--no-pretty-print` スイッチを使用します。 たとえば、リソース グループのデプロイの場合は `az deployment group what-if --no-pretty-print` を使用します。

カラーなしの結果を返す場合は、[Azure CLI 構成](/cli/azure/azure-cli-configuration)ファイルを開きます。 **no_color** を **yes** に設定します。

### <a name="azure-rest-api"></a>Azure REST API

REST API については、以下を使用します。

* リソース グループ デプロイの場合は「[デプロイ - What If](/rest/api/resources/deployments/whatif)」
* サブスクリプションのデプロイの場合は「[デプロイ - サブスクリプション スコープでの What If](/rest/api/resources/deployments/whatifatsubscriptionscope)」
* 管理グループのデプロイの場合は「[デプロイ - 管理グループ スコープでの What If](/rest/api/resources/deployments/whatifatmanagementgroupscope)」
* テナントのデプロイの場合は「[デプロイ - テナント スコープでの What If](/rest/api/resources/deployments/whatifattenantscope)」

## <a name="change-types"></a>変更の種類

what-if 操作では、6 種類の異なる変更が一覧表示されます。

- **作成**:現在、リソースは存在しませんが、テンプレートで定義されています。 リソースが作成されます。

- **[削除]** :この変更の種類は、デプロイの [完全モード](deployment-modes.md)を使用するときにのみ適用されます。 リソースは存在しますが、テンプレートでは定義されていません。 完全モードでは、リソースが削除されます。 この変更の種類には、[完全モードの削除をサポートしている](complete-mode-deletion.md)リソースのみが含まれます。

- **無視**: リソースは存在しますが、テンプレートでは定義されていません。 リソースはデプロイまたは変更されません。

- **変更なし**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされますが、リソースのプロパティは変更されません。 この変更の種類は、[ResultFormat](#result-format) が `FullResourcePayloads` に設定されている場合に返されます。これは既定値です。

- **変更**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされ、リソースのプロパティが変更されます。 この変更の種類は、[ResultFormat](#result-format) が `FullResourcePayloads` に設定されている場合に返されます。これは既定値です。

- **デプロイ**: リソースは存在し、テンプレートで定義されています。 リソースは再デプロイされます。 リソースのプロパティは、変更される場合と変更されない場合があります。 いずれかのプロパティが変更されるかどうか判断するのに十分な情報がない場合、操作ではこの変更の種類が返されます。 この状況は、[ResultFormat](#result-format) が `ResourceIdOnly` に設定されている場合にのみ表示されます。

## <a name="result-format"></a>結果の形式

予測される変更に関して返される詳細さのレベルを制御します。 2 つのオプションがあります。

* **FullResourcePayloads** - 変更されるリソースの一覧と、変更されるプロパティの詳細を返します
* **ResourceIdOnly** - 変更されるリソースの一覧を返します

既定値は **FullResourcePayloads** です。

PowerShell のデプロイ コマンドでは、`-WhatIfResultFormat` パラメーターを使用します。 プログラム オブジェクト コマンドでは、`ResultFormat` パラメーターを使用します。

Azure CLI の場合は、`--result-format` パラメーターを使用します。
 
次の結果では、2 つの異なる出力形式が示されています。

- 完全なリソース ペイロード

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- リソース ID のみ

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>what-if 操作を実行する

### <a name="set-up-environment"></a>環境を設定する

what-if がどのように動作するか見るため、いくつかのテストを実行してみましょう。 まず、[仮想ネットワークを作成するテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)をデプロイします。 この仮想ネットワークを使用して、what-if により変更がどのように報告されるかをテストします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>変更をテストする

デプロイが完了すると、what-if 操作をテストできる状態になります。 ここでは、[仮想ネットワークを変更するテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)をデプロイします。 元のタグのうち 1 つが存在せず、サブネットが削除され、アドレス プレフィックスが変更されています。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

what-if の出力は次のようになります。

![Resource Manager テンプレートのデプロイの what-if 操作の出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

テキスト出力は次のとおりです。

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

出力の上部で、変更の種類を示す色が定義されていることに注意してください。

出力の下部には、タグ Owner が削除されたことが表示されています。 アドレス プレフィックスが 10.0.0.0/16 から 10.0.0.0/15 に変更されました。 subnet001 という名前のサブネットが削除されました。 これらの変更は、デプロイされていないことに注意してください。 テンプレートをデプロイした場合に行われる変更のプレビューが表示されています。

削除済みとして一覧されたプロパティの一部は、実際には変更されません。 プロパティは、テンプレートに含まれていない場合、削除済みとして誤って報告されることがありますが、デプロイ時に既定値として自動的に設定されます。 この結果は、what-if 応答では "ノイズ" と見なされます。 最後にデプロイされたリソースには、プロパティ用の値セットがあります。 what-if 操作が成熟すると、これらのプロパティは結果から除外されます。

## <a name="programmatically-evaluate-what-if-results"></a>what-if 結果のプログラムによる評価

次に、コマンドを変数に設定して、what-if 結果をプログラムで評価してみましょう。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

それぞれの変更の概要を確認できます。

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>削除の確定

what-if 操作では、[デプロイ モード](deployment-modes.md)の使用がサポートされています。 完全モードに設定すると、テンプレートに含まれていないリソースは削除されます。 次の例では、[リソースが定義されていないテンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json)を完全モードでデプロイします。

テンプレートをデプロイする前に変更のプレビューを表示するには、デプロイ コマンドで confirm スイッチ パラメーターを使用します。 変更が期待どおりの場合は、デプロイを完了すると応答します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

テンプレートでリソースが何も定義されておらず、デプロイ モードが Complete に設定されているため、仮想ネットワークは削除されます。

![Resource Manager テンプレートのデプロイの what-if 操作の完全モード デプロイに対する出力](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

テキスト出力は次のとおりです。

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

期待通りの変更が表示され、デプロイを実行することを確認できます。

## <a name="sdks"></a>SDK

what-if 操作は、Azure SDK を介して使用できます。

* Python の場合は、[what-if](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-) を使用します。

* Java の場合は、[DeploymentWhatIf クラス](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)を使用します。

* .NET の場合は、[DeploymentWhatIf クラス](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)を使用します。

## <a name="next-steps"></a>次のステップ

- パイプラインで What-If 操作を使用するには、「[Test ARM templates with What-If in a pipeline](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)」 (パイプラインで What-If を使用して ARM テンプレートをテストする) を参照してください。
- what-if 操作から正しくない結果が表示された場合は、[https://aka.ms/whatifissues](https://aka.ms/whatifissues) で問題を報告してください。
- what if の使用に関して説明している Microsoft Learn モジュールについては、「[what-if と ARM テンプレート テスト ツールキットを使用して変更をプレビューし、Azure リソースを検証する](/learn/modules/arm-template-test/)」を参照してください。
