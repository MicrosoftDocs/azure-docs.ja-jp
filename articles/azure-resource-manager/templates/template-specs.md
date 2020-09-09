---
title: テンプレート スペックの概要
description: テンプレート スペックを作成し、組織内の他のユーザーと共有する方法について説明します。
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: fad7ca60e98dcaabc5f6fc106e0d2c1b77085d67
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227884"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager テンプレート スペック (プレビュー)

テンプレート スペックは、後でデプロイするために Azure に Azure Resource Manager テンプレート (ARM テンプレート) を格納するための新しいリソースの種類です。 このリソースの種類を使用すると、ARM テンプレートを組織内の他のユーザーと共有できます。 他の Azure リソースと同じように、ロールベースのアクセス制御 (RBAC) を使用してテンプレート スペックを共有できます。

**Microsoft.Resources/templateSpecs** は、テンプレート スペックの新しいリソースの種類です。 これは、メイン テンプレートと、リンクされた任意の数のテンプレートで構成されます。 Azure によって、テンプレート スペックはリソース グループに安全に格納されます。 Template Specs では、[バージョン管理](#versioning)がサポートされています。

テンプレート スペックをデプロイするには、PowerShell、Azure CLI、Azure portal、REST およびその他のサポートされている SDK およびクライアントなど、標準の Azure ツールを使用します。 テンプレートの場合と同じコマンドを使用します。

> [!NOTE]
> Template Specs は現在プレビューの段階です。 使用するには、[待機リストにサインアップする](https://aka.ms/templateSpecOnboarding)必要があります。

## <a name="why-use-template-specs"></a>テンプレート スペックを使用する理由は何ですか。

現在、GitHub リポジトリまたはストレージ アカウントにテンプレートがある場合に、テンプレートを共有して使用しようとすると、いくつかの問題が発生します。 ユーザーがテンプレートをデプロイするには、テンプレートがローカルであるか、テンプレートの URL にパブリックにアクセスできる必要があります。 この制限を回避するには、テンプレートのコピーをデプロイする必要があるユーザーと共有するか、リポジトリまたはストレージ アカウントへのアクセスを開きます。 ユーザーがテンプレートのローカル コピーを所有している場合は、これらのコピーが最終的に元のテンプレートから分岐する可能性があります。 リポジトリまたはストレージ アカウントにパブリックにアクセスできるようにすると、意図しないユーザーがテンプレートにアクセスできてしまう可能性があります。

テンプレート スペックを使用する利点は、正規のテンプレートを作成し、組織内のチームと共有できることです。 テンプレート スペックは、Azure Resource Manager をデプロイする際に使用できますが、RBAC アクセス許可を持っていないユーザーはアクセスできないため、セキュリティで保護されています。 ユーザーがテンプレートをデプロイするのに必要なのは、そのテンプレート スペックへの読み取りアクセス権のみです。そのため、他のユーザーに変更を許可することなくテンプレートを共有できます。

テンプレート スペックに含まれるテンプレートは、組織の要件とガイダンスに従うように組織の管理者が検証する必要があります。

## <a name="create-template-spec"></a>テンプレート スペックを作成する

次の例は、Azure でストレージ アカウントを作成するための簡単なテンプレートを示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

テンプレート スペックを作成するときは、PowerShell コマンドまたは CLI コマンドにメイン テンプレート ファイルが渡されます。 メイン テンプレートがリンクされたテンプレートを参照している場合は、コマンドでそれらのテンプレートが検索されてパッケージ化され、テンプレート スペックが作成されます。詳細については、「[リンクされたテンプレートを使用してテンプレート スペックを作成する](#create-a-template-spec-with-linked-templates)」を参照してください。

テンプレート スペックを作成するには、次のコマンドを使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

サブスクリプション内のすべてのテンプレート スペックを表示するには、次のコマンドを使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

テンプレート スペックの詳細 (そのバージョンを含む) を表示するには、次のコマンドを使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>テンプレート スペックをデプロイする

テンプレート スペックを作成すると、そのテンプレート スペックへの**読み取り**アクセス権を持つユーザーは、それをデプロイできます。 アクセスの許可については、「[チュートリアル: Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)」を参照してください。

Template Specs は、ポータル、PowerShell、Azure CLI を通じて、または大規模なテンプレートのデプロイでリンクされたテンプレートとしてデプロイできます。 組織内のユーザーは、Azure 内の任意のスコープ (リソース グループ、サブスクリプション、管理グループ、またはテナント) にテンプレート スペックをデプロイできます。

テンプレートのパスまたは URI を渡す代わりに、リソース ID を指定してテンプレート スペックをデプロイします。 リソース ID の形式は次のとおりです。

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

リソース ID にテンプレート スペックのバージョン番号が含まれていることに注目してください。

たとえば、次のコマンドを使用してテンプレート スペックをデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

実際には、通常は `Get-AzTemplateSpec` を実行して、デプロイするテンプレート スペックの ID を取得します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>パラメーター

テンプレート スペックにパラメーターを渡すことは、ARM テンプレートにパラメーターを渡すこととまったく同じです。 パラメーターの値は、インラインで、またはパラメーター ファイルに追加します。

パラメーターをインラインで渡すには、以下を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

ローカル パラメーター ファイルを作成するには、以下を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

また、そのパラメーター ファイルを以下を使用して渡します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="create-a-template-spec-with-linked-templates"></a>リンクされたテンプレートを使用してテンプレート スペックを作成する

テンプレート スペックのメイン テンプレートがリンクされたテンプレートを参照している場合は、PowerShell コマンドと CLI コマンドで、リンクされたテンプレートをローカル ドライブから自動的に検索してパッケージ化することができます。 テンプレート スペックをホストするために、ストレージ アカウントまたはリポジトリを手動で構成する必要はありません。テンプレート スペック リソースにすべて含まれています。

次の例は、メイン テンプレートと、リンクされた 2 つのテンプレートで構成されています。 この例は、テンプレートの抜粋にすぎません。 `relativePath` という名前のプロパティを使用して、他のテンプレートにリンクしていることに注目してください。 デプロイ リソースには、`2020-06-01` 以降の `apiVersion` を使用する必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

前の例で、テンプレート スペックを作成する PowerShell コマンドまたは CLI コマンドを実行すると、メイン テンプレート、Web アプリ テンプレート (`webapp.json`)、およびデータベース テンプレート (`database.json`) の 3 つのファイルが検索され、テンプレート スペックにパッケージ化されます。

詳細については、[リンクされたテンプレートを使用してテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。

## <a name="deploy-template-spec-as-a-linked-template"></a>テンプレート スペックをリンクされたテンプレートとしてデプロイする

テンプレート スペックを作成すると、ARM テンプレートや別のテンプレート スペックから簡単に再利用できます。テンプレートにリソース ID を追加して、テンプレート スペックにリンクします。 リンクされたテンプレート スペックは、メイン テンプレートのデプロイ時に自動的にデプロイされます。 この動作により、モジュール テンプレート スペックを開発し、必要に応じて再利用することができます。

たとえば、ネットワーク リソースをデプロイするテンプレート スペックと、ストレージ リソースをデプロイする別のテンプレート スペックを作成することができます。 ARM テンプレートでは、ネットワーク リソースまたはストレージ リソースを構成する必要があるときはいつでも、これらの 2 つのテンプレート スペックにリンクします。

次の例は、前の例と似ていますが、ローカル テンプレートにリンクする `relativePath` プロパティではなく、テンプレート スペックにリンクする `id` プロパティを使用します。 デプロイ リソースの API バージョンには `2020-06-01` を使用します。 この例では、テンプレート スペックは **templateSpecsRG** という名前のリソース グループにあります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

テンプレート スペックのリンクの詳細については、「[チュートリアル: テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。

## <a name="versioning"></a>バージョン管理

テンプレート スペックを作成するときは、そのバージョン番号を指定します。 テンプレート コードを反復処理するときは、既存のバージョンを更新する (修正プログラムの場合) か、または新しいバージョンを発行することができます。 バージョンはテキスト文字列です。 セマンティック バージョン管理など、任意のバージョン管理システムに従うことを選択できます。 テンプレート スペックのユーザーは、デプロイ時に使用するバージョン番号を指定できます。

## <a name="next-steps"></a>次のステップ

* テンプレート スペックを作成してデプロイするには、「[クイックスタート: テンプレート スペックの作成とデプロイ](quickstart-create-template-specs.md)」を参照してください。

* テンプレート スペックでテンプレートをリンクする方法の詳細については、「[チュートリアル: リンクされたテンプレートを使用してテンプレート スペックを作成する](template-specs-create-linked.md)」を参照してください。

* テンプレート スペックをリンクされたテンプレートとしてデプロイする方法の詳細については、「[チュートリアル: テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。
