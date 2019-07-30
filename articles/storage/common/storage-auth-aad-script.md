---
title: Azure AD 資格情報で Azure CLI または PowerShell コマンドを実行して BLOB またはキューのデータにアクセスする | Microsoft Docs
description: Azure CLI と PowerShell では、Azure AD 資格情報でサインインし、Azure Storage Blob とキューのデータにコマンドを実行できます。 セッションにはアクセス トークンが与えられ、呼び出し操作の承認に使用されます。 アクセス許可は、Azure AD セキュリティ プリンシパルに割り当てられた RBAC ロールによって異なります。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 80ab896e1393d6c68b22a61d1b96acd507aa6994
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249903"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure AD 資格情報で Azure CLI または PowerShell コマンドを実行して BLOB またはキューのデータにアクセスする

Azure Storage には、Azure CLI と PowerShell のための拡張機能があります。この機能では、Azure Active Directory (Azure AD) 資格情報でサインインし、スクリプト コマンドを実行できます。 Azure AD 資格情報で Azure CLI または PowerShell にサインインすると、OAuth 2.0 アクセス トークンが返されます。 そのトークンが CLI または PowerShell によって自動的に使用され、BLOB または Queue storage に対するその後のデータ操作が承認されます。 サポートされている操作については、コマンドでアカウント キーや SAS トークンを渡す必要がなくなりました。

BLOB とキューのデータへのアクセス許可をロールベースのアクセス制御 (RBAC) を介して Azure AD セキュリティ プリンシパルに割り当てることができます。 Azure Storage の RBAC ロールの詳細については、[RBAC を使用した Azure ストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)」を参照してください。

## <a name="supported-operations"></a>サポート対象の操作

拡張機能は、コンテナーとキューの操作でサポートされています。 呼び出す操作は、Azure CLI または PowerShell にサインインする Azure AD セキュリティ プリンシパルに与えられているアクセス許可に依存します。 Azure Storage のコンテナーまたはキューのアクセス許可は、ロールベースのアクセス制御 (RBAC) を介して割り当てられます。 たとえば、**BLOB データ閲覧者**ロールが割り当てられている場合、コンテナーまたはキューからデータを読み取るスクリプト コマンドを実行できます。 **BLOB データ共同作成者**ロールが割り当てられている場合、コンテナー、キュー、またはそれらに含まれているデータの読み取り、書き込み、削除を行うスクリプト コマンドを実行できます。 

コンテナーまたはキューでの各 Azure Storage 操作に必要なアクセス許可の詳細については、「[Call storage operations with OAuth tokens](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#call-storage-operations-with-oauth-tokens)」 (OAuth トークンを使用してストレージ操作を呼び出す) を参照してください。  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Azure AD サインイン情報を使用して CLI コマンドを呼び出す

Azure CLI では、BLOB とキューのデータに対するデータ操作について `--auth-mode` パラメーターがサポートされています。

- Azure AD セキュリティ プリンシパルを利用してサインインするために、`--auth-mode` パラメーターを `login` に設定します。
- アカウントの認証パラメーターが与えられない場合、アカウント キーを問い合わせる目的で、`--auth-mode` パラメーターをレガシー `key` 値に設定します。 

次の例では、Azure CLI から自分の Azure AD サインイン情報を使用して新しいストレージ アカウントにコンテナーを作成する方法を示します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。 

1. Azure CLI バージョン 2.0.46 以降がインストールされていることを確認してください。 `az --version` を実行し、インストールされたバージョンを確認します。

1. ブラウザー ウィンドウで `az login` と認証を実行します。 

    ```azurecli
    az login
    ```

1. 必要なサブスクリプションを指定します。 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) を使用してリソース グループを作成します。 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)を使用して、このリソース グループ内にストレージ アカウントを作成します。

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. コンテナーを作成する前に、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。 RBAC ロールの割り当ての詳細については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac.md)」を参照してください。

    > [!IMPORTANT]
    > RBAC ロールの割り当ての反映には数分かかることがあります。

1. [az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) コマンドを、`--auth-mode` パラメーターに `login` を設定して呼び出し、自分の Azure AD サインイン情報を使用してコンテナーを作成します。

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

`--auth-mode` パラメーターに関連付けられている環境変数は `AZURE_STORAGE_AUTH_MODE` です。 この環境変数に適切な値を指定すれば、Azure Storage データ操作を呼び出すたびにパラメーターに値を設定する必要はなくなります。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD サインイン情報を使用して PowerShell コマンドを呼び出す

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell を使用してサインインし Azure Storage に対する後続操作を Azure AD サインイン情報を使用して実行するには、ストレージ アカウントを参照するストレージ コンテキストを、`-UseConnectedAccount`パラメーターを含めて作成します。

次の例では、Azure PowerShell から自分の Azure AD サインイン情報を使用して新しいストレージ アカウントにコンテナーを作成する方法を示します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

1. `Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って自分の Azure AD サインイン情報を入力します。 

    ```powershell
    Connect-AzAccount
    ```

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を呼び出して、Azure リソース グループを作成します。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) を呼び出して、ストレージ アカウントを作成します。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) を呼び出して、新しいストレージ アカウントを指定するストレージ アカウント コンテキストを取得します。 ストレージ アカウントを操作するときは、サインイン情報を繰り返し渡す代わりに、このコンテキストを参照することができます。 後続のデータ操作を自分の Azure AD サインイン情報を使用して呼び出すには、`-UseConnectedAccount` パラメーターを含めます。

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. コンテナーを作成する前に、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。 RBAC ロールの割り当ての詳細については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac.md)」を参照してください。

    > [!IMPORTANT]
    > RBAC ロールの割り当ての反映には数分かかることがあります。

1. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) を呼び出して、コンテナーを作成します。 この呼び出しでは前の手順で作成したコンテキストが使用されるため、コンテナーは自分の Azure AD サインイン情報を使用して作成されます。 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>次の手順

- Azure Storage の RBAC ロールについては、[RBAC を使用したストレージ データへのアクセス権の管理](storage-auth-aad-rbac.md)に関するページをご覧ください。
- Azure Storage を使用して Azure リソースのマネージド ID を使用する方法については、「[Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する](storage-auth-aad-msi.md)」を参照してください。
- ストレージ アプリケーション内からコンテナーやキューへのアクセスを承認する方法については、[ストレージ アプリケーションで Azure AD を使用する](storage-auth-aad-app.md)方法に関するページを参照してください。
