---
title: Azure VM でテンプレートを使用してマネージド ID を構成する - Azure AD
description: Azure Resource Manager テンプレートを使用して、Azure VM で Azure リソースのマネージド ID を構成するための詳細な手順。
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
ms.openlocfilehash: 4948e17d1e0e782a8fa18c3eb5a2185e816a459a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631406"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>テンプレートを使用して Azure VM で Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。

この記事では、Azure Resource Manager デプロイ テンプレートを使用して、Azure VM で Azure リソースのマネージド ID の次の操作を実行する方法を説明します。

## <a name="prerequisites"></a>前提条件

- Azure Resource Manager デプロイ テンプレートを使い慣れていない場合は、[概要セクション](overview.md)をご覧ください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

Azure portal とスクリプトを使う場合と同じように、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) テンプレートを使うと、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイすることができます。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

   - [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
   - [元のデプロイ](../../azure-resource-manager/templates/export-template-portal.md)または[デプロイの現在の状態](../../azure-resource-manager/templates/export-template-portal.md)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
   - ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
   - Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)を使用して、テンプレートを作成およびデプロイします。  

選択するオプションにかかわらず、初めてのデプロイ時も再デプロイ時もテンプレートの構文は同じです。 新規または既存の VM での、システムまたはユーザー割り当てマネージド ID の有効化も同様に行われます。 また、既定で Azure Resource Manager はデプロイに対して[増分更新](../../azure-resource-manager/templates/deployment-modes.md)を行います。

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure Resource Manager テンプレートを使用して、システム割り当てマネージド ID を有効および無効にします。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Azure VM の作成時に、または既存の VM でシステム割り当てマネージド ID を有効にする

VM でシステム割り当てマネージド ID を有効にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. システム割り当てマネージド ID を有効にするには、テンプレートをエディターに読み込み、`resources` セクション内で対象の `Microsoft.Compute/virtualMachines` リソースを探し、`"type": "Microsoft.Compute/virtualMachines"` プロパティと同じレベルに `"identity"` プロパティを追加します。 次の構文を使用します。

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. 完了すると、テンプレートの `resource` セクションに次のセクションが追加され、テンプレートは次のようになります。

   ```JSON
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>VM のシステム割り当てマネージド ID にロールを割り当てる

VM でシステム割り当てマネージド ID を有効にしたら、作成先となったリソース グループへの **閲覧者** アクセス権などのロールを付与することができます。

VM のシステム割り当て ID にロールを割り当てるには、お使いのアカウントに[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールの割り当てが必要です。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを [エディター](#azure-resource-manager-templates)に読み込み、以下の情報を追加して、それが作成されたリソース グループへの **閲覧者** アクセス許可をご利用の VM に付与します。  テンプレートの構造は、選択したデプロイ モデルとエディターに応じて異なる場合があります。

   `parameters` セクションの下に、以下を追加します。

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    `variables` セクションの下に、以下を追加します。

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    `resources` セクションの下に、以下を追加します。

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM でシステム割り当てマネージド ID を無効にする

VM からシステム割り当てマネージド ID を削除するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachines` リソースを探します。 システム割り当てマネージド ID のみが割り当てられた VM がある場合は、ID の種類を `None` に変更することで無効にすることができます。  

   **Microsoft.Compute/virtualMachines API バージョン 2018-06-01**

   VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方が割り当てられている場合は、ID の種類から `SystemAssigned` を削除し、`userAssignedIdentities` ディクショナリ値と共に `UserAssigned` を保持します。

   **Microsoft.Compute/virtualMachines API バージョン 2018-06-01**

   `apiVersion` が `2017-12-01` であり、VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方が割り当てられている場合は、ID の種類から `SystemAssigned` を削除し、ユーザー割り当てマネージド ID の `identityIds` 配列と共に `UserAssigned` を保持します。  

次の例は、ユーザー割り当てマネージド ID が割り当てられていない VM からシステム割り当てマネージド ID を削除する方法を示しています。

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure Resource Manager テンプレートを使用して、Azure VM にユーザー割り当てマネージド ID を割り当てます。

> [!Note]
> Azure Resource Manager テンプレートを使用してユーザー割り当てマネージド ID を作成するには、「[Create a user-assigned managed identity (ユーザー割り当てマネージド ID を作成する)](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)」をご覧ください。

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Azure VM にユーザー割り当てマネージド ID を割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. ユーザー割り当てマネージド ID を VM に割り当てるには、`resources` 要素に次のエントリを追加します。  `<USERASSIGNEDIDENTITY>` は、作成したユーザー割り当てマネージド ID の名前に置き換えてください。

   **Microsoft.Compute/virtualMachines API バージョン 2018-06-01**

   `apiVersion` が `2018-06-01` の場合、ユーザー割り当てマネージド ID は `userAssignedIdentities` ディクショナリ形式で格納されます。`<USERASSIGNEDIDENTITYNAME>` 値は、テンプレートの `variables` セクションに定義された変数に格納する必要があります。

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines API バージョン 2017-12-01**

   `apiVersion` が `2017-12-01` の場合、ユーザー割り当てマネージド ID は `identityIds` 配列に格納されます。`<USERASSIGNEDIDENTITYNAME>` 値は、テンプレートの `variables` セクションに定義された変数に格納する必要があります。

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. 完了すると、テンプレートの `resource` セクションに次のセクションが追加され、テンプレートは次のようになります。

   **Microsoft.Compute/virtualMachines API バージョン 2018-06-01**    

   ```JSON
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```
   **Microsoft.Compute/virtualMachines API バージョン 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当てのマネージド ID を削除する

VM からユーザー割り当て ID を削除するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachines` リソースを探します。 ユーザー割り当てマネージド ID しか存在しない VM がある場合は、ID の種類を `None` に変更することによってそれを無効にすることができます。

   次の例は、システム割り当てマネージド ID が割り当てられていない VM からユーザー割り当てマネージド ID をすべて削除する方法を示しています。

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines API バージョン 2018-06-01**

   VM から 1 つのユーザー割り当てマネージド ID を削除するには、`useraAssignedIdentities` ディクショナリからそれを削除します。

   システム割り当てマネージド ID がある場合は、`identity` 値の `type` 値でそれを保持します。

   **Microsoft.Compute/virtualMachines API バージョン 2017-12-01**

   VM から 1 つのユーザー割り当てマネージド ID を削除するには、`identityIds` 配列からそれを削除します。

   システム割り当てマネージド ID がある場合は、`identity` 値の `type` 値でそれを保持します。

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
