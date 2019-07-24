---
title: Azure CLI を使用してユーザー割り当てマネージド ID を管理する方法
description: Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、および削除する方法の順を追った説明。
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28520b3ba5d4e62fd4e1c9b78e68cc7dc2b48c61
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447401"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、または削除する

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure リソースのマネージド ID は、Azure Active Directory で管理される ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure CLI を使用して、ユーザー割り当てマネージド ID を作成、一覧表示、および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。
    - Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[Azure CLI の最新バージョン (2.0.13 以降) をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 ユーザー割り当て ID をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用して、Azure に `az login` でサインインします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、[az identity create](/cli/azure/identity#az-identity-create) コマンドを使用します。 `-g` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID の一覧表示/読み取りには、お使いのアカウントに[マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator)ロールまたは[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を一覧表示するには、[az identity list](/cli/azure/identity#az-identity-list) コマンドを使用します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
json 応答内のユーザー割り当てマネージド ID には、キー `type` に対して返された `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値が含まれます。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当て ID を削除するには、[az identity delete](/cli/azure/identity#az-identity-delete) コマンドを使用します。  -n パラメーターにはユーザー割り当てマネージド ID の名前を指定し、-g パラメーターにはそれが作成されたリソース グループを指定します。 `<USER ASSIGNED IDENTITY NAME>` と `<RESOURCE GROUP>` のパラメーターの値を、実際の値に置き換えます。

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 それらを VM/VMSS から削除するには、`az vm/vmss identity remove` コマンドを使用します。

## <a name="next-steps"></a>次の手順

Azure CLI の ID コマンドの一覧は、[az identity](/cli/azure/identity) をご覧ください。

Azure VM にユーザー割り当てマネージド ID を割り当てる方法については、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)」を参照してください。


 
