---
title: Azure Resource Manager テンプレート (ARM テンプレート) を使用して特定のセキュリティ アラートに対するセキュリティ自動化を作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して、特定の Security Center アラートによってトリガーされる、ロジック アプリをトリガーする Azure Security Center の自動化を作成する方法について説明します。
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: e53a5c4840cee0651090bec0b68cc53e13102299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705491"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して特定のセキュリティ アラートへの自動応答を作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Security Center が特定のセキュリティ アラートを受信したときにロジック アプリをトリガーするワークフローの自動化を作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

Azure Security Center のワークフロー自動化機能を使用するために必要なロールとアクセス許可の一覧については、「[ワークフローの自動化](workflow-automation.md)」をご覧ください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/)からのものです。

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>関連リソース

- [**Microsoft.Security/automations**](/azure/templates/microsoft.security/automations): 特定の文字列を含む Azure Security Center アラートを受信したときにロジック アプリをトリガーする自動化。
- [**Microsoft.Logic/workflows**](/azure/templates/microsoft.logic/workflows): トリガー可能な空のロジック アプリ。

その他の Security Center クイックスタート テンプレートについては、[コミュニティ提供のテンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular)を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

- **PowerShell**:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI**:

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **ポータル**:

  [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  このデプロイ オプションの詳細については、「[デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする](../azure-resource-manager/templates/deploy-to-azure-button.md)」をご覧ください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用して、ワークフローの自動化がデプロイされていることを確認します。

1. [Azure portal](https://portal.azure.com) から **Security Center** を開きます。
1. 上部のメニュー バーからフィルター アイコンを選択し、新しいワークフロー自動化をデプロイした特定のサブスクリプションを選択します。
1. Security Center のサイドバーから **[ワークフローの自動化]** を開き、新しい自動化を確認します。
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="構成済みの自動化の一覧" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > サブスクリプションに多数のワークフロー自動化がある場合は、 **[名前でフィルター]** オプションを使用します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ワークフローの自動化が不要になったら、Azure portal を使用して削除します。

1. [Azure portal](https://portal.azure.com) から **Security Center** を開きます。
1. 上部のメニュー バーからフィルター アイコンを選択し、新しいワークフロー自動化をデプロイした特定のサブスクリプションを選択します。
1. Security Center のサイドバーから **[ワークフローの自動化]** を開き、削除する自動化を見つけます。
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="ワークフローの自動化を削除する手順" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. 削除する項目のチェック ボックスをオンにします。
1. ツール バーから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
