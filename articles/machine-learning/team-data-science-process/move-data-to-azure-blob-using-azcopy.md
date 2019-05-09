---
title: AzCopy を使用して BLOB ストレージのデータをコピーする - Team Data Science Process
description: AzCopy を使用して Azure Blob Storage 間でデータをコピーする
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c0951eb6ad3b7651da97e1a49c5edf5ab55a199
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209576"
---
# <a name="copy-data-to-and-from-azure-blob-storage-using-azcopy"></a>AzCopy を使用して Azure Blob Storage 間でデータをコピーする
AzCopy は、Microsoft Azure の BLOB、ファイル、テーブル ストレージとの間でデータをアップロード、ダウンロード、コピーすることを目的としたコマンド ライン ユーティリティです。

AzCopy のインストール手順と、Azure プラットフォームでの使い方に関する情報については、「 [AzCopy コマンド ライン ユーティリティの概要](../../storage/common/storage-use-azcopy.md)」を参照してください。

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure のデータ サイエンス用仮想マシン](virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、AzCopy は既に VM にインストールされています。
> 
> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「 [Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」を参照してください。

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


## <a name="copy-blobs-between-azure-containers"></a>Azure コンテナー間で BLOB をコピーする
Azure コンテナー 間で BLOB をコピーするには、次のコマンドを使用します。

    # Copying blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be copied. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>AzCopy を使用するためのヒント
> [!TIP]
> 1. ファイルを **アップロード** する場合、 */S* を指定すると、ファイルが再帰的にアップロードされます。 このパラメーターを指定しなかった場合、サブディレクトリ内のファイルはアップロードされません。  
> 2. ファイルを **ダウンロード** する場合、 */S* を指定すると、指定したディレクトリとそのサブディレクトリ内のすべてのファイル、または特定のディレクトリとそのサブディレクトリ内の指定したパターンと一致するすべてのファイルがダウンロードされるまで、コンテナーを再帰的に検索します。  
> 3. **/Source** パラメーターを使用して、ダウンロードする *特定の BLOB ファイル* を指定することはできません。 特定のファイルをダウンロードするには、 */Pattern* パラメーターを使用してダウンロードする BLOB ファイル名を指定します。 **/S** パラメーターは、AzCopy にファイル名のパターンを再帰的に検索させるために使用できます。 パターン パラメーターを指定しなかった場合、そのディレクトリ内のすべてのファイルがダウンロードされます。
> 
> 

