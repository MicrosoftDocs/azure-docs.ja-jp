---
title: Azure Kubernetes Service クラスターの監視を停止する方法 | Microsoft Docs
description: この記事では、Container insights で Azure AKS クラスターの監視を停止する方法について説明します。
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: eab34f27d1e33b166971203082cce99fb2b0e106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723539"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>Container insights による Azure Kubernetes Service (AKS) の監視を停止する方法

AKS クラスターの監視を有効にした後、監視する必要がなくなったと判断した場合は、クラスターの監視を停止できます。 この記事では、Azure CLI を使用して、または提供されている Azure Resource Manager テンプレートで、これを行う方法を示します。  


## <a name="azure-cli"></a>Azure CLI

Container insights を無効にするには、[az aks disable-addons](/cli/azure/aks#az-aks-disable-addons) コマンドを使用します。 このコマンドでは、クラスター ノードからエージェントが削除されます。ソリューションや、既に収集されて Azure Monitor リソースに格納されているデータは削除されません。  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

クラスターの監視を再度有効にする方法については、「[Azure CLI を使用して監視を有効にする](container-insights-enable-new-cluster.md#enable-using-azure-cli)」をご覧ください。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

リソース グループ内のソリューション リソースの一貫した反復的な削除をサポートするため、2 つの Azure Resource Manager テンプレートが提供されています。 1 つは監視を停止する構成を指定する JSON テンプレートであり、もう一方には、クラスターがデプロイされている AKS クラスター リソース ID とリソース グループを指定するために構成するパラメーター値が含まれています。

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>テンプレートはクラスターの同じリソース グループ内にデプロイする必要があります。 このテンプレートの使用時にその他のいずれかのプロパティまたはアドオンを省略すると、それらがクラスターから削除される場合があります。 たとえば、ご利用のクラスターに実装されている Kubernetes RBAC ポリシーの *enableRBAC* が挙げられるほか、AKS クラスターにタグが指定されている場合は *aksResourceTagValues* が該当します。  
>

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

### <a name="create-template"></a>テンプレートの作成

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. このファイルを **OptOutTemplate.json** という名前でローカル フォルダーに保存します。

3. 以下の JSON 構文をファイルに貼り付けます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. **aksResourceId** と **aksResourceLocation** の値を、選択したクラスターの **[プロパティ]** ページに表示されている AKS クラスターの値を使用して編集します。

    ![コンテナーのプロパティ ページ](media/container-insights-optout/container-properties-page.png)

    **[プロパティ]** ページの **[ワークスペース リソース ID]** もコピーします。 この値は、後で Log Analytics ワークスペースを削除する場合に必要になります。 このプロセスでは、Log Analytics ワークスペースの削除は行いません。

    AKS クラスターに指定されている既存のタグ値に一致するように、**aksResourceTagValues** の値を編集します。

5. このファイルを **OptOutParam.json** という名前でローカル フォルダーに保存します。

6. これでこのテンプレートをデプロイする準備が整いました。

### <a name="remove-the-solution-using-azure-cli"></a>Azure CLI を使用してソリューションを削除する

Linux 上で Azure CLI を使用して次のコマンドを実行してソリューションを削除し、AKS クラスターの構成をクリーンアップします。

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>PowerShell を使用してソリューションを削除する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

テンプレートが保存されているフォルダーで次の PowerShell コマンドを実行してソリューションを削除し、AKS クラスターから構成をクリーンアップします。    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが返されます。

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>次のステップ

クラスターの監視をサポートするためだけにワークスペースが作成され、不要になった場合、手動で削除する必要があります。 ワークスペースを削除する方法の詳細については、「[Azure Portal で Azure Log Analytics ワークスペースを削除する](../logs/delete-workspace.md)」を参照してください。 上記の手順 4 でコピーした、**ワークスペース リソース ID** は、この後の手順で必要になります。