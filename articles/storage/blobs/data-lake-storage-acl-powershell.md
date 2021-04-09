---
title: Azure Data Lake Storage Gen2 で PowerShell を使用して ACL を管理する
description: PowerShell のコマンドレットを使用して、階層型名前空間 (HNS) が有効なストレージ アカウントのアクセス制御リスト (ACL) を管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104702543"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で PowerShell を使用して ACL を管理する

この記事では、PowerShell を使用して、ディレクトリとファイルのアクセス制御リストを取得、設定、更新する方法について説明します。 

ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 しかし、親ディレクトリの既存の子項目に対して ACL を再帰的に追加、更新、および削除することもできます。それぞれの子項目に対してこれらの変更を個別に行う必要はありません。 

[参照](/powershell/module/Az.Storage/) | [再帰 ACL のサンプル](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) | [フィードバックの提供](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- Azure CLI バージョン `2.6.0` 以上。

- 次のセキュリティのアクセス許可のいずれか。

  - ターゲット コンテナー、親リソース グループ、またはサブスクリプションのいずれかのスコープで[ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)ロールが割り当てられた、プロビジョニングされた Azure Active Directory (AD) [セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)。  

  - ACL 設定を適用する予定のターゲット コンテナーまたはディレクトリの所有ユーザー。 ACL を再帰的に設定する場合、これには、ターゲット コンテナーまたはディレクトリ内のすべての子項目が含まれます。
  
  - ストレージ アカウント キー。

## <a name="install-the-powershell-module"></a>PowerShell モジュールをインストールする

1. 次のコマンドを使用して、インストールされている PowerShell のバージョンが `5.1` 以降であることを確認します。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   お使いの PowerShell のバージョンをアップグレードするには、「[既存の Windows PowerShell をアップグレードする](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)」を参照してください。

2. **Az.Storage** モジュールをインストールします。

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照してください。

## <a name="connect-to-the-account"></a>アカウントに接続する

コマンドでストレージ アカウントに対する承認を取得する方法を選択します。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>オプション 1: Azure Active Directory (AD) を使用して承認を取得する

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してアクセスを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](./data-lake-storage-access-control-model.md)」を参照してください。

この方法を使用すると、ご利用のユーザー アカウントに、適切な Azure ロールベースのアクセス制御 (Azure RBAC) の割り当てと ACL のアクセス許可がシステムによって確実に付与されます。

1. Windows PowerShell コマンド ウィンドウを開き、`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、ディレクトリを作成して管理するストレージ アカウントのサブスクリプションに設定します。 この例では、`<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. ストレージ アカウント コンテキストを取得します。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>オプション 2:ストレージ アカウント キーを使用して承認を取得する

この方法を使用する場合、Azure RBAC アクセス許可も、ACL アクセス許可もシステムによってチェックされません。 アカウント キーを使用して、ストレージ アカウント コンテキストを取得します。

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>ACL を取得する

`Get-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリまたはファイルの ACL を取得します。

この例では、**コンテナー** のルート ディレクトリの ACL を取得し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

この例では、**ディレクトリ** の ACL を取得し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

この例では、**ファイル** の ACL を取得し、その ACL をコンソールに出力します。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

次の画像は、ディレクトリの ACL を取得した後の出力を示しています。

![ディレクトリの ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可があります。 所有グループには、読み取りと実行のアクセス許可のみがあります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

## <a name="set-acls"></a>ACL を設定する

ACL を "*設定する*" 場合は、ACL 全体 (そのすべてのエントリを含む) を **置換** します。 セキュリティ プリンシパルのアクセス許可レベルの変更または ACL への新しいセキュリティ プリンシパルの追加を、他の既存のエントリに影響を与えることなく行いたい場合は、代わりに ACL を "*更新*" する必要があります。 ACL を置換するのでなく更新するには、この記事の「[ACL を更新する](#update-acls)」セクションを参照してください。  

ACL を "*設定*" する場合は、所有ユーザーのエントリ、所有グループのエントリ、および他のすべてのユーザーのエントリを追加する必要があります。 所有ユーザー、所有グループ、およびその他のすべてのユーザーの詳細については、「[ユーザーと ID](data-lake-storage-access-control.md#users-and-identities)」を参照してください。

ここでは、次の操作方法について説明します。

- ACL を設定する
- ACL を再帰的に設定する

### <a name="set-an-acl"></a>ACL を設定する

`set-AzDataLakeGen2ItemAclObject` コマンドレットを使用して、所有ユーザー、所有グループ、またはその他のユーザーの ACL を作成します。 その後、`Update-AzDataLakeGen2Item` コマンドレットを使用して ACL をコミットします。

この例では、所有ユーザー、所有グループ、またはその他のユーザーの **コンテナー** のルート ディレクトリに ACL を設定し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

この例では、所有ユーザー、所有グループ、または他のユーザーの **ディレクトリ** に ACL を設定し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> **既定の** ACL エントリを設定する場合は、**Set-AzDataLakeGen2ItemAclObject** コマンドを実行するときに **-DefaultScope** パラメーターを使用します。 (例: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`)。 

この例では、所有ユーザー、所有グループ、または他のユーザーの **ファイル** に ACL を設定し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> 特定のグループまたはユーザーの ACL を設定するには、それぞれのオブジェクト ID を使用します。 たとえば、`group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` または `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` です。

次の画像は、ファイルの ACL を設定した後の出力を示しています。

![ファイルの ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

この例では、所有ユーザーと所有グループには、読み取りと書き込みのアクセス許可のみがあります。 他のすべてのユーザーには、書き込みと実行のアクセス許可があります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="set-acls-recursively"></a>ACL を再帰的に設定する

**Set-AzDataLakeGen2AclRecursive** コマンドレットを使用して、ACL を再帰的に設定します。

この例では、`my-parent-directory` という名前のディレクトリの ACL を設定します。 これらのエントリでは、所有ユーザーには読み取り、書き込み、実行のアクセス許可を付与し、所有グループには読み取りと実行のアクセス許可のみを付与し、他のすべてのユーザーにはアクセスを許可しません。 この例の最後の ACL エントリでは、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" というオブジェクト ID を持つ特定のユーザーに、読み取りと実行のアクセス許可を付与しています。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> **既定の** ACL エントリを設定する場合は、**Set-AzDataLakeGen2ItemAclObject** コマンドを実行するときに **-DefaultScope** パラメーターを使用します。 (例: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`)。

バッチ サイズを指定してバッチ内の ACL を再帰的に設定する例については、[Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) のリファレンス記事をご覧ください。

## <a name="update-acls"></a>ACL を更新する

ACL を "*更新する*" 場合は、ACL を置換するのでなく ACL を変更します。 たとえば、ACL にリストされている他のセキュリティ プリンシパルに影響を与えることなく、新しいセキュリティ プリンシパルを ACL に追加することができます。  ACL を更新するのでなく、置換する場合は、この記事の「[ACL を設定する](#set-acls)」セクションを参照してください。

ACL を更新するには、更新したい ACL エントリを含む新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の更新操作で使用します。 既存の ACL は取得せずに、更新する ACL エントリを指定するだけです。

ここでは、次の操作方法について説明します。

- ACL を更新する
- ACL を再帰的に更新する

### <a name="update-an-acl"></a>ACL を更新する

まず、ACL を取得します。 次に、`set-AzDataLakeGen2ItemAclObject` コマンドレットを使用して、ACL エントリを追加または更新します。 `Update-AzDataLakeGen2Item` コマンドレットを使用して ACL をコミットします。

この例では、ユーザーの **ディレクトリ** で ACL を作成または更新します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> **既定の** ACL エントリを更新する場合は、**Set-AzDataLakeGen2ItemAclObject** コマンドを実行するときに **-DefaultScope** パラメーターを使用します。 (例: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`)。

### <a name="update-acls-recursively"></a>ACL を再帰的に更新する

**Update-AzDataLakeGen2AclRecursive** コマンドレットを使用して、ACL を再帰的に更新します。

この例では、書き込みアクセス許可を持つ ACL エントリを更新します。

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> 特定のグループまたはユーザーの ACL を設定するには、それぞれのオブジェクト ID を使用します。 たとえば、`group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` または `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` です。

バッチ サイズを指定してバッチ内の ACL を再帰的に更新する例については、[Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) のリファレンス記事をご覧ください。

## <a name="remove-acl-entries"></a>ACL エントリを削除する

ここでは、次の操作方法について説明します。

- ACL エントリを削除する
- ACL エントリを再帰的に削除する

### <a name="remove-an-acl-entry"></a>ACL エントリを削除する

この例では、既存の ACL からエントリを削除します。

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>ACL エントリを再帰的に削除する

1 つ以上の ACL エントリを再帰的に削除できます。 ACL エントリを削除するには、削除する ACL エントリ用に新しい ACL オブジェクトを作成してから、そのオブジェクトを ACL の削除操作で使用します。 既存の ACL は取得せずに、削除する ACL エントリを指定するだけです。

**Remove-AzDataLakeGen2AclRecursive** コマンドレットを使用して、ACL エントリを再帰的に削除します。

この例では、コンテナーのルート ディレクトリから ACL エントリを削除します。  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> **既定の** ACL エントリを削除する場合は、**Set-AzDataLakeGen2ItemAclObject** コマンドを実行するときに **-DefaultScope** パラメーターを使用します。 (例: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`)。

バッチ サイズを指定してバッチ内の ACL を再帰的に削除する例については、[Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) のリファレンス記事をご覧ください。

## <a name="recover-from-failures"></a>エラーからの復旧

ACL を再帰的に変更するときに、実行時またはアクセス許可のエラーが発生する場合があります。 

実行時エラーが発生した場合は、プロセスを最初から再開します。 アクセス許可エラーは、セキュリティ プリンシパルに、変更対象のディレクトリ階層内にあるディレクトリまたはファイルの ACL を変更するための十分なアクセス許可がない場合に発生する可能性があります。 アクセス許可の問題を解決してから、継続トークンを使用してエラーの発生時点からプロセスを再開するか、最初からプロセスを再開するかを選択します。 最初から再開する場合は、継続トークンを使用する必要はありません。 ACL エントリは、悪影響を及ぼすことなく再適用できます。

この例では、結果を変数に返し、失敗したエントリを書式設定されたテーブルにパイプします。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

テーブルの出力に基づいて、アクセス許可のエラーを修正し、継続トークンを使用して実行を再開できます。

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

バッチ サイズを指定してバッチ内の ACL を再帰的に設定する例については、[Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) のリファレンス記事をご覧ください。

アクセス許可エラーによって中断されずにプロセスを完了したい場合は、それを指定できます。

この例では、`ContinueOnFailure` パラメーターを使用して、操作中にアクセス許可エラーが検出された場合でも実行が継続されるようにします。 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

バッチ サイズを指定してバッチ内の ACL を再帰的に設定する例については、[Set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) のリファレンス記事をご覧ください。

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>関連項目

- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Storage PowerShell コマンドレット](/powershell/module/az.storage)
- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control.md)」
- [AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)
