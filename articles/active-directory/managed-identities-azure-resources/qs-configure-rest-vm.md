---
title: Azure VM で REST を使用してマネージド ID を構成する - Azure AD
description: REST API を呼び出す CURL を使用して、Azure VM でシステム割り当てマネージド ID とユーザー割り当てマネージド ID を構成するための詳細な手順について説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587521"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>REST API 呼び出しを使用して Azure VM 上に Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動管理されるシステム ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Resource Manager REST エンドポイントを呼び出す CURL を使用して、Azure VM で次の Azure リソースのマネージド ID 操作を実行する方法について説明します。

- Azure VM 上でシステム割り当てマネージド ID を有効および無効にする
- Azure VM 上でユーザー割り当てマネージド ID を追加および削除する

まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID について不明な場合は、「[Azure リソースのマネージド ID とは](overview.md)」を参照してください。 システム割り当てとユーザー割り当ての両方の種類のマネージド ID の詳細については、「[マネージド ID の種類](overview.md#managed-identity-types)」をご覧ください。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、Azure VM でシステム割り当てマネージド ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM の作成中にシステム割り当てマネージド ID を有効にする

システム割り当てマネージド ID を有効にして Azure VM を作成するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. [az group create](/cli/azure/group/#az-group-create) を使用して、VM とその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/management/overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. ご利用の VM の[ネットワーク インターフェイス](/cli/azure/network/nic#az-network-nic-create)を作成します。

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Azure Cloud Shell を使用して、Azure Resource Manager REST エンドポイントを呼び出す CURL を使って VM を作成します。 次の例では、値 `"identity":{"type":"SystemAssigned"}` によって要求本文で識別される、システム割り当てマネージド ID を使用して *myVM* という VM を作成します。 `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **要求ヘッダー**
   
   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 
   
   **要求本文**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>既存の Azure VM においてシステム割り当て ID を有効にする

もともとシステム割り当てマネージド ID をプロビジョニングされていなかった VM でシステム割り当てマネージド ID を有効にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. ご利用の VM で、*myVM* という VM の値 `{"identity":{"type":"SystemAssigned"}` により要求本文で識別される、システム割り当てマネージド ID を有効にするには、以下の CURL コマンドを使用して Azure Resource Manager REST エンドポイントを呼び出します。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
   
   > [!IMPORTANT]
   > VM に割り当てられている既存のユーザー割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用してユーザー割り当てマネージド ID を一覧表示する必要があります。`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 応答の `identity` 値で識別される、ユーザー割り当てマネージド ID が VM に割り当てられている場合は、VM でシステム割り当てマネージド ID を有効にしている間、ユーザー割り当てマネージド ID を保持する方法を示す手順 3 に進みます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 
   
   **要求本文**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. 既存のユーザー割り当てマネージド ID を持つ VM でシステム割り当てマネージド ID を有効にするには、`SystemAssigned` を `type` 値に追加する必要があります。  
   
   たとえば、VM にユーザー割り当てマネージド ID `ID1` と `ID2` が割り当てられている状態で、VM にシステム割り当てマネージド ID を追加する場合は、次の CURL 呼び出しを使用します。 `<ACCESS TOKEN>` と `<SUBSCRIPTION ID>` は、ご利用の環境に適した値に置き換えます。

   API バージョン `2017-12-01` では、ユーザー割り当てマネージド ID が `identityIds` 値に配列形式で保存されていましたが、API バージョン `2018-06-01` では `userAssignedIdentities` 値にディクショナリ形式で保存されます。
   
   **API バージョン 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API バージョン 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM でシステム割り当てマネージド ID を無効にする

VM でシステム割り当てマネージド ID を無効にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、システム割り当てマネージド ID を無効にして VM を更新します。  次の例では、*myVM* という VM から値 `{"identity":{"type":"None"}}` によって要求本文で識別される、システム割り当てマネージド ID を無効にします。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   > [!IMPORTANT]
   > VM に割り当てられている既存のユーザー割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用してユーザー割り当てマネージド ID を一覧表示する必要があります。`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` 応答の `identity` 値で識別される、ユーザー割り当てマネージド ID が VM に割り当てられている場合は、VM でシステム割り当てマネージド ID を無効にしている間、ユーザー割り当てマネージド ID を保持する方法を示す手順 3 に進みます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   **API バージョン 2018-06-01** を使用している場合、ユーザー割り当てマネージド ID を持つ仮想マシンからシステム割り当てマネージド ID を削除するには、`UserAssigned` 値と `userAssignedIdentities` ディクショナリ値を維持したまま、`{"identity":{"type:" "}}` 値から `SystemAssigned` を削除します。 **API バージョン 2017-12-01** またはそれ以前を使用している場合は、`identityIds` 配列を維持します。

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、Azure VM でユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Azure VM の作成中にユーザー割り当てマネージド ID を割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. ご利用の VM の[ネットワーク インターフェイス](/cli/azure/network/nic#az-network-nic-create)を作成します。

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 次のセクション「[ユーザー割り当てマネージド ID を作成する](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)」の手順を使用して、ユーザー割り当てマネージド ID を作成します。

5. CURL を使用して Azure Resource Manager REST エンドポイントを呼び出し、VM を作成します。 次の例では、値 `"identity":{"type":"UserAssigned"}` によって要求本文で識別される、ユーザー割り当てマネージド ID `ID1` を使用して、リソース グループの *myResourceGroup* で *myVM* という VM を作成します。 `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。
 
   **API バージョン 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API バージョン 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>ユーザー割り当てマネージド ID を既存の Azure VM に割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  「[Create a user assigned managed identity](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)」(ユーザー割り当てマネージド ID を作成する) に示されている手順を使用して、ユーザー割り当てマネージド ID を作成します。

3. VM に割り当てられている既存のユーザーまたはシステム割り当てマネージド ID を削除しないようにするには、次の CURL コマンドを使用して、VM に割り当てられている ID の種類を一覧表示する必要があります。 仮想マシン スケール セットにマネージド ID が割り当てられている場合、`identity` 値に一覧表示されます。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。

    応答の `identity` 値で識別される、ユーザーまたはシステム割り当てマネージド ID が VM に割り当てられている場合は、VM でユーザー割り当てマネージド ID を追加しながら、システム割り当てマネージド ID を保持する方法を示す手順 5 に進みます。

4. VM にユーザー割り当てマネージド ID が割り当てられていない場合は、次の CURL コマンドを使用して Azure Resource Manager REST エンドポイントを呼び出し、VM に最初のユーザー割り当てマネージド ID を割り当てます。

   次の例では、リソース グループの *myResourceGroup* 内の *myVM* という VM に、ユーザー割り当てマネージド ID `ID1` を割り当てます。  `<ACCESS TOKEN>` は前の手順で Bearer アクセス トークンを要求したときに受け取った値に置き換え、`<SUBSCRIPTION ID>` 値はご利用の環境に合わせて置き換えます。

   **API バージョン 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |
 
   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API バージョン 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. VM に既存のユーザー割り当てマネージド ID またはシステム割り当てマネージド ID が割り当てられている場合:
   
   **API バージョン 2018-06-01**

   ユーザー割り当てマネージド ID を `userAssignedIdentities` ディクショナリ値に追加します。
    
   たとえば、現在、VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID `ID1` が割り当てられており、これにユーザー割り当てマネージド ID `ID2` を追加する場合は、次のようにします。

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API バージョン 2017-12-01**

   新しいユーザー割り当てマネージド ID を追加する一方で、`identityIds` 配列値に維持するユーザー割り当てマネージド ID を保持します。

   たとえば、現在、VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID `ID1` が割り当てられており、これにユーザー割り当てマネージド ID `ID2` を追加する場合は、次のようにします。 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当てのマネージド ID を削除する

VM からユーザー割り当ての ID を削除にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。

1. Bearer アクセス トークンを取得します。このトークンは、Authorization ヘッダーでシステム割り当てマネージド ID を使用して VM を作成する次の手順で使用します。

   ```azurecli-interactive
   az account get-access-token
   ```

2. VM に割り当てたままにする既存のユーザー割り当てマネージド ID を削除しないようにするか、システム割り当てマネージド ID を削除するには、次の CURL コマンドを使用して、マネージド ID を一覧表示する必要があります。 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。
 
   VM にマネージド ID が割り当てられている場合、応答の `identity` 値に一覧表示されます。

   たとえば、ユーザー割り当てマネージド ID `ID1` と `ID2` が VM 割り当てられており、`ID1` を割り当てられたままにしてシステム割り当て ID を保持する場合:
   
   **API バージョン 2018-06-01**

   次のように、削除するユーザー割り当てマネージド ID に `null` を追加します。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API バージョン 2017-12-01**

   次のように、`identityIds` 配列に維持するユーザー割り当てマネージド ID のみを保持します。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **要求ヘッダー**

   |要求ヘッダー  |説明  |
   |---------|---------|
   |*Content-Type*     | 必須。 `application/json` を設定します。        |
   |*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方がある場合は、次のコマンドを使用してシステム割り当てマネージド ID のみを使用するように切り替えることによって、すべてのユーザー割り当てマネージド ID を削除できます。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**要求ヘッダー**

|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。 | 

**要求本文**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
VM にユーザー割り当てマネージド ID のみがあり、そのすべてを削除する場合は、次のコマンドを使用します。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**要求ヘッダー**

|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。| 

**要求本文**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>次のステップ

REST を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する方法については、以下を参照してください。

- [REST API 呼び出しを使用して、ユーザー割り当てマネージド ID を作成、一覧表示、削除する](how-to-manage-ua-identity-rest.md)
