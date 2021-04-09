---
title: Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する - Azure AD
description: Azure PowerShell を使用して、ユーザー割り当てマネージド ID を作成、一覧表示、削除する手順を説明します。
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
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548224"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Azure PowerShell を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除する

Azure リソースのマネージド ID は、Azure Active Directory で管理される ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure PowerShell を使って、ユーザー割り当てマネージド ID を作成、一覧表示、削除する方法について説明します。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。これは、コード ブロックの右上隅にある **[使ってみる]** ボタンを使用して開くことができます。
    - Azure PowerShell を使用して、スクリプトをローカルで実行します。次のセクションの説明を参照してください。

### <a name="configure-azure-powershell-locally"></a>ローカルで Azure PowerShell を構成する

この記事で、(Cloud Shell ではなく) ローカルで Azure PowerShell を使用するには、次の手順を実行します。

1. [最新バージョンの Azure PowerShell](/powershell/azure/install-az-ps) をインストールします (まだインストールしていない場合)。

1. Azure にサインインします。

    ```azurepowershell
    Connect-AzAccount
    ```

1. [PowerShellGet の最新バージョン](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)をインストールします。

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    次の手順に備えて、このコマンドを実行した後に現在の PowerShell セッションから `Exit` する必要がある場合があります。

1. この記事のユーザー割り当てマネージド ID 操作を実行するために、`Az.ManagedServiceIdentity` モジュールのプレリリース バージョンをインストールします。

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、`New-AzUserAssignedIdentity` コマンドを使用します。 `ResourceGroupName` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-Name` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID の一覧表示/読み取りには、お使いのアカウントに[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator)ロールまたは[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を一覧表示するには、[Get-AzUserAssigned] コマンドを使います。  `-ResourceGroupName` パラメーターでは、ユーザー割り当てマネージド ID を作成したリソース グループを指定します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
応答内のユーザー割り当てマネージド ID には、キー `Type` に対して返された `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値が含まれます。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を削除するには、`Remove-AzUserAssignedIdentity` コマンドを使用します。  `-ResourceGroupName` パラメーターではユーザー割り当て ID を作成したリソース グループを指定し、`-Name` パラメーターではその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 ID の割り当ては、別に削除する必要があります。

## <a name="next-steps"></a>次のステップ

Azure リソースの Azure PowerShell マネージド ID コマンドの詳細については、「[Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)」を参照してください。
