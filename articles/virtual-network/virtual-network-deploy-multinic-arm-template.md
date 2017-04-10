---
title: "複数の NIC を持つ VM の作成 - Azure Resource Manager テンプレート | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して、複数の NIC を持つ VM を作成します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9f9908466dbfd471bc881387c0968c924140c67b
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>テンプレートを使用して複数の NIC を持つ VM を作成する
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](virtual-network-deploy-multinic-classic-ps.md)ではなくこのモデルをお勧めします。
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

次の手順では、*IaaSStory* という名前のリソース グループを WEB サーバーに、*IaaSStory-BackEnd* という名前のリソース グループを DB サーバーに使用します。

## <a name="prerequisites"></a>前提条件
DB サーバーを作成する前に、このシナリオで必要なすべてのリソースを備えた *IaaSStory* リソース グループを作成する必要があります。 これらのリソースを作成するには、次の手順に従います。

1. [テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)に移動します。
2. テンプレート ページの **[親リソース グループ]** の右側にある **[Azure へのデプロイ]** をクリックします。
3. 必要に応じて、パラメーター値を変更し、Azure プレビュー ポータル内の手順に従ってリソース グループをデプロイします。

> [!IMPORTANT]
> ストレージ アカウント名が一意であることを確認してください。 Azure では重複するストレージ アカウント名を使用できません。
> 

## <a name="understand-the-deployment-template"></a>デプロイ テンプレートについて
このドキュメントに付属するテンプレートをデプロイする前に、テンプレートを理解する必要があります。 次の手順では、テンプレートの概要についてわかりやすく説明しています。

1. [テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)に移動します。
2. **azuredeploy.json** をクリックしてテンプレート ファイルを開きます。
3. 次に示す *osType* パラメーターに注目してください。 このパラメーターは、複数のオペレーティング システム関連の設定と共に、データベース サーバー用の VM イメージを選択するために使用します。

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. 下にスクロールして変数の一覧を表示し、次に示す **dbVMSetting** 変数の定義を確認します。 これは、 **dbVMSettings** 変数に格納されている配列要素の&1; つを受け取ります。 ソフトウェア開発用語に詳しい場合は、 **dbVMSettings** 変数をハッシュテーブルまたはディクショナリと考えることができます。

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. バック エンドで SQL が動作している Windows VM をデプロイするとします。 **osType** の値は *Windows* となり、**bVMSetting** 変数には次に示す要素 (**dbVMSettings** 変数の最初の値を表す) が格納されます。

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. **vmSize** に値 *Standard_DS3* が格納されることに注意してください。 特定の VM のサイズに対してのみ、複数の NIC の使用が許可されます。 複数の NIC をサポートしている VM のサイズの確認方法については、[Windows Server VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)と [Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

7. 下にスクロールして **resources** を表示し、最初の要素に注目してください。 ここでは、ストレージ アカウントについて記述しています。 このストレージ アカウントは、各データベース VM で使用されるデータ ディスクの管理に使用されます。 このシナリオでは、各データベース VM で、OS ディスクが標準ストレージに格納され、2 つのデータ ディスクが SSD (Premium) ストレージに格納されます。

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. 下にスクロールして、次に示す次のリソースを表示します。 このリソースは、各データベース VM でデータベース アクセスに使用される NIC を表しています。 このリソースで **copy** 関数を使用していることに注目してください。 このテンプレートを使用すると、 **dbCount** パラメーターに基づいて、必要な数の VM をデプロイできます。 したがって、データベース アクセス用に同じ数 (VM ごとに&1; つ) の NIC を作成する必要があります。

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. 下にスクロールして、次に示す次のリソースを表示します。 このリソースは、各データベース VM で管理に使用される NIC を表しています。 ここでも、データベース VM ごとにこれらの NIC が&1; つ必要です。 RDP/SSH へのアクセスを許可する NSG をこの NIC のみにリンクする、 **networkSecurityGroup** 要素に注目してください。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. 下にスクロールして、次に示す次のリソースを表示します。 このリソースは、すべてのデータベース VM によって共有される可用性セットを表しています。 このように、保守中に動作するセット内に常に&1; 台の VM が存在するようにします。

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. 下にスクロールして、次のリソースを表示します。 次の最初の数行が示すように、このリソースはデータベース VM を表しています。 ここでも、**copy** 関数を使用していることに注目し、複数の VM が **dbCount** パラメーターに基づいて作成されることを確認してください。 また、 **dependsOn** コレクションにも注目してください。 可用性セット、ストレージ アカウントと共に、VM をデプロイするときに、事前に作成する必要のある&2; つの NIC が示されます。

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. VM リソース内を下にスクロールして、次に示す **networkProfile** 要素を表示します。 各 VM で参照される&2; つの NIC があることに注目してください。 VM に対して複数の NIC を作成する場合は、1 つの NIC の **primary** プロパティを *true* に設定し、残りを *false* に設定する必要があります。

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>[デプロイ] をクリックして ARM テンプレートをデプロイする

> [!IMPORTANT]
> 次の手順を実行する前に、 [前提条件](#Pre-requisites) の手順に従っていることを確認してください。
> 

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。 "クリックしてデプロイ" を使用してこのテンプレートをデプロイするには、[このリンク](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)に従って、**バックエンド リソース グループ (ドキュメントを参照)** の右側にある **[Azure へのデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

次の図は、デプロイ後の新しいリソース グループの内容を示しています。

![Back end resource group](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>PowerShell を使用してテンプレートをデプロイする
PowerShell を使用してダウンロードしたテンプレートをデプロイするには、「[Install and configure PowerShell (PowerShell のインストールと構成)](/powershell/azureps-cmdlets-docs)」の記事にある手順で PowerShell をインストールし構成してから、次の手順を実行します。

テンプレートを使用してリソース グループを作成するには、 **`New-AzureRmResourceGroup`** コマンドレットを実行します。

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

予想される出力:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Azure CLI を使用してテンプレートをデプロイする
Azure CLI を使用してテンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **`azure config mode`** コマンドを実行してリソース マネージャー モードに切り替えます。

    ```azurecli
    azure config mode arm
    ```

    予想される出力は次のとおりです。

        info:    New mode is arm

3. [パラメーター ファイル](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json)を開き、内容を選択して、コンピューター内のファイルに保存します。 この例では、パラメーター ファイルを *parameters.json*に保存しました。
4. **`azure group deployment create`** コマンドレットを実行し、上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用して新しい VNet をデプロイします。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    予想される出力:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK


