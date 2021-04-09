---
title: クイック スタート:Automation アカウントを作成する - Azure テンプレート
titleSuffix: Azure Automation
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用して Automation アカウントを作成する方法について説明します。
services: automation
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 4d7d4b5af9b86fe5116dc29f6ed1a789cdbf2047
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581088"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>クイック スタート:ARM テンプレートを使用して Automation アカウントを作成する

Azure Automation は、Azure 環境と非 Azure 環境を一貫性をもって管理するクラウドベースのオートメーションと構成サービスを提供します。 このクイックスタートでは、Automation アカウントを作成する Azure Resource Manager テンプレート (ARM テンプレート) をデプロイする方法について説明します。 ARM テンプレートを使用すると、他のデプロイ方法より手順が減ります。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このサンプル テンプレートでは、次のことを実行します。

* Azure Monitor Log Analytics ワークスペースの作成を自動化します。
* Azure Automation アカウントの作成を自動化します。
* Automation アカウントを Log Analytics ワークスペースにリンクします。
* サンプルの Automation Runbook をアカウントに追加します。

>[!NOTE]
>ARM テンプレートを使用する場合、Automation の実行アカウントの作成はサポートされません。 ポータルからまたは PowerShell を使用して実行アカウントを手動で作成するには、[実行アカウントの作成](create-run-as-account.md)に関するページを参照してください。

これらの手順を完了したら、Runbook ジョブの状態とジョブ ストリームを、リンクされた Log Analytics ワークスペースに送信するために、Automation アカウントの[診断設定を構成する](automation-manage-send-joblogs-log-analytics.md)必要があります。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-automation/)からのものです。

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API のバージョン

次の表は、この例で使用されているリソースの API バージョンの一覧です。

| リソース | リソースの種類 | API バージョン |
|:---|:---|:---|
| [ワークスペース](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation アカウント](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [ワークスペースのリンクされたサービス](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>テンプレートを使用する前に

次の項目を指定する JSON パラメーター テンプレートが構成されています。

* ワークスペースの名前。
* ワークスペースを作成するリージョン。
* Automation アカウントの名前。
* Automation アカウントを作成するリージョン。

テンプレートの次のパラメーターには、Log Analytics ワークスペースの既定値が設定されます。

* *sku* の既定値は、2018 年 4 月の価格モデルでリリースされた 1 GB あたりの価格レベルです。
* *dataRetention* の既定値は 30 日です。

>[!WARNING]
>2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは *PerGB2018* のみです。
>

JSON テンプレートでは、環境で標準構成として使用される可能性のある他のパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作の詳細については、[ARM テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)に関するページを参照してください。

Azure Automation と Azure Monitor を初めて使用する場合は、次の構成の詳細を理解しておくことが重要です。 これは、新しい Automation アカウントにリンクされた Log Analytics ワークスペースを作成、構成、および使用するときに、エラーを回避するのに役立ちます。

* [追加の詳細](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)を確認し、アクセス制御モード、価格レベル、保有期間、容量予約レベルなどのワークスペースの構成オプションについて十分に理解します。

* [ワークスペース マッピング](how-to/region-mappings.md)を確認し、サポートされているリージョンを、インラインまたはパラメーター ファイルで指定します。 サブスクリプション内で Log Analytics ワークスペースと Automation アカウントをリンクすることは、特定のリージョンでのみサポートされています。

* Azure Monitor ログを初めて使用し、ワークスペースをまだデプロイしていない場合は、[ワークスペースの設計ガイダンス](../azure-monitor/logs/design-logs-deployment.md)を確認してください。 これは、アクセスの制御について学習し、組織に推奨される設計の実装戦略を理解するのに役立ちます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートでは、Azure Automation アカウントと Log Analytics ワークスペースを作成し、Automation アカウントをワークスペースにリンクします。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. 値を入力します。

3. デプロイが完了するまでに数分かかる場合があります。 完了すると、次のように出力されます。

    ![デプロイ完了時の結果の例](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal で、先ほど作成した Automation アカウントを開きます。 

3. 左側のペインから、 **[Runbook]** を選択します。 **[Runbook]** ページに、Automation アカウントで作成された 3 つのチュートリアル Runbook が表示されています。

    ![Automation アカウントで作成されたチュートリアル Runbook](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. 左側のペインから、 **[リンクされたワークスペース]** を選択します。 **[リンクされたワークスペース]** ページに、先ほど指定した対象の Automation アカウントにリンクされている Log Analytics ワークスペースが表示されます。

    ![Log Analytics ワークスペースにリンクされた Automation アカウント](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、Log Analytics ワークスペースから Automation アカウントのリンクを解除し、Automation アカウントとワークスペースを削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Automation アカウントと Log Analytics ワークスペースを作成し、それらをリンクしました。

さらに学習するには、Azure Automation のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Automation のチュートリアル](learn/automation-tutorial-runbook-graphical.md)