---
title: Azure Resource Manager テンプレートを使用して Update Management を有効にする | Microsoft Docs
description: この記事では、Azure Resource Manager テンプレートを使用して、Update Management を有効にする方法について説明します。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: c4b29db8bbcb741116fcd425b4489973228066e6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021597"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Update Management を有効にする

[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用して、リソース グループ内の Azure Automation Update Management 機能を有効にすることができます。 この記事では、以下のことを自動化するサンプル テンプレートについて説明します。

* Azure Monitor Log Analytics ワークスペースの作成。
* Azure Automation アカウントの作成。
* Automation アカウントから Log Analytics ワークスペースへのリンク (まだリンクされていない場合)。
* Update Management の有効化。

このテンプレートでは、1 つ以上の Azure VM または Azure 以外の VM で、Update Management の有効化は自動化されません。

サブスクリプションでサポートされているリージョンに Log Analytics ワークスペースと Automation アカウントが既にデプロイされている場合、これらはリンクされていません。 このテンプレートを使用すると、リンクが正常に作成され、Update Management がデプロイされます。

## <a name="api-versions"></a>API のバージョン

次の表は、このテンプレートで使用されているリソースの API バージョンの一覧です。

| リソース | リソースの種類 | API バージョン |
|:---|:---|:---|
| ワークスペース | workspaces | 2020-03-01-preview |
| Automation アカウント | automation | 2018-06-30 |
| 解決策 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>テンプレートを使用する前に

PowerShell をローカルにインストールして使用する場合、この記事では Azure PowerShell Az モジュールが必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) を実行して Azure との接続を作成する必要もあります。 Azure PowerShell では、デプロイに [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) が使用されます。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.1.0 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 Azure CLI では、このデプロイに [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) が使用されます。 

JSON テンプレートは、次のことを求めるように構成されています。

* ワークスペースの名前。
* ワークスペースを作成するリージョン。
* リソースまたはワークスペースのアクセス許可を有効にするかどうか。
* Automation アカウントの名前。
* アカウントを作成するリージョン。

JSON テンプレートでは、環境で標準構成用に使用される可能性のある他のパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作について詳しくは、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

テンプレートの次のパラメーターには、Log Analytics ワークスペースの既定値が設定されます。

* sku - 既定値は、2018 年 4 月の価格モデルでリリースされた新しい 1 GB あたりの価格レベル
* data retention - 既定値は 30 日

>[!WARNING]
>新しい 2018 年 4 月の価格モデルを選択したサブスクリプションで Log Analytics ワークスペースを作成または構成する場合、有効な Log Analytics 価格レベルは **PerGB2018** のみです。
>

JSON テンプレートでは、環境で標準構成として使用される可能性のある他のパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作について詳しくは、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

Azure Automation と Azure Monitor を初めて使用する場合、新しい Automation アカウントにリンクされている Log Analytics ワークスペースを作成、構成、使用するときにエラーを避けるために、次の構成詳細を理解することが重要です。

* [追加の詳細](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)を確認し、アクセス制御モード、価格レベル、保有期間、容量予約レベルなどのワークスペースの構成オプションについて十分に理解します。

* お使いのサブスクリプションでは Log Analytics ワークスペースと Automation アカウントをリンクできるリージョンが限られているため、[ワークスペース マッピング](../how-to/region-mappings.md)を確認し、サポートされているリージョンをインラインで、またはパラメーター ファイル内で指定してください。

* Azure Monitor ログを初めてご利用になり、ワークスペースをまだデプロイしていない場合は、[ワークスペースの設計](../../azure-monitor/platform/design-logs-deployment.md)に関するガイダンスを確認してアクセス制御について学習し、組織に推奨される設計の実装戦略について理解しておく必要があります。

## <a name="deploy-template"></a>テンプレートのデプロイ

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
              }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('location')]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            }
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. 要件に合わせてテンプレートを編集します。 パラメーターをインライン値として渡す代わりに、[Resource Manager パラメーター ファイル](../../azure-resource-manager/templates/parameter-files.md)を作成することを検討してください。

3. このファイルを **deployUMSolutiontemplate.json** としてローカル フォルダーに保存します。

4. これでこのテンプレートをデプロイする準備が整いました。 PowerShell または Azure CLI を使用できます。 ワークスペースと Automation アカウント名の入力を求められたら、すべての Azure サブスクリプションでグローバルに一意の名前を指定します。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    デプロイが完了するまでに数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ![デプロイ完了時の結果の例](media/update-mgmt-enable-template/template-output.png)

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[VM の更新プログラムとパッチを管理する](update-mgmt-manage-updates-for-vm.md)」を参照してください。

* Update Management を使用しなくなったため、削除したい場合は、[Update Management の機能を削除する](update-mgmt-remove-feature.md)方法に関する記事に記載の手順を参照してください。

* Update Management から VM を削除するには、「[Update Management から VM を削除する](update-mgmt-remove-vms.md)」を参照してください。
