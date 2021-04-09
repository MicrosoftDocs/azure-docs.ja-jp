---
title: アカウントのアクセス キーを管理する
titleSuffix: Azure Storage
description: ストレージ アカウントのアクセスキーを表示、管理、およびローテーションする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89069661"
---
# <a name="manage-storage-account-access-keys"></a>ストレージ アカウント アクセス キーを管理する

ストレージ アカウントを作成すると、Azure により 512 ビットのストレージ アカウント アクセス キーが 2 つ生成されます。 これらのキーは、共有キー認証を使用してストレージ アカウント内のデータへのアクセスする際、認証に使用されます。

アクセスキーの管理には Azure Key Vault を使用し、キーのローテーションと再生成は定期的に行うことをお勧めします。 Azure Key Vault を使用すると、アプリケーションを中断することなく簡単にキーをローテーションできます。 また、キーを手動でローテーションすることもできます。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>アカウントのアクセス キーを表示する

アカウント アクセス キーは、Azure portal、PowerShell、または Azure CLI を使用して表示したり、コピーしたりできます。 Azure portal にはストレージ アカウントの接続文字列も用意されているのでコピーできます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からストレージ アカウントのアクセス キーまたは接続文字列を表示およびコピーするには:

1. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. **[設定]** で **[アクセス キー]** を選択します。 アカウント アクセス キーと、各キーの完全な接続文字列が表示されます。
1. **[key1]** で **[キー]** 値を見つけ、 **[コピー]** ボタンをクリックしてアカウント キーをコピーします。
1. あるいは、接続文字列全体をコピーできます。 **[Key1]** の **[接続文字列]** の値を見つけて **[コピー]** ボタンをクリックし、接続文字列をコピーします。

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Azure portal でアクセス キーを表示する方法を示したスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell でアカウント アクセス キーを取得するには、[Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) コマンドを呼び出します。

次の例では、最初のキーを取得します。 2 つ目のキーを取得するには、`Value[0]` の代わりに `Value[1]` を使用します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でアカウント アクセス キーを一覧表示するには、次の例のように [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) コマンドを呼び出します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

2 つのキーのいずれかを使用して Azure Storage にアクセスすることもできますが、一般的には、最初のキーを使用し、キーのローテーション時に 2 番目のキーの使用を予約することをお勧めします。

アカウントのアクセス キーを表示または読み込むには、ユーザーがサービス管理者であるか、**Microsoft.Storage/storageAccounts/listkeys/action** を含む Azure ロールが割り当てられている必要があります。 このアクションが含まれる Azure 組み込みロールには、**所有者**、**共同作成者**、および **ストレージ アカウント キー オペレーターのサービス ロール** があります。 サービス管理者ロールの詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関する記事を参照してください。 Azure Storage 用の組み込み RBAC ロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault を使用してアクセスキーを管理する

Microsoft では、Azure Key Vault を使用して、アクセスキーの管理とローテーションをするようお勧めします。 アプリケーションは、Key Vault 内のキーに安全にアクセスできるので、アプリケーションコードと一緒に格納することは避けてください。 キー管理に Key Vault を使用する方法の詳細については、次の記事を参照してください:

- [Azure Key Vault と PowerShell を使用してストレージ アカウント キーを管理する](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Azure Key Vault と Azure CLI を使用してストレージアカウントキーを管理する](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>アクセス キーを手動でローテーションする

Microsoft では、ストレージアカウントのセキュリティを確保するために、アクセスキーを定期的にローテーションすることをお勧めします。 可能であれば、Azure Key Vault を使用してアクセスキーを管理してください。 Key Vault を使用していない場合は、キーを手動でローテーションする必要があります。

キーをローテーションさせることができるように、2 つのアクセス キーが割り当てられます。 2 つのキーで、アプリケーションはプロセス全体を通じて Azure Storage へのアクセスを維持します。

> [!WARNING]
> アクセス キーの再生成は、ストレージ アカウント キーに依存するあらゆるアプリケーションまたは Azure サービスに影響を与える可能性があります。 メディア サービス、クラウド、モバイル アプリケーション、Azure Storage 向けのグラフィカル ユーザー インターフェイス アプリケーション ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) を含む、アクセス キーを使用してストレージ アカウントにアクセスするクライアントは新しいキーを使用するように更新する必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal でストレージ アカウントのアクセス キーをローテーションさせるには:

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
1. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. **[設定]** で **[アクセス キー]** を選択します。
1. ストレージ アカウントのプライマリ アクセス キーを再生成するには、プライマリ アクセス キーの隣にある **[再生成]** ボタンを選択します。
1. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
1. 同様に、セカンダリ アクセス キーを再生成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してストレージ アカウントのアクセス キーをローテーションするには:

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
1. 次の例に示すように、[New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) コマンドを呼び出し、プライマリ アクセス キーを再生成します。

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
1. 同様に、セカンダリ アクセス キーを再生成します。 セカンダリ キーを再生成するには、キー名として `key1` の代わりに `key2` を使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントのアクセス キーをローテーションするには:

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
1. 次の例に示すように、[az storage account keys renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) コマンドを呼び出し、プライマリ アクセス キーを再生成します。

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
1. 同様に、セカンダリ アクセス キーを再生成します。 セカンダリ キーを再生成するには、キー名として `key1` の代わりに `key2` を使用します。

---

> [!NOTE]
> Microsoft では、すべてのアプリケーションで、同時にいずれかのキーのみを使用することをお勧めします。 キー 1 を使用する場所とキー 2 を使用する場所がある場合、キーを循環させるときに、一部のアプリケーションがアクセス権を失います。

アカウントのアクセス キーをローテーションさせるには、ユーザーがサービス管理者であるか、**Microsoft.Storage/storageAccounts/regeneratekey/action** を含む Azure ロールが割り当てられている必要があります。 このアクションが含まれる Azure 組み込みロールには、**所有者**、**共同作成者**、および **ストレージ アカウント キー オペレーターのサービス ロール** があります。 サービス管理者ロールの詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関する記事を参照してください。 Azure Storage 用の Azure 組み込みロールの詳細については、[Azure RBAC の Azure 組み込みロール](../../role-based-access-control/built-in-roles.md#storage)に関するページの「**ストレージ**」セクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure ストレージ アカウントの概要](storage-account-overview.md)
- [ストレージ アカウントの作成](storage-account-create.md)
