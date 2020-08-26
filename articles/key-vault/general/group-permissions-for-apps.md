---
title: アプリケーションに Azure のキー コンテナーへのアクセス許可を付与する - Azure Key Vault | Microsoft Docs
description: サービス プリンシパルを手動で登録し、アクセス制御ポリシーを使用して Azure Key Vault へのアクセスを提供する方法について説明します。Azure Key Vault へのアクセスは、場合によっては必要になります。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d0607b1adb76fd32ce9f4dc9ebede92c7aa96862
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588757"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>アクセス制御ポリシーを使用して Key Vault の認証を提供する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Key Vault に対してクラウドベースのアプリケーションを認証するための最も簡単な方法は、マネージド ID を使用することです。詳細については、[App Service マネージド ID を使用した Azure Key Vault へのアクセス](managed-identity.md)に関するページを参照してください。  オンプレミス アプリケーションを作成する場合、ローカル開発を行う場合、またはマネージド ID を使用できない場合は、代わりにサービス プリンシパルを手動で登録し、アクセス制御ポリシーを使用してキー コンテナーへのアクセスを提供することができます。  

キー コンテナーでは、最大 1,024 個のアクセス ポリシー エントリがサポートされており、各エントリでは、"プリンシパル" に対して異なるアクセス許可セットを付与します。 たとえば、これは、[.NET 用 Azure Key Vault クライアント ライブラリのクイックスタート](../secrets/quick-create-net.md)のコンソール アプリがキー コンテナーにアクセスするしくみです。

Key Vault のアクセス制御の詳細については、「[Azure Key Vault セキュリティ:ID 管理とアクセス管理](overview-security.md#identity-and-access-management)」を参照してください。 アクセス制御の詳細については、以下を参照してください。 

- [[キー]](../keys/index.yml)
- [シークレットのアクセス制御](../secrets/index.yml)
- [証明書のアクセス制御](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

- キー コンテナー。 既存のキー コンテナーを使用することも、次のいずれかのクイックスタートの手順に従って新しいキー コンテナーを作成することもできます。
   - [Azure CLI を使用してキー コンテナーを作成する](../secrets/quick-create-cli.md)
   - [Azure PowerShell を使用してキー コンテナーを作成する](../secrets/quick-create-powershell.md)
   - [Azure portal を使用してキー コンテナーを作成する](../secrets/quick-create-portal.md)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。 別の方法として、[Azure portal](https://portal.azure.com) を使用することもできます。

## <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

各キー コンテナーのアクセス ポリシー エントリでは、プリンシパルに異なるアクセス許可セットを付与します。

- **アプリケーション** アプリケーションがクラウドベースの場合は、可能な限り、代わりに[マネージド ID を使用して Azure Key Vault にアクセスする](managed-identity.md)必要があります。
- **Azure AD グループ** キー コンテナーでは 1,024 個のアクセス ポリシー エントリしかサポートされていませんが、1 つの Azure AD グループに複数のアプリケーションとユーザーを追加し、そのグループを 1 つのエントリとしてアクセス制御ポリシーに追加することができます。
- **ユーザー** ユーザーにキー コンテナーへの直接アクセスを許可することは、**お勧めできません**。 ユーザーは、Azure AD グループに追加し、さらにそのグループにキー コンテナーへのアクセス権を付与することをお勧めします。 「[Azure Key Vault セキュリティ:ID 管理とアクセス管理](overview-security.md#identity-and-access-management)」を参照してください。


### <a name="get-the-objectid"></a>objectID を取得する

アプリケーション、Azure AD グループ、またはユーザーにキー コンテナーへのアクセス権を付与するには、まず、その objectId を取得する必要があります。

#### <a name="applications"></a>アプリケーション

アプリケーションの objectId は、関連付けられているサービス プリンシパルに対応しています。 サービス プリンシパルの詳細については、 「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。 

アプリケーションの objectId を取得する方法は 2 とおりあります。  1 つ目は、アプリケーションを Azure Active Directory に登録することです。 そのためには、[Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)クイックスタートの手順に従います。 登録が完了すると、objectID が "アプリケーション (クライアント) ID" として一覧に表示されます。

2 つ目は、ターミナル ウィンドウでサービス プリンシパルを作成することです。 Azure CLI で [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用し、一意のサービス プリンシパル名を -n フラグに "http://&lt;my-unique-service-principal-name&gt;" という形式で指定します。

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name"
```

この objectId は出力に `clientID` として表示されます。

Azure PowerShell では、[New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) コマンドレットを使用します。


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principal-name>
```

この objectId は出力に (`ApplicationId` ではなく) `Id` として表示されます。

#### <a name="azure-ad-groups"></a>Azure AD グループ

複数のアプリケーションとユーザーを Azure AD グループに追加し、そのグループにキー コンテナーへのアクセス権を付与することができます。  詳細については、以下の「[メンバーを作成して Azure AD グループに追加する](#creating-and-adding-members-to-an-azure-ad-group)」セクションを参照してください。

Azure CLI を使用して Azure AD グループの objectId を確認するには、[az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) コマンドを使用します。 組織内に多数のグループが存在する可能性があるため、`--display-name` パラメーターに検索文字列を指定する必要もあります。

```azurecli-interactive
az ad group list --display-name <search-string>
```
objectId は JSON 形式で返されます。

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Azure PowerShell を使用して Azure AD グループの objectId を確認するには、[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) コマンドレットを使用します。 組織内に多数のグループが存在する可能性があるため、`-SearchString` パラメーターに検索文字列を指定する可能性も高くなります。

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

この出力では、objectId が `Id` として表示されます。

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>ユーザー

また、キー コンテナーのアクセス制御ポリシーに個々のユーザーを追加することもできます。 **これはお勧めしません。** 代わりに、ユーザーを Azure AD グループに追加し、そのグループをポリシーに追加することをお勧めします。

それでも Azure CLI を使用してユーザーを探したい場合は、[az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) コマンドを使用して、`--id` パラメーターにユーザーのメール アドレスを渡します。


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

このユーザーの objectId が出力で返されます。

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Azure PowerShell を使用してユーザーを探すには、[Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) コマンドレットを使用して、`-UserPrincipalName` パラメーターにユーザーのメール アドレスを渡します。

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

このユーザーの objectId が出力で `Id` として返されます。

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>キー コンテナーへのアクセス権をプリンシパルに付与する

プリンシパルの objectID を取得したので、キー コンテナーのアクセス ポリシーを作成して、キーとシークレットの両方に対する取得、一覧表示、設定、削除のアクセス許可に加えて、必要な追加のアクセス許可を付与できます。

Azure CLI を使用する場合、これを行うには、[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドに objectId を渡します。

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Azure PowerShell を使用する場合、これを行うには、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) コマンドレットに objectId を渡します。 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>メンバーを作成して Azure AD グループに追加する

Azure AD グループを作成し、そのグループにアプリケーションとユーザーを追加して、キー コンテナーへのアクセス権をそのグループに付与できます。  これにより、多数のアプリケーションを 1 つのアクセス ポリシー エントリとしてキー コンテナーに追加できるため、キー コンテナーに対する直接アクセス権をユーザーに付与する (非推奨) 必要がなくなります。 詳細については、「[Azure Active Directory グループを使用したアプリとリソース アクセスの管理](../../active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。

### <a name="additional-prerequisites"></a>追加の前提条件

[上記の前提条件](#prerequisites)に加えて、Azure Active Directory テナント内でグループを作成または編集するためのアクセス許可が必要になります。 アクセス許可を持っていない場合は、Azure Active Directory 管理者に連絡する必要があります。

PowerShell を使用する場合は、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)も必要です。

### <a name="create-an-azure-active-directory-group"></a>Azure Active Directory グループを作成する

Azure CLI の [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) コマンドまたは Azure PowerShell の [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) コマンドレットを使用して、新しい Azure Active Directory グループを作成します。


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

どちらの場合も、新たに作成されたグループの GroupId を書き留めておいてください。以下の手順で必要になります。

### <a name="find-the-objectids-of-your-applications-and-users"></a>アプリケーションとユーザーの objectId を確認する

Azure CLI を使用してアプリケーションの objectId を確認するには、`--show-mine` パラメーターを指定した [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) コマンドを使用します。

```azurecli-interactive
az ad sp list --show-mine
```

Azure PowerShell を使用してアプリケーションの objectId を確認するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) コマンドレットを使用し、`-SearchString` パラメーターに検索文字列を渡します。

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

ユーザーの objectId を確認するには、上記の「[ユーザー](#users)」セクションの手順に従います。

### <a name="add-your-applications-and-users-to-the-group"></a>グループにアプリケーションとユーザーを追加する

ここで、新たに作成された Azure AD グループに objectId を追加します。

Azure CLI を使用する場合は、[az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add) を使用して、`--member-id` パラメーターに objectId を渡します。


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Azure PowerShell を使用する場合は、[Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) を使用して、`-MemberObjectId` パラメーターに objectId を渡します。

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>AD グループにキー コンテナーへのアクセス権を付与する

最後に、Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドまたは Azure PowerShell の [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) コマンドレットを使用して、AD グループにキー コンテナーへのアクセス許可を付与します。 たとえば、前述の[アプリケーション、Azure AD グループ、またはユーザーに対するキー コンテナーへのアクセス権の付与](#give-the-principal-access-to-your-key-vault)に関するセクションを参照してください。

またアプリケーションには、キー コンテナーに割り当てられた IAM (Identity and Access Management: ID とアクセス管理) ロールが少なくとも 1 つ必要となります。 それがないとログインすることができず、サブスクリプションにアクセスする権限の不足でエラーになります。

> [!WARNING]
> マネージド ID を持つ Azure AD グループでは、トークンを更新して有効になるまでに、最大で 8 時間かかる場合があります。

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault セキュリティ: ID 管理とアクセス管理](overview-security.md#identity-and-access-management)
- [App Service のマネージド ID を使用して Key Vault の認証を提供する](managed-identity.md)
- [キー コンテナーをセキュリティで保護する](secure-your-key-vault.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
- [Azure Key Vault のベスト プラクティス](best-practices.md)を確認する
