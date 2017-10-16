---
title: "Azure Active Directory の管理対象サービス ID (MSI) の FAQ と既知の問題"
description: "Azure Active Directory の管理対象サービス ID の既知の問題です。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 3cfd1eb55a031696635270a56ed5028e3b249543
ms.contentlocale: ja-jp
ms.lasthandoff: 09/23/2017

---

# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Azure Active Directory の管理対象サービス ID (MSI) の FAQ と既知の問題

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI は Active Directory Authentication Library (ADAL) または Microsoft Authentication Library (MSAL) で動作しますか。

いいえ、MSI はまだ ADAL または MSAL と統合されていません。

### <a name="what-are-the-supported-linux-distributions"></a>どのような Linux ディストリビューションがサポートされていますか。

次の Linux ディストリビューションが MSI をサポートしています。 

- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04

他の Linux ディストリビューションは現在サポートされておらず、サポートされていないディストリビューションでは拡張機能が失敗する可能性があります。

CentOS 6.9 では拡張機能が機能します。 ただし、6.9 はシステム サポートされていないため、クラッシュした場合や停止した場合は拡張機能が自動的に再起動しません。 6.9 は VM の再起動時に再起動します。 拡張機能を手動で再起動するには、「[MSI 拡張機能はどのようにすれば再起動できますか](#how-do-you-restart-the-msi-extension)」を参照してください。

### <a name="how-do-you-restart-the-msi-extension"></a>MSI 拡張機能はどのようにすれば再起動できますか。
Windows と特定のバージョンの Linux で拡張機能が停止した場合は、次のコマンドレットを使用して手動で再起動できます。

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

各値の説明: 
- Windows の拡張機能の名前と種類: ManagedIdentityExtensionForWindows
- Linux の拡張機能の名前と種類: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>既知の問題

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>構成ブレードが Azure ポータルに表示されない

VM 構成ブレードが VM に表示されない場合、MSI はご利用のリージョンのポータルでまだ有効になっていません。  後でもう一度確認してください。  [PowerShell](msi-qs-configure-powershell-windows-vm.md) または [Azure CLI](msi-qs-configure-cli-windows-vm.md) を使用して、VM の MSI を有効にすることもできます。

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Access Control (IAM) ブレードで、仮想マシンにアクセスを割り当てることができない

**仮想マシン**が Azure ポータルの **[アクセス コントロール (IAM)]** > **[アクセス許可の追加]** で **[アクセスの割り当て先]** の選択肢として表示されない場合、管理対象サービス ID がご利用のリージョンのポータルでまだ有効になっていません。 後でもう一度確認してください。  MSI のサービス プリンシパルを検索することで、ロールの割り当ての管理対象サービス ID を選択することもできます。  **[選択]** フィールドに VM の名前を入力すると、サービス プリンシパルが検索結果に表示されます。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>リソース グループまたはサブスクリプションから移動した後に VM を開始できない

実行中の状態で VM を移動する場合、VM は移動中も実行し続けます。 ただし、移動後に、VM を停止および再起動すると、VM を開始できなくなります。 この問題は、VM が MSI ID への参照を更新できず、以前のリソース グループで指し続けるために発生します。

**対処法** 
 
VM で更新プログラムをトリガーし、MSI の正しい値を取得できるようにします。 MSI ID への参照を更新するには、VM プロパティの変更を行うことができます。 たとえば、次のコマンドを使用して、VM で新しいタグの値を設定できます。

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
このコマンドは、新しいタグ "fixVM" を値 1 で VM に設定します。 
 
このプロパティの設定によって、VM は正しい MSI リソース URI で更新され、VM を開始できます。 
 
VM が開始されると、次のコマンドを使用してタグを削除できます。

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

