---
title: ユーザー割り当てマネージド ID の管理 - Azure CLI - Azure AD
description: Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、および削除する方法の順を追った説明。
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 68f305156645d69049519cd383f06b28ab9b5e03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184874"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、または削除する


Azure リソースのマネージド ID は、Azure Active Directory のマネージド ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure CLI を使用して、ユーザー割り当てマネージド ID を作成、一覧表示、および削除する方法について説明します。

まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID について不明な場合は、「[Azure リソースのマネージド ID とは](overview.md)」を参照してください。 システム割り当てとユーザー割り当ての両方の種類のマネージド ID の詳細については、「[マネージド ID の種類](overview.md#managed-identity-types)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> CLI を使用してアプリのサービス プリンシパルを使用しているときにユーザーのアクセス許可を変更するには、サービス プリンシパルに Azure AD Graph API の追加のアクセス許可を提供する必要があります。これは、CLI の一部が Graph API に対して GET 要求を実行するからです。 そうしないと、"この操作を完了するのに十分な特権がありません" というメッセージが表示される可能性があります。 これを行うには、Azure Active Directory でアプリの登録に移動し、アプリを選択し、[API のアクセス許可] をクリックし、下へスクロールして Azure Active Directory Graph を選択する必要があります。 そこから、[アプリケーションのアクセス許可] を選択し、適切なアクセス許可を追加します。 

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を作成するには、[az identity create](/cli/azure/identity#az-identity-create) コマンドを使用します。 `-g` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` および `<USER ASSIGNED IDENTITY NAME>` パラメーターの値は、実際の値に置き換えます。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID の一覧表示/読み取りには、お使いのアカウントに[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator)ロールまたは[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当てマネージド ID を一覧表示するには、[az identity list](/cli/azure/identity#az-identity-list) コマンドを使用します。 `<RESOURCE GROUP>` は実際の値に置き換えます。

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

json 応答内のユーザー割り当てマネージド ID には、キー `type` に対して返された `"Microsoft.ManagedIdentity/userAssignedIdentities"` の値が含まれます。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

ユーザー割り当て ID を削除するには、[az identity delete](/cli/azure/identity#az-identity-delete) コマンドを使用します。  -n パラメーターにはユーザー割り当てマネージド ID の名前を指定し、-g パラメーターにはそれが作成されたリソース グループを指定します。 `<USER ASSIGNED IDENTITY NAME>` と `<RESOURCE GROUP>` のパラメーターの値を、実際の値に置き換えます。

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 それらを VM/VMSS から削除するには、`az vm/vmss identity remove` コマンドを使用します。

## <a name="next-steps"></a>次のステップ

Azure CLI の ID コマンドの一覧は、[az identity](/cli/azure/identity) をご覧ください。

Azure VM にユーザー割り当てマネージド ID を割り当てる方法については、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)」を参照してください。
