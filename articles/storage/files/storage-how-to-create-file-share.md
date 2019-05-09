---
title: Azure ファイル共有を作成する方法 | Microsoft Docs
description: Azure ポータル、PowerShell、Azure CLI を使用して Azure Files に Azure ファイル共有を作成する方法。
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 1620683f4bc02d607afd8e0371e8d4a5461612b3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772332"
---
# <a name="create-a-file-share-in-azure-files"></a>Azure Files にファイル共有を作成する
Azure のファイル共有は、 [Azure portal](https://portal.azure.com/)、Azure Storage の PowerShell コマンドレット、Azure Storage のクライアント ライブラリ、または Azure Storage の REST API を使用して作成することができます。 このチュートリアルでは、次の事項について説明します。
* Azure portal を使用して Azure ファイル共有を作成する方法
* [PowerShell を使用して Azure ファイル共有を作成する方法](#create-file-share-through-powershell)
* [CLI を使用して Azure ファイル共有を作成する方法](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>前提条件
Azure ファイル共有を作成するには、既存のストレージ アカウントを使用するか、[新しい Azure ストレージ アカウントを作成する](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)ことができます。 PowerShell で Azure ファイル共有を作成するには、アカウント キーとストレージ アカウントの名前が必要になります。 PowerShell または CLI を使用する場合は、ストレージ アカウント キーが必要になります。

## <a name="create-a-file-share-through-the-azure-portal"></a>Azure portal を使用したファイル共有の作成
1. **Azure portal の [ストレージ アカウント] ブレードに移動します**。    
    ![[ストレージ アカウント] ブレード](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **ファイル共有の追加ボタンをクリックします**。    
    ![ファイル共有の追加ボタンをクリックする](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **名前とクォータを指定します。クォータの現在の最大値は 5 TiB です**。    
    ![新しいファイル共有の名前と必要なクォータを指定する](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **新しいファイル共有を表示します**。![新しいファイル共有を表示する](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **ファイルをアップロードします**。![ファイルをアップロードする](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **ファイル共有を参照し、ディレクトリとファイルを管理します**。![ファイル共有を参照する](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>PowerShell を使用したファイル共有の作成

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell の使用を準備するために、Azure PowerShell コマンドレットをダウンロードしてインストールします。 インストール先とインストール方法については、 [Azure PowerShell のインストールおよび構成方法](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) に関するページを参照してください。

> [!Note]  
> 最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

1. **ストレージ アカウントとキーのコンテキストを作成します**。コンテキストは、ストレージ アカウント名とアカウント キーをカプセル化します。  [Azure portal](https://portal.azure.com/) からアカウント キーをコピーする手順については、 [ストレージ アカウントのアクセス キー](../common/storage-account-manage.md#access-keys)に関するページを参照してください。

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **新しいファイル共有を作成します**。    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有とファイルの名前付けの詳細については、「 [Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

## <a name="create-file-share-through-command-line-interface-cli"></a>コマンド ライン インターフェイス (CLI) を使用したファイル共有の作成
1. **コマンド ライン インターフェイス (CLI) の使用の準備をするには、Azure CLI をダウンロードしてインストールします。**  
    「 [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」と「[Azure CLI の概要](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)」を参照してください。

2. **共有の作成先となるストレージ アカウントへの接続文字列を作成します。**  
    次の例の  ```<storage-account>```  と  ```<resource_group>```  は、実際のストレージ アカウント名とリソース グループに置き換えてください。

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **ファイル共有を作成します**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>次の手順
* [ファイル共有の接続とマウント - Windows](storage-how-to-use-files-windows.md)
* [ファイル共有の接続とマウント - Linux](../storage-how-to-use-files-linux.md)
* [ファイル共有の接続とマウント - macOS](storage-how-to-use-files-mac.md)

Azure Files の詳細については、次のリンクをご覧ください。

* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux 上でのトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)   
