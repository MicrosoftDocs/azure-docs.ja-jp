---
title: BYOS (Bring Your Own Storage) を Profiler および Visual Studio スナップショット デバッガー用に構成する
description: BYOS (Bring Your Own Storage) を Profiler および Visual Studio スナップショット デバッガー用に構成する
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589674"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>BYOS (Bring Your Own Storage) を Application Insights Profiler および Visual Studio スナップショット デバッガー用に構成する

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Bring Your Own Storage (BYOS) の概要とそれが必要な理由 
Application Insights Profiler または Visual Studio スナップショット デバッガーを使用する場合、アプリケーションによって生成された成果物は、パブリック インターネットを経由して Azure ストレージ アカウントにアップロードされます。 これらのアカウントは有料で、処理と分析のために Microsoft によって収集されます。 Microsoft は、これらの成果物の保存時の暗号化ポリシーと有効期間の管理ポリシーを制御します。

Bring Your Own Storage を使用すると、これらの成果物は、ユーザーが制御するストレージ アカウントにアップロードされます。 つまり、保存時の暗号化ポリシー、有効期間の管理ポリシー、ネットワーク アクセスをユーザーが制御することになります。 ただし、そのストレージ アカウントに関連するコストについては、お客様が責任を負うものとします。

> [!NOTE]
> Private Link を有効にする場合、Bring Your Own Storage は必須です。 Application Insights 用の Private Link の詳細については、[こちらのドキュメントを参照してください。](../logs/private-link-security.md)
>
> カスタマー マネージド キーを有効にする場合、Bring Your Own Storage は必須です。 Application Insights 用のカスタマー マネージド キーの詳細については、[こちらのドキュメントを参照してください。](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>ストレージ アカウントにアクセスする方法
1. Virtual Machines または App Service 内で実行されるエージェントでは、成果物 (プロファイル、スナップショット、シンボル) をご使用のアカウント内の BLOB コンテナーにアップロードします。 このプロセスでは、Application Insights Profiler または Visual Studio スナップショット デバッガーに接続して、ご使用のストレージ アカウント内の新しい BLOB の SAS (Shared Access Signature) トークンを取得する必要があります。
1. Application Insights Profiler または Visual Studio スナップショット デバッガー サービスでは、受信 BLOB を分析し、分析結果とログ ファイルを BLOB ストレージに書き戻します。 使用可能なコンピューティング容量に応じて、このプロセスはアップロード後いつでも発生する可能性があります。
1. プロファイラー トレースまたはスナップショット デバッガー分析を表示すると、サービスにより、分析結果が BLOB ストレージから取り込まれます。

## <a name="prerequisites"></a>前提条件
* ストレージ アカウントは、必ず Application Insights リソースと同一の場所に作成してください。 例: Application Insights リソースが米国西部 2 内にある場合、ストレージ アカウントも米国西部 2 内にある必要があります。 
* Access Control (IAM) UI を使用して、ストレージ アカウント内の AAD アプリケーション "Diagnostic Services Trusted Storage Access" に "ストレージ BLOB データ共同作成者" ロールを付与します。
* Private Link を有効にする場合、Virtual Network から信頼された Microsoft サービスへの接続を許可するための追加設定を構成します。 

## <a name="how-to-enable-byos"></a>BYOS を有効にする方法

### <a name="create-storage-account"></a>ストレージ アカウントの作成
Application Insights リソースと同じ場所に、新しいストレージ アカウントを作成します (持っていない場合)。
Application Insights リソースが `West US 2` にある場合、ストレージ アカウントも `West US 2` 内にある必要があります。

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>ストレージ アカウントに診断サービスへのアクセスを許可する
BYOS ストレージ アカウントは、Application Insights リソースにリンクされます。 ストレージ アカウントは Application Insights リソースあたり 1 つのみで、両方が同じ場所にある必要があります。 同じストレージ アカウントを複数の Application Insights リソースで使用することができます。

最初に、Application Insights Profiler および Visual Studio スナップショット デバッガー サービスにストレージ アカウントへのアクセスを許可する必要があります。 アクセスを許可するには、図 1.0 に示すストレージ アカウントの [Access Control (IAM)] ページで、`Diagnostic Services Trusted Storage Access` という名前の AAD アプリケーションに `Storage Blob Data Contributor` ロールを追加します。 

手順: 
1. [ロールの割り当ての追加] セクションにある [追加] ボタンをクリックします 
1. [ストレージ BLOB データ共同作成者] ロールを選択する 
1. [アクセスの割り当て先] セクションで、[Azure AD のユーザー、グループ、サービス プリンシパル] を選択します 
1. "Diagnostic Services Trusted Storage Access" アプリケーションを検索して選択します 
1. 変更を保存する

_![図 1.0](media/profiler-bring-your-own-storage/figure-10.png)_
_図 1.0_ 

ロールを追加すると、図 1.1 に示すように、[ロールの割り当て] セクションにロールが表示されます。 
_![図 1.1](media/profiler-bring-your-own-storage/figure-11.png)_
_図 1.1_ 

Private Link も使用する場合、Virtual Network から信頼された Microsoft サービスへの接続を許可するための 1 つの追加構成が必要です。 [ストレージ ネットワーク セキュリティに関するドキュメント](../../storage/common/storage-network-security.md#trusted-microsoft-services)を参照してください。

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>ストレージ アカウントを Application Insights リソースにリンクする
BYOS をコードレベルの診断 (プロファイラー/デバッガー) 用に構成するには、次の 3 つのオプションがあります。

* Azure PowerShell コマンドレットの使用
* Azure コマンド ライン インターフェイス (CLI) の使用
* Azure Resource Manager テンプレートの使用

#### <a name="configure-using-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットを使用して構成する

1. Az PowerShell 4.2.0 以上がインストールされていることを確認します。

    Azure PowerShell をインストールするには、[Azure PowerShell の公式ドキュメント](/powershell/azure/install-az-ps)を参照してください。

1. Application Insights PowerShell 拡張機能をインストールします。
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Azure アカウントでサインインします
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    サインイン方法の詳細については、[Connect-AzAccount に関するドキュメント](/powershell/module/az.accounts/connect-azaccount)を参照してください。

1. Application Insights リソースにリンクされている以前のストレージ アカウントを削除します。

    パターン:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    例:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. ご使用のストレージ アカウントを Application Insights リソースに接続します。
    
    パターン:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    例:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Azure CLI を使用して構成する

1. Azure CLI がインストールされていることを確認します。

    Azure CLI をインストールするには、[Azure CLI の公式ドキュメント](/cli/azure/install-azure-cli)を参照してください。

1. Application Insights CLI 拡張機能をインストールします。
    ```powershell
    az extension add -n application-insights
    ```

1. ご使用のストレージ アカウントを Application Insights リソースに接続します。

    パターン:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    例:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    予想される出力:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Application Insights リソースにリンクするストレージ アカウントの更新を実行するには、[Application Insights CLI に関するドキュメント](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)を参照してください。

#### <a name="configure-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して構成する

1. 次のコンテンツ (byos.template.json) を含む Azure Resource Manager テンプレート ファイルを作成します。
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 次の PowerShell コマンドを使用して、前述のテンプレートをデプロイします (リンクするストレージ アカウントを作成します)。

    パターン:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    例:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. PowerShell コンソールにプロンプトが表示されたら、次のパラメーターを指定します。
    
    |           パラメーター           |                                説明                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | BYOS を有効にする Application Insights リソースの名前。            |
    | storage_account_name          | BYOS として使用するストレージ アカウント リソースの名前。 |
    
    予想される出力:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Azure portal を介して対象のワークロードでコードレベルの診断 (Profiler またはデバッガー) を有効にします ([App Service] > [Application Insights]) _![図 2.0](media/profiler-bring-your-own-storage/figure-20.png)_
_図 2.0_

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="template-schema-schema_uri-isnt-supported"></a>テンプレート スキーマ '{schema_uri}' がサポートされていません。
* テンプレートの `$schema` プロパティが有効であることを確認します。 パターン `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#` に従う必要があります。
* テンプレートの `schema_version` が有効な値 `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` 内にあることを確認します。
    エラー メッセージ:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>場所 '{location}' の登録済みのリソース プロバイダーが見つかりません。
* リソース `microsoft.insights/components` の `apiVersion` が `2015-05-01` であることを確認します。
* リソース `linkedStorageAccount` の `apiVersion` が `2020-03-01-preview` であることを確認します。
    エラー メッセージ:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>ストレージ アカウントの場所は AI コンポーネントの場所と一致している必要があります。
* Application Insights リソースの場所がストレージ アカウントと同じであることを確認します。
    エラー メッセージ:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Profiler の一般的なトラブルシューティングについては、[Profiler のトラブルシューティングに関するドキュメント](profiler-troubleshooting.md)を参照してください。

Visual Studio スナップショット デバッガーの一般的なトラブルシューティングについては、[Visual Studio スナップショット デバッガーのトラブルシューティングに関するドキュメント](snapshot-debugger-troubleshoot.md)を参照してください。 

## <a name="faqs"></a>FAQ
* Profiler またはスナップショットを有効にした後、BYOS を有効にした場合、データは自分のストレージ アカウントに移行されますか?
    "_いいえ、移行されません。_ "

* BYOS で、保存時の暗号化およびカスタマー マネージド キーを使用できますか?
    "_はい。正確には、カスタマー マネージド キーを使用してプロファイラーまたはデバッガーを有効にするには、BYOS が必須です。_ "

* BYOS は、インターネットから分離された環境で動作しますか?
    "_はい。実際、分離されたネットワーク シナリオには、BYOS が必須です。_ "

* カスタマー マネージド キーと Private Link の両方を有効にした場合、BYOS は動作しますか? 
    "_はい、できます。_ "

* BYOS を有効にした場合、診断サービスのストレージ アカウントを使用して戻り、収集されたデータを保存することはできますか? 
    "_はい、できます。ただし、現時点では、BYOS からのデータ移行はサポートされていません。_ "

* BYOS を有効にした後、それに関連するすべての関連コスト (Azure Storage と Azure ネットワーク) は引き継がれますか? 
    _はい_
