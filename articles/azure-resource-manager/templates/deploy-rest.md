---
title: REST API とテンプレートを使用してリソースをデプロイする
description: Azure Resource Manager と Resource Manager REST API を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: e688d7abfaca442c3de395d25961b4e81e6c7b24
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889197"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>ARM テンプレートと Azure Resource Manager REST API を使用してリソースをデプロイする

この記事では、Azure Resource Manager REST API と Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure にリソースをデプロイする方法について説明します。

テンプレートは要求本文またはファイルへのリンクに含めることができます。 ファイルを使用する場合は、ローカル ファイルまたは URI を通じてアクセスできる外部ファイルを使用できます。 テンプレートがストレージ アカウントにある場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## <a name="deployment-scope"></a>デプロイのスコープ

リソース グループ、Azure サブスクリプション、管理グループ、またはテナントをデプロイのターゲットにすることができます。 使用するコマンドは、デプロイのスコープに応じて異なります。

- **リソース グループ** にデプロイするには、[デプロイ - 作成](/rest/api/resources/deployments/createorupdate)に関するページのコマンドを使用します。 要求は以下に送信されます。

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

- **サブスクリプション** にデプロイするには、[デプロイ - サブスクリプション スコープで作成](/rest/api/resources/deployments/createorupdateatsubscriptionscope)に関するページのコマンドを使用します。 要求は以下に送信されます。

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  サブスクリプション レベルでのデプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

- **管理グループ** にデプロイするには、[デプロイ - 管理グループ スコープで作成](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)に関するページを参照してください。 要求は以下に送信されます。

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  管理グループ レベルでのデプロイの詳細については、「[管理グループ レベルでリソースを作成する](deploy-to-management-group.md)」を参照してください。

- **テナント** にデプロイするには [デプロイ - テナントのスコープでの作成または更新](/rest/api/resources/deployments/createorupdateattenantscope)に関するページを使用します。 要求は以下に送信されます。

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  テナント レベルでのデプロイの詳細については、「[テナント レベルでリソースを作成する](deploy-to-tenant.md)」を参照してください。

この記事の例では、リソース グループ デプロイを使用します。

## <a name="deploy-with-the-rest-api"></a>REST API でデプロイする

1. [一般的なパラメーターおよびヘッダー](/rest/api/azure/) (認証トークンを含む) を設定します。

1. 存在しないリソース グループにデプロイする場合、リソース グループを作成する必要があります。 ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、場所を指定します。 詳細については、「[リソース グループの作成](/rest/api/resources/resourcegroups/createorupdate)」を参照してください。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
   ```

   次のような要求本文を使用します。

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. テンプレートをデプロイする前に、テンプレートが環境に与える変更をプレビューすることができます。 [what-if 操作](template-deploy-what-if.md)を使用して、テンプレートによって必要な変更が行われるかどうかを確認します。 What-if はまた、テンプレートのエラーも検証します。

1. テンプレートをデプロイするには、要求 URI にサブスクリプション ID、リソース グループの名前、デプロイの名前を指定します。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2020-10-01
   ```

   要求の本文に、テンプレートとパラメーター ファイルへのリンクを指定します。 パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](parameter-files.md)」を参照してください。

   `mode` が **[増分]** に設定されていることに注意してください。 完全デプロイメントを実行するには、 `mode` を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    応答の内容、要求の内容、またはその両方を記録するには、要求に `debugSetting` を含めます。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    ストレージ アカウントを設定して、Shared Access Signature (SAS) トークンを使用することができます。 詳細については、[Shared Access Signature (SAS) を使用したアクセスの委任](/rest/api/storageservices/delegate-access-with-shared-access-signature)に関するページを参照してください。

    パラメーターに機密性の高い値(パスワードなど) を提供する必要がある場合は、その値を Key Vault に追加します。 前の例で示したように、デプロイ中に Key Vault を取得します。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください

1. テンプレートとパラメーターのファイルにリンクするのではなく、それらを要求本文に含めることができます。 次の例は、テンプレートとパラメーターをインラインにした要求の本文を示しています。

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. テンプレートのデプロイの状態を取得するには、[デプロイ - 取得](/rest/api/resources/deployments/get)に関するページを参照してください。

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
   ```

## <a name="deployment-name"></a>デプロイ名

デプロイには `ExampleDeployment` のような名前を付けることができます。

デプロイを実行するたびに、リソース グループのデプロイ履歴にデプロイ名のエントリが追加されます。 別のデプロイを実行するときに同じ名前を付けると、現在のデプロイによって前のエントリが置き換えられます。 デプロイ履歴に一意のエントリを保持する場合は、デプロイごとに一意の名前を付けます。

一意の名前を作成するために、ランダムな数値を割り当てることができます。 または、日付の値を追加します。

同じリソース グループに対して同じ名前のデプロイを同時に実行した場合は、最後のデプロイのみが完了します。 完了していない同じ名前のデプロイは、最後のデプロイによって置き換えられます。 たとえば、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを同時に実行した場合は、1 つのストレージ アカウントのみがデプロイされます。 結果のストレージ アカウントの名前は `storage2` になります。

ただし、`storage1` という名前のストレージ アカウントをデプロイする `newStorage` という名前のデプロイを実行し、その完了直後に、`storage2` という名前のストレージ アカウントをデプロイする `newStorage` という名前の別のデプロイを実行した場合は、ストレージ アカウントは 2 つになります。 1 つは `storage1` という名前に、もう 1 つは `storage2` という名前になります。 ただし、デプロイ履歴にはエントリが 1 つだけ存在します。

デプロイごとに一意の名前を指定すると、競合なしでそれらを同時に実行できます。 `storage1` という名前のストレージ アカウントをデプロイする `newStorage1` という名前のデプロイを実行し、`storage2` という名前のストレージ アカウントをデプロイする `newStorage2` という名前の別のデプロイを同時に実行した場合は、2 つのストレージ アカウントがデプロイされ、デプロイ履歴には 2 つのエントリが存在します。

同時デプロイによる競合を回避し、デプロイ履歴に一意のエントリが確実に存在するようにするには、各デプロイに一意の名前を付けます。

## <a name="next-steps"></a>次のステップ

- エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- 非同期 REST 操作の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](../management/async-operations.md)」を参照してください。
- テンプレートの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
