---
title: 仮想マシン スケール セットでマネージド ID を使用するためにテンプレートを構成する - Azure AD
description: Azure Resource Manager テンプレートを使用して、仮想マシン スケール セットで Azure リソースのマネージド ID を構成するための詳細な手順。
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
ms.openlocfilehash: 377bbb9ce111f3cf2daf8426e128186711c30e5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587453"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>テンプレートを使用して Azure 仮想マシン スケール セットで Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。

この記事では、Azure Resource Manager デプロイ テンプレートを使用して、Azure 仮想マシン スケール セットで Azure リソースのマネージド ID の次の操作を実行する方法を説明します。
- Azure 仮想マシン スケール セットでシステム割り当てマネージド ID を有効および無効にする
- Azure 仮想マシン スケール セットでユーザー割り当てマネージド ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次の Azure のロールベースのアクセス制御の割り当てが必要です。

    > [!NOTE]
    > Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

    - [仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): 仮想マシン スケール セットを作成する、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効にする、および仮想マシン スケール セットから削除することができるロールです。
    - [マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロール。ユーザー割り当てマネージド ID を作成します。
    - [マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator)ロール。ユーザー割り当てマネージド ID の仮想マシン スケール セットへの割り当ておよび仮想マシン スケール セットからの削除を実行します。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

Azure portal とスクリプトを使う場合と同じように、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) テンプレートを使うと、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイすることができます。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

   - [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
   - [元のデプロイ](../../azure-resource-manager/templates/export-template-portal.md)または[デプロイの現在の状態](../../azure-resource-manager/templates/export-template-portal.md)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
   - ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
   - Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)を使用して、テンプレートを作成およびデプロイします。  

選択するオプションにかかわらず、初めてのデプロイ時も再デプロイ時もテンプレートの構文は同じです。 新規または既存の VM での、Azure リソースのマネージド ID の有効化も同様に行われます。 また、既定で Azure Resource Manager はデプロイに対して[増分更新](../../azure-resource-manager/templates/deployment-modes.md)を行います。

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure Resource Manager テンプレートを使用して、システム割り当てマネージド ID を有効および無効にします。

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成時に、または既存の仮想マシン スケール セットでシステム割り当てマネージド ID を有効にする

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。
2. システム割り当てマネージド ID を有効にするには、テンプレートをエディターに読み込み、resources セクション内で対象の `Microsoft.Compute/virtualMachinesScaleSets` リソースを探し、`"type": "Microsoft.Compute/virtualMachinesScaleSets"` プロパティと同じレベルに `identity` プロパティを追加します。 次の構文を使用します。

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> オプションで、Azure リソース仮想マシン スケール セット拡張機能のマネージド ID をプロビジョニングできます。そのためには、これをテンプレートの `extensionProfile` 要素内に指定します。 Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。  詳細については、[認証のための VM 拡張機能から Azure IMDS への移行](howto-migrate-vm-extension.md)に関するページを参照してください。


4. 完了すると、次のセクションがテンプレートのリソース セクションに追加されます。次のようなセクションになります。

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当てマネージド ID を無効にする

システム割り当てマネージド ID が不要になった仮想マシン スケール セットがある場合:

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 システム割り当てマネージド ID のみが割り当てられた VM がある場合は、ID の種類を `None` に変更することで無効にすることができます。

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2018-06-01**

   お使いの apiVersion が `2018-06-01` であり、VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方が割り当てられている場合は、ID の種類から `SystemAssigned` を削除し、userAssignedIdentities ディクショナリ値と共に `UserAssigned` を保持します。

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2018-06-01**

   お使いの apiVersion が `2017-12-01` であり、仮想マシン スケール セットにシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方が割り当てられている場合は、ID の種類から `SystemAssigned` を削除し、ユーザー割り当てマネージド ID の `identityIds` 配列と共に `UserAssigned` を保持します。



   次の例は、ユーザー割り当てマネージド ID が割り当てられていない仮想マシン スケール セットからシステム割り当てマネージド ID を削除する方法を示しています。

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure Resource Manager テンプレートを使用して、仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てます。

> [!Note]
> Azure Resource Manager テンプレートを使用してユーザー割り当てマネージド ID を作成するには、「[Create a user-assigned managed identity (ユーザー割り当てマネージド ID を作成する)](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)」をご覧ください。

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>仮想マシン スケール セットにユーザー割り当て マネージド ID を割り当てる

1. ユーザー割り当てマネージド ID を仮想マシン スケール セットに割り当てるには、`resources` 要素に次のエントリを追加します。  `<USERASSIGNEDIDENTITY>` は、作成したユーザー割り当てマネージド ID の名前に置き換えてください。

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2018-06-01**

   お使いの apiVersion が `2018-06-01` の場合、ユーザー割り当てマネージド ID は `userAssignedIdentities` ディクショナリ形式で格納されます。`<USERASSIGNEDIDENTITYNAME>` 値は、テンプレートの `variables` セクションに定義された変数に格納する必要があります。

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2017-12-01**

   お使いの `apiVersion` が `2017-12-01` 以前の場合、ユーザー割り当てマネージド ID は `identityIds` 配列に格納されます。`<USERASSIGNEDIDENTITYNAME>` 値は、テンプレートの variables セクションに定義された変数に格納する必要があります。

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> オプションで、Azure リソース仮想マシン スケール セット拡張機能のマネージド ID をプロビジョニングできます。そのためには、これをテンプレートの `extensionProfile` 要素内に指定します。 Azure Instance Metadata Service (IMDS) の ID エンドポイントを使ってトークンを取得することもできるため、このステップは省略可能です。  詳細については、[認証のための VM 拡張機能から Azure IMDS への移行](howto-migrate-vm-extension.md)に関するページを参照してください。

3. 完了すると、テンプレートは以下の例のようになります。

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API バージョン 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID が不要になった仮想マシン スケール セットがある場合は、次の手順に従います。

1. Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。

2. テンプレートを[エディター](#azure-resource-manager-templates)に読み込み、`resources` セクション内で関心のある `Microsoft.Compute/virtualMachineScaleSets` リソースを探します。 ユーザー割り当てマネージド ID しか存在しない仮想マシン スケール セットがある場合は、ID の種類を `None` に変更することによってそれを無効にすることができます。

   次の例は、システム割り当てマネージド ID が割り当てられていない VM からユーザー割り当てマネージド ID をすべて削除する方法を示しています。

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2018-06-01**

   仮想マシン スケール セットから 1 つのユーザー割り当てマネージド ID を削除するには、`userAssignedIdentities` ディクショナリからそれを削除します。

   システム割り当て ID がある場合は、`identity` 値の `type` 値でそれを保持します。

   **Microsoft.Compute/virtualMachineScaleSets API バージョン 2017-12-01**

   仮想マシン スケール セットから 1 つのユーザー割り当てマネージド ID を削除するには、`identityIds` 配列からそれを削除します。

   システム割り当てマネージド ID がある場合は、`identity` 値の `type` 値でそれを保持します。

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)