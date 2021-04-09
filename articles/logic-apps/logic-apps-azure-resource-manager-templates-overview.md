---
title: 概要 - Azure Logic Apps のデプロイを自動化する
description: Azure Logic Apps のデプロイを自動化するための Azure Resource Manager テンプレートについて説明します
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: ec72431d927fd59677075e7adfdf7df171574882
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772945"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>概要:Azure Resource Manager テンプレートを使用して Azure Logic Apps のデプロイを自動化する

ロジック アプリの作成とデプロイを自動化する準備ができたら、ロジック アプリの基になるワークフロー定義を [Azure Resource Manager テンプレート](../azure-resource-manager/management/overview.md)に展開することができます。 このテンプレートでは、ロジック アプリをプロビジョニングおよびデプロイするためのインフラストラクチャ、リソース、パラメーター、およびその他の情報を定義します。 デプロイ時に変動する値に対してパラメーターを定義する ("*パラメーター化*" とも呼ばれます) ことにより、さまざまなデプロイ ニーズに応じてロジック アプリを繰り返し一貫した方法でデプロイすることができます。

たとえば、開発環境、テスト環境、運用環境にデプロイする場合、環境ごとに異なる接続文字列を使用すると効果的です。 異なる接続文字列を受け取るテンプレート パラメーターを宣言した後、それらの文字列を別々の[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)に格納することができます。 こうすることで、テンプレートを更新して再デプロイしなくても、これらの値を変更することができます。 パスワードやシークレットのような機密性の高いまたはセキュリティ保護が必要なパラメーター値を使用しているシナリオでは、これらの値を [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) に格納し、パラメーター ファイルによってこれらの値を取得することができます。 ただし、このようなシナリオでは、再デプロイを行って現在の値を取得することをお勧めします。

この概要では、ロジック アプリのワークフロー定義を含む Resource Manager テンプレートの属性について説明します。 テンプレートとワークフロー定義には、どちらも JSON 構文を使用しますが、ワークフロー定義は[ワークフロー定義言語スキーマ](../logic-apps/logic-apps-workflow-definition-language.md)にも従っているため、いくつか相違点があります。 たとえば、テンプレートの式とワークフロー定義の式では、[パラメーターを参照する](#parameter-references)方法および使用できる値に違いがあります。

> [!TIP]
> ほぼデプロイできる状態の有効なパラメーター化ロジック アプリ テンプレートを取得するには、Visual Studio (無料の Community Edition 以上) と Azure Logic Apps Tools for Visual Studio を使用する方法が最も簡単です。 その後、[Visual Studio でロジック アプリを作成](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)するか、または [Azure portal で既存のロジック アプリを見つけて Visual Studio にダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。
>
> または、[Azure PowerShell の LogicAppTemplate モジュール](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)を使ってロジック アプリ テンプレートを作成することもできます。

このトピックのサンプル ロジック アプリでは、新しい電子メールが届いたときに発動する [Office 365 Outlook トリガー](/connectors/office365/)と、電子メール本文の BLOB を作成してその BLOB を Azure ストレージ コンテナーにアップロードする [Azure Blob Storage アクション](/connectors/azureblob/)が使用されています。 また、このサンプルでは、デプロイ時に変動する値をパラメーター化する方法も示されています。

Resource Manager テンプレートの詳細については、次のトピックをご覧ください。

* [Azure Resource Manager テンプレートの構造と構文](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager テンプレートのベスト プラクティス](../azure-resource-manager/templates/template-best-practices.md)
* [クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](../azure-resource-manager/templates/templates-cloud-consistency.md)

ロジック アプリ、統合アカウント、統合アカウントの成果物に固有のテンプレート リソースの情報については、「[Microsoft.Logic resource types](/azure/templates/microsoft.logic/allversions)」(Microsoft.Logic リソースの種類) を参照してください。

サンプルのロジック アプリ テンプレートについては、次の例をご覧ください。

* このトピックのサンプルで使用されている[完全なテンプレート](#full-example-template)
* GitHub にある[サンプルのクイックスタート ロジック アプリ テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create)

Logic Apps REST API については、最初に [Azure Logic Apps REST API の概要](/rest/api/logic)をご覧ください。

<a name="template-structure"></a>

## <a name="template-structure"></a>テンプレートの構造

最上位レベルでは、Resource Manager テンプレートの構造は次のようになっています。詳しくは、[Azure Resource Manager テンプレートの構造と構文](../azure-resource-manager/templates/template-syntax.md)に関するトピックをご覧ください。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

ロジック アプリ テンプレートでは、主に次のテンプレート オブジェクトを使用します。

| 属性 | 説明 |
|-----------|-------------|
| `parameters` | Azure でデプロイするリソースを作成およびカスタマイズするときに使用する値を受け取る[テンプレート パラメーター](../azure-resource-manager/templates/template-syntax.md#parameters)を宣言します。 たとえば、これらのパラメーターは、ロジック アプリの名前と場所、接続、およびデプロイに必要なその他のリソースの値を受け取ります。 これらのパラメーターの値は、このトピックで後述する[パラメーター ファイル](#template-parameter-files)に格納することができます。 一般的な詳細については、[Resource Manager テンプレートの構造と構文のパラメーター](../azure-resource-manager/templates/template-syntax.md#parameters)に関する記事をご覧ください。 |
| `resources` | ロジック アプリ、接続、Azure ストレージ アカウントなど、作成または更新して Azure リソース グループにデプロイする[リソース](../azure-resource-manager/templates/template-syntax.md#resources)を定義します。 一般的な詳細については、[Resource Manager テンプレートの構造と構文のリソース](../azure-resource-manager/templates/template-syntax.md#resources)に関する記事をご覧ください。 |
||||

ロジック アプリ テンプレートのファイル名には、次の形式を使用します。

**<*logic-app-name*>.json**

> [!IMPORTANT]
> テンプレート構文では大文字と小文字が区別されるため、大文字と小文字の区別には一貫性を持たせてください。 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Template parameters

ロジック アプリ テンプレートには、さまざまなレベルに存在し、さまざまな機能を実行する複数の `parameters` オブジェクトがあります。 たとえば、最上位レベルでは、Azure でリソースを作成およびデプロイするときに受け取って使用する値の[テンプレート パラメーター](../azure-resource-manager/templates/template-syntax.md#parameters)を宣言できます。次に例を示します。

* ロジック アプリ
* [マネージド コネクタ](../connectors/apis-list.md)を介して他のサービスやシステムにアクセスするためにロジック アプリが使用する接続
* ロジック アプリのデプロイに必要なその他のリソース

  たとえば、ロジック アプリで企業間 (B2B) のシナリオに[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を使用している場合、テンプレートの最上位レベルの `parameters` オブジェクトによって、その統合アカウントのリソース ID を受け取るパラメーターが宣言されます。

パラメーター定義の一般的な構造と構文を次に示します。詳しくは、[Resource Manager テンプレートの構造と構文のパラメーター](../azure-resource-manager/templates/template-syntax.md#parameters)に関する記事をご覧ください。

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

この例では、Azure でこれらのリソースを作成およびデプロイするために使用する値のテンプレート パラメーターのみが示されています。

* ロジック アプリの名前と場所
* ロジック アプリにリンクされた統合アカウントに使用する ID

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

ユーザー名、パスワード、シークレットのような機密性の高いまたはセキュリティ保護が必要な値を扱うパラメーターを除き、これらのパラメーターにはすべて `defaultValue` 属性が含まれます。ただし、場合によっては、既定値が空の値になることもあります。 これらのテンプレート パラメーターに使用されるデプロイの値は、このトピックで後述するサンプルの[パラメーター ファイル](#template-parameter-files)によって提供されます。

テンプレート パラメーターのセキュリティ保護の詳細については、次のトピックを参照してください。

* [テンプレート パラメーターに関するセキュリティの推奨事項](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [テンプレート パラメーターのセキュリティを強化する](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)

その他のテンプレート オブジェクトは、多くの場合、テンプレート パラメーターを参照することで、テンプレート パラメーターを通じて渡される値を使用できるようになります。次に例を示します。

* このトピックで後述する[テンプレートのリソース オブジェクト](#template-resources)では、[ロジック アプリのリソース定義](#logic-app-resource-definition)など、作成およびデプロイする Azure の各リソースを定義します。 多くの場合、これらのリソースでは、ロジック アプリの名前と場所や接続情報といったテンプレート パラメーターの値が使用されます。

* ロジック アプリのリソース定義のより深いレベルでは、[ワークフロー定義の parameters オブジェクト](#workflow-definition-parameters)によって、ロジック アプリの実行時に使用される値のパラメーターが宣言されます。 たとえば、HTTP トリガーが認証に使用するユーザー名とパスワードに対して、ワークフロー定義のパラメーターを宣言できます。 ワークフロー定義のパラメーターの値を指定するには、ワークフロー定義の "*外側*"、かつロジック アプリのリソース定義の "*内側*" にある `parameters` オブジェクトを使用します。 この外側の `parameters` オブジェクトでは、前に宣言したテンプレート パラメーターを参照して、デプロイ時にパラメーター ファイルから値を受け取ることができます。

パラメーターを参照する際、テンプレートの式および関数では、ワークフロー定義の式および関数とは異なる構文を使用し、動作も異なります。 これらの違いについて詳しくは、このトピックで後述する「[パラメーターの参照](#parameter-references)」をご覧ください。

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>ベスト プラクティス - テンプレート パラメーター

パラメーターの定義に関するベスト プラクティスをいくつか紹介します。

* デプロイのニーズに応じて変動する値に対してのみ、パラメーターを宣言します。 デプロイ要件が違っても変動しない値に対しては、パラメーターを宣言しないでください。

* 機密性の高い値やセキュリティ保護が必要な値を除き、すべてのパラメーターに `defaultValue` 属性を含めます。これには空の値を指定できます。 ユーザー名、パスワード、およびシークレットには、常にセキュリティで保護されたパラメーターを使用してください。 機密性の高いパラメーター値を非表示にしたり保護したりするには、次のトピックのガイダンスに従ってください。

  * [テンプレート パラメーターに関するセキュリティの推奨事項](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [テンプレート パラメーターのセキュリティを強化する](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)

* テンプレート パラメーター名をワークフロー定義パラメーター名と区別するために、テンプレート パラメーターには `TemplateFabrikamPassword` のようなわかりやすい名を使用できます。

テンプレートに関するその他のベスト プラクティスについては、[テンプレート パラメーターのベスト プラクティス](../azure-resource-manager/templates/template-best-practices.md#parameters)に関する記事をご覧ください。

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>テンプレート パラメーター ファイル

テンプレート パラメーターの値を指定するには、それらの値を[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)に格納します。 そうすることで、デプロイのニーズに応じてさまざまなパラメーター ファイルを使用できます。 使用するファイル名の形式は次のようになります。

* ロジック アプリ テンプレート ファイルの名前: **<*logic-app-name*>.json**
* パラメーター ファイルの名前: **<*logic-app-name*>.parameters.json**

パラメーター ファイル内の構造は次のとおりです。これには、[Azure Key Vault を使用してセキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)ためのキー コンテナー参照が含まれています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>"
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

このサンプル パラメーター ファイルでは、このトピックで前に宣言したテンプレート パラメーターの値を指定します。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Template resources

テンプレートには `resources` オブジェクトがあります。これは、[ロジック アプリのリソース定義](#logic-app-resource-definition)、[接続リソースの定義](#connection-resource-definitions)、ロジック アプリのデプロイに必要なその他すべてのリソースなど、Azure で作成してデプロイする各リソースの定義が格納された配列です。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> テンプレートには複数のロジック アプリのリソース定義を含めることができるため、すべてのロジック アプリ リソースで同じ Azure リソース グループが指定されていることを確認してください。 Visual Studio を使ってテンプレートを Azure リソース グループにデプロイすると、どのロジック アプリを開くかを尋ねるメッセージが表示されます。 また、Azure リソース グループ プロジェクトには複数のテンプレートを含めることができるので、メッセージが表示されたら、正しいパラメーター ファイルを選択していることを確認してください。

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>リソース定義を表示する

Azure リソース グループ内のすべてのリソースのリソース定義を確認するには、[Azure から Visual Studio にロジック アプリをダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。これは、デプロイの準備がほとんど整っているパラメーター化された有効なロジック アプリ テンプレートを作成するための最も簡単な方法です。

テンプレート リソースとその属性に関する一般的な情報については、次のトピックをご覧ください。

* [Azure Resource Manager テンプレートの構造と構文のリソース](../azure-resource-manager/templates/template-syntax.md#resources)
* [テンプレート リソースのベスト プラクティス](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>ロジック アプリのリソース定義

ロジック アプリの[テンプレート内のワークフロー リソース定義](/azure/templates/microsoft.logic/workflows)は、`properties` オブジェクトで始まり、次の情報を含んでいます。

* デプロイ時のロジック アプリの状態
* ロジック アプリで使用される統合アカウントの ID
* ロジック アプリのワークフロー定義
* 実行時に使用する値を設定する `parameters` オブジェクト
* ロジック アプリに関するその他のリソース情報 (名前、種類、場所、実行時の構成設定など)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

ロジック アプリのリソース定義に固有の属性は次のとおりです。

| 属性 | 必須 | Type | 説明 |
|-----------|----------|------|-------------|
| `state` | はい | String | デプロイ時のロジック アプリの状態。`Enabled` はロジック アプリがアクティブな状態であることを意味し、`Disabled` はロジック アプリが非アクティブな状態であることを意味します。 たとえば、ロジック アプリをアクティブにする準備ができておらず、ドラフト バージョンをデプロイする必要がある場合は、`Disabled` オプションを使用できます。 |
| `integrationAccount` | いいえ | Object | 企業間 (B2B) のシナリオで成果物を格納する統合アカウントをロジック アプリで使用している場合、このオブジェクトには、統合アカウントの ID を指定する `id` 属性が含まれます。 |
| `definition` | はい | Object | ロジック アプリの基になるワークフロー定義。コード ビューに表示されるのと同じオブジェクトであり、[ワークフロー定義言語のスキーマ参照](../logic-apps/logic-apps-workflow-definition-language.md)に関するトピックで詳しく説明されています。 このワークフロー定義では、`parameters` オブジェクトによって、ロジック アプリの実行時に使用される値のパラメーターが宣言されます。 詳しくは、「[ワークフロー定義とパラメーター](#workflow-definition-parameters)」をご覧ください。 <p><p>ロジック アプリのワークフロー定義内の属性を表示するには、Azure portal または Visual Studio で "デザイン ビュー" から "コード ビュー" に切り替えるか、[Azure Resource Explorer](https://resources.azure.com) などのツールを使用します。 |
| `parameters` | いいえ | Object | ロジック アプリの実行時に使用される[ワークフロー定義のパラメーター値](#workflow-definition-parameters)。 これらの値のパラメーター定義は、[ワークフロー定義の parameters オブジェクト](#workflow-definition-parameters)内に表示されます。 また、ロジック アプリで[マネージド コネクタ](../connectors/apis-list.md)を使用して他のサービスやシステムにアクセスしている場合、このオブジェクトには、実行時に使用する接続値を設定する `$connections` オブジェクトが含まれます。 |
| `accessControl` | いいえ | Object | 要求トリガーまたは実行履歴の入出力に対する IP アクセスの制限など、ロジック アプリのセキュリティ属性を指定します。 詳細については、[ロジック アプリへのアクセスのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md)に関するページをご覧ください。 |
| `runtimeConfiguration` | いいえ | Object | 実行時のロジック アプリの動作を制御する `operationOptions` プロパティを指定します。 たとえば、ロジック アプリを[高スループット モード](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)で実行できます。 |
|||||

これらの Logic Apps オブジェクトのリソース定義の詳細については、「[Microsoft.Logic resource types](/azure/templates/microsoft.logic/allversions)」(Microsoft.Logic リソースの種類) を参照してください。

* [ワークフロー リソース定義](/azure/templates/microsoft.logic/workflows)
* [統合サービス環境のリソース定義](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [統合サービス環境のマネージド API リソース定義](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [統合アカウントのリソース定義](/azure/templates/microsoft.logic/integrationaccounts)

* 統合アカウントのアーティファクト:

  * [契約リソース定義](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [アセンブリ リソース定義](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [バッチ構成リソース定義](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [証明書リソース定義](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [マップ リソース定義](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [パートナー リソース定義](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [スキーマ リソース定義](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [セッション リソース定義](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>ワークフロー定義とパラメーター

ロジック アプリのワークフロー定義は、`definition` オブジェクトに表示されます。このオブジェクトは、ロジック アプリのリソース定義内の `properties` オブジェクトに表示されます。 この `definition` オブジェクトは、コード ビューに表示されるのと同じオブジェクトであり、[ワークフロー定義言語のスキーマ参照](../logic-apps/logic-apps-workflow-definition-language.md)に関するトピックで詳しく説明されています。 ワークフロー定義内には `parameters` 宣言オブジェクトが含まれており、実行時にワークフロー定義によって使用される値に対して、新しいパラメーターを定義したり、既存のパラメーターを編集したりできます。 その後、これらのパラメーターをワークフローのトリガー内またはアクション内で参照できます。 ロジック アプリで[マネージド コネクタ](../connectors/apis-list.md)を介した他のサービスやシステムへの接続を作成していない限り、この `parameters` オブジェクトは既定で空になります。

ワークフロー定義のパラメーターの値を設定するには、ワークフロー定義の "*外側*"、かつロジック アプリのリソース定義の "*内側*" にある `parameters` オブジェクトを使用します。 この外側の `parameters` オブジェクトでは、以前に宣言したテンプレート パラメーターを参照し、デプロイ時にパラメーター ファイルから値を受け取ることができます。

> [!TIP]
>
> ベスト プラクティスとしては、デプロイ時に評価されるテンプレート パラメーターをワークフロー定義内から直接参照しないでください。 代わりに、ワークフロー定義のパラメーターを宣言します。これは、ワークフロー定義の "*外側*"、かつロジック アプリのリソース定義の "*内側*" にある `parameters` オブジェクトに設定できます。 詳細については、「[パラメーターの参照](#parameter-references)」をご覧ください。

この構文では、テンプレート レベルとワークフロー定義レベルの両方でパラメーターを宣言できる場所とともに、テンプレートとワークフロー定義パラメーターを参照することによってこれらのパラメーターの値を設定できる場所を示しています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>ワークフロー定義のパラメーターをセキュリティで保護する

実行時に機密情報、パスワード、アクセス キー、またはシークレットを扱うワークフロー定義パラメーターについては、`securestring` または `secureobject` のパラメーター型を使用するようにパラメーターを宣言または編集します。 このパラメーターは、ワークフロー定義の全体および内部で参照できます。 テンプレートの最上位レベルで、デプロイ時にこの情報を処理する同じ型を持つパラメーターを宣言します。

ワークフロー定義のパラメーターの値を設定するには、ワークフロー定義の "*外側*"、かつロジック アプリのリソース定義の "*内側*" にある `parameters` オブジェクトを使用して、テンプレート パラメーターを参照します。 最後に、デプロイ時にテンプレート パラメーターに値を渡すには、その値を [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) に格納し、デプロイ時にテンプレートによって使用される[パラメーター ファイル](#template-parameter-files)でそのキー コンテナーを参照します。

このサンプル テンプレートでは、必要に応じてセキュリティで保護されたパラメーターを定義し、それらの値を Azure Key Vault に格納できるようにすることで、これらのタスクを完了する方法を示しています。

* アクセスの認証に使用される値に対して、セキュリティで保護されたパラメーターを宣言します。
* これらの値は、テンプレートとワークフロー定義の両方のレベルで使用します。
* これらの値を指定するには、パラメーター ファイルを使用します。

**テンプレート**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**パラメーター ファイル**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>ベスト プラクティス - ワークフロー定義のパラメーター

ロジック アプリ デザイナーでワークフロー定義のパラメーターが正しく表示されるようにするには、次のベスト プラクティスに従います。

* 機密性の高い値やセキュリティ保護が必要な値を除き、すべてのパラメーターに `defaultValue` 属性を含めます。これには空の値を指定できます。

* ユーザー名、パスワード、およびシークレットには、常にセキュリティで保護されたパラメーターを使用してください。 機密性の高いパラメーター値を非表示にしたり保護したりするには、次のトピックのガイダンスに従ってください。

  * [アクション パラメーターのセキュリティに関する推奨事項](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [ワークフロー定義のパラメーターのセキュリティに関する推奨事項](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)

ワークフロー定義のパラメーターの詳細については、[ワークフロー定義言語のパラメーター](../logic-apps/logic-apps-workflow-definition-language.md#parameters)に関する記事をご覧ください。

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>接続リソースの定義

ロジック アプリで[マネージド コネクタ](../connectors/apis-list.md)を使って他のサービスやシステムへの接続を作成および使用している場合、テンプレートの `resources` オブジェクトには、それらの接続のリソース定義が含まれます。 ロジック アプリ内から接続を作成しますが、接続は独自のリソース定義を持つ個別の Azure リソースです。 これらの接続リソース定義を確認するには、[Azure から Visual Studio にロジック アプリをダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。これは、デプロイの準備がほとんど整っているパラメーター化された有効なロジック アプリ テンプレートを作成するための最も簡単な方法です。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

接続リソース定義では、テンプレートの最上位パラメーターの値を参照します。つまり、パラメーター ファイルを使ってデプロイ時にこれらの値を指定できます。 接続で使用する Azure リソース グループと場所が、ロジック アプリと同じものであることを確認してください。

Office 365 Outlook 接続のリソース定義と、対応するテンプレート パラメーターの例を次に示します。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

ロジック アプリのリソース定義は、次の方法で接続リソースの定義とともに使用することもできます。

* このワークフロー定義内では、`parameters` オブジェクトによって、ロジック アプリの実行時に使用される接続値の `$connections` パラメーターが宣言されます。 また、接続を作成するトリガーまたはアクションでは、この `$connections` パラメーターを通じて渡される、対応する値が使用されます。

* ワークフロー定義の "*外側*"、かつロジック アプリのリソース定義の "*内側*" では、別の `parameters` オブジェクトで対応するテンプレート パラメーターを参照し、実行時に使用される `$connections` パラメーターの値を設定します。 これらの値はテンプレート式を使用して、ロジック アプリ内に接続のメタデータを安全に格納するリソースを参照します。

  たとえば、メタデータに接続文字列とアクセス トークンを含めて、それを [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) に格納することができます。 これらの値をテンプレート パラメーターに渡すには、デプロイ時にテンプレートによって使用される[パラメーター ファイル](#template-parameter-files)でそのキー コンテナーを参照します。 パラメーターの参照の違いについて詳しくは、このトピックで後述する「[パラメーターの参照](#parameter-references)」をご覧ください。

  Azure portal または Visual Studio のコード ビューでロジック アプリのワークフロー定義を開くと、ワークフロー定義の外側、かつ同じレベルに `$connections` オブジェクトが表示されます。 コード ビューでのこの順序付けによって、ワークフロー定義を手動で更新するときに、これらのパラメーターを参照しやすくなります。

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* ロジック アプリのリソース定義には、ロジック アプリによって使用される接続の依存関係を指定する `dependsOn` オブジェクトがあります。

Azure で作成する各接続には、一意の名前が付けられます。 同じサービスまたはシステムへの接続を複数作成すると、各接続名に番号が付加されます。これは、新しい接続が作成されるたびに、`office365`、`office365-1` のように増えていきます。

この例は、ロジック アプリのリソース定義と Office 365 Outlook の接続リソース定義の間の相互作用を示しています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         <other-logic-app-resource-information>,
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>接続パラメーターをセキュリティで保護する

機密情報、パスワード、アクセスキー、またはシークレットを扱う接続パラメーターの場合、接続のリソース定義には、名前と値のペアの形式でこれらの値を指定する `parameterValues` オブジェクトが含まれます。 この情報を非表示にするには、`securestring` または `secureobject` のパラメーター型を使ってこれらの値のテンプレート パラメーターを宣言または編集します。 その後、その情報を [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) に格納することができます。 これらの値をテンプレート パラメーターに渡すには、デプロイ時にテンプレートによって使用される[パラメーター ファイル](#template-parameter-files)でそのキー コンテナーを参照します。

Azure Blob Storage 接続のアカウント名とアクセス キーを指定する例を次に示します。

**パラメーター ファイル**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**テンプレート**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>接続の認証

デプロイ後、ロジック アプリは有効なパラメーターを使用してエンド ツー エンドで動作します。 ただし、引き続き OAuth 接続を承認し、[資格情報を認証する](../active-directory/develop/authentication-vs-authorization.md)ための有効なアクセス トークンを生成する必要があります。 詳しくは、「[Authorize OAuth connections (OAuth 接続の承認)](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)」をご覧ください。

一部の接続では、[Azure AD に登録](../active-directory/develop/quickstart-register-app.md)されているロジック アプリの接続を承認するために、Azure Active Directory (Azure AD) [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)の使用がサポートされています。 たとえば、この Azure Data Lake 接続リソース定義は、サービス プリンシパルの情報を扱うテンプレート パラメーターを参照する方法と、テンプレートでこれらのパラメーターを宣言する方法を示しています。

**接続リソースの定義**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| 属性 | 説明 |
|-----------|-------------|
| `token:clientId` | サービス プリンシパルに関連付けられているアプリケーションまたはクライアントの ID |
| `token:clientSecret` | サービス プリンシパルに関連付けられているキー値 |
| `token:TenantId` | Azure AD テナントのディレクトリ ID |
| `token:grantType` | 要求された付与タイプ (必ず `client_credentials` になります)。 詳しくは、「[Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)」をご覧ください。 |
|||

**テンプレート パラメーターの定義**

テンプレートの最上位レベルの `parameters` オブジェクトによって、サンプル接続のこれらのパラメーターが宣言されます。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

サービス プリンシパルの使用方法について詳しくは、次のトピックをご覧ください。

* [Azure Portal を使用してサービス プリンシパルを作成する](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell を使用して Azure サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps)
* [Azure PowerShell を使用して証明書でのサービス プリンシパルを作成する](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>パラメーターの参照

テンプレート パラメーターを参照するには、デプロイ時に評価されるテンプレート式と[テンプレート関数](../azure-resource-manager/templates/template-functions.md)を使用できます。 テンプレート式では、角かっこ (**[]**) を使用します。

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

ワークフロー定義のパラメーターを参照するには、実行時に評価される[ワークフロー定義言語の式と関数](../logic-apps/workflow-definition-language-functions-reference.md)を使用します。 一部のテンプレート関数とワークフロー定義関数の名前が同じであることにお気付きでしょう。 ワークフロー定義の式は、アットマーク ( **@** ) で始まります。

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

テンプレート パラメーターの値は、ロジック アプリの実行時に使用されるワークフロー定義に渡すことができます。 ただし、ロジック アプリ デザイナーではテンプレート要素がサポートされていないため、ワークフロー定義でテンプレートのパラメーター、式、および構文を使用しないようにしてください。 また、テンプレートの構文と式を使用すると、式が評価されるときの違いによってコードが複雑になることがあります。

代わりに、次の一般的な手順に従い、実行時に使用されるワークフロー定義パラメーターを宣言および参照し、デプロイ時に使用されるテンプレート パラメーターを宣言および参照し、パラメーター ファイルによってデプロイ時に渡される値を指定します。 詳しくは、このトピックで前述した「[ワークフローの定義とパラメーター](#workflow-definition-parameters)」セクションをご覧ください。

1. テンプレートを作成し、デプロイ時に受け取って使用する値のテンプレート パラメーターを宣言します。

1. ワークフロー定義で、実行時に受け取って使用する値のパラメーターを宣言します。 これにより、これらの値をワークフロー定義の全体および内部で参照できるようになります。

1. ワークフロー定義の "*外側*"、かつロジック アプリのリソース定義の "*内側*" にある `parameters` オブジェクトで、対応するテンプレート パラメーターを参照してワークフロー定義のパラメーターの値を設定します。 それにより、テンプレート パラメーター値をワークフロー定義パラメーターに渡すことができます。

1. パラメーター ファイルで、デプロイ時にテンプレートで使用する値を指定します。

<a name="full-example-template"></a>

## <a name="full-example-template"></a>完全なサンプル テンプレート

このトピックの例で使用した、パラメーター化されたサンプル テンプレートを次に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps テンプレートの作成](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
