---
title: "AzCopy を使用して Azure Blob Storage 間でデータを移動する | Microsoft Docs"
description: "AzCopy を使用して Azure BLOB ストレージ間でデータを移動する"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 9b32c0d9c3bc19a187873eebd6ab21036ee06db8
ms.openlocfilehash: 1acc98f9688ced2e1cf177a881b28ce98c0bff9e
ms.lasthandoff: 12/07/2016


---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>AzCopy を使用して Azure Blob Storage 間でデータを移動する
AzCopy は、Microsoft Azure の BLOB、ファイル、テーブル ストレージとの間でデータをアップロード、ダウンロード、コピーすることを目的としたコマンド ライン ユーティリティです。

AzCopy のインストール手順と、Azure プラットフォームでの使い方に関する情報については、「 [AzCopy コマンド ライン ユーティリティの概要](../storage/storage-use-azcopy.md)」を参照してください。

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、AzCopy は既に VM にインストールされています。
> 
> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「 [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

## <a name="run-azcopy-commands"></a>AzCopy コマンドを実行する
AzCopy コマンドを実行するには、コマンド ウィンドウを開き、AzCopy.exe 実行可能ファイルが格納されている、コンピューターの AzCopy インストール ディレクトリに移動します。 

AzCopy コマンドの基本構文は次のとおりです。

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> AzCopy のインストール先をシステム パスに追加し、任意のディレクトリからコマンドを実行できます。 既定では、AzCopy は *%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy* または *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy* にインストールされています。
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>ファイルを Azure BLOB にアップロードする
ファイルをアップロードするには、次のコマンドを使用します。

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Azure BLOB からファイルをダウンロードする
Azure BLOB からファイルをダウンロードするには、次のコマンドを使用します。

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Azure コンテナー間で BLOB を転送する
Azure コンテナー 間で BLOB を転送するには、次のコマンドを使用します。

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>AzCopy を使用するためのヒント
> [!TIP]
> 1. ファイルを **アップロード** する場合、 */S* を指定すると、ファイルが再帰的にアップロードされます。 このパラメーターを指定しなかった場合、サブディレクトリ内のファイルはアップロードされません。  
> 2. ファイルを **ダウンロード** する場合、 */S* を指定すると、指定したディレクトリとそのサブディレクトリ内のすべてのファイル、または特定のディレクトリとそのサブディレクトリ内の指定したパターンと一致するすべてのファイルがダウンロードされるまで、コンテナーを再帰的に検索します。  
> 3. **/Source** パラメーターを使用して、ダウンロードする *特定の BLOB ファイル* を指定することはできません。 特定のファイルをダウンロードするには、 */Pattern* パラメーターを使用してダウンロードする BLOB ファイル名を指定します。 **/S** パラメーターは、AzCopy にファイル名のパターンを再帰的に検索させるために使用できます。 パターン パラメーターを指定しなかった場合、そのディレクトリ内のすべてのファイルがダウンロードされます。
> 
> 


