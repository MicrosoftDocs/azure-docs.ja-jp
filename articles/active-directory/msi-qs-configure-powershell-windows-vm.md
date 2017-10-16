---
title: "PowerShell を使用して Azure VM で MSI を構成する方法"
description: "PowerShell を使用して、Azure VM で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 612311f1c4e081e87dde76ce4a1d8efd46428c06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>PowerShell を使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用して Azure VM の MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

まだ [Azure PowerShell バージョン 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) がインストールされていなければ、それもインストールします。

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM の作成中に MSI を有効にする

MSI 対応 VM を作成するには

1. 次のいずれかの Azure VM クイックスタートを参照して、必要なセクション (「Azure へのログイン」、「リソース グループの作成」、「ネットワーク グループの作成」、「VM の作成」) のみを実行してください。 

   > [!IMPORTANT] 
   > 「VM の作成」セクションに到達したときに、[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットの構文にわずかな変更を加えます。 たとえば、`-IdentityType "SystemAssigned"` パラメーターを追加して、MSI で VM をプロビジョニングします。
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [PowerShell で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-powershell.md)



2. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type` パラメーターを使用して、MSI VM 拡張機能を追加します。 VM の種類に応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>既存の Azure VM で MSI を有効にする

既存の仮想マシンで MSI を有効にする必要がある場合は、次のようにします。

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```powershell
   Login-AzureRmAccount
   ```

2. 最初に、`Get-AzureRmVM` コマンドレットを使用して VM プロパティを取得します。 MSI を有効にするには、[Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) コマンドレットで `-IdentityType` スイッチを使用します。

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) コマンドレットで `-Type` パラメーターを使用して、MSI VM 拡張機能を追加します。 VM の種類に応じて "ManagedIdentityExtensionForWindows" または "ManagedIdentityExtensionForLinux" を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。 既存の VM の場所に一致する正しい `-Location` パラメーターを指定してください。

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM から MSI を削除する

MSI が不要になった仮想マシンがある場合、`RemoveAzureRmVMExtension` コマンドレットを使用して VM から MSI を削除することができます。

1. `Login-AzureRmAccount` を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```powershell
   Login-AzureRmAccount
   ```

2. `-Name` スイッチと [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) コマンドレットを使用して、拡張機能を追加したときに使用したのと同じ名前を指定します。

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>関連コンテンツ

- [管理対象サービス ID の概要](msi-overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。
  
  - [PowerShell で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-powershell.md) 

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
















