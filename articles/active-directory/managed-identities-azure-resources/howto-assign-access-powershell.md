---
title: PowerShell を使用して、リソースにマネージド ID アクセスを割り当てる - Azure AD
description: PowerShell を使用して、1 つのリソースにマネージド ID を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9fcca72234340a6284dbba5443ae6fb735d4a04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608281"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>PowerShell を使用して、リソースにマネージド ID アクセスを割り当てる

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースにマネージド ID アクセスを付与できます。 この例では、PowerShell を使用して、Azure 仮想マシンのマネージド ID アクセスを Azure ストレージ アカウントに付与する方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- [最新バージョンの Azure PowerShell](/powershell/azure/install-az-ps) をインストールします (まだインストールしていない場合)。

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる

[Azure VM などの](qs-configure-powershell-windows-vm.md) Azure リソースでマネージド ID を有効にした後、次のようにします。

1. `Connect-AzAccount` コマンドレットを使用して Azure にサインインします。 次のように、マネージド ID を構成した Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```powershell
   Connect-AzAccount
   ```
2. この例では、ストレージ アカウントに Azure VM アクセスを許可しています。 まず、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用して `myVM` という VM のサービス プリンシパルを取得します。これは、マネージド ID が有効になっているときに作成されたものです。 次に、[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) を使用して、VM の**閲覧者**アクセスを `myStorageAcct` というストレージ アカウントに付与します。

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure VM 上でマネージド ID を有効にするには、「[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](qs-configure-powershell-windows-vm.md)」を参照してください。
