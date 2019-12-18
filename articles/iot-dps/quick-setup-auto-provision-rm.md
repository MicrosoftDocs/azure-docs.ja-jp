---
title: Azure Resource Manager テンプレートを使用した Azure IoT Hub Device Provisioning の設定
description: Azure クイックスタート - テンプレートを使用して Azure IoT Hub Device Provisioning Service (DPS) を設定する
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 482401b75cadf44e2cef03cced8dd216d0980524
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969583"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用して IoT Hub Device Provisioning Service を設定する

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使うと、デバイスのプロビジョニングで必要になる Azure のクラウド リソースをプログラミングを使って設定することができます。 ここで紹介する手順では、Azure Resource Manager テンプレートを使用して IoT ハブおよび新しい IoT Hub Device Provisioning Service を作成し、その 2 つのサービスをリンクする方法を示します。 このクイック スタートでは、リソース グループの作成とテンプレートのデプロイに必要なプログラミング手順を実行するために [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) を使用していますが、[Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal)、[PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) のほか、.NET、Ruby などのプログラミング言語を使用してこれらの手順を実行し、テンプレートを簡単にデプロイできます。 


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このクイック スタートでは、Azure CLI をローカルで実行する必要があります。 また、Azure CLI バージョン 2.0 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Azure にサインインし、リソース グループを作成する

Azure アカウントにサインインしてサブスクリプションを選択します。

1. コマンド プロンプトで、[ログイン コマンド][lnk-login-command]を実行します。
    
    ```azurecli
    az login
    ```

    指示に従って、コードを使用して認証し、Web ブラウザーで Azure アカウントにサインインします。

2. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべてのAzure アカウントへのアクセスが許可されます。 以下の、利用できる [Azure アカウントを一覧表示するコマンド][lnk-az-account-command]を使用します。
    
    ```azurecli
    az account list 
    ```

    以下のコマンドを使用して、コマンドを実行して IoT ハブを作成するために使用するサブスクリプションを選択します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. IoT ハブ、プロビジョニング サービスなどの Azure クラウド リソースを作成するときには、リソース グループに作成します。 既存のリソース グループを使用するか、以下の、[リソース グループを作成するコマンド][lnk-az-resource-command]を実行します。
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 上の例では、West US という場所にリソース グループを作成します。 `az account list-locations -o table` コマンドを実行すると、利用できる場所を一覧表示できます。
    >
    >

## <a name="create-a-resource-manager-template"></a>Resource Manager テンプレートを作成する

JSON テンプレートを使用し、リソース グループにプロビジョニング サービスと、リンクされている IoT ハブを作成します。 Azure Resource Manager テンプレートを使用して、既存のプロビジョニング サービスまたは IoT ハブに変更を加えることもできます。

1. テキスト エディターを使用して、骨組みとなる次の内容を備えた **template.json** という Azure Resource Manager テンプレートを作成します。 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. **parameters** セクションを次の内容に置き換えます。 parameters セクションは、別のファイルから渡すことができる値を持つパラメーターを定義するものです。 このセクションには、作成する IoT ハブとプロビジョニング サービスの名前を定義します。 また、IoT ハブとプロビジョニング サービスの両方の場所もここで定義します。 指定できる値は、IoT ハブとプロビジョニング サービスをサポートしている Azure リージョンに限られます。 Device Provisioning Service をサポートしている場所の一覧は、次のコマンド `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` を実行するか、[[Azure の状態]](https://azure.microsoft.com/status/) ページに移動して "Device Provisioning Service" を検索すると確認できます。

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. **variables** セクションを次の内容に置き換えます。 プロビジョニング サービスと IoT ハブをリンクさせるには、IoT ハブの接続文字列が必要です。このセクションは、後でその IoT ハブの接続文字列を作成する際に使用する値を定義するものです。 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. IoT ハブを作成するために、**resources** コレクションに次の行を追加します。 この JSON では、IoT ハブを作成するために最低限必要なプロパティを指定しています。 **name** と **location** の値は、別のファイルからパラメーターとして渡されます。 テンプレート内の IoT ハブに指定できるプロパティの詳細については、[Microsoft.Devices/IotHubs テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs)に関する記事を参照してください。

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. プロビジョニング サービスを作成するために、**resources** コレクションで IoT ハブの詳細が書かれた場所よりも下に次の行を追加します。 プロビジョニング サービスの **name** と **location** は、パラメーターとして渡されます。 **iotHubs** コレクションには、プロビジョニング サービスにリンクさせる IoT ハブを指定します。 リンクさせる IoT ハブのそれぞれには、少なくとも **connectionString** と **location** の 2 つのプロパティを指定する必要があります。 このほか、各 IoT ハブには **allocationWeight**、**applyAllocationPolicy** などのプロパティ、プロビジョニング サービスそのものには **allocationPolicy**、**authorizationPolicies** などのプロパティを、それぞれ設定できます。 詳細については、「[Microsoft.Devices/provisioningServices template reference (Microsoft.Devices/provisioningServices のテンプレート リファレンス)](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices)」を参照してください。

   **dependsOn** プロパティを使用しているのは、Resource Manager がプロビジョニング サービスよりも先に IoT ハブを作成するようにするためです。 このテンプレートでは IoT ハブからプロビジョニング サービスへのリンクを指定するにあたって IoT ハブの接続文字列が必要なので、ハブとそのキーを最初に作成する必要があります。 テンプレートでは **concat**、**listKeys** などの関数を使用して、パラメーター化された変数から接続文字列を作成します。 詳細は、「[Azure Resource Manager テンプレートの関数](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)」を参照してください。

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. テンプレート ファイルを保存します。 完成したテンプレートは次のようになります。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Resource Manager パラメーター ファイルを作成する

前の手順で定義したテンプレートでは、IoT ハブの名前、プロビジョニング サービスの名前、およびこれらを作成する場所 (Azure リージョン) を指定するためにパラメーターを使用します。 これらのパラメーターは、別個のファイルからテンプレートに渡します。 このようにすることによって、同じテンプレートを多数のデプロイに再利用することができます。 パラメーター ファイルの作成手順は次のとおりです。

1. テキスト エディターを使用して、骨組みとなる次の内容を備えた **parameters.json** という Azure Resource Manager パラメーター ファイルを作成します。 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. parameter セクションに値 **iotHubName** を追加します。  IoT ハブ名は Azure でグローバルに一意である必要があるため、一意のプレフィックスまたはサフィックスを例の名前に追加するか、まったく新しい名前を選択できます。 名前は、IoT ハブの適切な名前付け規則に従う必要があります。長さは 3 から 50 文字にする必要があるほか、使用できるのは大文字または小文字の英数字とハイフン ("-") のみです。 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. parameter セクションに値 **provisioningServiceName** を追加します。 また、プロビジョニング サービスに対して、グローバルに一意の名前を選択する必要があります。 名前は、IoT Hub Device Provisioning Service の適切な名前付け規則に従う必要があります。長さは 3 から 64 文字にする必要があるほか、使用できるのは大文字または小文字の英数字とハイフン ("-") のみです。

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. parameter セクションに値 **hubLocation** を追加します。 この値は、IoT ハブとプロビジョニング サービスの両方の場所を指定するものです。 値は、テンプレート ファイルのパラメーターの定義にある **allowedValues** コレクションで指定した場所のいずれかに対応している必要があります。 このコレクションにより、値が IoT ハブとプロビジョニング サービスを両方ともサポートしている Azure リージョンに限定されます。 Device Provisioning Service をサポートしている場所の一覧は、コマンド `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` を実行するか、[[Azure の状態]](https://azure.microsoft.com/status/) ページに移動して "Device Provisioning Service" を検索すると確認できます。

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. ファイルを保存します。 


> [!IMPORTANT]
> IoT ハブとプロビジョニング サービスはどちらも DNS エンドポイントとして公開されます。このため、名前を付ける際は機密情報を含めないようにしてください。
>

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次の Azure CLI コメンドを使用してテンプレートをデプロイし、デプロイを確認します。

1. テンプレートをデプロイするには、テンプレートとパラメーター ファイルが格納されているフォルダーに移動し、次に示す[デプロイを開始するコマンド](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)を実行します。
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   この操作は、完了するまで数分かかる場合があります。 完了したら、出力で **provisioningState** プロパティが "Succeeded" に設定されていることを確認します。 

   ![プロビジョニングの出力](./media/quick-setup-auto-provision-rm/output.png) 


2. デプロイを確認するには、次に示す[リソースを列挙するコマンド](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list)を実行し、出力で新しいプロビジョニング サービスと IoT ハブを探します。

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 引き続きクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 引き続き作業を行わない場合には、Azure CLI を使用して、IoT Hub やプロビジョニング サービスなどの[個々のリソースを削除][lnk-az-resource-command]することも、リソース グループとそのすべてのリソースを削除することもできます。

プロビジョニング サービスを削除するには、次のコマンドを実行します。

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
IoT Hub を削除するには、次のコマンドを実行します。

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

リソース グループとそのすべてのリソースを削除するには、次のコマンドを実行します。

```azurecli
az group delete --name {your resource group name}
```

また、Azure portal、PowerShell、または REST API のほか、Azure Resource Manager または IoT Hub Device Provisioning Service 向けに公開済みのサポートされているプラットフォーム SDK を使って、リソース グループと個々のリソースを削除することもできます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT ハブと Device Provisioning Service インスタンスをデプロイし、この 2 つのリソースをリンクしました。 ここで行った設定を使用して、シミュレートされたデバイスをプロビジョニングする方法については、シミュレートされたデバイスの作成に関するクイックスタートを参照してください。

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを作成するためのクイックスタート](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
