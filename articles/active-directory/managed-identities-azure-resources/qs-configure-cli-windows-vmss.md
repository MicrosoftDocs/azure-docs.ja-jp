---
title: Azure CLI を使用して Azure 仮想マシン スケール セット上にシステム割り当てマネージド ID とユーザー割り当て ID を構成する方法
description: Azure CLI を使用して、仮想マシン スケール セット上にシステム割り当てマネージド ID とユーザー割り当てマネージド ID を構成するための順を追った説明。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04a3c9eba1f6498796a7e617b400649963c996d1
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444473"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用して、Azure 仮想マシン スケール セット上で Azure リソースのマネージド ID に対して次の操作を実行する方法について説明します。
- Azure 仮想マシン スケール セットでシステム割り当てマネージド ID を有効および無効にする
- Azure 仮想マシン スケール セットでユーザー割り当てマネージド ID を追加および削除する


## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次の Azure のロール ベースのアクセス制御の割り当てが必要です。

    > [!NOTE]
    > Azure AD ディレクトリ ロールを追加で割り当てる必要はありません。

    - [仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 仮想マシン スケール セットを作成する、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を有効にする、および仮想マシン スケール セットから削除することができるロールです。
    - [マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): ユーザー割り当てマネージド ID を作成することができるロールです。
    - [マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator)ロール。ユーザー割り当てマネージド ID の仮想マシン スケール セットへの割り当ておよび仮想マシン スケール セットからの削除を実行します。
- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。
    - Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[Azure CLI の最新バージョン (2.0.13 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 
      
      > [!NOTE]
      > コマンドは、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) の最新リリースを反映するように更新されています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

このセクションでは、Azure CLI を使用して、Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中にシステム割り当てマネージド ID を有効にする

システム割り当てマネージド ID を有効にして仮想マシン スケール セットを作成する場合:

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 仮想マシン スケール セットをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az-group-create) を使用して、仮想マシン スケール セットとその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vmss create](/cli/azure/vmss/#az-vmss-create) を使用して仮想マシン スケール セットを作成します。 次の例では、`--assign-identity` パラメーターからの要求により、システム割り当てマネージド ID を使用して *myVM* という仮想マシン スケール セットを作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効にする

既存の Azure 仮想マシン スケール セット上でシステム割り当てマネージド ID を有効にする必要がある場合:

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) コマンドを使用して、既存の VM に対するシステム割り当てマネージド ID を有効にします。

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットでシステム割り当てマネージド ID を無効にする

システム割り当てマネージド ID は不要になったが、ユーザー割り当てマネージド ID はまだ必要な仮想マシン スケール セットがある場合は、次のコマンドを使用します。

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

システム割り当てマネージド ID が不要になり、ユーザー割り当てマネージド ID がない仮想マシンがある場合は、次のコマンドを使用します。

> [!NOTE]
> 値 `none` では、大文字と小文字が区別されます。 小文字にする必要があります。 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

> [!NOTE]
> Azure リソースの VM 拡張機能 (非推奨になる予定) のマネージド ID をプロビジョニングしている場合は、[az vmss extension delete](https://docs.microsoft.com/cli/azure/vm/) を使用して削除する必要があります。 詳細については、[認証のための VM 拡張機能から Azure IMDS への移行](howto-migrate-vm-extension.md)に関するページを参照してください。

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure CLI を使用してユーザー割り当てマネージド ID を有効にする方法と削除する方法について説明します。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にユーザー割り当てマネージド ID を割り当てる

このセクションでは、仮想マシン スケール セットを作成する方法と、ユーザー割り当てマネージド ID を仮想マシン スケール セットに割り当てる方法について説明します。 使用する仮想マシン スケール セットが既にある場合は、このセクションをスキップして次のセクションに進んでください。

1. 使用するリソース グループが既にある場合は、この手順をスキップできます。 [az group create](/cli/azure/group/#az-group-create) を使用して、ユーザー割り当てマネージド ID の格納と配置を行う[リソース グループ](~/articles/azure-resource-manager/resource-group-overview.md#terminology)を作成します。 `<RESOURCE GROUP>` と `<LOCATION>` のパラメーターの値は、必ず実際の値に置き換えてください。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az-identity-create) を使用して、ユーザー割り当てマネージド ID を作成します。  `-g` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` と `<USER ASSIGNED IDENTITY NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   応答には、次のように、作成されたユーザー割り当てマネージド ID の詳細が含まれています。 ユーザー割り当てマネージド ID に割り当てられたリソース `id` 値は、次の手順で使用されます。

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

3. [az vmss create](/cli/azure/vmss/#az-vmss-create) を使用して仮想マシン スケール セットを作成します。 次の例では、`--assign-identity` パラメーターで指定された新しいユーザー割り当てマネージド ID に関連付けられている仮想マシン スケール セットを作成します。 `<RESOURCE GROUP>`、`<VMSS NAME>`、`<USER NAME>`、`<PASSWORD>`、および `<USER ASSIGNED IDENTITY>` の各パラメーターの値は、必ず実際の値に置き換えてください。 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>既存の仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てる

1. [az identity create](/cli/azure/identity#az-identity-create) を使用して、ユーザー割り当てマネージド ID を作成します。  `-g` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` と `<USER ASSIGNED IDENTITY NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   応答には、次のように、作成されたユーザー割り当てマネージド ID の詳細が含まれています。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity) を使用して、ユーザー割り当てマネージド ID を仮想マシン スケール セットに割り当てます。 `<RESOURCE GROUP>` と `<VIRTUAL MACHINE SCALE SET NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY>` は、前の手順で作成されたユーザー割り当て ID のリソース `name` プロパティです。

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからユーザー割り当てマネージド ID を削除する

仮想マシン スケール セットからユーザー割り当てマネージド ID を削除するには、[az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove) を使用します。 仮想マシン スケール セットに割り当てられている唯一のユーザー割り当てマネージド ID の場合は、ID 型の値から `UserAssigned` が削除されます。  `<RESOURCE GROUP>` と `<VIRTUAL MACHINE SCALE SET NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `<USER ASSIGNED IDENTITY>` はユーザー割り当てマネージド ID の `name` プロパティになります。これは、`az vmss identity show` を使用して、仮想マシン スケール セットの ID セクションで見つけることができます。

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

仮想マシン スケール セットにシステム割り当てマネージド ID がないときに、ユーザー割り当てマネージド ID をすべて削除する場合は、次のコマンドを使用します。

> [!NOTE]
> 値 `none` では、大文字と小文字が区別されます。 小文字にする必要があります。

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

仮想マシン スケール セットにシステム割り当てマネージド ID とユーザー割り当てマネージド ID の両方がある場合は、システム割り当てマネージド ID のみを使用するように切り替えることによって、すべてのユーザー割り当てマネージド ID を削除できます。 次のコマンドを使用します。

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>次の手順

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure 仮想マシン スケール セットの全作成に関するクイック スタートについては、次を参照してください。 

  - [CLI を使用して仮想マシン スケール セットを作成する](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















