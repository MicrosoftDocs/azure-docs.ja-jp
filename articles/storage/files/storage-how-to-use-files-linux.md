---
title: "Linux で Azure File Storage を使用する | Microsoft Docs"
description: "Linux で SMB 経由で Azure File 共有をマウントする方法。"
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/8/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: d8987082c559a374b8d19fd69e20cf5e81cb25ef
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="use-azure-file-storage-with-linux"></a>Linux で Azure File Storage を使用する
[Azure File Storage](../storage-dotnet-how-to-use-files.md) は、Microsoft の使いやすいクラウド ファイル システムです。 [Samba プロジェクト](https://www.samba.org/) の[cifs-utils パッケージ](https://wiki.samba.org/index.php/LinuxCIFS_utils)を使用して、Linux ディストリビューションに Azure File 共有をマウントできます。 この記事では、Azure File 共有を `mount` コマンドを使用してオンデマンドでマウントするか、`/etc/fstab` にエントリを作成することで起動時にマウントするという 2 つの方法について説明します。

> [!NOTE]  
> Azure File 共有がホストされている Azure リージョン以外の場所 (オンプレミスや他の Azure リージョンなど) に Azure File 共有をマウントするには、OS が SMB 3.0 の暗号化機能をサポートしている必要があります。 Linux 用の SMB 3.0 の暗号化機能は 4.11 カーネルで導入されました。 この機能によって、オンプレミスから、または異なる Azure リージョンから Azure ファイル共有をマウントできます。 この機能は、公開時に 16.04 以上の Ubuntu に移植されました。


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Linux で cifs-utils パッケージを使用してAzure File 共有をマウントするための前提条件
* **cifs-utils パッケージがインストールされている Linux ディストリビューションを選択する**: Microsoft では、Azure イメージ ギャラリーの次の Linux ディストリビューションをお勧めしています。

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**cifs-utils パッケージがインストールされている**: cifs-utils は、選択した Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 

    **Ubuntu** と **Debian ベースの**ディストリビューションでは、`apt-get` パッケージ マネージャーを使用します。

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    **RHEL** と **CentOS** では、`yum` パッケージ マネージャーを使用します。

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    **openSUSE** では、`zypper` パッケージ マネージャーを使用します。

    ```
    sudo zypper install samba*
    ```

    他のディストリビューションでは、適切なパッケージ マネージャーを使用するか、[ソースからコンパイル](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)します。

* **マウントされた共有のディレクトリ/ファイルのアクセス権限を決定する**: この後の例では、0777 を使用して、すべてのユーザーに読み取り、書き込み、および実行権限を与えています。 必要に応じて、他の [chmod 権限](https://en.wikipedia.org/wiki/Chmod)に置き換えることができます。 

* **ストレージ アカウント名**: Azure File 共有をマウントするには、ストレージ アカウントの名前が必要です。

* **ストレージ アカウント キー**: Azure File 共有をマウントするには、プライマリ (またはセカンダリ) ストレージ キーが必要です。 現時点では、SAS キーは、マウントではサポートされていません。

* **ポート 445 が開いていることを確認する**: SMB は、TCP ポート 445 経由で通信します。ファイアウォールによってクライアント コンピューターの TCP ポート 445 がブロックされないことを確認してください。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Azure Files 共有を `mount` を使用してオンデマンドでマウントする
1. **[使用する Linux ディストリビューション用の cifs-utils パッケージをインストールします](#install-cifs-utils)**。

2. **マウント ポイント用のフォルダーを作成する**: これは、ファイル システムで任意の場所で実行することができます。

    ```
    mkdir mymountpoint
    ```

3. **mount コマンドを使用して Azure File共有をマウントします**。`<storage-account-name>``<share-name>`、および `<storage-account-key>` を適切な情報に置き換えることを忘れないでください。

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Azure File 共有の使用を完了したら、`sudo umount ./mymountpoint` を使用して共有を解除できます。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Azure File 共有の永続的なマウント ポイントを作成する`/etc/fstab`
1. **[使用する Linux ディストリビューション用の cifs-utils パッケージをインストールします](#install-cifs-utils)**。

2. **マウント ポイント用のフォルダーを作成します**。これはファイル システムの任意の場所に作成できますが、フォルダーの絶対パスを書き留めておく必要があります。 次の例では、ルートの下にフォルダーを作成します。

    ```
    sudo mkdir /mymountpoint
    ```

3. **次のコマンドを使用して、次の行を `/etc/fstab` に追加します**。`<storage-account-name>`、`<share-name>`、および `<storage-account-key>` を適切な情報に置き換えることを忘れないでください。

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> `/etc/fstab` を編集した後、再起動する代わりに、`sudo mount -a` を使用して、Azure File 共有をマウントできます。

## <a name="feedback"></a>フィードバック
Linux ユーザーからのご意見をお待ちしています。

Azure File storage for Linux ユーザーのグループによって、File ストレージを Linux で評価および使用するときにフィードバックを共有できるフォーラムが提供されています。 [Azure File Storage Linux ユーザー](mailto:azurefileslinuxusers@microsoft.com) にメールを送信して、ユーザー グループに参加してください。

## <a name="next-steps"></a>次のステップ
Azure File Storage の詳細については、次のリンクを参照してください。
* [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Microsoft Azure Storage で AzCopy を使用する方法](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage での Azure CLI の使用](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [FAQ](../storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)

