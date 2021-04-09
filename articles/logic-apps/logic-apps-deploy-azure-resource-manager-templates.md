---
title: ロジック アプリ テンプレートのデプロイ
description: Azure Logic Apps 用に作成された Azure Resource Manager テンプレートをデプロイする方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705338"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps 用の Azure Resource Manager テンプレートをデプロイする

ロジック アプリ用の Azure Resource Manager テンプレートを作成したら、次の方法でテンプレートをデプロイできます。

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure リソース マネージャー REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure portal を使用してデプロイする

ロジック アプリ テンプレートを Azure に自動的にデプロイするには、次の **[Azure に配置する]** ボタンを選択します。これにより、Azure portal にサインインし、ロジック アプリに関する情報の入力を求められます。 その後、ロジック アプリ テンプレートまたはパラメーターに必要な変更を加えることができます。

[![Azure へのデプロイ](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

たとえば、Azure portal にサインインした後に次の情報の入力を求められます。

* Azure サブスクリプション名
* 使用するリソース グループ
* ロジック アプリの場所
* ロジック アプリの名前
* テスト URI
* 規定の使用条件への同意

詳細については、以下のトピックを参照してください。

* [概要:Azure Resource Manager テンプレートを使用したロジック アプリのデプロイの自動化](logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager テンプレートと Azure portal を使用してリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Visual Studio でのデプロイ

Visual Studio を使用して作成した Azure Resource Group プロジェクトからロジック アプリ テンプレートをデプロイするには、[これらの手順に従って手動でロジック アプリを Azure にデプロイします](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell でのデプロイ

特定の "*Azure リソース グループ*" にデプロイするには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

詳細については、以下のトピックを参照してください。

* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

特定の "*Azure リソース グループ*" にデプロイするには、次のコマンドを使用します。

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

詳細については、以下のトピックを参照してください。

* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps を使用してデプロイする

ロジック アプリ テンプレートをデプロイし、環境を管理するために、チームは一般的に [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services) の [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) のようなツールを使用します。 Azure Pipelines は、あらゆるビルドまたはリリース パイプラインに追加できる [[Azure リソース グループの配置] タスク](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) を提供します。 リリース パイプラインをデプロイおよび生成するための承認には、Azure Active Directory (AD) [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)も必要です。 詳細は、[Azure Pipelines でのサービス プリンシパルの使用](/azure/devops/pipelines/library/connect-to-azure)に関するページを参照してください。

Azure Pipelines と Azure Resource Manager テンプレートの継続的インテグレーションおよび継続的デプロイ (CI/CD) の詳細については、以下のトピックとサンプルを参照してください。

* [Azure Pipelines を使用した Resource Manager テンプレートの統合](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [チュートリアル:Azure Pipelines を使用した Azure Resource Manager テンプレートの継続的インテグレーション](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [サンプル:Azure Logic Apps から Azure Service Bus キューに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps から Azure Storage アカウントに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps の関数アプリ アクションを設定し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps から統合アカウントに接続し、Azure DevOps に Azure Pipelines を使用してデプロイする](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps を使用した Azure Pipelines の調整](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Azure Pipelines を使用するための一般的な大まかな手順は次のとおりです。

1. Azure Pipelines で、空のパイプラインを作成します。

1. ロジック アプリ テンプレートやテンプレート パラメーター ファイルなど、パイプラインに必要なリソースを選択します。これらは手動で、またはビルド プロセスの一環として生成します。

1. エージェント ジョブの場合は、 **[Azure リソース グループの配置]** タスクを見つけて追加します。

   ![[Azure リソース グループの配置] タスクの追加](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. [サービス プリンシパル](/azure/devops/pipelines/library/connect-to-azure)を使用して構成します。

1. ロジック アプリ テンプレートとテンプレート パラメーター ファイルへの参照を追加します。

1. 必要に応じて、その他の環境、自動化されたテスト、承認者のために、リリース プロセスの手順の構築を続行します。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth 接続を作成する

デプロイ後、ロジック アプリは有効なパラメーターを使用してエンドツーエンドで動作しますが、[資格情報を認証する](../active-directory/develop/authentication-vs-authorization.md)ための有効なアクセス トークンを生成するには、引き続き事前承認された OAuth 接続を承認または使用する必要があります。 ただし、API 接続リソースのデプロイおよび認証は 1 回だけ行う必要があります。つまり、接続情報を更新する必要がある場合を除き、それらの接続リソースを後続のデプロイに含める必要はありません。 継続的インテグレーションと継続的デプロイ パイプラインを使用する場合は、更新されたロジック アプリ リソースのみをデプロイし、接続を毎回再認証する必要はありません。

接続の承認に関するいくつかの推奨事項を次に示します。

* 同じリージョンにあるロジック アプリ全体で、API 接続リソースを事前承認して共有します。 API 接続は、ロジック アプリとは別に Azure リソースとして存在します。 ロジック アプリは API 接続リソースに依存しますが、API 接続リソースはロジック アプリに依存せず、依存するロジック アプリを削除した後も維持されます。 また、ロジック アプリは他のリソース グループに存在する API 接続を使用できます。 ただし、ロジック アプリ デザイナーは、使用するロジック アプリと同じリソース グループ内の API 接続の作成のみサポートします。

  > [!NOTE]
  > API 接続の共有を検討している場合、ソリューションによって[調整に関する問題を解決できる](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling)ことを確認してください。 調整は接続レベルで行われるため、複数のロジック アプリにわたって同じ接続を再利用すると、調整の問題が発生する可能性が高くなることがあります。

* 多要素認証を必要とするサービスやシステムがシナリオに含まれている場合を除き、PowerShell スクリプトを使用して、承認と同意が既に提供されたアクティブなブラウザー セッションを持つ仮想マシン上で継続的インテグレーション ワーカーを通常のユーザー アカウントとして実行することによって、各 OAuth 接続への同意を提供することができます。 たとえば、[Logic Apps GitHub リポジトリの LogicAppConnectionAuth プロジェクト](https://github.com/logicappsio/LogicAppConnectionAuth)によって提供されるサンプル スクリプトを再利用できます。

* Azure portal または Visual Studio のいずれかで、Logic App Designer でロジック アプリを開くことによって OAuth 接続を手動で承認します。

* 接続を承認するのではなく Azure Active Directory (Azure AD) [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)を使用する場合は、[ロジック アプリ テンプレートでサービス プリンシパル パラメーターを指定する方法](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)のページを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ロジック アプリの監視](../logic-apps/monitor-logic-apps.md)
