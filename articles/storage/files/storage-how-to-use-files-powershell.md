---
title: "PowerShell を使用して Azure File Storage を管理する方法 | Microsoft Docs"
description: "PowerShell を使用して Azure File Storage を管理する方法を説明します。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ce62d4423ce711a6902aed7b8174ff4e827f6083
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="how-to-use-powershell-to-manage-azure-file-storage"></a>PowerShell を使用して Azure File Storage を管理する方法
Azure PowerShell を使用して、ファイル共有を作成および管理することができます。

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Azure Storage 用の PowerShell コマンドレットのインストール
PowerShell の使用を準備するために、Azure PowerShell コマンドレットをダウンロードしてインストールします。 インストール先とインストール方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

> [!NOTE]
> 最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。
> 
> 

**[スタート]** をクリックし、「**Windows PowerShell**」と入力して、Azure PowerShell ウィンドウを開きます。 PowerShell ウィンドウに自動的に Azure Powershell モジュールが読み込まれます。

## <a name="create-a-context-for-your-storage-account-and-key"></a>ストレージ アカウントおよびキーのコンテキストの作成
ストレージ アカウント コンテキストを作成します。 コンテキストによってストレージ アカウント名とアカウント キーがカプセル化されます。 [Azure Portal](https://portal.azure.com) からアカウント キーをコピーする手順については、「[ストレージ アクセス キーの表示とコピー](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys)」を参照してください。

次の例の `storage-account-name` と `storage-account-key` は、実際のストレージ アカウント名とキーに置き換えてください。

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>新しいファイル共有の作成
`logs` という名前の新しい共有を作成します。

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

これで、File Storage にファイル共有が作成されました。 次に、ディレクトリとファイルを追加します。

> [!IMPORTANT]
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>ファイル共有でのディレクトリの作成
共有内にディレクトリを作成します。 次の例では、ディレクトリに `CustomLogs` という名前を付けています。

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>ディレクトリへのローカル ファイルのアップロード
次に、ディレクトリにローカル ファイルをアップロードします。 次の例では、`C:\temp\Log1.txt` からファイルをアップロードします。 ファイル パスを編集して、ローカル マシン上の有効なファイルを指定してください。

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>ディレクトリ内のファイルの一覧表示
ディレクトリ内のすべてのファイルを一覧表示して、ファイルを確認できます。 このコマンドは、CustomLogs ディレクトリ内にあるファイルとサブディレクトリ (ある場合) を返します。

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile は、ディレクトリ オブジェクトが渡されるファイルとディレクトリの一覧を返します。 "Get-AzureStorageFile -Share $s" は、ルート ディレクトリ内のファイルとディレクトリの一覧を返します。 サブディレクトリ内のファイルの一覧を取得するには、サブディレクトリを Get-AzureStorageFile に渡す必要があります。 コマンドの処理手順として、コマンドの最初の部分からパイプまでの処理で、サブディレクトリ CustomLogs のディレクトリ インスタンスが返されます。 次に、そのインスタンスが Get-AzureStorageFile に渡され、CustomLogs 内のファイルとディレクトリが返されます。

## <a name="copy-files"></a>ファイルのコピー
Azure PowerShell のバージョン 0.9.7 以降では、ファイルを別のファイルにコピーしたり、ファイルを BLOB にコピーしたり、BLOB をファイルにコピーしたりすることができます。 次に、PowerShell コマンドレットを使用してこれらのコピー操作を実行する方法を示します。

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>次のステップ
Azure File Storage の詳細については、次のリンクを参照してください。

* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux 上でのトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)    
