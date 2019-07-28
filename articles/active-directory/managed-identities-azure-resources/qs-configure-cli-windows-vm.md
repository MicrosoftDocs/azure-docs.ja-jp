---
title: Azure CLI を使用して Azure VM 上でシステム割り当てマネージド ID とユーザー割り当て ID を構成する方法
description: Azure CLI を使用して、Azure VM 上にシステム割り当てマネージド ID とユーザー割り当てマネージド ID を構成する順を追った説明。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c61313190615c2f30a7d37202bc0f9bcf14d800
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112885"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用して、Azure VM 上に次の Azure リソースのマネージド ID 操作を実行する方法について説明します。

- Azure VM 上でシステム割り当てマネージド ID を有効および無効にする
- Azure VM 上でユーザー割り当てマネージド ID を追加および削除する

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。
    - Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[最新バージョンの Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 
      
      > [!NOTE]
      > コマンドは、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) の最新リリースを反映するように更新されています。     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure CLI を使用して、Azure VM 上でシステム割り当てマネージド ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM の作成中にシステム割り当てマネージド ID を有効にする

システム割り当てマネージド ID を有効にして Azure VM を作成するには、お使いのアカウントに[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 次のように、VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az-group-create) を使用して、VM とその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vm create](/cli/azure/vm/#az-vm-create) を使用して VM を作成します。 次の例では、`--assign-identity` パラメーターの要求どおりに、システム割り当てマネージド ID を持つ *myVM* という名前の VM を作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>既存の Azure VM 上でシステム割り当てマネージド ID を有効にする

VM でシステム割り当てマネージド ID を有効にするには、お使いのアカウントに[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 目的の VM が含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az vm identity assign](/cli/azure/vm/identity/) と `identity assign` コマンドを使用して、既存の VM に対するシステム割り当て ID を有効にします。

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Azure VM でシステム割り当て ID を無効にする

VM でシステム割り当てマネージド ID を無効にするには、お使いのアカウントに[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールの割り当てが必要です。  Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

システム割り当て ID は不要になったが、ユーザー割り当て ID はまだ必要な仮想マシンがある場合は、次のコマンドを使用します。

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

システム割り当て ID は不要になった、ユーザー割り当て ID がない仮想マシンがある場合は、次のコマンドを使用します。

> [!NOTE]
> 値 `none` では、大文字と小文字が区別されます。 小文字にする必要があります。 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```
> [!NOTE]
> Azure リソースの VM 拡張機能 (非推奨になる予定) のマネージド ID をプロビジョニングしている場合は、[az vm extension delete](https://docs.microsoft.com/cli/azure/vm/) を使用して削除する必要があります。 詳細については、[認証のための VM 拡張機能から Azure IMDS への移行](howto-migrate-vm-extension.md)に関するページを参照してください。

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure CLI を使用して、Azure VM に対してユーザー割り当てマネージド ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Azure VM の作成中にユーザー割り当てマネージド ID を割り当てる

ユーザー割り当て ID を作成中の VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールと[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. 使用するリソース グループが既にある場合は、この手順をスキップできます。 [az group create](/cli/azure/group/#az-group-create) を使用して、ユーザー割り当てマネージド ID の格納と配置を行う[リソース グループ](~/articles/azure-resource-manager/resource-group-overview.md#terminology)を作成します。 `<RESOURCE GROUP>` と `<LOCATION>` のパラメーターの値は、必ず実際の値に置き換えてください。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az-identity-create) を使用して、ユーザー割り当てマネージド ID を作成します。  `-g` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   応答には、次のように、作成されたユーザー割り当てマネージド ID の詳細が含まれています。 ユーザー割り当てマネージド ID に割り当てられたリソース ID 値は、次の手順で使用されます。

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [az vm create](/cli/azure/vm/#az-vm-create) を使用して VM を作成します。 次の例では、`--assign-identity` パラメーターで指定された新しいユーザー割り当て ID に関連付けられている VM を作成します。 `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>`、および `<USER ASSIGNED IDENTITY NAME>` の各パラメーターの値は、必ず実際の値に置き換えてください。 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>ユーザー割り当てマネージド ID を既存の Azure VM に割り当てる

ユーザー割り当て ID を VM に割り当てるには、お使いのアカウントに[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールと[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロールの割り当てが必要です。 Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

1. [az identity create](/cli/azure/identity#az-identity-create) を使用してユーザー割り当て ID を作成します。  `-g` パラメーターにはユーザー割り当て ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` と `<USER ASSIGNED IDENTITY NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    > [!IMPORTANT]
    > 名前に特殊文字 (アンダースコアなど) が含まれるユーザー割り当てマネージド ID の作成は現在サポートされていません。 英数字を使用してください。 アップデートは後ほどご確認ください。  詳細については、[よく寄せられる質問と既知の問題](known-issues.md)に関する記事をご覧ください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   応答には、次のように、作成されたユーザー割り当てマネージド ID の詳細が含まれています。 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vm identity assign](/cli/azure/vm) を使用して、ユーザー割り当て ID を VM に割り当てます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY NAME>` は、前の手順で作成されたユーザー割り当てマネージド ID のリソース `name` プロパティです。

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM からユーザー割り当てのマネージド ID を削除する

VM からユーザー割り当ての ID を削除にするには、お使いのアカウントに[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールの割り当てが必要です。 

仮想マシンに割り当てられている唯一のユーザー割り当てマネージド ID の場合は、ID の種類の値から `UserAssigned` が削除されます。  `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY>` はユーザー割り当て ID の `name` プロパティになります。これは、`az vm identity show` を使用して、仮想マシンの ID セクションで見つけることができます。

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

VM にシステム割り当てマネージド ID がないときに、ユーザー割り当て ID をすべて削除する場合は、次のコマンドを使用します。

> [!NOTE]
> 値 `none` では、大文字と小文字が区別されます。 小文字にする必要があります。

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

VM にシステム割り当て ID とユーザー割り当て ID の両方がある場合は、システム割り当て ID のみを使用するように切り替えることによって、すべてのユーザー割り当て ID を削除できます。 次のコマンドを使用します。

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>次の手順
- [Azure リソースのマネージド ID の概要](overview.md)
- 完全な Azure VM 作成のクイックスタートについては、次を参照してください。 
  - [CLI で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-cli.md)  
  - [CLI で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-cli.md) 

















