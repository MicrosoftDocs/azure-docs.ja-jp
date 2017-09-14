---
title: "Azure ファイル共有を作成する方法 | Microsoft Docs"
description: "Azure Portal、PowerShell、Azure CLI を使用して Azure File Storage に Azure ファイル共有を作成する方法。"
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
ms.openlocfilehash: b81701e2544ace092f007e5d98b3141e1f7da724
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>Azure File Storage にファイル共有を作成する
Azure ファイル共有は、[Azure Portal](https://portal.azure.com/)、Azure Storage PowerShell コマンドレット、Azure Storage クライアント ライブラリ、または Azure Storage REST API を使用して作成することができます。このチュートリアルでは、以下のことを学習します。
* [Azure Portal を使用して Azure ファイル共有を作成する方法](#Create file share through the Portal)
* [PowerShell を使用して Azure ファイル共有を作成する方法](#Create file share using PowerShell)
* [CLI を使用して Azure ファイル共有を作成する方法](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>前提条件
Azure ファイル共有を作成するには、既存のストレージ アカウントを使用するか、[新しい Azure ストレージ アカウントを作成する](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)ことができます。 PowerShell で Azure ファイル共有を作成するには、アカウント キーとストレージ アカウントの名前が必要になります。 PowerShell または CLI を使用する場合は、ストレージ アカウント キーが必要になります。

## <a name="create-file-share-through-the-portal"></a>ポータルを使用したファイル共有の作成
1. **Azure Portal の [ストレージ アカウント] ブレードに移動します**。    
    ![[ストレージ アカウント] ブレード](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **ファイル共有の追加ボタンをクリックします**。    
    ![ファイル共有の追加ボタンをクリックする](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **名前とクォータを指定します。クォータは現在、最大 5 TB にすることができます**。    
    ![新しいファイル共有の名前と必要なクォータを指定する](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **新しいファイル共有を表示します**。  ![新しいファイル共有を表示する](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **ファイルをアップロードします**。  ![ファイルをアップロードする](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **ファイル共有を参照し、ディレクトリとファイルを管理します**。  ![ファイル共有を参照する](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>PowerShell を使用したファイル共有の作成
PowerShell の使用を準備するために、Azure PowerShell コマンドレットをダウンロードしてインストールします。 インストール先とインストール方法については、「 [Azure PowerShell のインストールおよび構成方法](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) 」を参照してください。

> [!Note]  
> 最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

1. **ストレージ アカウントとキーのコンテキストを作成します**。コンテキストは、ストレージ アカウント名とアカウント キーをカプセル化します。 [Azure Portal](https://portal.azure.com/) からアカウント キーをコピーする手順については、「[ストレージ アクセス キーの表示とコピー](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys)」を参照してください。

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **新しいファイル共有を作成します**。    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

## <a name="create-file-share-through-command-line-interface-cli"></a>コマンド ライン インターフェイス (CLI) を使用したファイル共有の作成
1. **コマンド ライン インターフェイス (CLI) の使用の準備をするには、Azure CLI をダウンロードしてインストールします。**  
    [Azure CLI 2.0 のインストール](/cli/azure/install-az-cli2.md)と [Azure CLI 2.0 の使用開始](/cli/azure/get-started-with-azure-cli.md)に関する各ページを参照してください。

2. **共有の作成先となるストレージ アカウントへの接続文字列を作成します。**  
    次の例の ```<storage-account>``` と ```<resource_group>``` は、実際のストレージ アカウント名とリソース グループに置き換えてください。

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

## <a name="next-steps"></a>次のステップ
* [ファイル共有の接続とマウント - Windows](storage-how-to-use-files-windows.md)
* [ファイル共有の接続とマウント - Linux](../storage-how-to-use-files-linux.md)
* [ファイル共有の接続とマウント - macOS](storage-how-to-use-files-mac.md)

Azure File Storage の詳細については、次のリンクを参照してください。

* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux 上でのトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)   
