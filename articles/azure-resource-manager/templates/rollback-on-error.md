---
title: エラー発生時に正常なデプロイにロールバックする
description: 失敗したデプロイを、正常なデプロイにロールバックすることを指定します。
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492214"
---
# <a name="rollback-on-error-to-successful-deployment"></a>エラー発生時に正常なデプロイにロールバックする

デプロイに失敗した場合、デプロイ履歴から以前に成功したデプロイを自動的に再デプロイすることができます。 この機能は、インフラストラクチャのデプロイに関して既知の正常な状態があって、その状態に戻したい場合に便利です。 特定の以前のデプロイを指定することも、最後に成功したデプロイを指定することもできます。

> [!IMPORTANT]
> この機能を使用すると、以前のデプロイを再デプロイすることで、失敗したデプロイをロールバックすることができます。 この結果は、失敗したデプロイを元に戻すことから期待していたものと異なる場合があります。 以前のデプロイがどのように再デプロイされるかを確実に理解してください。

## <a name="considerations-for-redeploying"></a>再デプロイに関する注意点

この機能を使用する前に、再デプロイの処理方法に関する次の詳細を考慮してください。

- 以前のデプロイで[増分モード](./deployment-modes.md#incremental-mode)を使用した場合でも、前のデプロイは[完全モード](./deployment-modes.md#complete-mode)を使用して実行されます。 完全モードで再デプロイすると、以前のデプロイで増分を使用していた場合に、予期しない結果が生じる可能性があります。 完全モードは、前のデプロイに含まれていないすべてのリソースが削除されることを意味します。 リソース グループ内に存在する必要があるすべてのリソースとその状態を表す、以前のデプロイを指定します。 詳細については、[デプロイ モード](./deployment-modes.md)に関するページを参照してください。
- 再デプロイは、以前に実行されたときとまったく同じパラメーターで実行されます。 パラメーターを変更することはできません。
- 再デプロイではリソースのみが影響を受け、データの変更には影響ありません。
- この機能は、リソース グループのデプロイでのみ使用できます。 サブスクリプション、管理グループ、またはテナント レベルのデプロイはサポートされていません。 サブスクリプション レベル デプロイについて詳しくは、「[サブスクリプション レベルでリソース グループとリソースを作成する](./deploy-to-subscription.md)」をご覧ください。
- このオプションは、ルート レベルのデプロイでのみ使用できます。 入れ子になったテンプレートのデプロイを再デプロイに使用することはできません。

このオプションを使用するには、デプロイの名前をデプロイ履歴の中で一意にする必要があります。 特定のデプロイを識別できるのは、一意の名前だけです。 一意の名前が付いていないと、失敗したデプロイによって、履歴にある成功したデプロイが上書きされる可能性があります。

デプロイ履歴に存在しない以前のデプロイを指定すると、ロールバックでエラーが返されます。

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
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

特定のデプロイを再デプロイするには、`--rollback-on-error` パラメーターを使用してデプロイの名前を指定します。 指定したデプロイは成功している必要があります。

```azurecli-interactive
az deployment group create \
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

## <a name="next-steps"></a>次のステップ

- 完全および増分のモードについては、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
