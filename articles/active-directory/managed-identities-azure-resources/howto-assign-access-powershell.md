---
title: PowerShell を使用して、リソースにマネージド ID アクセスを割り当てる - Azure AD
description: PowerShell を使用して、1 つのリソースにマネージド ID を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf1a7a608df7a2b752d9a6bed52a4024fd776c5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592502"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>PowerShell を使用して、リソースにマネージド ID アクセスを割り当てる

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースにマネージド ID アクセスを付与できます。 この例では、PowerShell を使用して、Azure 仮想マシンのマネージド ID アクセスを Azure ストレージ アカウントに付与する方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[Try It]\(使ってみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールしてスクリプトをローカルで実行した後、`Connect-AzAccount` を使用して Azure にサインインします。 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる

1. [Azure VM などの](qs-configure-powershell-windows-vm.md) Azure リソースでマネージド ID を有効にします。

1. この例では、ストレージ アカウントに Azure VM アクセスを許可しています。 まず、[Get-AzVM](/powershell/module/az.compute/get-azvm) を使用して `myVM` という VM のサービス プリンシパルを取得します。これは、マネージド ID が有効になっているときに作成されたものです。 次に、[New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) を使用して、VM の **閲覧者** アクセスを `myStorageAcct` というストレージ アカウントに付与します。

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure VM 上でマネージド ID を有効にするには、「[PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する](qs-configure-powershell-windows-vm.md)」を参照してください。
