---
title: Azure AD 資格情報を使用して PowerShell コマンドを実行し、キューのデータにアクセスする
titleSuffix: Azure Storage
description: PowerShell では、Azure AD 資格情報でサインインし、Azure Queue Storage のデータにコマンドを実行できます。 セッションにはアクセス トークンが与えられ、呼び出し操作の承認に使用されます。 アクセス許可は、Azure AD セキュリティ プリンシパルに割り当てられた Azure ロールによって異なります。
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 61bcf7abca2860078bd89da070309a0057360f0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370225"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>Azure AD 資格情報を使用して PowerShell コマンドを実行し、キューのデータにアクセスする

Azure Storage には、PowerShell のための拡張機能があります。この機能では、Azure Active Directory (Azure AD) 資格情報でサインインし、スクリプト コマンドを実行できます。 Azure AD 資格情報で PowerShell にサインインすると、OAuth 2.0 アクセス トークンが返されます。 そのトークンが PowerShell によって自動的に使用され、Queue Storage に対するその後のデータ操作が承認されます。 サポートされている操作については、コマンドでアカウント キーや SAS トークンを渡す必要がなくなりました。

キュー データへのアクセス許可を Azure ロールベースのアクセス制御 (Azure RBAC) を介して Azure AD セキュリティ プリンシパルに割り当てることができます。 Azure Storage の Azure ロールの詳細については、[Azure RBAC を使用した Azure Storage データへのアクセス権の管理](../common/storage-auth-aad-rbac-portal.md)に関する記事を参照してください。

## <a name="supported-operations"></a>サポート対象の操作

Azure Storage 拡張機能は、キュー データの操作でサポートされています。 呼び出す操作は、PowerShell にサインインする Azure AD セキュリティ プリンシパルに与えられているアクセス許可に依存します。 キューへのアクセス許可は、Azure RBAC を介して割り当てられます。 たとえば、**キュー データ閲覧者** ロールが割り当てられている場合、キューからデータを読み取るスクリプト コマンドを実行できます。 **キュー データ共同作成者** ロールが割り当てられている場合、キューまたはキューに含まれているデータの読み取り、書き込み、削除を行うスクリプト コマンドを実行できます。

キューでの各 Azure Storage 操作に必要なアクセス許可の詳細については、「[OAuth トークンを使用したストレージ操作の呼び出し](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)」を参照してください。

> [!IMPORTANT]
> ストレージ アカウントが Azure Resource Manager **ReadOnly** ロックでロックされている場合、そのストレージ アカウントに対して [キーの一覧表示](/rest/api/storagerp/storageaccounts/listkeys)操作は許可されません。 **キーの一覧表示** は POST 操作であり、アカウントに対して **ReadOnly** ロックが設定されている場合、すべての POST 操作が禁止されます。 このため、アカウントが **ReadOnly** ロックでロックされている場合、アカウント キーをまだ所有していないユーザーは、Azure AD 資格情報を使用してキュー データにアクセスする必要があります。 PowerShell では、`-UseConnectedAccount` パラメーターを使用して、Azure AD 資格情報で **AzureStorageContext** オブジェクトを作成します。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD サインイン情報を使用して PowerShell コマンドを呼び出す

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell を使用してサインインし Azure Storage に対する後続操作を Azure AD サインイン情報を使用して実行するには、ストレージ アカウントを参照するストレージ コンテキストを作成し、`-UseConnectedAccount` パラメーターを含めます。

次の例では、Azure PowerShell から自分の Azure AD サインイン情報を使用して新しいストレージ アカウントにキューを作成する方法を示します。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

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

1. キューを作成する前に、[ストレージ キュー データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)ロールを自分に割り当てます。 自分がアカウント オーナーである場合でも、ストレージ アカウントに対してデータ操作を実行するための明示的なアクセス許可が必要となります。 Azure ロールの割り当ての詳細については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-portal.md)」を参照してください。

    > [!IMPORTANT]
    > Azure ロールの割り当ての反映には数分かかることがあります。

1. [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) を呼び出してキューを作成します。 この呼び出しでは前の手順で作成したコンテキストが使用されるため、キューは自分の Azure AD サインイン情報を使用して作成されます。

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>次のステップ

- [PowerShell を使用して、BLOB およびキュー データへのアクセスのための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-powershell.md)
- [Azure リソースに対するマネージド ID を使用して BLOB およびキュー データへのアクセスを認証する](../common/storage-auth-aad-msi.md)
