---
title: Azure Resource Manager テンプレートを使用して Update Management を有効にする
description: この記事では、Azure Resource Manager テンプレートを使用して、Update Management を有効にする方法について説明します。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 95ef52acedc9171ba86110a665d08ea97c59bfbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575821"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Update Management を有効にする

[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用して、リソース グループ内の Azure Automation Update Management 機能を有効にすることができます。 この記事では、以下のことを自動化するサンプル テンプレートについて説明します。

* Azure Monitor Log Analytics ワークスペースの作成を自動化します。
* Azure Automation アカウントの作成を自動化します。
* Automation アカウントを Log Analytics ワークスペースにリンクします。
* サンプルの Automation Runbook をアカウントに追加します。
* Update Management 機能を有効にします。

このテンプレートでは、1 つ以上の Azure VM または Azure 以外の VM で、Update Management の有効化は自動化されません。

サブスクリプションでサポートされているリージョンに Log Analytics ワークスペースと Automation アカウントが既にデプロイされている場合、これらはリンクされていません。 このテンプレートを使用すると、リンクが正常に作成され、Update Management がデプロイされます。

>[!NOTE]
>ARM テンプレートを使用する場合、Automation の実行アカウントの作成はサポートされません。 ポータルからまたは PowerShell を使用して実行アカウントを手動で作成するには、[実行アカウントの作成](../create-run-as-account.md)に関するページを参照してください。

これらの手順を完了したら、Runbook ジョブの状態とジョブ ストリームを、リンクされた Log Analytics ワークスペースに送信するために、Automation アカウントの[診断設定を構成する](../automation-manage-send-joblogs-log-analytics.md)必要があります。

## <a name="api-versions"></a>API のバージョン

次の表は、この例で使用されているリソースの API バージョンの一覧です。

| リソース | リソースの種類 | API バージョン |
|:---|:---|:---|
| [ワークスペース](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation アカウント](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [ワークスペースのリンクされたサービス](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |
| [ソリューション](/azure/templates/microsoft.operationsmanagement/solutions) | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>テンプレートを使用する前に

JSON テンプレートは、次のことを求めるように構成されています。

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

JSON テンプレートでは、環境で標準構成として使用される可能性のある他のパラメーターの既定値を指定します。 このテンプレートは、組織内の共有アクセス用に Azure ストレージ アカウントに格納できます。 テンプレートの操作の詳細については、[ARM テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)に関するページを参照してください。

Azure Automation と Azure Monitor を初めて使用する場合は、次の構成の詳細を理解しておくことが重要です。 これは、新しい Automation アカウントにリンクされた Log Analytics ワークスペースを作成、構成、および使用するときに、エラーを回避するのに役立ちます。

* [追加の詳細](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)を確認し、アクセス制御モード、価格レベル、保有期間、容量予約レベルなどのワークスペースの構成オプションについて十分に理解します。

* [ワークスペース マッピング](../how-to/region-mappings.md)を確認し、サポートされているリージョンを、インラインまたはパラメーター ファイルで指定します。 サブスクリプション内で Log Analytics ワークスペースと Automation アカウントをリンクすることは、特定のリージョンでのみサポートされています。

* Azure Monitor ログを初めて使用し、ワークスペースをまだデプロイしていない場合は、[ワークスペースの設計ガイダンス](../../azure-monitor/logs/design-logs-deployment.md)を確認してください。 これは、アクセスの制御について学習し、組織に推奨される設計の実装戦略を理解するのに役立ちます。

## <a name="deploy-template"></a>テンプレートのデプロイ

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
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
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
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
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
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
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
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

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    デプロイが完了するまでに数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ![デプロイ完了時の結果の例](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal で、作成した Automation アカウントを開きます。

3. 左側のペインから、 **[Runbook]** を選択します。 **[Runbook]** ページに、Automation アカウントで作成された 3 つのチュートリアル Runbook が表示されています。

    ![Automation アカウントで作成されたチュートリアル Runbook](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. 左側のペインから、 **[リンクされたワークスペース]** を選択します。 **[リンクされたワークスペース]** ページに、先ほど指定した対象の Automation アカウントにリンクされている Log Analytics ワークスペースが表示されます。

    ![Log Analytics ワークスペースにリンクされた Automation アカウント](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. 左側のウィンドウから、 **[更新の管理]** を選択します。 有効にしたばかりのため、 **[更新の管理]** ページには情報が何もない評価ページが表示され、マシンは管理用に構成されていません。

    ![Update Management 機能の評価ビュー](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、Log Analytics ワークスペースから **更新** ソリューションを削除し、ワークスペースから Automation アカウントのリンクを解除し、Automation アカウントとワークスペースを削除します。

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[VM の更新プログラムとパッチを管理する](manage-updates-for-vm.md)」を参照してください。

* Update Management を使用しなくなったため、削除したい場合は、[Update Management の機能を削除する](remove-feature.md)方法に関する記事に記載の手順を参照してください。

* Update Management から VM を削除するには、「[Update Management から VM を削除する](remove-vms.md)」を参照してください。