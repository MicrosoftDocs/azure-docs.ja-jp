---
title: Azure Resource Manager テンプレートを使用し、Web アプリのプライベート エンドポイントをデプロイする
description: ARM テンプレートを使用して Web アプリ用のプライベート エンドポイントをデプロイする方法について説明します。
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652014"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して App Service アプリを作成し、プライベート エンドポイントをデプロイする

このクイックスタートでは、Azure Resource Manager (ARM) テンプレートを使用して Web アプリを作成し、それをプライベート エンドポイントで公開します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>前提条件

アクティブなサブスクリプションを含む Azure アカウントが必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

このテンプレートでは、Azure Web アプリのプライベート エンドポイントを作成します。

### <a name="review-the-template"></a>テンプレートを確認する

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure Resource Manager テンプレートを Azure にデプロイする方法を次に示します。

1. Azure にサインインしてテンプレートを開くためには、このリンクを選択します。[Azure にデプロイされている](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)。 このテンプレートでは、仮想ネットワーク、Web アプリ、プライベート エンドポイント、プライベート DNS ゾーンを作成します。
2. リソース グループを選択または作成します。
3. Web アプリの名前、Azure App Service プラン、プライベート エンドポイントを入力します。
5. 使用条件に関する声明を読みます。 同意する場合は **[上記の使用条件に同意する]**  >  **[購入]** を選択します。 デプロイが完了するまでに数分かかる場合があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート エンドポイントと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、プライベート エンドポイントとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

- その他の Azure App Service Web Apps 用 Azure Resource Manager テンプレートは、[ARM テンプレートのサンプル](../samples-resource-manager-templates.md)に関するページにあります。
