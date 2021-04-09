---
title: デプロイのためのロジック アプリ テンプレートの作成
description: Azure Logic Apps でのデプロイを自動化するための Azure Resource Manager テンプレートを作成する方法を説明します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 4535e6bf11f8c2abf20b1b323925c3fc3299d362
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90971793"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Logic Apps でのデプロイを自動化するために Azure Resource Manager テンプレートを作成する

この記事では、ロジック アプリの作成とデプロイを自動化するために、ロジック アプリ用の [Azure Resource Manager テンプレート](../azure-resource-manager/management/overview.md)を作成する方法について説明します。 デプロイに必要なワークフロー定義やその他のリソースを含むテンプレートの構造と構文の概要については、「[Overview: Automate deployment for logic apps with Azure Resource Manager templates](logic-apps-azure-resource-manager-templates-overview.md)」 (概要: Azure Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する) を参照してください。

Azure Logic Apps には、ロジック アプリの作成だけでなく、デプロイに使用するリソースとパラメーターの定義にも再利用できる[あらかじめ構築されたロジック アプリ Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)が用意されています。 このテンプレートを独自のビジネス シナリオで使用することも、要件に合わせてカスタマイズすることもできます。

> [!IMPORTANT]
> テンプレート内の接続で使用する Azure のリソース グループと場所が、ロジック アプリと同じものであることを確認してください。

Azure Resource Manager テンプレートの詳細については、これらのトピックを参照してください。

* [Azure Resource Manager テンプレートの構造と構文](../azure-resource-manager/templates/template-syntax.md)
* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)
* [クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Visual Studio を使用したテンプレートの作成

ほぼデプロイできる状態の有効なパラメーター化ロジック アプリ テンプレートを作成するには、Visual Studio (無料の Community Edition 以上) と Azure Logic Apps Tools for Visual Studio を使用する方法が最も簡単です。 その後に、[Visual Studio でロジック アプリを作成](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)するか、または [Azure portal で既存のロジック アプリを見つけて Visual Studio にダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。

ロジック アプリをダウンロードすると、ロジック アプリの定義やその他のリソース (接続など) が含まれたテンプレートを取得できます。 また、このテンプレートは、ロジック アプリとその他のリソースのデプロイに使用される値の *パラメーター化* (パラメーターの定義) を行います。 これらのパラメーターには、独立したパラメーター ファイルで値を提供できます。 これにより、デプロイのニーズに応じてこれらの値をより簡単に変更できます。 詳細については、以下のトピックを参照してください。

* [Visual Studio でロジック アプリを作成する](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visual Studio でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Azure PowerShell を使用したテンプレートの作成

Resource Manager テンプレートを作成するには、Azure PowerShell と [LogicAppTemplate モジュール](https://github.com/jeffhollan/LogicAppTemplateCreator)を使用します。 このオープン ソース モジュールは、最初にロジック アプリとロジック アプリで使用している接続を評価します。 次に、モジュールは、デプロイに必要なパラメーターでテンプレート リソースを生成します。

たとえば、Azure Service Bus キューからメッセージを受信し、Azure SQL Database にデータをアップロードするロジック アプリがあるとします。 このモジュールは、オーケストレーション ロジック全体を保存し、SQL および Service Bus の接続文字列をパラメーター化することで、それらの値をデプロイのニーズに応じて提供し、変更できるようにします。

これらのサンプルでは、Azure Resource Manager テンプレート、Azure DevOps の Azure Pipelines、および Azure PowerShell を使用してロジック アプリを作成およびデプロイする方法が示されています。

* [サンプル:Azure Logic Apps から Azure Service Bus キューへの接続](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps から Azure Storage アカウントへの接続](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps に対する関数アプリ アクションの設定](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [サンプル:Azure Logic Apps から統合アカウントへの接続](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell モジュールをインストールする

1. [Azure PowerShell](/powershell/azure/install-az-ps) がまだインストールされていない場合は、インストールします。

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/LogicAppTemplate)から LogicAppTemplate モジュールをインストールする最も簡単な方法は、次のコマンドを実行することです。

   ```powershell
   Install-Module -Name LogicAppTemplate
   ```

   最新バージョンに更新するには、次のコマンドを実行します。

   ```powershell
   Update-Module -Name LogicAppTemplate
   ```

または、手動でインストールする場合は、GitHub に示されている [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) での手順に従ってください。

### <a name="install-azure-resource-manager-client"></a>Azure Resource Manager クライアントのインストール

LogicAppTemplate モジュールが Azure のテナントとサブスクリプションのアクセス トークンで機能するようにするには、[Azure Resource Manager クライアント ツール](https://github.com/projectkudu/ARMClient)をインストールします。これは、Azure Resource Manager API を呼び出すシンプルなコマンドライン ツールです。

このツールで `Get-LogicAppTemplate` コマンドを実行すると、コマンドがまず ARMClient ツールを通じてアクセス トークンを取得し、トークンを PowerShell スクリプトにパイプし、テンプレートを JSON ファイルとして作成します。 このツールの詳細については、こちらの [Azure Resource Manager クライアント ツールに関する記事](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)を参照してください。

### <a name="generate-template-with-powershell"></a>PowerShell を使用したテンプレートの生成

LogicAppTemplate モジュールと [Azure CLI](/cli/azure/) をインストールした後にテンプレートを生成するには、次の PowerShell コマンドを実行します。

```powershell
$parameters = @{
    Token = (az account get-access-token | ConvertFrom-Json).accessToken
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

推奨事項に従って [Azure Resource Manager クライアント ツール](https://github.com/projectkudu/ARMClient)からトークンをパイプ処理するには、代わりに次のコマンドを実行します。この中の `$SubscriptionId` はお使いの Azure サブスクリプション ID です。

```powershell
$parameters = @{
    LogicApp = '<logic-app-name>'
    ResourceGroup = '<Azure-resource-group-name>'
    SubscriptionId = $SubscriptionId
    Verbose = $true
}

armclient token $SubscriptionId | Get-LogicAppTemplate @parameters | Out-File C:\template.json
```

抽出後に、次のコマンドを実行してテンプレートからパラメーター ファイルを作成できます。

```powershell
Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Azure Key Vault 参照 (静的のみ) を使用して抽出する場合は、次のコマンドを実行します。

```powershell
Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| パラメーター | 必須 | 説明 |
|------------|----------|-------------|
| TemplateFile | はい | テンプレート ファイルのファイル パス |
| KeyVault | いいえ | 有効なキー コンテナー値の処理方法を記述する列挙。 既定では、 `None`です。 |
||||

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ロジック アプリ テンプレートのデプロイ](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
