---
title: システム割り当てマネージド ID を使用して Azure Key Vault にアクセスする
description: App Service アプリケーション用のマネージド ID を作成し、それを使用して Azure Key Vault にアクセスする方法を説明します
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 0aa9a9728c3a59b6f47ef1427cbcd368d4f1e77c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586158"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>マネージド ID で Key Vault の認証を提供する

アプリで Azure Active Directory のマネージド ID を使用すると、Azure AD で保護された他のリソースに簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 詳細については、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。 

この記事では、App Service アプリケーション用のマネージド ID を作成し、それを使用して Azure Key Vault にアクセスする方法を説明します。 Azure VM でホストされているアプリケーションの場合は、「[Windows VM のシステム割り当てマネージド ID を使用して Azure Key Vault にアクセスする](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)」をご覧ください。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件 

このガイドを完了するには、以下のリソースが必要です。 

- キー コンテナー。 既存のキー コンテナーを使用することも、次のいずれかのクイックスタートの手順に従って新しいキー コンテナーを作成することもできます。
   - [Azure CLI を使用してキー コンテナーを作成する](../secrets/quick-create-cli.md)
   - [Azure PowerShell を使用してキー コンテナーを作成する](../secrets/quick-create-powershell.md)
   - [Azure portal を使用してキー コンテナーを作成する](../secrets/quick-create-portal.md)。
- キー コンテナーへのアクセスを許可する既存の App Service アプリケーション。 [App Service のドキュメント](../../app-service/overview.md)の手順に従って簡単に作成できます。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。 別の方法として、[Azure portal](https://portal.azure.com) を使用することもできます。


## <a name="adding-a-system-assigned-identity"></a>システム割り当て ID の追加 

まず、システムによって割り当てられた ID をアプリケーションに追加する必要があります。 
 
### <a name="azure-portal"></a>Azure portal 

ポータルでマネージド ID を設定するには、最初に通常の方法でアプリケーションを作成した後、機能を有効にします。 

1. 関数アプリを使っている場合は、 **[プラットフォーム機能]** に移動します。 他のアプリの種類の場合は、左側のナビゲーションを下にスクロールして **[設定]** グループに移動します。 

1. **[マネージド ID]** を選択します。 

1. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。 **[保存]** をクリックします。 

   ![システム割り当て ID の保存を示すスクリーンショット。](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

このクイックスタートには、Azure CLI バージョン 2.0.4 以降が必要です。 現在のバージョンを調べるには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 

Azure CLI でサインインするには、[az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) コマンドを使用します。

```azurecli-interactive
az login
```

Azure CLI を使用したログイン オプションについて詳しくは、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」をご覧ください。 

このアプリケーション用の ID を作成するには、Azure CLI の [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) コマンドまたは [az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) コマンドを使用します。


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

次のセクションで必要になるので、`PrincipalId` を記録しておきます。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Key Vault へのアクセス許可をアプリに付与する 

### <a name="azure-portal"></a>Azure portal

1.  Key Vault リソースに移動します。 

1.  **[アクセス ポリシー]** を選択し、 **[アクセス ポリシーの追加]** をクリックします。 

1.  **[シークレットのアクセス許可]** で、 **[Get, List]\(取得、一覧表示\)** を選択します。 

1.  **[プリンシパルの選択]** を選択し、検索フィールドにアプリの名前を入力します。  結果の一覧でアプリを選択し、 **[選択]** をクリックします。 

1.  **[追加]** をクリックして、新しいアクセス ポリシーの追加を完了します。

    ![Azure portal での新しいアクセス ポリシーの追加を示すスクリーンショット。](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

キー コンテナーへのアクセス権をアプリケーションに付与するには、Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを使用し、**ObjectId** パラメーターとして前に記録した **principalId** を指定します。

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ: ID 管理とアクセス管理](overview-security.md#identity-and-access-management)
- [アクセス制御ポリシーを使用して Key Vault の認証を提供する](group-permissions-for-apps.md)
- [キー コンテナーをセキュリティで保護する](secure-your-key-vault.md)).
- [Azure Key Vault 開発者ガイド](developers-guide.md)
- [Azure Key Vault のベスト プラクティス](best-practices.md)を確認する