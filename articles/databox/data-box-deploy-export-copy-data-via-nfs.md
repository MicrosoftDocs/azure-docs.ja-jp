---
title: Azure Data Box から NFS 経由でデータをコピーするチュートリアル | Microsoft Docs
description: Azure Data Box から NFS 経由でデータをコピーする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 64bb5e94c4b18626d1f85d7e61252aae74202eb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680614"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs"></a>チュートリアル:Azure Data Box から NFS 経由でデータをコピーする

このチュートリアルでは、Data Box のローカル Web UI から NFS 経由でオンプレミスのデータ サーバーにデータをコピーする方法について説明します。 Data Box のデータは、Azure Storage アカウントからエクスポートされます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 前提条件
> * Data Box に接続する
> * Data Box からデータをコピーする

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. Azure Data Box を注文済みであること。
    - インポート注文については、「[チュートリアル: Azure Data Box を注文する](data-box-deploy-ordered.md)」を参照してください。
    - エクスポート注文については、「[チュートリアル: Azure Data Box の注文](data-box-deploy-export-ordered.md)に関するページを参照してください。
2. Data Box の受け取りが済んでいて、ポータル内での注文の状態が **[配信済み]** であること。
3. Data Box のデータのコピー先となるホスト コンピューターが用意されていること。 このホスト コンピューターは次の条件を満たしている必要があります。
   * [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
   * 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用します。ただし、コピー速度は低下します。

## <a name="connect-to-data-box"></a>Data Box に接続する

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Linux ホスト コンピューターを使用している場合は、次の手順を実行して、NFS クライアントへのアクセスを許可するように Data Box を構成します。 Data Box は、一度に最大 5 つの NFS クライアントに接続できます。

1. 共有へのアクセスを許可するクライアントの IP アドレスを指定します。

    1.  ローカル Web UI で、 **[接続とコピー]** ページに移動します。 **[NFS の設定]** で、 **[NFS のクライアント アクセス]** をクリックします。 

        ![[NFS のクライアント アクセス] を開く](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. NFS クライアントを追加するには、クライアントの IP アドレスを指定し、 **[追加]** をクリックします。 Data Box は、一度に最大 5 つの NFS クライアントに接続できます。 完了したら、 **[OK]** をクリックします。

         ![NFS クライアントを追加する](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Linux ホスト コンピューターに[サポートされているバージョン](data-box-system-requirements.md)の NFS クライアントがインストールされていることを確認します。 お使いの Linux ディストリビューションの特定のバージョンを使用します。 

3. NFS クライアントをインストールした後、次のコマンドを使用して、Data Box デバイスに NFS 共有をマウントします。

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    NFS 経由で Data Box の共有に接続する方法の例を次に示します。 Data Box デバイスの IP は `10.161.23.130` で、共有 `Mystoracct_Blob` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxubuntuhost/databox` です。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac クライアントの場合は、次のように、さらにオプションを追加する必要があります。 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。

## <a name="copy-data-from-data-box"></a>Data Box からデータをコピーする

Data Box 共有に接続したら、次にデータをコピーします。

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 これで、データのコピーを開始できます。 Linux ホスト コンピューターを使用している場合は、Robocopy のようなコピー ユーティリティを使用します。 Linux で使用できる代替手段は、[`rsync`](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/)、[Ultracopier](https://ultracopier.first-world.info/) などです。  

`cp` コマンドは、ディレクトリをコピーするのに最適なオプションの 1 つです。 使用方法について詳しくは、[cp の man ページ](http://man7.org/linux/man-pages/man1/cp.1.html)をご覧ください。

マルチスレッドのコピーに `rsync` オプションを使用する場合は、以下のガイドラインに従ってください。

* Linux クライアントで使用されているファイル システムに応じて、**CIFS Utils** または **NFS Utils** パッケージをインストールします。

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* `rsync` および **Parallel** をインストールします (Linux ディストリビューションのバージョンによって異なります)。

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* マウント ポイントを作成します。

    `sudo mkdir /mnt/databox`

* ボリュームをマウントします。

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* フォルダーのディレクトリ構造をミラー化します。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* ファイルをコピーします。

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     j は並列化の数を指定し、X は並列コピーの数です。

     16 並列コピーから始めて、使用可能なリソースに応じてスレッドの数を増やすことをお勧めします。

> [!IMPORTANT]
> Linux ファイルの種類のうち、シンボリック リンク、文字ファイル、ブロック ファイル、ソケット、パイプはサポートされていません。 これらのファイルの種類を使用すると、**発送準備** 手順でエラーが発生します。

コピーが完了したら、**ダッシュボード** に移動し、デバイスの使用済み領域と空き領域を確認します。

これで、Microsoft への Data Box の発送に進むことができます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * 前提条件
> * Data Box に接続する
> * Data Box からデータをコピーする

次のチュートリアルに進み、お客様の Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に発送する](./data-box-deploy-export-picked-up.md)
