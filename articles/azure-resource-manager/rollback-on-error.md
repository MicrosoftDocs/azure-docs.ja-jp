---
title: エラー発生時に正常な Azure デプロイにロールバックする
description: 失敗したデプロイを、正常なデプロイにロールバックすることを指定します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 035b684bb9f5fbc10eb13e642c3fd5945b85c561
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975378"
---
# <a name="rollback-on-error-to-successful-deployment"></a>エラー発生時に正常なデプロイにロールバックする

デプロイに失敗した場合、デプロイ履歴から以前に成功したデプロイを自動的に再デプロイすることができます。 この機能は、インフラストラクチャのデプロイに関して既知の正常な状態があって、その状態に戻したい場合に便利です。 いくつかの注意事項と制限があります。

- 再デプロイは、以前に実行されたときとまったく同じパラメーターで実行されます。 パラメーターを変更することはできません。
- 以前のデプロイは、[完全モード](./deployment-modes.md#complete-mode)を使用して実行されます。 以前のデプロイに含まれていなかったリソースはすべて削除され、すべてのリソースの構成は以前の状態に設定されます。 [デプロイ モード](./deployment-modes.md)を完全に理解しておいてください。
- 再デプロイではリソースのみが影響を受け、データの変更には影響ありません。
- この機能は、サブスクリプション レベルまたは管理グループ レベルのデプロイではなく、リソース グループのデプロイでのみ使用できます。 サブスクリプション レベル デプロイについて詳しくは、「[サブスクリプション レベルでリソース グループとリソースを作成する](./deploy-to-subscription.md)」をご覧ください。
- このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

このオプションを使用するには、ご自身のデプロイを履歴で特定できるように、デプロイの名前は一意でなければなりません。 一意の名前が付いていないと、失敗した現在のデプロイによって、履歴にある以前の成功したデプロイが上書きされる可能性があります。

## <a name="powershell"></a>PowerShell

最後に成功したデプロイを再デプロイするには、`-RollbackToLastDeployment` パラメーターをフラグとして追加します。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

特定のデプロイを再デプロイするには、`-RollBackDeploymentName` パラメーターを使用してデプロイの名前を指定します。 指定したデプロイは成功している必要があります。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

最後に成功したデプロイを再デプロイするには、`--rollback-on-error` パラメーターをフラグとして追加します。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

特定のデプロイを再デプロイするには、`--rollback-on-error` パラメーターを使用してデプロイの名前を指定します。 指定したデプロイは成功している必要があります。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

現在のデプロイが失敗した場合、前回の成功したデプロイを再デプロイするには、次を使用します。

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

現在のデプロイが失敗した場合、特定のデプロイを再デプロイするには、次を使用します。

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

指定したデプロイは成功している必要があります。

## <a name="next-steps"></a>次の手順

- 複数のリージョン間で、サービスを安全にロールアウトするには、[Azure Deployment Manager](deployment-manager-overview.md) に関する記事を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
- SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。
