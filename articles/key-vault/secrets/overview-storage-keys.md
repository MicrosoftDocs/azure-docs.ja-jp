---
title: Azure Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する
description: ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e89716d0560cbf7960cb7bde67156c8df0045a31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499222"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する
> [!IMPORTANT]
> Azure Storage と Azure Active Directory (Azure AD) の統合 (Microsoft のクラウドベースの ID およびアクセス管理サービス) を使用することをお勧めします。 Azure AD 統合は [Azure BLOB およびキュー](../../storage/common/storage-auth-aad.md)で利用できます。また、Azure Key Vault と同様に、Azure Storage へのトークンベースの OAuth2 アクセスが提供されます。 Azure AD では、ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証することができます。 Azure で実行するときは、[Azure AD マネージド ID](../../active-directory/managed-identities-azure-resources/index.yml) を使用できます。 マネージド ID を使用すると、クライアント認証やアプリケーションでの資格情報の保存が不要になります。 Azure AD 認証が不可能な場合にのみ、以下のソリューションを使用してください。

Azure ストレージ アカウントでは、アカウント名とキーで構成された資格情報が使用されます。 キーは自動生成され、暗号キーではなくパスワードとして機能します。 Key Vault では、ストレージ アカウントでストレージ アカウント キーを定期的に再生成することでそれらのキーの管理が行われることに加え、ストレージ アカウント内のリソースへの委任アクセス用の Shared Access Signature トークンが提供されます。

Key Vault マネージド ストレージ アカウント キー機能を使用して、Azure ストレージ アカウントのキーを一覧表示し (同期)、定期的にキーを再生成 (ローテーション) できます。 ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理できます。

マネージド ストレージ アカウント キー機能を使用する場合は、次の点を考慮してください。

- キーの値は、呼び出し元への応答で返されることはありません。
- ストレージ アカウント キーの管理は Key Vault のみが行う必要があります。 キーを自分で管理したり、Key Vault のプロセスに干渉したりしないでください。
- ストレージ アカウント キーの管理は、1 つの Key Vault オブジェクトのみが行う必要があります。 複数のオブジェクトからのキー管理を許可しないでください。
- キーの再生成は、Key Vault のみを使用して行います。 ストレージ アカウント キーを手動で再生成しないでください。

## <a name="service-principal-application-id"></a>サービス プリンシパルのアプリケーション ID

Azure AD テナントは、登録されている各アプリケーションに[サービス プリンシパル](../../active-directory/develop/developer-glossary.md#service-principal-object)を提供します。 サービス プリンシパルはアプリケーション ID として機能し、Azure RBAC を介した他の Azure リソースへのアクセスに対する承認のセットアップ時に使用されます。

Key Vault は、すべての Azure AD テナントに事前登録されている Microsoft アプリケーションです。 Key Vault は、各 Azure クラウド内に同じアプリケーション ID で登録されています。

| テナント | クラウド | アプリケーション ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure Public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| その他  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>前提条件

このガイドを完了するには、まず次の手順を実行する必要があります。

- [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。
- [Key Vault を作成します](quick-create-cli.md)
- [Azure Storage アカウント](../../storage/common/storage-account-create.md?tabs=azure-cli)を作成します。 ストレージ アカウント名には小文字と数字のみを使用する必要があります。 名前の長さは 3 文字から 24 文字でなければなりません。
      
## <a name="manage-storage-account-keys"></a>ストレージ アカウント キーを管理する

### <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

[az login](/powershell/module/az.accounts/connect-azaccount) コマンドを使用して、Azure CLI セッションを認証します。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault にストレージ アカウントへのアクセス権を付与する

Azure CLI の [az role assignment create](/cli/azure/role/assignment) コマンドを使用して、ストレージ アカウントへのアクセスを Key Vault に付与します。 コマンドで次のパラメーター値を設定します。

- `--role`:"Storage Account Key Operator Service Role" の Azure ロールを渡します。 このロールは、アクセス スコープをお使いのストレージ アカウントに制限します。 従来のストレージ アカウントには、"従来のストレージ アカウント キー オペレーターのサービス ロール" を渡します。
- `--assignee`:値 "https://vault.azure.net" を渡します。これは Azure パブリック クラウドのキー コンテナーの URL です。 (Azure Goverment クラウドの場合、"--asingee-object-id" を代わりに使用します。「[サービス プリンシパルのアプリケーション ID](#service-principal-application-id)」を参照してください)
- `--scope`:ストレージ アカウントのリソース ID を渡します。これは `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` という形式で指定します。 サブスクリプション ID を検索するには、Azure CLI の [az account list](/cli/azure/account?#az-account-list) コマンドを使用します。ストレージ アカウント名とストレージ アカウント リソース グループを検索するには、Azure CLI の [az storage account list](/cli/azure/storage/account?#az-storage-account-list) コマンドを使用します。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>マネージド ストレージ アカウントにユーザー アカウントのアクセス許可を与える

Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) コマンドレットを使用して、Key Vault アクセス ポリシーを更新し、ストレージ アカウントのアクセス許可をユーザー アカウントに付与します。

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

ストレージ アカウントのアクセス許可は、Azure portal のストレージ アカウントの [アクセス ポリシー] ページで使用できないことに注意してください。
### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault のマネージド ストレージ アカウントを作成する

 Azure CLI の [az keyvault storage](/cli/azure/keyvault/storage?#az-keyvault-storage-add) コマンドを使用して、Key Vault マネージド ストレージ アカウントを作成します。 90 日間の再生成期間を設定します。 交換時期になると、Key Vault はアクティブでないキーを再生成し、新しく作成されたキーをアクティブとして設定します。 SAS トークンを発行するために使用されるキーは常に 1 つだけで、これがアクティブなキーです。 コマンドで次のパラメーター値を設定します。

- `--vault-name`:キー コンテナーの名前を渡します。 キー コンテナーの名前を検索するには、Azure CLI の [az keyvault list](/cli/azure/keyvault?#az-keyvault-list) コマンドを使用します。
- `-n`:ストレージ アカウントの名前を渡します。 ストレージ アカウントの名前を確認するには、Azure CLI の [az storage account list](/cli/azure/storage/account?#az-storage-account-list) コマンドを使用します。
- `--resource-id`:ストレージ アカウントのリソース ID を渡します。これは `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` という形式で指定します。 サブスクリプション ID を検索するには、Azure CLI の [az account list](/cli/azure/account?#az-account-list) コマンドを使用します。ストレージ アカウント名とストレージ アカウント リソース グループを検索するには、Azure CLI の [az storage account list](/cli/azure/storage/account?#az-storage-account-list) コマンドを使用します。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Shared Access Signature トークン

Shared Access Signature トークンを生成するように Key Vault に指示することもできます。 Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 アカウント キーを共有することなく、ストレージ アカウント内のリソースへのアクセス権をクライアントに付与できます。 Shared Access Signature により、アカウント キーを侵害されることなくストレージ リソースを安全に共有することができます。

このセクションのコマンドは、次の操作を実行します。

- アカウントの Shared Access Signature 定義 `<YourSASDefinitionName>` を設定します。 この定義は、Key Vault `<YourKeyVaultName>` 内の Key Vault マネージド ストレージ アカウント `<YourStorageAccountName>` に設定されます。
- BLOB、ファイル、テーブル、およびキューの各サービスに対してアカウントの Shared Access Signature トークンを作成します。 サービス、コンテナー、およびオブジェクトのリソースの種類に対してトークンが作成されます。 トークンは、すべてのアクセス許可を持ち、https で、指定した開始日と終了日を使用して作成されます。
- Key Vault マネージド ストレージの Shared Access Signature 定義をコンテナーに設定します。 この定義には、作成された Sared Access Signature トークンのテンプレート URI があります。 この定義は、Shared Access Signature の種類 `account` を持っており、N 日間有効です。
- Shared Access Signature がキー コンテナーにシークレットとして保存されていることを確認します。

### <a name="create-a-shared-access-signature-token"></a>Shared Access Signature トークンの作成

Azure CLI の [az storage account generate-sas](/cli/azure/storage/account?#az-storage-account-generate-sas) コマンドを使用して、Shared Access Signature 定義を作成します。 この操作には `storage` と `setsas` のアクセス許可が必要です。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
操作が正常に実行されたら、出力をコピーします。

```console
"se=2020-01-01&sp=***"
```

この出力は、次の手順で `--template-uri` パラメーターに渡されます。

### <a name="generate-a-shared-access-signature-definition"></a>Shared Access Signature 定義の生成

Azure CLI の[az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create) コマンドを使用して、前の手順の出力を `--template-uri` パラメーターに渡し、Shared Access Signature 定義を作成します。  `-n` パラメーターには任意の名前を指定できます。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Shared Access Signature 定義の検証

Shared Access Signature 定義がキー コンテナーに格納されていることを確認するには、Azure CLI の [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) コマンドを使用します。

ここで、[az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) コマンドと `id` プロパティを使用して、そのシークレットの内容を表示できます。

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>次のステップ

- [キー、シークレット、証明書](/rest/api/keyvault/)について学習します。
- [Azure Key Vault チームのブログ](/archive/blogs/kv/)の記事を確認します。
- [az keyvault storage](/cli/azure/keyvault/storage) のリファレンス ドキュメントを参照してください。
