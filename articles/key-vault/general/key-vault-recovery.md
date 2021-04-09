---
title: Azure Key Vault の回復の概要 | Microsoft Docs
description: Key Vault の回復機能は、キー コンテナーと、キー コンテナー内のシークレット、キー、証明書が誤って、または悪意で削除されるのを防ぐように設計されています。
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: a8e8e791f0dbe18322ad43364ae4ffd09b430caf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790386"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>論理的な削除と消去保護を使用した Azure Key Vault の回復の管理

この記事では、Azure Key Vault の 2 つの回復機能である論理的な削除と消去保護について説明します。 このドキュメントでは、これらの機能の概要について説明し、Azure portal、Azure CLI、Azure PowerShell を使用してそれらを管理する方法を示します。

Key Vault の詳細については、以下を参照してください。
- [Key Vault の概要](overview.md)
- [Azure Key Vault のキー、シークレット、証明書の概要](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/dotnet)
* [PowerShell モジュール](/powershell/azure/install-az-ps)。
* [Azure CLI](/cli/azure/install-azure-cli)
* キー コンテナー - [Azure portal](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md)、または [Azure PowerShell](../general/quick-create-powershell.md) を使用して作成できます
* 論理的に削除されたコンテナーに対して操作を実行するには、ユーザーには次のアクセス許可 (サブスクリプション レベル) が必要です。

  | 権限 | 説明 |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|論理的に削除された Key Vault のプロパティを表示します。|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|論理的に削除された Key Vault を消去します。|


## <a name="what-are-soft-delete-and-purge-protection"></a>論理的な削除および消去保護とは

[論理的な削除](soft-delete-overview.md)と消去保護は、Key Vault の 2 つの異なる回復機能です。

> [!IMPORTANT]
> 論理的な削除を有効にすることは、キー コンテナーと資格情報が誤って削除されないように保護するために重要です。 ただし、場合によってはアプリケーション ロジックの変更や、サービス プリンシパルに対する追加のアクセス許可の付与が必要になるため、論理的な削除の有効化は破壊的変更と見なされます。 以下の手順を使用して論理的な削除を有効にする前に、[**こちら**](soft-delete-change.md)のドキュメントを参照して、アプリケーションがこの変更と互換性があることをご確認ください。

**論理的な削除** は、キー コンテナーおよびキー コンテナー内に格納されているキー、シークレット、証明書が誤って削除されるのを防ぐように設計されています。 論理的な削除はごみ箱のようなものと考えることができます。 キー コンテナーまたはキー コンテナー オブジェクトを削除すると、ユーザーが構成可能な保持期間または既定の 90 日の間、回復可能な状態に維持されます。 論理的に削除された状態のキー コンテナーは、**消去** することもできます。これは、完全に削除されることを意味します。 これにより、同じ名前でキー コンテナーとキー コンテナー オブジェクトを作成し直すことができます。 キー コンテナーとオブジェクトの回復と削除のどちらについても、昇格されたアクセス ポリシーのアクセス許可が必要です。 **いったん有効にした論理的な削除を、無効にすることはできません。**

**キー コンテナーの名前はグローバルに一意である** ことに注意してください。そのため、論理的に削除された状態のキー コンテナーと同じ名前のキー コンテナーを作成することはできません。 同様に、キー、シークレット、証明書の名前は、キー コンテナー内で一意です。 論理的に削除された状態の別のものと同じ名前で、シークレット、キー、または証明書を作成することはできません。

**消去保護** は、悪意のある内部関係者によってキー コンテナー、キー、シークレット、証明書が削除されるのを防ぐように設計されています。 これは、時間ベースのロック機能を備えたごみ箱と考えてください。 構成可能な保持期間中であればいつでも、項目を回復できます。 **キー コンテナーは、保持期間が経過するまでは、完全に削除したり消去したりできません。** 保持期間が経過すると、キー コンテナーまたはキー コンテナー オブジェクトは自動的に消去されます。

> [!NOTE]
> 消去保護は、管理者のロールまたはアクセス許可によって消去保護を上書き、無効化、または回避することができないように設計されています。 **Microsoft を含むすべてのユーザーは、いったん有効にされた消去保護は、無効にしたり上書きしたりできません。** つまり、キー コンテナー名を再利用するには、最初に削除されたキー コンテナーを回復するか、保持期間が経過するまで待つ必要があります。

論理的な削除の詳細については、「[Azure Key Vault の論理的な削除の概要](soft-delete-overview.md)」を参照してください。

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>キー コンテナーで論理的な削除が有効になっているかどうかを確認し、論理的な削除を有効にする

1. Azure ポータルにログインします。
1. キー コンテナーを選択します。
1. [プロパティ] ブレードをクリックします。
1. 論理的な削除の横にあるオプション ボタンが、"回復を有効にする" に設定されているかどうかを確認します。
1. キー コンテナーで論理的な削除が有効になっていない場合は、オプション ボタンをクリックして論理的な削除を有効にし、[保存] をクリックします。

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="[プロパティ] では、有効にするための値として [論理的な削除] が強調表示されます。":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>削除されたシークレットを消去および回復するためのアクセス権をサービス プリンシパルに許可する

1. Azure ポータルにログインします。
1. キー コンテナーを選択します。
1. [アクセス ポリシー] ブレードをクリックします。
1. テーブルで、アクセスを許可するセキュリティ プリンシパルの行を見つけます (または、新しいセキュリティ プリンシパルを追加します)。
1. キー、証明書、シークレットのドロップダウンをクリックします。
1. ドロップダウンの一番下までスクロールし、[回復] と [削除] をクリックします
1. セキュリティ プリンシパルがほとんどの操作を実行するには、取得と一覧表示の機能も必要です。

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="左側のナビゲーション ウィンドウで、[アクセス ポリシー] が強調表示されます。[アクセス ポリシー] には [Secret Positions]\(シークレット位置\) のドロップダウン リストが表示され、4 つの項目が選択されます: 取得、一覧表示、回復、消去":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>論理的に削除されたキー コンテナーを一覧表示、回復、または消去する

1. Azure ポータルにログインします。
1. ページの上部にある検索バーをクリックします。
1. [最近のサービス] で、[Key Vault] をクリックします。 個々のキー コンテナーはクリックしないでください。
1. 画面の上部で、[Manage deleted vaults]\(削除されたコンテナーを管理する\) オプションをクリックします
1. 画面の右側にコンテキスト ペインが表示されます。
1. サブスクリプションを選択します。
1. キー コンテナーが論理的に削除されている場合は、右側のコンテキスト ペインに表示されます。
1. コンテナーの数が多すぎる場合は、コンテキスト ペインの下部にある [さらに読み込む] をクリックするか、CLI または PowerShell を使用して結果を取得します。
1. 回復または消去するコンテナーが見つかったら、その隣にあるチェック ボックスをオンにします。
1. キー コンテナーを回復する場合は、コンテキスト ペインの下部にある回復オプションを選択します。
1. キー コンテナーを完全に削除する場合は、消去オプションを選択します。

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="キー コンテナーで、[Manage deleted vaults]\(削除されたコンテナーの管理\) オプションが協調表示されます。":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="[Manage deleted vaults]\(削除されたコンテナーの管理\) では、表示された唯一のキー コンテナーが選択された状態で強調表示され、[回復] が強調表示されます。":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>論理的に削除されたシークレット、キー、証明書を一覧表示、回復、または消去する

1. Azure ポータルにログインします。
1. キー コンテナーを選択します。
1. 管理するシークレットの種類 (キー、シークレット、または証明書) に対応するブレードを選択します。
1. 画面の上部で、[Manage deleted (keys, secrets, or certificates)]\(削除された (キー、シークレット、または証明書) の管理\) をクリックします
1. 画面の右側にコンテキスト ペインが表示されます。
1. シークレット、キー、または証明書が一覧に表示されない場合は、論理的に削除された状態ではありません。
1. 管理するシークレット、キー、または証明書を選択します。
1. コンテキスト ペインの下部にある回復または消去のオプションを選択します。

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="[キー] では、[Manage deleted keys]\(削除されたキーの管理\) オプションが協調表示されます。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>キー コンテナー (CLI)

* キー コンテナーの論理的な削除が有効になっているかどうかを確認する

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* キー コンテナーの論理的な削除を有効にする

    新しいキー コンテナーはすべて、既定で論理的な削除が有効になります。 現在、論理的な削除が有効になっていないキー コンテナーがある場合、論理的な削除を有効にするには次のコマンドを使用します。

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* キー コンテナーを削除する (論理的な削除が有効になっている場合は回復可能)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 論理的に削除されたすべてのキー コンテナーの一覧を表示する

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* 論理的に削除されたキー コンテナーを回復する

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 論理的に削除されたキー コンテナーを消去する **(警告! この操作を行うと、キー コンテナーが完全に削除されます)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* キー コンテナーの消去保護を有効にする

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>証明書 (CLI)

* 証明書の消去と回復のためのアクセス権を付与する

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* 証明書の削除

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 削除された証明書の一覧を表示する

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 削除された証明書を回復する

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 論理的に削除された証明書を消去する **(警告! この操作を行うと、証明書が完全に削除されます)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>キー (CLI)

* キーの消去と回復のためのアクセス権を付与する

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Delete キー

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 削除したキーを一覧表示する

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 削除されたキーを回復する

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 論理的に削除されたキーを消去する **(警告! この操作を行うと、キーが完全に削除されます)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>シークレット (CLI)

* シークレットの消去と回復のためのアクセス権を付与する

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* シークレットを削除する

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 削除されたシークレットの一覧を表示する

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 削除されたシークレットを回復する

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 論理的に削除されたシークレットを消去する **(警告! この操作を行うと、シークレットが完全に削除されます)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>キー コンテナー (PowerShell)

* キー コンテナーの論理的な削除が有効になっているかどうかを確認する

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* キー コンテナーを削除する

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* 論理的に削除されたすべてのキー コンテナーの一覧を表示する

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* 論理的に削除されたキー コンテナーを回復する

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* 論理的に削除されたキー コンテナーを消去する **(警告! この操作を行うと、キー コンテナーが完全に削除されます)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* キー コンテナーの消去保護を有効にする

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>証明書 (PowerShell)

* 証明書を回復および削除するためのアクセス許可を付与する

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* 証明書を削除する

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* キー コンテナー内の削除されたすべての証明書の一覧を表示する

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* 削除された状態の証明書を回復する

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* 論理的に削除された証明書を消去する **(警告! この操作を行うと、証明書が完全に削除されます)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>キー (PowerShell)

* キーを回復および削除するためのアクセス許可を付与する

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* キーを削除する

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* キー コンテナー内の削除されたすべての証明書の一覧を表示する

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* 論理的に削除されたキーを回復するには

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* 論理的に削除されたキーを消去する **(警告! この操作を行うと、キーが完全に削除されます)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>シークレット (PowerShell)

* シークレットを回復および削除するためのアクセス許可を付与する

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* SQLPassword という名前のシークレットを削除する

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* キー コンテナー内の削除されたすべてのシークレットの一覧を表示する

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* 削除された状態のシークレットを回復する

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* 削除された状態のシークレットを消去する **(警告! この操作を行うと、キーが完全に削除されます)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault の PowerShell コマンドレット](/powershell/module/az.keyvault)
- [Key Vault の Azure CLI コマンド](/cli/azure/keyvault)
- [Azure Key Vault のバックアップ](backup.md)
- [Key Vault のログ記録を有効にする方法](howto-logging.md)
- [キー コンテナーへのアクセスをセキュリティで保護する](secure-your-key-vault.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
- [キー コンテナーを使用するためのベスト プラクティス](security-overview.md)