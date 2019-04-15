---
title: Azure Logic Apps のデプロイメント テンプレートの作成 | Microsoft Docs
description: ロジック アプリをデプロイするための Azure Resource Manager テンプレートの作成
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894236"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>ロジック アプリをデプロイするための Azure Resource Manager テンプレートの作成

ロジック アプリを作成する場合、ロジック アプリの定義を [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-overview.md)に展開することができます。 デプロイを自動化するために、このテンプレートを使用するには、[パラメーター ファイル](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)経由で、デプロイに使用するリソースとパラメーターを定義し、パラメーター値を指定します。
これにより、目的の任意の環境または Azure リソース グループにロジック アプリを簡単にデプロイできます。 

Azure Logic Apps には、ロジック アプリの作成だけでなく、デプロイに使用するリソースとパラメーターの定義にも再利用できる[事前構築済みのロジック アプリ Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)が用意されています。 このテンプレートを独自のビジネス シナリオで使用することも、要件に合わせてカスタマイズすることもできます。 [Resource Manager テンプレートの構造と構文](../azure-resource-manager/resource-group-authoring-templates.md)に関する詳細を確認してください。 JSON の構文とプロパティについては、[Microsoft.Logic のリソースの種類](/azure/templates/microsoft.logic/allversions)に関する記事を参照してください。

Azure Resource Manager テンプレートの詳細については、次の記事を参照してください。

* [Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)
* [クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>ロジック アプリの構造

ロジック アプリ定義には、これらの基本セクションがあり、"デザイナー ビュー" から"コード ビュー" に切り替えるか、または [Azure Resource Explorer](http://resources.azure.com) などのツールを使用して、表示できます。 ロジック アプリ定義では Javascript Object Notation (JSON) が使われているため、JSON 構文とプロパティの詳細については、「[Microsoft.Logic resource types](/azure/templates/microsoft.logic/allversions)」(Microsoft.Logic リソースの種類) を参照してください。

* **ロジック アプリ リソース**:ロジック アプリの場所やリージョン、価格プラン、ワークフロー定義などの情報について説明します。

* **ワークフロー定義**:ロジック アプリのトリガーとアクション、および Azure Logic Apps サービスがワークフローを実行する方法について説明します。 コード ビューの `definition` セクションでワークフロー定義を見つけることができます。

* **接続**:ロジック アプリで管理対象コネクタを使用する場合、`$connections` セクションは、接続文字列やアクセス トークンなど、ロジック アプリと他のシステムやサービス間でこれらの接続に関するメタデータを安全に保存するその他のリソースを参照します。 ロジック アプリ定義内で、これらの接続への参照は、ロジック アプリ定義の `parameters` セクション内に表示されます。

Azure リソース グループ デプロイで使用できるロジック アプリ テンプレートを作成するには、リソースを定義し、必要に応じてパラメーター化する必要があります。 たとえば、開発環境、テスト環境、運用環境にデプロイする場合、環境ごとに異なる接続文字列を SQL データベースに使用すると効果的です。
または、異なるサブスクリプションやリソース グループ内でデプロイすることもできます。

## <a name="create-logic-app-deployment-templates"></a>ロジック アプリ デプロイ テンプレートの作成

有効なロジック アプリ デプロイ テンプレートを作成する最も簡単な方法として、Visual Studio と Azure Logic Apps Tools for Visual Studio 拡張を使用します。 Azure portal から Visual Studio にロジック アプリをダウンロードすることによって、任意の Azure サブスクリプションと場所で使用できる有効なデプロイ テンプレートを取得します。 さらに、ロジック アプリをダウンロードすると、テンプレートに埋め込まれているロジック アプリ定義が自動的にパラメーター化されます。
Visual Studio でのロジック アプリの作成と管理の詳細については、[Visual Studio によるロジック アプリの作成](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)と [Visual Studio によるロジック アプリの管理](../logic-apps/manage-logic-apps-with-visual-studio.md)に関するページを参照してください。

このトピックのガイダンスに従って、Visual Studio またはテンプレートと必要なパラメーターを手動で作成する以外に、[ロジック アプリ テンプレートの作成用の PowerShell モジュール](https://github.com/jeffhollan/LogicAppTemplateCreator)を使用することもできます。 このオープン ソース モジュールは、最初にロジック アプリとロジック アプリで使用している接続を評価します。 次に、モジュールは、デプロイに必要なパラメーターでテンプレート リソースを生成します。 たとえば、Azure Service Bus キューからメッセージを受信し、Azure SQL データベースにデータを追加するロジック アプリがあるとします。 モジュール ツールはすべてのオーケストレーション ロジックを保存し、SQL および Service Bus 接続文字列をパラメーター化して、デプロイ時にそれらの値を設定できるようにします。

> [!IMPORTANT]
> 接続はロジック アプリと同じリソース グループ内に存在している必要があります。
> PowerShell モジュールがあらゆる Azure テナントやサブスクリプション アクセス トークンで機能するように、[Azure Resource Manager クライアント ツール](https://github.com/projectkudu/ARMClient)でモジュールを使用します。 詳細については、ARMClient について詳しく説明しているこの [Azure Resource Manager クライアント ツールに関する記事](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)を参照してください。

### <a name="install-powershell-module-for-logic-app-templates"></a>ロジック アプリ テンプレート用 PowerShell モジュールのインストール

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)からモジュールをインストールする最も簡単な方法として、このコマンドを使用します。

`Install-Module -Name LogicAppTemplate`

PowerShell モジュールを手動でインストールすることもできます。

1. 最新の [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases) をダウンロードします。

1. PowerShell モジュール フォルダー (通常は `%UserProfile%\Documents\WindowsPowerShell\Modules`) にフォルダーを解凍します。

### <a name="generate-logic-app-template---powershell"></a>ロジック アプリ テンプレートの生成 - PowerShell

PowerShell をインストールした後は、次のコマンドを使用してテンプレートを生成できます。

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` は Azure サブスクリプション ID です。 この行はまず、ARMClient を使用してアクセス トークンを取得し、このアクセス トークンを PowerShell スクリプトにパイプして、JSON ファイルでテンプレートを作成します。

## <a name="parameters-in-logic-app-templates"></a>ロジック アプリ テンプレートのパラメーター

ロジック アプリ テンプレートを作成したら、必要な任意のパラメーターを追加および編集することができます。 テンプレートには、複数の `parameters` セクションがあります。たとえば、次のようになります。 

* ロジック アプリのワークフロー定義には、独自の [`parameters` セクション](../logic-apps/logic-apps-workflow-definition-language.md#parameters)があり、ここには、ロジック アプリがデプロイ時に入力を受け入れるために使用するすべてのパラメーターを定義できます。

* Resource Manager テンプレートには、ロジック アプリの `parameters` セクションとは別に、独自の [`parameters` セクション](../azure-resource-manager/resource-group-authoring-templates.md#parameters)があります。 例: 

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

たとえば、ロジック アプリ定義で、Azure 関数または入れ子になったロジック アプリ ワークフローを表すリソース ID を参照しており、そのリソース ID をロジック アプリと共に単一のデプロイとしてデプロイするとします。 その ID をリソースとしてテンプレートに追加し、その ID をパラメーター化できます。 各 Azure リソース グループと共にデプロイするカスタム API または OpenAPI エンドポイント (以前の "Swagger") への参照に、この同じアプローチを使用できます。

デプロイ テンプレートでパラメーターを使用する場合、Logic Apps デザイナーで、それらのパラメーターを正しく表示できるように、次のガイダンスに従ってください。

* パラメーターは、次のトリガーとアクションでのみ使用します。

  * Azure Functions アプリ
  * 入れ子になったまたは子ロジック アプリ ワークフロー
  * API Management 呼び出し
  * API 接続ランタイム URL
  * API 接続パス

* パラメーターを定義する場合、`defaultValue` プロパティ値を使用して、既定値を指定してください。たとえば、次のように指定します。

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* テンプレート内の機密情報を保護するか、非表示にするには、パラメーターをセキュリティで保護します。 [セキュリティ保護されたパラメーターの使用方法](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)の詳細を確認してください。

次の例に、Azure Service Bus の "メッセージ送信" アクションをパラメーター化する方法を示します。

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>依存リソースの参照

ロジック アプリで依存リソースを参照する必要がある場合は、[Azure Resource Manager テンプレート関数](../azure-resource-manager/resource-group-template-functions.md)を、ロジック アプリのデプロイ テンプレートで使うことができます。 たとえば、ロジック アプリで、ロジック アプリと共にデプロイするパートナー、契約、およびその他アーティファクトの定義で、Azure 関数または統合アカウントを参照するとします。
`parameters`、`variables`、`resourceId`、`concat` などの Resource Manager テンプレート関数を使用できます。

次の例に、これらのパラメーターを定義することによって、Azure 関数のリソース ID を置換する方法を示します。

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

次に、Azure 関数を参照するときにこれらのパラメーターを使用する方法を示します。

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>リソース グループ プロジェクトにロジック アプリを追加する

既存の Azure リソース グループ プロジェクトがある場合は、[JSON アウトライン] ウィンドウを使用してそのプロジェクトにロジック アプリを追加できます。 また、以前に作成したアプリと共に別のロジック アプリを追加することもできます。

1. ソリューション エクスプローラーで、`<template>.json` ファイルを開きます。

2. **[表示]** メニューの **[その他のウィンドウ]** > **[JSON アウトライン]** を選択します。

3. テンプレート ファイルにリソースを追加するには、[JSON アウトライン] ウィンドウの上部にある **[リソースの追加]** を選択します。 または、[JSON アウトライン] ウィンドウで、**[リソース]** を右クリックして、**[新しいリソースの追加]** を選びます。

   ![[JSON アウトライン] ウィンドウ](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. **[リソースの追加]** ダイアログ ボックスで、**[ロジック アプリ]** を探して選びます。 ロジック アプリの名前を指定し、**[追加]** を選びます。

   ![リソースの追加](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>サポートを受ける

質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ロジック アプリ テンプレートのデプロイ](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
