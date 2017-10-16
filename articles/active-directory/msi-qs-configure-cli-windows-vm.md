---
title: "Azure CLI を使用して Azure VM で MSI を構成する方法"
description: "Azure CLI を使用して、Azure VM で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
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
ms.openlocfilehash: fe276fe802eceb1f062ed8bda685dd44a1e3d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Azure CLI を使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用して Azure VM の MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。

- Azure ポータルから [Azure Cloud Shell](../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
- 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョンをインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 以降) 。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM の作成中に MSI を有効にする

MSI 対応 VM を作成するには

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/#login) を使用して Azure にサインインします。 次のように、VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#create) を使用して、VM とその関連リソースの管理およびデプロイ用に[リソース グループ](../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vm create](/cli/azure/vm/#create) を使用して VM を作成します。 次の例では、`--assign-identity` パラメーターからの要求により、MSI を使用して *myVM* という VM を作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>既存の Azure VM で MSI を有効にする

既存の仮想マシンで MSI を有効にする必要がある場合は、次のようにします。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/#login) を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```azurecli-interactive
   az login
   ```

2. `--assign-identity` パラメーターを指定して [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity) を使用し、MSI を既存の VM に追加します。

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM から MSI を削除する

MSI が不要になった仮想マシンがある場合は、次のようにします。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/#login) を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

   ```azurecli-interactive
   az login
   ```

2. [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) で、(VM の種類に応じて) `-n ManagedIdentityExtensionForWindows` または `-n ManagedIdentityExtensionForLinux` スイッチを指定して MSI ファイルを削除します。

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>関連コンテンツ

- [管理対象サービス ID の概要](msi-overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。 

  - [CLI で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-cli.md)  
  - [CLI で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-cli.md) 

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
















