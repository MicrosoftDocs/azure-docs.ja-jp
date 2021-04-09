---
title: PowerShell を使用して仮想マシン スケール セット上にマネージド ID を構成する - Azure AD
description: PowerShell を使用して、仮想マシン スケール セット上にシステム割り当てマネージド ID とユーザー割り当てマネージド ID を構成するための詳細な手順について説明します。
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
ms.openlocfilehash: bd7ec1f6cdfc8ecfe32de04f5d06b42f9492b88c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590904"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>PowerShell を使用して仮想マシン スケール セット上に Azure リソースのマネージド ID を構成する方法

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用して、仮想マシン スケール セット上で Azure リソースのマネージド ID 操作を実行する方法について説明します。

- 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効および無効にする
- 仮想マシン スケール セット上でユーザー割り当てマネージド ID の追加および削除を行う

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を確認してください**。

- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

- この記事の管理操作を実行するアカウントには、次の Azure のロールベースのアクセス制御の割り当てが必要です。

    > [!NOTE]
    > Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

    - [仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロール。仮想マシン スケール セットを作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、仮想マシン スケール セットから削除したりします。
    - [マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロール。ユーザー割り当てマネージド ID を作成します。
    - [マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator)ロール。ユーザー割り当てマネージド ID の仮想マシン スケール セットへの割り当ておよび仮想マシン スケール セットからの削除を実行します。

- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[Try It]\(使ってみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps) をインストールしてスクリプトをローカルで実行した後、`Connect-AzAccount` を使用して Azure にサインインします。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure PowerShell を使用してシステム割り当てマネージド ID を有効にする方法と削除する方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にシステム割り当てマネージド ID を有効にする

システム割り当てマネージド ID を有効にして仮想マシン スケール セットを作成する場合:

1. システム割り当てマネージド ID を持つ仮想マシン スケール セットの作成については、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) コマンドレット リファレンス記事の "*例 1*" を参照してください。  パラメーター `-IdentityType SystemAssigned` を `New-AzVmssConfig` コマンドレットに追加します。

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効にする

既存の Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効にする必要がある場合:

1. お使いの Azure アカウントが、"仮想マシン共同作成者" など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。
   
1. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) コマンドレットを使用して、仮想マシン スケール セットのプロパティを取得します。 システム割り当てマネージド ID を有効にするには、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットで `-IdentityType` スイッチを使用します。

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を無効にする

システム割り当てマネージド ID は不要になったが、ユーザー割り当てマネージド ID はまだ必要な仮想マシン スケール セットがある場合は、次のコマンドを使用します。

1. お使いのアカウントが、"仮想マシン共同作成者" など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

1. 次のコマンドレットを実行します。

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. システム割り当てマネージド ID が不要になった、ユーザー割り当てマネージド ID を持たない仮想マシン スケール セットがある場合は、次のコマンドを使用します。

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure PowerShell を使用して、仮想マシン スケール セットに対してユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にユーザー割り当てマネージド ID を割り当てる

ユーザー割り当てマネージド ID を持つ新しい仮想マシン スケール セットの作成は、PowerShell では現在サポートされていません。 次のセクションで、既存の仮想マシン スケール セットにユーザー割り当てマネージド ID を追加する方法を確認してください。 アップデートは後ほどご確認ください。

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てる

既存の Azure 仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てるには:

1. お使いのアカウントが、"仮想マシン共同作成者" など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

1. `Get-AzVM` コマンドレットを使用して、仮想マシン スケール セットのプロパティを取得します。 次に、ユーザー割り当てマネージド ID を仮想マシン スケール セットに割り当てるため、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットで `-IdentityType` スイッチと `-IdentityID` スイッチを使用します。 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、`<USER ASSIGNED ID1>`、`USER ASSIGNED ID2` を、実際の値に置き換えます。

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当てマネージド ID を削除する

仮想マシン スケール セットに複数のユーザー割り当てマネージド ID がある場合は、次のコマンドを使用して、最後の ID 以外の ID をすべて削除できます。 `<RESOURCE GROUP>` と `<VIRTUAL MACHINE SCALE SET NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY NAME>` はユーザー割り当てマネージド ID の名前プロパティであり、仮想マシン スケール セット上に残す必要があります。 この情報は、`az vmss show` を使用して、仮想マシン スケール セットの ID セクションで見つけることができます。

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
仮想マシン スケール セットにシステム割り当てマネージド ID がないときに、ユーザー割り当てマネージド ID をすべて削除する場合は、次のコマンドを使用します。

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
仮想マシン スケール セットにシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方がある場合は、システム割り当てマネージド ID のみを使用するように切り替えることによって、すべてのユーザー割り当てマネージド ID を削除できます。

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。
  
  - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md)
