---
title: Microsoft.Resources 内のリソースのリージョンを移動する
description: Microsoft.Resources 名前空間にあるリソースを新しいリージョンに移動する方法について説明します。
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88950811"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Microsoft.Resources のリソースを新しいリージョンに移動する

既存のリソースを新しいリージョンに移動することが必要になる場合があります。 この記事では、Microsoft.Resources 名前空間内にある 2 つのリソースの種類 templateSpecs と deploymentScripts を移動する方法について説明します。

## <a name="move-template-specs-to-new-region"></a>テンプレート スペックを新しいリージョンに移動する

あるリージョンにある[テンプレート スペック](../templates/template-specs.md)を新しいリージョンに移動したい場合は、テンプレート スペックをエクスポートして再デプロイすることができます。

1. コマンドを使用して、既存のテンプレート スペックをエクスポートします。パラメーターの値として、エクスポートするテンプレート スペックと一致する値を指定します。

   Azure PowerShell では、次を使用します。

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Azure CLI では、次を使用します。

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. エクスポートされたテンプレート スペックを使用して、新しいテンプレート スペックを作成します。次の例では新しいリージョンとして `westus` が示されていますが、任意のリージョンを指定できます。

   Azure PowerShell では、次を使用します。

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Azure CLI では、次を使用します。

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>デプロイ スクリプトを新しいリージョンに移動する

1. 新しいリージョンに移動するデプロイ スクリプトが含まれているリソース グループを選択します。

1. [テンプレートをエクスポートします](../templates/export-template-portal.md)。 エクスポートするときに、デプロイ スクリプトと他の必要なリソースを選択します。

1. エクスポートされたテンプレートで、次のプロパティを削除します。

   * tenantId
   * principalId
   * clientId

1. エクスポートされたテンプレートでは、デプロイ スクリプトのリージョンの値がハードコーディングされています。

   ```json
   "location": "westus2",
   ```

   場所を設定するパラメーターを許可するように、テンプレートを変更します。 詳細については、「[ARM テンプレートでリソースの場所を設定する](../templates/resource-location.md)」を参照してください

   ```json
   "location": "[parameters('location')]",
   ```

1. [エクスポートしたテンプレートをデプロイ](../templates/deploy-powershell.md)し、デプロイ スクリプトの新しいリージョンを指定します。

## <a name="next-steps"></a>次のステップ

* 新しいリソース グループまたはサブスクリプションへのリソースの移動については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](move-resource-group-and-subscription.md)」を参照してください。
* 新しいリージョンへのリソースの移動については、「[リージョン間の Azure リソースの移動](move-region.md)」を参照してください。
