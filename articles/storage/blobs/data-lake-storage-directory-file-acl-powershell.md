---
title: 'PowerShell を使用してデータを管理する: Azure Data Lake Storage Gen2'
description: PowerShell コマンドレット を使用して、階層型名前空間が有効になっているストレージ アカウントでディレクトリとファイルを管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 552d53ff0257105ff61397e281504c5270512319
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573865"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>PowerShell を使用して Azure Data Lake Storage Gen2 でディレクトリとファイルを管理する

この記事では、階層型名前空間が有効になっているストレージ アカウントで、PowerShell を使用してディレクトリとファイルを作成および管理する方法を示します。

ディレクトリとファイルのアクセス制御リスト (ACL) を取得、設定、および更新する方法については、「[Azure Data Lake Storage Gen2 で PowerShell を使用して ACL を管理する](data-lake-storage-acl-powershell.md)」を参照してください。

[リファレンス](/powershell/module/Az.Storage/) | [Gen1 から Gen2 へのマッピング](#gen1-gen2-map) | [フィードバックの送信](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間が有効になっているストレージ アカウント。 作成するには、[こちら](create-data-lake-storage-account.md)の手順に従います。

- .NET Framework は 4.7.2 以降がインストールされていること。 「[.NET Framework のダウンロード](https://dotnet.microsoft.com/download/dotnet-framework)」を参照してください。

- PowerShell バージョン `5.1` 以降。

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>オプション 1: Azure Active Directory (Azure AD) を使用して認可を取得する

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

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーは、ご使用のファイルのファイル システムとして機能します。 `New-AzStorageContainer` コマンドレットを使用して、ファイル システムを作成できます。 

この例では、`my-file-system` という名前のコンテナーを作成します。

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>ディレクトリを作成する

`New-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリ参照を作成します。 

この例では、`my-directory` という名前のディレクトリをコンテナーに追加します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

この例では、同じディレクトリを追加しますが、アクセス許可、umask、プロパティ値、メタデータ値も設定します。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>ディレクトリのプロパティを表示する

この例では、`Get-AzDataLakeGen2Item` コマンドレットを使用してディレクトリを取得し、次にプロパティ値をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> コンテナーのルート ディレクトリを取得するには、`-Path` パラメーターを省略します。

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

`Move-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリの名前を変更するか、ディレクトリを移動します。

この例では、ディレクトリの名前を `my-directory` から `my-new-directory` に変更します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> プロンプトを表示せずに上書きする場合は、`-Force` パラメーターを使用します。

この例では、`my-directory` という名前のディレクトリを `my-directory-2` の (`my-subdirectory` という名前の) サブディレクトリに移動します。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

`Remove-AzDataLakeGen2Item` コマンドレットを使用してディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

`-Force` パラメーターを使用すると、プロンプトを表示せずにファイルを削除できます。

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

`Get-AzDataLakeGen2ItemContent` コマンドレットを使用して、ディレクトリからファイルをダウンロードします。

この例では、`my-directory` という名前のディレクトリから `upload.txt` という名前のファイルをダウンロードします。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

`Get-AzDataLakeGen2ChildItem` コマンドレットを使用して、ディレクトリの内容を一覧表示します。 省略可能なパラメーター `-OutputUserPrincipalName` を使用すると、ユーザーの名前 (オブジェクト ID ではなく) を取得できます。

この例では、`my-directory` という名前のディレクトリの内容を一覧表示します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

次の例では、ディレクトリ内の各項目の `ACL`、`Permissions`、`Group`、および `Owner` の各プロパティを一覧表示します。 `ACL` プロパティの値を取得するには、`-FetchProperty` パラメーターが必要です。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> コンテナーのルート ディレクトリの内容を一覧表示するには、`-Path` パラメーターを省略します。

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

`New-AzDataLakeGen2Item` コマンドレットを使用して、ファイルをディレクトリにアップロードします。

この例では、`upload.txt` という名前のファイルを `my-directory` という名前のディレクトリにアップロードします。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

この例では、同じファイルをアップロードしますが、その後に対象ファイルのアクセス許可、umask、プロパティ値、メタデータ値を設定します。 また、この例では、これらの値をコンソールに出力します。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> コンテナーのルート ディレクトリにファイルをアップロードするには、`-Path` パラメーターを省略します。

## <a name="show-file-properties"></a>ファイルのプロパティを表示する

この例では、`Get-AzDataLakeGen2Item` コマンドレットを使用してファイルを取得し、次にプロパティ値をコンソールに出力します。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>ファイルを削除する

`Remove-AzDataLakeGen2Item` コマンドレットを使用してファイルを削除します。

この例では、`upload.txt` という名前のファイルを削除します。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

`-Force` パラメーターを使用すると、プロンプトを表示せずにファイルを削除できます。

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 から Gen2 へのマッピング

次の表は、Data Lake Storage Gen1 で使用するコマンドレットが、Data Lake Storage Gen2 のコマンドレットにどのように対応するかを示しています。

|Data Lake Storage Gen1 のコマンドレット| Data Lake Storage Gen2 のコマンドレット| Notes |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|既定では、Get-AzDataLakeGen2ChildItem コマンドレットは、最初のレベルの子項目のみを一覧表示します。 -Recurse パラメーターは、子項目を再帰的に一覧表示します。 |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item コマンドレットの出力項目には、次のプロパティが含まれます。Acl、Owner、Group、Permission です。|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent コマンドレットは、ファイルのコンテンツをローカル ファイルにダウンロードします。|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|このコマンドレットは、ローカル ファイルから新しいファイルのコンテンツをアップロードします。|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Update-AzDataLakeGen2Item コマンドレットは、再帰的ではなく、1 つの項目のみを更新します。 再帰的に更新する場合は、Get-AzDataLakeStoreChildItem コマンドレットを使用して項目を一覧表示して、Update-AzDataLakeGen2Item コマンドレットに渡します。|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|項目が存在しない場合、Get-AzDataLakeGen2Item コマンドレットではエラーを報告します。|

## <a name="see-also"></a>関連項目

- [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Storage PowerShell コマンドレット](/powershell/module/az.storage)
