---
title: PowerShell を使用して、仮想マシン スケール セット上に Azure リソースのマネージド ID を構成する方法
description: PowerShell を使用して、仮想マシン スケール セット上にシステム割り当てマネージド ID とユーザー割り当てマネージド ID を構成するための順を追った説明。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4917720af2396b68ccd36cc0410c9acbbba2d9b2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448578"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>PowerShell を使用して仮想マシン スケール セット上に Azure リソースのマネージド ID を構成する方法

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用して、仮想マシン スケール セット上で Azure リソースのマネージド ID 操作を実行する方法について説明します。
- 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効および無効にする
- 仮想マシン スケール セット上でユーザー割り当てマネージド ID の追加および削除を行う

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次の Azure のロール ベースのアクセス制御の割り当てが必要です。

    > [!NOTE]
    > Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

    - [仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロール。仮想マシン スケール セットを作成して、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効化したり、仮想マシン スケール セットから削除したりします。
    - [マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。ユーザー割り当てマネージド ID を作成します。
    - [マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator)ロール。ユーザー割り当てマネージド ID の仮想マシン スケール セットへの割り当ておよび仮想マシン スケール セットからの削除を実行します。
- [最新バージョンの Azure PowerShell](/powershell/azure/install-az-ps) をインストールします (まだインストールしていない場合)。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure PowerShell を使用してシステム割り当てマネージド ID を有効にする方法と削除する方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にシステム割り当てマネージド ID を有効にする

システム割り当てマネージド ID を有効にして仮想マシン スケール セットを作成する場合:

1. システム割り当てマネージド ID を持つ仮想マシン スケール セットの作成については、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) コマンドレット リファレンス記事の "*例 1*" を参照してください。  パラメーター `-IdentityType SystemAssigned` を `New-AzVmssConfig` コマンドレットに追加します。

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> オプションで、Azure リソース仮想マシン スケール セット拡張機能のマネージド ID をプロビジョニングできますが、これは間もなく非推奨になります。 Azure Instance Metadata の ID エンドポイントを認証に使用することをお勧めします。 詳細については、[VM 拡張機能の使用停止、および認証での Azure IMDS エンドポイントの使用開始](howto-migrate-vm-extension.md)に関するページを参照してください。


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効にする

既存の Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効にする必要がある場合:

1. `Connect-AzAccount` を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシン共同作業者」など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```powershell
   Connect-AzAccount
   ```

2. まず、[`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) コマンドレットを使用して、仮想マシン スケール セットのプロパティを取得します。 システム割り当てマネージド ID を有効にするには、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットで `-IdentityType` スイッチを使用します。

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> オプションで、Azure リソース仮想マシン スケール セット拡張機能のマネージド ID をプロビジョニングできますが、これは間もなく非推奨になります。 Azure Instance Metadata の ID エンドポイントを認証に使用することをお勧めします。 詳細については、[認証のための VM 拡張機能から Azure IMDS エンドポイントへの移行](howto-migrate-vm-extension.md)に関するページを参照してください。

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を無効にする

システム割り当てマネージド ID は不要になったが、ユーザー割り当てマネージド ID はまだ必要な仮想マシン スケール セットがある場合は、次のコマンドを使用します。

1. `Connect-AzAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシン共同作業者」など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 次のコマンドレットを実行します。

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

システム割り当てマネージド ID が不要になった、ユーザー割り当てマネージド ID を持たない仮想マシン スケール セットがある場合は、次のコマンドを使用します。

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure PowerShell を使用して、仮想マシン スケール セットに対してユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にユーザー割り当てマネージド ID を割り当てる

ユーザー割り当てマネージド ID を持つ新しい仮想マシン スケール セットの作成は、PowerShell では現在サポートされていません。 次のセクションで、既存の仮想マシン スケール セットにユーザー割り当てマネージド ID を追加する方法を確認してください。 アップデートは後ほどご確認ください。

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てる

既存の Azure 仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てるには:

1. `Connect-AzAccount` を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシン共同作業者」など、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```powershell
   Connect-AzAccount
   ```

2. まず、`Get-AzVM` コマンドレットを使用して、仮想マシン スケール セットのプロパティを取得します。 次に、ユーザー割り当てマネージド ID を仮想マシン スケール セットに割り当てるため、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットで `-IdentityType` スイッチと `-IdentityID` スイッチを使用します。 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、`<USER ASSIGNED ID1>`、`USER ASSIGNED ID2` を、実際の値に置き換えます。

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当てマネージド ID を削除する

仮想マシン スケール セットに複数のユーザー割り当てマネージド ID がある場合は、次のコマンドを使用して、最後の ID 以外の ID をすべて削除できます。 `<RESOURCE GROUP>` と `<VIRTUAL MACHINE SCALE SET NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY NAME>` はユーザー割り当てマネージド ID の名前プロパティであり、仮想マシン スケール セット上に残す必要があります。 この情報は、`az vmss show` を使用して、仮想マシン スケール セットの ID セクションで見つけることができます。

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
仮想マシン スケール セットにシステム割り当てマネージド ID がないときに、ユーザー割り当てマネージド ID をすべて削除する場合は、次のコマンドを使用します。

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
仮想マシン スケール セットにシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方がある場合は、システム割り当てマネージド ID のみを使用するように切り替えることによって、すべてのユーザー割り当てマネージド ID を削除できます。

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>次の手順

- [Azure リソースのマネージド ID の概要](overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。
  
  - [PowerShell で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-powershell.md) 

















