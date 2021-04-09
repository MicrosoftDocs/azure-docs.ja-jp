---
title: チュートリアル - リソースのオンボード
description: カスタム プロバイダー経由でリソースをオンボードすると、既存の Azure リソースを操作して拡張することができます。
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75648609"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>チュートリアル:Azure カスタム プロバイダーを使用したリソースのオンボード

このチュートリアルでは、Microsoft.CustomProviders/associations リソースの種類を使用して、Azure Resource Manager API を拡張するカスタム リソース プロバイダーを Azure にデプロイします。 チュートリアルでは、カスタム プロバイダー インスタンスが配置されているリソース グループの外にある既存のリソースを拡張する方法を示します。 このチュートリアルでは、カスタム リソース プロバイダーは Azure Logic Apps によって提供されていますが、任意のパブリック API エンドポイントを使用してもかまいません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下に関する知識が必要です。

* [Azure カスタム プロバイダー](overview.md)の機能。
* [カスタム プロバイダーを使用したリソースのオンボード](concepts-resource-onboarding.md)に関する基本情報。

## <a name="get-started-with-resource-onboarding"></a>リソース オンボードの概要

このチュートリアルでは、カスタム プロバイダーと関連付けという 2 つの部品をデプロイする必要があります。 プロセスを簡単にするために、必要に応じて、両方をデプロイする単一のテンプレートを使用できます。

テンプレートでは、次のリソースが使用されます。

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>カスタム プロバイダー インフラストラクチャをデプロイする

テンプレートの最初の部分では、カスタム プロバイダー インフラストラクチャがデプロイされます。 このインフラストラクチャには、関連付けリソースの効果が定義されています。 カスタム プロバイダーの詳細については、[カスタム プロバイダーの基本](overview.md)に関するページを参照してください。

カスタム プロバイダー インフラストラクチャをデプロイしましょう。 前のテンプレートをコピー、保存、デプロイするか、Azure portal を使用して手順に沿ってインフラストラクチャをデプロイします。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. **[すべてのサービス]** 内か、または主要な検索ボックスを使用して、**テンプレート** を検索します。

   ![テンプレートの検索](media/tutorial-resource-onboarding/templates.png)

3. **[テンプレート]** ウィンドウ上で **[追加]** を選択します。

   ![[追加] を選択する](media/tutorial-resource-onboarding/templatesadd.png)

4. **[全般]** の下で、新しいテンプレートの **[名前]** と **[説明]** を入力します。

   ![テンプレートの名前と説明](media/tutorial-resource-onboarding/templatesdescription.png)

5. この記事の「リソース オンボードの概要」セクションの JSON テンプレートをコピーして、Resource Manager テンプレートを作成します。

   ![Resource Manager テンプレートを作成する](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. **[追加]** を選択して、テンプレートを作成します。 新しいテンプレートが表示されない場合は、 **[更新]** を選択します。

7. 新しく作成されたテンプレートを選択して、 **[デプロイ]** を選択します。

   ![新しいテンプレートを選択してから [デプロイ] を選択する](media/tutorial-resource-onboarding/templateselectspecific.png)

8. 必須フィールドに設定を入力し、サブスクリプションとリソース グループを選択します。 **[Custom Resource Provider Id]\(カスタム リソース プロバイダー ID\)** ボックスは空のままにできます。

   | 設定名 | 必須 | 説明 |
   | ------------ | -------- | ----------- |
   | 場所 | はい | テンプレート内のリソースの場所。 |
   | ロジック アプリ名 | いいえ | ロジック アプリの名前。 |
   | カスタム リソース プロバイダー名 | いいえ | カスタム リソース プロバイダーの名前。 |
   | カスタム リソース プロバイダー ID | いいえ | 関連付けリソースをサポートする既存のカスタム リソース プロバイダー。 ここに値を指定すると、ロジック アプリとカスタム プロバイダーのデプロイはスキップされます。 |
   | 関連付けの名前 | いいえ | 関連付けリソースの名前。 |

   サンプル パラメーター:

   ![テンプレート パラメーターの入力](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. デプロイへ移動し、完了するまで待ちます。 次のような画面が表示されます。 新しい関連付けリソースが出力として表示されます。

   ![デプロイに成功](media/tutorial-resource-onboarding/customproviderdeployment.png)

   次に、 **[非表示の型の表示]** が選択されている状態で、リソース グループを示します。

   ![カスタム プロバイダーのデプロイ](media/tutorial-resource-onboarding/showhidden.png)

10. ロジック アプリの **[実行の履歴]** タブを調べて、関連付けの作成の呼び出しを確認します。

    ![ロジック アプリの [実行の履歴]](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>追加の関連付けをデプロイする

カスタム プロバイダー インフラストラクチャをセットアップすると、以降はより多くの関連付けを簡単にデプロイできます。 追加の関連付け用のリソース グループを、カスタム プロバイダー インフラストラクチャをデプロイしたリソース グループと同じにする必要はありません。 関連付けを作成するには、指定されたカスタム リソース プロバイダー ID 上で Microsoft.CustomProviders/resourceproviders/write アクセス許可を保持している必要があります。

1. 前のデプロイのリソース グループ内にあるカスタム プロバイダーの **Microsoft.CustomProviders/resourceProviders** リソースに移動します。 **[非表示の型の表示]** チェック ボックスをオンにすることが必要になります。

   ![リソースへ移動する](media/tutorial-resource-onboarding/showhidden.png)

2. カスタム プロバイダーのリソース ID プロパティをコピーします。

3. **[すべてのサービス]** 内か、または主要な検索ボックスを使用して、**テンプレート** を検索します。

   ![テンプレートの検索](media/tutorial-resource-onboarding/templates.png)

4. 以前に作成されたテンプレートを選択して、 **[デプロイ]** を選択します。

   ![以前に作成されたテンプレートを選択して、[デプロイ] を選択します](media/tutorial-resource-onboarding/templateselectspecific.png)

5. 必須フィールドに設定を入力し、サブスクリプションと別のリソース グループを選択します。 **[カスタム リソース プロバイダー ID]** 設定に、先にデプロイしたカスタム プロバイダーからコピーしたリソース ID を入力します。

6. デプロイへ移動し、完了するまで待ちます。 この際、新しい関連付けリソースのみをデプロイする必要があります。

   ![新しい関連付けリソース](media/tutorial-resource-onboarding/createdassociationresource.png)

必要に応じて、ロジック アプリの **[実行の履歴]** に戻って、ロジック アプリへの別の呼び出しが行われたことを確認できます。 ロジック アプリを更新して、作成された関連付けごとに追加の機能を拡張できます。

## <a name="getting-help"></a>ヘルプの表示

Azure カスタム プロバイダーについて質問がある場合は、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) 上で確認してみてください。 同様の質問が既に回答されていることもあるため、投稿する前にまず確認してください。 `azure-custom-providers` タグを追加して早く応答を受け取りましょう。

