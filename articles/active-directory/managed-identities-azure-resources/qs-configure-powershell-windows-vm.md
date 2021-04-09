---
title: Azure VM で PowerShell を使用してマネージド ID を構成する - Azure AD
description: PowerShell を使用して、Azure VM で Azure リソースのマネージド ID を構成するための詳細な手順について説明します。
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
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4209ce159c9d0bbee01dd422b98832f6bb5713
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968993"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>PowerShell を使用して Azure VM 上の Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。

この記事では、PowerShell を使用して、Azure VM 上に次の Azure リソースのマネージド ID 操作を実行する方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[Try It]\(使ってみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールしてスクリプトをローカルで実行した後、`Connect-AzAccount` を使用して Azure にサインインします。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure PowerShell を使用してシステム割り当てマネージド ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM の作成中にシステム割り当てマネージド ID を有効にする

システム割り当てマネージド ID を有効にして Azure VM を作成するには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. 次のいずれかの Azure VM クイック スタートを参照して、必要なセクション (「Azure へのサインイン」、「リソース グループの作成」、「ネットワーク グループの作成」、「VM の作成」) のみを実行してください。

    「VM の作成」セクションに到達したときに、[New-AzVMConfig](/powershell/module/az.compute/new-azvm) コマンドレットの構文にわずかな変更を加えます。 必ず `-IdentityType SystemAssigned` パラメーターを追加し、システム割り当て ID を有効にして VM のプロビジョニングを行います。次に例を示します。

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>既存の Azure VM 上でシステム割り当てマネージド ID を有効にする

もともとシステム割り当てマネージド ID をプロビジョニングされていなかった VM でシステム割り当てマネージド ID を有効にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. `Get-AzVM` コマンドレットを使用して VM プロパティを取得します。 システム割り当てマネージド ID を有効にするには、[Update-AzVM](/powershell/module/az.compute/update-azvm) コマンドレットで `-IdentityType` スイッチを使用します。

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>VM のシステム割り当て ID をグループに追加する

VM でシステム割り当て ID を有効にした後は、その ID をグループに追加できます。  以降の手順では、VM のシステム割り当て ID をグループに追加します。

1. VM のサービス プリンシパルの `ObjectID` (返された値の `Id` フィールドで指定されています) を取得し、メモします。

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. グループの `ObjectID` (返された値の `Id` フィールドで指定されています) を取得し、メモします。

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. VM のサービス プリンシパルをグループに追加します。

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM でシステム割り当てマネージド ID を無効にする

VM でシステム割り当てマネージド ID を無効にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

システム割り当てマネージド ID は不要になったが、ユーザー割り当てマネージド ID はまだ必要な仮想マシンがある場合は、次のコマンドレットを使用します。

1. `Get-AzVM` コマンドレットを使用して VM プロパティを取得し、`-IdentityType` パラメーターを `UserAssigned` に設定します。

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

システム割り当てマネージド ID が不要になり、ユーザー割り当てマネージド ID がない仮想マシンがある場合は、次のコマンドを使用します。

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure PowerShell を使用して、VM との間でユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>作成中にユーザー割り当てマネージド ID を VM に割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. 次のいずれかの Azure VM クイック スタートを参照して、必要なセクション (「Azure へのサインイン」、「リソース グループの作成」、「ネットワーク グループの作成」、「VM の作成」) のみを実行してください。

    「VM の作成」セクションに到達したときに、[`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) コマンドレットの構文にわずかな変更を加えます。 `-IdentityType UserAssigned` および `-IdentityID` パラメーターを追加し、ユーザー割り当て ID を使用して VM のプロビジョニングを行います。  `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、および `<USER ASSIGNED IDENTITY NAME>` を独自の値に置き換えます。  次に例を示します。

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>ユーザー割り当てマネージド ID を既存の Azure VM に割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールと[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) コマンドレットを使用して、ユーザー割り当てマネージド ID を作成します。  出力の `Id` は次の手順で必要になるので書き留めます。

   > [!IMPORTANT]
   > ユーザー割り当てのマネージド ID の作成では、英数字、下線、およびハイフン (0-9、a-z、A-Z、\_、-) 文字のみがサポートされます。 また、VM/VMSS への割り当てを正常に機能させるには、名前の長さを 3 ～ 128 文字に制限する必要があります。 詳細については、[よく寄せられる質問と既知の問題](known-issues.md)に関する記事をご覧ください。

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. `Get-AzVM` コマンドレットを使用して VM プロパティを取得します。 次に、ユーザー割り当てマネージド ID を Azure VM に割り当てるため、[Update-AzVM](/powershell/module/az.compute/update-azvm) コマンドレットで `-IdentityType` および `-IdentityID` スイッチを使用します。  `-IdentityId` パラメーターの値は、前の手順で書き留めた `Id` です。  `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、および `<USER ASSIGNED IDENTITY NAME>` を独自の値に置き換えます。

   > [!WARNING]
   > 以前のユーザー割り当てマネージド ID を VM に割り当てておくには、VM オブジェクト (たとえば `$vm.Identity`) の `Identity` プロパティのクエリを実行します。  ユーザー割り当てマネージド ID が返された場合、VM に割り当てる新しいユーザー割り当てマネージド ID とともにこれらを次のコマンドに含めます。

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当てのマネージド ID を削除する

VM からユーザー割り当ての ID を削除にするには、お使いのアカウントに[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロールの割り当てが必要です。

VM に複数のユーザー割り当てマネージド ID がある場合は、次のコマンドを使用して、最後の ID 以外の ID をすべて削除できます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY NAME>` はユーザー割り当てマネージド ID の名前プロパティであり、VM 上に残す必要があります。 この情報は、VM オブジェクトの `Identity` プロパティのクエリを実行することにより見つかります。  例: `$vm.Identity`

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
VM にシステム割り当てマネージド ID がないときに、ユーザー割り当てマネージド ID をすべて削除する場合は、次のコマンドを使用します。

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
VM にシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方がある場合は、システム割り当てマネージド ID のみを使用するように切り替えることによって、すべてのユーザー割り当てマネージド ID を削除できます。

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。

  - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md)
  - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)
