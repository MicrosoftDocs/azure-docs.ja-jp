---
title: SMB を使用して Azure Files への Azure Active Directory 認証を有効にする (プレビュー) - Azure Storage
description: Azure Active Directory (Azure AD) Domain Services を使用し SMB (Server Message Block) 経由で ID ベースの認証を有効にする方法 (プレビュー) について説明します。 ドメインに参加している Windows 仮想マシン (VM) は、Azure AD の資格情報を使用して Azure ファイル共有にアクセスできます。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696122"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>SMB を使用して Azure Files への Azure Active Directory ドメイン サービス認証を有効にする (プレビュー)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

SMB を使用した Azure Files への Azure AD 認証の概要については、[SMB を使用した Azure Files への Azure Active Directory 認証 (プレビュー) の概要](storage-files-active-directory-overview.md)に関する記事を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>ワークフローの概要
SMB を使用した Azure Files への Azure AD DS 認証を有効にする前に、Azure AD と Azure Storage 環境が正しく構成されていることを確認してください。 [前提条件](#prerequisites)を参照して、必要な手順をすべて実行したことを確認することをお勧めします。 

次に、以下の手順を実行して、Azure AD 資格情報を使用して Azure Files のリソースへのアクセス許可を付与します。 

1. 関連付けられた Azure AD Domain Services デプロイにストレージ アカウントを登録するため、SMB を使用した、ストレージ アカウントの Azure AD DS 認証を有効にします。
2. Azure AD の ID (ユーザー、グループ、またはサービス プリンシパル) に、共有のアクセス許可を割り当てます。
3. SMB を使用したディレクトリおよびファイルへの NTFS アクセス許可を構成します。
4. ドメインに参加している VM から Azure ファイル共有をマウントします。

次の図は、SMB を使用して Azure Files への Azure AD DS 認証を有効にするためのエンドツーエンドのワークフローを示しています。 

![SMB を使用した Azure Files への Azure AD ワークフローを示す図](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>前提条件 

Azure Files への SMB 経由の Azure AD を有効にする前に、次の前提条件を完了していることを確認してください。

1.  **Azure AD テナントを選択または作成します。**

    SMB を使用した Azure AD 認証には、新規または既存のテナントを使用できます。 アクセスするテナントとファイル共有は、同じサブスクリプションに関連付けられている必要があります。

    新しく Azure AD テナントを作成するには、[Azure AD テナントと Azure AD サブスクリプションを追加](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)できます。 既存の Azure AD テナントがあるが、Azure Files で使用するために新しいテナントを作成する場合は、「[Azure Active Directory テナントを作成する](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)」を参照してください。

2.  **Azure AD テナントで Azure AD Domain Services を有効にします。**

    Azure AD 資格情報を使用した認証をサポートするには、Azure AD テナントの Azure AD Domain Services を有効にする必要があります。 Azure AD テナントの管理者でない場合は、管理者に連絡し、[Azure portal を使用した Azure Active Directory Domain Services の有効化](../../active-directory-domain-services/create-instance.md)に関する記事に書かれている手順を実行します。

    通常、Azure AD Domain Services のデプロイが完了するまでには 15 分ほどかかります。 次の手順に進む前に、Azure AD Domain Services の正常性状態が**実行中**と表示されており、パスワード ハッシュ同期が有効になっていることを確認します。

3.  **Azure AD Domain Services を使用している Azure VM ドメインに参加します。**

    VM から Azure AD の資格情報を使用してファイル共有にアクセスするには、VM が Azure AD Domain Services のドメインに参加している必要があります。 VM のドメイン参加方法の詳細については、「[Windows Server 仮想マシンのマネージド ドメインへの参加](../../active-directory-domain-services/join-windows-vm.md)」を参照してください。

    > [!NOTE]
    > SMB を使用した Azure Files の Azure AD DS 認証は、Windows 7 または Windows Server 2008 R2 以降の OS バージョンで実行されている Azure VM でのみサポートされます。

4.  **Azure ファイル共有を選択または作成します。**

    Azure AD テナントと同じサブスクリプションに関連付けられている新規または既存のファイル共有を選択します。 新しいファイル共有を作成する方法の詳細については、「[Azure Files にファイル共有を作成する](storage-how-to-create-file-share.md)」を参照してください。 
    パフォーマンスを最適化するには、共有にアクセスする予定の VM と同じリージョンにファイル共有を配置することをお勧めします。

5.  **ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることにより、Azure Files の接続を確認します。**

    VM とファイル共有が正しく構成されていることを確認するには、ストレージ アカウント キーを使用してファイル共有をマウントします。 詳細については、「[Windows で Azure ファイル共有をマウントして共有にアクセスする](storage-how-to-use-files-windows.md)」を参照してください。

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>アカウントへの Azure AD DS 認証を有効にする

Azure Files への SMB 経由の Azure AD DS 認証を有効にするには、Azure portal、Azure PowerShell、または Azure CLI を使用して、2018 年 9 月 24 日以降に作成されたストレージ アカウントでプロパティを設定できます。 このプロパティを設定すると、ストレージ アカウントが、関連付けられている Azure AD Domain Services デプロイに登録されます。 その後、ストレージ アカウント内のすべての新規および既存のファイル共有に対する、SMB を使用した Azure AD DS 認証が有効になります。 

SMB を使用した Azure AD DS 認証を有効にするには、Azure AD テナントに Azure AD Domain Services が正常にデプロイされている必要があることに注意してください。 詳細については、[前提条件](#prerequisites)を参照してください。

### <a name="azure-portal"></a>Azure ポータル

[Azure portal](https://portal.azure.com) を使用して SMB 経由の Azure AD DS 認証を有効にするには、次の手順に従います。

1. Azure Portal で、既存のストレージ アカウントに移動するか、または[ストレージ アカウントを作成](../common/storage-quickstart-create-account.md)します。
2. **[設定]** セクションで、 **[構成]** を選択します。
3. **[Azure Active Directory Authentication for Azure Files (preview)] (Azure Files への Azure Active Directory 認証 (プレビュー))** を有効にします。

次の図は、ストレージ アカウントへの SMB 経由の Azure AD 認証を有効にする方法を示しています。

![Azure Portal で SMB 経由の Azure AD 認証を有効にする](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Azure PowerShell を使用して SMB 経由の Azure AD DS 認証を有効にするには、まず、最新の Az モジュール (2.4 以降) または Az.Storage モジュール (1.5 以降) をインストールします。PowerShell モジュールのインストールの詳細については、[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-Az-ps)に関する記事を参照してください。

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

次に、新しいストレージ アカウントを作成してから [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) を呼び出し、**EnableAzureActiveDirectoryDomainServicesForFile** パラメーターを **true** に設定します。 以下の例のプレースホルダーをお客様独自の値に置き換えてください。 (以前のプレビュー モジュールを使用していた場合、機能を有効にするためのパラメーターは **EnableAzureFilesAadIntegrationForSMB** です。)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
既存のストレージ アカウントでこの機能を有効にするには、次のコマンドを使用します。

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Azure CLI 2.0 から SMB 経由の Azure AD 認証を有効にするには、まず `storage-preview` 拡張機能をインストールします。

```cli-interactive
az extension add --name storage-preview
```
  
次に、新しいストレージ アカウントを作成してから [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) を呼び出し、`--file-aad` プロパティを **true** に設定します。 以下の例のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>ID にアクセス許可を割り当てる 

Azure AD の資格情報を使用して Azure Files のリソースにアクセスするには、ID (ユーザー、グループ、またはサービス プリンシパル) に、共有レベルで必要なアクセス許可が必要です。 このプロセスは、ファイル共有に対し特定のユーザーが持っているアクセス権の種類を指定する、Windows 共有のアクセス許可の指定に似ています。 このセクションのガイダンスでは、ID にファイル共有のための読み取り、書き込み、または削除のアクセス許可を割り当てる方法を示します。

ユーザーに共有レベルのアクセス許可を付与するための、2 つの Azure 組み込みロールが導入されました。ストレージ ファイル データ SMB 共有閲覧者と、ストレージ ファイル データ SMB 共有共同作成者です。 

- **ストレージ ファイル データ SMB 共有閲覧者**では、SMB 経由の Azure Storage ファイル共有での読み取りアクセスが許可されます
- **ストレージ ファイル データ SMB 共有共同作成者**では、SMB 経由の Azure Storage ファイル共有での読み取り、書き込み、削除アクセスが許可されます

> [!IMPORTANT]
> ID にロールを割り当てる機能など、ファイル共有を完全に管理制御するには、ストレージ アカウント キーを使用する必要があります。 Azure AD 資格情報を使用した管理制御はサポートされていません。 

Azure portal、PowerShell、または Azure CLI を使用して、共有レベルのアクセス許可を付与するために、組み込みのロールをユーザーの Azure AD ID に割り当てることができます。 

#### <a name="azure-portal"></a>Azure ポータル
[Azure portal](https://portal.azure.com) を使用して RBAC ロールを Azure AD ID に割り当てるには、次の手順に従います。

1. Azure portal で、ファイル共有に移動するか、または [Azure Files 内にファイル共有を作成](storage-how-to-create-file-share.md)します。
2. **[アクセス制御 (IAM)]** を選択します。
3. **[ロールの割り当てを追加する]** を選択します。
4. **[ロールの割り当ての追加]** ブレードで、 **[ロール]** ドロップダウン リストから適切な組み込みロール (ストレージ ファイル データ SMB 共有閲覧者、ストレージ ファイル データ SMB 共有共同作成者) を選択します。 **[アクセスの割り当て先]** は次の既定値のままとします: [Azure AD のユーザー、グループ、サービス プリンシパル]。それから対象の Azure AD ID を名前またはメール アドレスで選択します。 
5. 最後に **[保存]** を選択して、ロールの割り当て操作を完了します。

#### <a name="powershell"></a>PowerShell

次の PowerShell コマンドは、RBAC ロールをサインイン名に基づいて Azure AD ID に割り当てる方法を示しています。 PowerShell を使用した RBAC ロールの割り当ての詳細については、「[RBAC と Azure PowerShell を使用してアクセスを管理する](../../role-based-access-control/role-assignments-powershell.md)」を参照してください。

次のサンプル スクリプトを実行するときは、プレースホルダー値 (かっこを含む) を独自の値に置き換えることを忘れないでください。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
次の CLI 2.0 コマンドは、RBAC ロールをサインイン名に基づいて Azure AD ID に割り当てる方法を示しています。 Azure CLI を使用した RBAC ロールの割り当ての詳細については、「[RBAC と Azure CLI を使用してアクセスを管理する](../../role-based-access-control/role-assignments-cli.md)」を参照してください。 

次のサンプル スクリプトを実行するときは、プレースホルダー値 (かっこを含む) を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB 経由の NTFS アクセス許可を構成する 
RBAC に共有レベルのアクセス許可を割り当てたら、ルート、ディレクトリ、またはファイル レベルに適切な NTFS アクセス許可を割り当てる必要があります。 共有レベルのアクセス許可は、ユーザーが共有にアクセスできるかどうかを決定する高度なゲートキーパーであり、NTFS アクセス許可はユーザーがディレクトリまたはファイル レベルでどんな操作を実行できるかを規定し、より詳細なレベルで動作すると考えることができます。 

Azure Files では、NTFS の基本的なアクセス許可と詳細なアクセス許可で構成される完全なセットをサポートします。 共有をマウントし、Windows の [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) または [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) コマンドを実行することにより、Azure ファイル共有内のディレクトリとファイルに対する NTFS アクセス許可を表示し構成できます。 

> [!NOTE]
> プレビュー リリースでは、Windows ファイル エクスプローラーを使用したアクセス許可の表示のみがサポートされています。 アクセス許可の編集はまだサポートされていません。

スーパー ユーザー権限を持つ NTFS アクセス許可を構成するには、ドメインに参加している VM からのストレージ アカウント キーを使って共有をマウントする必要があります。 コマンド プロンプトから Azure ファイル共有をマウントし、それに応じて NTFS アクセス許可を構成するには、次のセクションの説明に従ってください。

ファイル共有のルート ディレクトリでは、次のアクセス許可セットがサポートされています。

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT authority \authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>コマンド プロンプトからファイル共有をマウントする

Windows の **net use** コマンドを使用して Azure ファイル共有をマウントします。 例中のプレースホルダーをお客様独自の値に置き換えてください。 ファイル共有のマウントの詳細については、「[Windows で Azure ファイル共有をマウントして共有を使用する](storage-how-to-use-files-windows.md)」を参照してください。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls を使用して NTFS アクセス許可を構成する
ルート ディレクトリを含む、ファイル共有下のすべてのディレクトリとファイル共有に完全なアクセス許可を付与するには、次の Windows コマンドを使用します。 例でブラケットに囲まれているプレースホルダーは、お客様独自の値に置き換えてください。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls を使用して NTFS アクセス許可を設定する方法や、サポートされるさまざまな種類のアクセス許可の詳細については、[コマンド ライン リファレンスの icacls ](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)に関する記事を参照してください。

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>ドメインに参加している VM からファイル共有をマウントする 

上記の手順が正常に完了したことを確認する準備が整いましたので、Azure AD 資格情報を使用して、ドメインに参加している VM から Azure のファイル共有にアクセスします。 まず、次の図のように、アクセス許可を付与した Azure AD の ID を使用して VM にサインインします。

![ユーザー認証のための Azure AD サインイン画面を示すスクリーン ショット](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

次に、以下のコマンドを使用して Azure ファイル共有をマウントします。 プレースホルダーをお客様独自の値に置き換えてください。 既に認証されているため、ストレージ アカウント キーまたは Azure AD のユーザー名やパスワードを入力する必要はありません。 SMB を使用する Azure AD では、Azure AD の資格情報を使用したシングル サインオン エクスペリエンスがサポートされています。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

これで、SMB を使用した Azure AD 認証を有効にし、ファイル共有へのアクセスを提供するカスタム ロールが Azure AD の ID に割り当てられました。 追加のユーザーにファイル共有へのアクセス許可を付与するには、手順 2 と 3 で説明されている手順を実行します。

## <a name="next-steps"></a>次の手順

Azure Files や、SMB 経由で Azure AD を使用する方法の詳細については、これらのリソースを参照してください。

- [Azure Files の概要](storage-files-introduction.md)
- [SMB を使用して Azure Files への Azure Active Directory 認証を有効にする方法の概要 (プレビュー)](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
