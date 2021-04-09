---
title: Azure ファイル共有へのアクセスを制御する - オンプレミス AD DS 認証
description: ストレージ アカウントを表す Active Directory Domain Services ID にアクセス許可を割り当てる方法について説明します。 これにより、ID ベースの認証を使用してアクセスを制御できます。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91716032"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>パート 2: ID に共有レベルのアクセス許可を割り当てる

この記事を開始する前に、前の記事「[アカウントへの AD DS 認証を有効にする](storage-files-identity-ad-ds-enable.md)」を完了しておいてください。

ストレージ アカウントで Active Directory Domain Services (AD DS) 認証を有効にしたら、ファイル共有にアクセスするために、共有レベルのアクセス許可を構成する必要があります。 Azure ファイル共有リソースへのアクセスに使用する ID は、AD DS と Azure AD の両方に存在するハイブリッド ID にする必要があります。 たとえば、AD DS でのユーザーが user1@onprem.contoso.com であり、Azure AD Connect 同期を使用して user1@contoso.com として Azure AD に同期しているとします。このユーザーが Azure Files にアクセスできるようにするには、user1@contoso.com に共有レベルのアクセス許可を割り当てる必要があります。 グループまたはサービス プリンシパルにも同じ概念が適用されます。 このため、Azure AD Connect 同期を使用して、ユーザーとグループを AD DS から Azure AD に同期する必要があります。 

Azure ファイル共有への AD DS 認証をサポートするために、共有レベルのアクセス許可を、AD DS 内の同じユーザーまたはグループを表す Azure AD ID に割り当てる必要があります。 Azure Managed Identities (MSIs) など、Azure AD にのみ存在する ID に対する認証と承認は、AD DS 認証ではサポートされていません。 この記事では、ファイル共有のための共有レベルのアクセス許可を ID に割り当てる方法について説明します。


## <a name="share-level-permissions"></a>共有レベルのアクセス許可

一般的に、ユーザーと ID のグループを表す Azure AD グループに対する高レベルのアクセス管理に共有レベルのアクセス許可を使用してから、ディレクトリおよびファイル レベルでのきめ細かいアクセス制御に Windows ACL を利用することを推奨します。 

ユーザーに共有レベルのアクセス許可を付与するための、3 つの Azure 組み込みロールがあります。

- **ストレージ ファイル データ SMB 共有閲覧者** では、SMB 経由の Azure Storage ファイル共有での読み取りアクセスが許可されます。
- **ストレージ ファイル データ SMB 共有共同作成者** では、SMB 経由の Azure Storage ファイル共有での読み取り、書き込み、削除アクセスが許可されます。
- **ストレージ ファイル データの SMB 共有の管理者特権共同作成者** では、SMB 経由の Azure Storage ファイル共有での Windows ACL の読み取り、書き込み、削除、および変更が許可されます。

> [!IMPORTANT]
> ファイルの所有権を引き受ける機能を含めて、ファイル共有を完全に管理制御するには、ストレージ アカウント キーを使用する必要があります。 Azure AD 資格情報を使用した管理制御はサポートされていません。

Azure portal、Azure PowerShell、または Azure CLI を使用して、共有レベルのアクセス許可を付与するために、組み込みのロールをユーザーの Azure AD ID に割り当てることができます。

## <a name="assign-an-azure-role"></a>Azure ロールを割り当てる

### <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com) を使用して Azure ロールを Azure AD ID に割り当てるには、これらの手順に従います。

1. Azure portal でファイル共有に移動するか、[ファイル共有を作成](storage-how-to-create-file-share.md)します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当てを追加する]** を選択します。
1. **[ロールの割り当ての追加]** ブレードで、 **[ロール]** リストから適切な組み込みロール (ストレージ ファイル データ SMB 共有閲覧者、ストレージ ファイル データ SMB 共有共同作成者) を選択します。 **[アクセスの割り当て先]** は既定の設定のままにしておきます。**Azure AD のユーザー、グループ、サービス プリンシパル**。 名前またはメール アドレスで、ターゲット Azure AD ID を選択します。 **選択した Azure AD ID は、ハイブリッド ID である必要があり、クラウドのみの ID にすることはできません。** これは、同じ ID が AD DS でも表示されることを意味します。
1. **[保存]** を選択して、ロールの割り当て操作を完了します。

### <a name="powershell"></a>PowerShell

次の PowerShell サンプルは、Azure ロールをサインイン名に基づいて Azure AD ID に割り当てる方法を示しています。 PowerShell で Azure ロールを割り当てる方法の詳細については、[Azure PowerShell モジュールを使用した Azure ロールの割り当ての追加または削除](../../role-based-access-control/role-assignments-powershell.md)に関するページをご覧ください。

次のサンプル スクリプトを実行する前に、プレースホルダー値 (かっこを含む) を独自の値に置き換えます。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
次の CLI 2.0 コマンドでは、Azure ロールをサインイン名に基づいて Azure AD ID に割り当てています。 Azure CLI で Azure ロールを割り当てる方法の詳細については、「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」をご覧ください。 

次のサンプル スクリプトを実行する前に、プレースホルダー値 (かっこを含む) を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>次のステップ

共有レベルのアクセス許可を割り当てたので、ディレクトリとファイル レベルのアクセス許可を構成する必要があります。 次の記事に進みます。

[パート 3: SMB 経由でディレクトリとファイル レベルのアクセス許可を構成する](storage-files-identity-ad-ds-configure-permissions.md)
