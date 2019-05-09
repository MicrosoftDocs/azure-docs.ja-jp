---
title: Azure Resource Manager テンプレートを使用してロジック アプリをデプロイする - Azure Logic Apps
description: Azure Resource Manager テンプレートを使用してロジック アプリをデプロイする
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: bbb10bf0174b6e06e28d171510345ed92b6642d9
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357088"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してロジック アプリをデプロイする

ロジック アプリをデプロイするための Azure Resource Manager テンプレートを作成したら、次の方法でテンプレートをデプロイできます。

* [Azure ポータル](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure portal を使用してデプロイする

ロジック アプリ テンプレートを Azure に自動的にデプロイするには、次の **[Azure に配置する]** ボタンを選択します。これにより、Azure portal にサインインし、ロジック アプリに関する情報の入力を求められます。 その後、ロジック アプリ テンプレートまたはパラメーターに必要な変更を加えることができます。

[![DAzure に配置する(./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

たとえば、Azure portal にサインインした後にこの情報の入力を求められます。

* Azure サブスクリプション名
* 使用するリソース グループ
* ロジック アプリの場所
* ロジック アプリの名前
* テスト URI
* 規定の使用条件への同意

詳細については、「[Deploy resources with Azure Resource Manager templates and the Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md)」 (Azure Resource Manager テンプレートと Azure Portal を使用したリソースのデプロイ) を参照してください。

## <a name="authorize-oauth-connections"></a>OAuth 接続を作成する

デプロイ後、ロジック アプリは有効なパラメーターを使用してエンド ツー エンドで動作します。 ただし、有効なアクセス トークンを生成するには、OAuth 接続を承認する必要があります。 自動デプロイの場合は、[GitHub LogicAppConnectionAuth プロジェクトにおけるこのスクリプトの例](https://github.com/logicappsio/LogicAppConnectionAuth)のように、各 OAuth 接続に同意するスクリプトを使用できます。 Logic Apps デザイナーでロジック アプリを開くことで、Azure portal または Visual Studio で OAuth 接続を承認することもできます。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell でのデプロイ

特定の *Azure リソース グループ*にデプロイするには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

特定の Azure サブスクリプションにデプロイするには、次のコマンドを使用します。

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

特定の *Azure リソース グループ*にデプロイするには、次のコマンドを使用します。

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

特定の Azure サブスクリプションにデプロイするには、次のコマンドを使用します。

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

詳細については、以下のトピックを参照してください。 

* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps を使用してデプロイする

ロジック アプリ テンプレートをデプロイし、環境を管理するために、チームは一般的に [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) の [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) のようなツールを使用します。 Azure Pipelines は、あらゆるビルドまたはリリース パイプラインに追加できる [[Azure リソース グループの配置] タスク](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) を提供します。
リリース パイプラインをデプロイおよび生成するための承認には、Azure Active Directory (AD) [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)も必要です。 詳細は、[Azure Pipelines でのサービス プリンシパルの使用](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)に関するページを参照してください。 

Azure Pipelines を使用するための一般的な大まかな手順は次のとおりです。

1. Azure Pipelines で、空のパイプラインを作成します。

1. ロジック アプリ テンプレートやテンプレート パラメーター ファイルなど、パイプラインに必要なリソースを選択します。これらは手動で、またはビルド プロセスの一環として生成します。

1. エージェント ジョブの場合は、**[Azure リソース グループの配置]** タスクを見つけて追加します。

   ![[Azure リソース グループの配置] タスクの追加](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. [サービス プリンシパル](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)を使用して構成します。 

1. ロジック アプリ テンプレートとテンプレート パラメーター ファイルへの参照を追加します。

1. 必要に応じて、その他の環境、自動化されたテスト、承認者のために、リリース プロセスの手順の構築を続行します。

## <a name="get-support"></a>サポートを受ける

質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ロジック アプリの監視](../logic-apps/logic-apps-monitor-your-logic-apps.md)
