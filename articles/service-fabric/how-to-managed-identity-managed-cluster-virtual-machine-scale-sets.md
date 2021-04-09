---
title: Service Fabric マネージド クラスターのノード　タイプにマネージド ID を追加する (プレビュー)
description: この記事では、Service Fabric マネージド クラスターのノード　タイプにマネージド ID を追加する方法について説明します
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3ff5d66160ddbb037469378634826fd9eeae0c54
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651648"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Service Fabric マネージド クラスターのノード　タイプにマネージド ID を追加する (プレビュー)

Service Fabric 管理対象クラスター内の各ノード タイプは、仮想マシン スケール セットによって提供されます。 マネージド クラスターのノード タイプでマネージド ID を使用できるようにするために、プロパティ `vmManagedIdentity` が、使用される ID の一覧 `userAssignedIdentities` を含むノード タイプ定義に追加されました。 機能には、マネージド ID を非マネージド クラスター内でどのように使用できるか (たとえば、[Azure Key Vault 仮想マシン スケール セット拡張機能](../virtual-machines/extensions/key-vault-windows.md)でマネージド ID を使用するなど) が反映されます。


ノード タイプでマネージド ID を利用する Service Fabric マネージド クラスターのデプロイの例については、[こちらのテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)を参照してください。 サポートされているリージョンの一覧については、「[マネージド クラスターに関してよく寄せられる質問](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview)」をご覧ください。

> [!NOTE]
> 現在、この機能でサポートされているのはユーザー割り当て ID みです。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、次のことを行います。

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* PowerShell の使用を計画している場合は、CLI リファレンス コマンドを実行するために Azure CLI を[インストール](/cli/azure/install-azure-cli)します。

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てのマネージド ID は、デプロイ時に作成するために、Azure Resource Manager (ARM) テンプレートのリソース セクションで定義できます。

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

または PowerShell を使用して作成されます。

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Service Fabric リソース プロバイダーでロールの割り当てを追加する

Service Fabric リソース プロバイダー アプリケーションを使用して、マネージド ID にロールの割り当てを追加します。 この割り当てにより、Service Fabric リソース プロバイダーは、マネージド クラスターの仮想マシン スケール セットに ID を割り当てることができます。 

次の値を該当する場所で使用する必要があります。

|名前|対応する Service Fabric リソース プロバイダーの値|
|----|-------------------------------------|
|アプリケーション ID|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|Object ID|fbc587f2-66f5-4459-a027-bcd908b9d278|


|ロール定義名|ロール定義 ID|
|----|-------------------------------------|
|Managed Identity Operator|f1a07417-d97a-45cb-824c-7a7467783830
|



このロールの割り当ては、リソース セクションでオブジェクト ID とロール定義 ID を使用して定義できます。

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

または、アプリケーション ID とロール定義 ID のいずれかを使用して、PowerShell で作成されます。

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

またはオブジェクト ID とロール定義 ID。

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>マネージド ID プロパティをノード タイプ定義に追加する

最後に、`vmManagedIdentity` と `userAssignedIdentities` プロパティを、マネージド クラスターのノード タイプ定義に追加します。 `apiVersion` には、必ず **2021-01-01-preview** 以降を使用してください。

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-01-01-preview",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
            ]
        }
    }
}
```

デプロイ後、作成されたマネージド ID は指定されたノード タイプの仮想マシン スケール セットに追加されており、非マネージド クラスターと同様に、想定どおりに使用できます。

## <a name="troubleshooting"></a>トラブルシューティング

ロールの割り当てを適切に追加できなかった場合は、デプロイ時に次のエラーが発生します。

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure portal deployment error showing the client with SFRP's object/application ID not having permission to perform identity management activity (SFRP のオブジェクトまたはアプリケーション ID を持つクライアントに、ID 管理アクティビティを実行する権限がないことを示す Azure portal のデプロイ エラー)":::

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスターにアプリをデプロイする](./tutorial-managed-cluster-deploy-app.md)