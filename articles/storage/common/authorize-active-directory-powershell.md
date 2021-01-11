---
title: Azure AD 資格情報を使用して PowerShell コマンドを実行して BLOB またはキューのデータにアクセスする
titleSuffix: Azure Storage
description: PowerShell では、Azure AD 資格情報でサインインし、Azure Storage BLOB とキューのデータにコマンドを実行できます。 セッションにはアクセス トークンが与えられ、呼び出し操作の承認に使用されます。 アクセス許可は、Azure AD セキュリティ プリンシパルに割り当てられた Azure ロールによって異なります。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 75ca39ad00966928bb7887cb14255470b17579dd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589148"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure AD 資格情報を使用して PowerShell コマンドを実行して BLOB またはキューのデータにアクセスする

Azure Storage には、PowerShell のための拡張機能があります。この機能では、Azure Active Directory (Azure AD) 資格情報でサインインし、スクリプト コマンドを実行できます。 Azure AD 資格情報で PowerShell にサインインすると、OAuth 2.0 アクセス トークンが返されます。 そのトークンが PowerShell によって自動的に使用され、BLOB または Queue storage に対するその後のデータ操作が承認されます。 サポートされている操作については、コマンドでアカウント キーや SAS トークンを渡す必要がなくなりました。

BLOB とキューのデータへのアクセス許可をロールベースのアクセス制御 (RBAC) を介して Azure AD セキュリティ プリンシパルに割り当てることができます。 Azure Storage の Azure ロールの詳細については、[RBAC を使用した Azure Storage データへのアクセス権の管理](storage-auth-aad-rbac.md)に関する記事を参照してください。

## <a name="supported-operations"></a>サポート対象の操作

Azure Storage 拡張機能は、BLOB とキュー データの操作でサポートされています。 呼び出す操作は、PowerShell にサインインする Azure AD セキュリティ プリンシパルに与えられているアクセス許可に依存します。 Azure Storage のコンテナーまたはキューのアクセス許可は、RBAC を介して割り当てられます。 たとえば、**BLOB データ閲覧者**ロールが割り当てられている場合、コンテナーまたはキューからデータを読み取るスクリプト コマンドを実行できます。 **BLOB データ共同作成者**ロールが割り当てられている場合、コンテナー、キュー、またはそれらに含まれているデータの読み取り、書き込み、削除を行うスクリプト コマンドを実行できます。

コンテナーまたはキューでの各 Azure Storage 操作に必要なアクセス許可の詳細については、「[Call storage operations with OAuth tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)」 (OAuth トークンを使用してストレージ操作を呼び出す) を参照してください。  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD サインイン情報を使用して PowerShell コマンドを呼び出す

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell を使用してサインインし Azure Storage に対する後続操作を Azure AD サインイン情報を使用して実行するには、ストレージ アカウントを参照するストレージ コンテキストを作成し、`-UseConnectedAccount` パラメーターを含めます。

次の例では、Azure PowerShell から自分の Azure AD サインイン情報を使用して新しいストレージ アカウントにコンテナーを作成する方法を示します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドを使用して Azure アカウントにサインインします。

    ```powershell
    Connect-AzAccount
    ```

    PowerShell を使用した Azure へのサインインの詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

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

1. コンテナーを作成する前に、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。 Azure ロールの割り当ての詳細については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac.md)」を参照してください。

    > [!IMPORTANT]
    > Azure ロールの割り当ての反映には数分かかることがあります。

1. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) を呼び出して、コンテナーを作成します。 この呼び出しでは前の手順で作成したコンテキストが使用されるため、コンテナーは自分の Azure AD サインイン情報を使用して作成されます。

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>次のステップ

- [PowerShell を使用して、BLOB およびキュー データへのアクセスのための Azure ロールを割り当てる](storage-auth-aad-rbac-powershell.md)
- [Azure リソースに対するマネージド ID を使用して BLOB およびキュー データへのアクセスを認証する](storage-auth-aad-msi.md)
