---
title: Azure Data Box に NFS 経由でデータをコピーするチュートリアル | Microsoft Docs
description: このチュートリアルでは、ローカル Web UI から NFS を使用して Azure Data Box に接続し、ホスト コンピューターからデータをコピーする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678884"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>チュートリアル:Azure Data Box に NFS 経由でデータをコピーする

このチュートリアルでは、ローカル Web UI を使用してホスト コンピューターに接続し、そこからデータをコピーする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 前提条件
> * Data Box に接続する
> * Data Box にデータをコピーする

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [チュートリアル:チュートリアル](data-box-deploy-set-up.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータルで注文の状態が **[配信済み]** に更新されていること。
3. Data Box にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。 

## <a name="connect-to-data-box"></a>Data Box に接続する

選択したストレージ アカウントに基づいて、Data Box では最大で次のものが作成されます。
- GPv1 および GPv2 に対して関連付けられているストレージ アカウントごとに 3 つの共有。
- Premium ストレージに対して 1 つの共有。 
- BLOB ストレージ アカウントに対して 1 つの共有。 

ブロック BLOB とページ BLOB の共有では、第 1 レベルのエンティティはコンテナーであり、第 2 レベルのエンティティは BLOB です。 Azure Files の共有では、第 1 レベルのエンティティは共有であり、第 2 レベルのエンティティはファイルです。

次の表は、Data Box 上の共有への UNC パスと、データのアップロード先である Azure Storage のパスの URL を示しています。 Azure Storage の最終的なパスの URL は、UNC 共有パスから導き出すことができます。
 
| Azure Storage の種類| Data Box 共有                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure ブロック BLOB | <li>共有への UNC パス: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure ページ BLOB  | <li>共有への UNC パス: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>共有への UNC パス: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Linux ホスト コンピューターを使用している場合は、次の手順を実行して、NFS クライアントへのアクセスを許可するように Data Box を構成します。

1. 共有にアクセスできる許可するクライアントの IP アドレスを指定します。 ローカル Web UI で、 **[接続とコピー]** ページに移動します。 **[NFS の設定]** で、 **[NFS のクライアント アクセス]** をクリックします。 

    ![NFS のクライアント アクセスを構成する](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. NFS クライアントの IP アドレスを指定して、 **[追加]** をクリックします。 この手順を繰り返すことにより、複数の NFS クライアントに対するアクセスを構成できます。 **[OK]** をクリックします。

    ![NFS クライアントの IP アドレスを構成する](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux ホスト コンピューターに[サポートされているバージョン](data-box-system-requirements.md)の NFS クライアントがインストールされていることを確認します。 お使いの Linux ディストリビューションの特定のバージョンを使用します。 

3. NFS クライアントをインストールした後、次のコマンドを使用して、Data Box デバイスに NFS 共有をマウントします。

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    NFS 経由で Data Box の共有に接続する方法の例を次に示します。 Data Box デバイスの IP は `10.161.23.130` で、共有 `Mystoracct_Blob` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxubuntuhost/databox` です。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac クライアントの場合は、次のように、さらにオプションを追加する必要があります。 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。

## <a name="copy-data-to-data-box"></a>Data Box にデータをコピーする

Data Box の共有に接続したら、次にデータをコピーします。 データのコピーを開始する前に、次の考慮事項を確認してください。

* 適切なデータ形式に対応する共有にデータをコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用の共有にコピーしてください。 VHD をページ BLOB にコピーします。 データ形式が適切な共有の種類と一致しない場合、後続の手順で、Azure へのデータのアップロードに失敗します。
*  データをコピーするときは、データのサイズが、[Azure ストレージ アカウントのサイズ制限](data-box-limits.md#azure-storage-account-size-limits)に関する記事に記載されているサイズ制限に従っていること確認してください。
* Data Box によってアップロードされているデータが、Data Box の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。
* SMB と NFS の両方を同時に使用しないこと、または同じデータを Azure 上の同じ最終コピー先にコピーしないことをお勧めします。 このような場合は、最終的な結果がわからなくなります。
* **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。
* 大文字と小文字を区別するディレクトリとファイル名を NFS 共有から Data Box 上の NFS に取り込む場合:
  * 名前の大文字と小文字の区別は保持されます。
  * ファイルの大文字と小文字は区別されません。

    たとえば、`SampleFile.txt` と `Samplefile.Txt` をコピーする場合、Data Box にコピーされるときに名前の大文字と小文字の区別は保持されますが、2 番目のファイルによって最初のファイルは上書きされます。これは、同じファイルと見なされるためです。

> [!IMPORTANT]
> Data Box によって Azure Storage にデータが転送されたことを確認できるまでは、ソース データのコピーを保持するようにしてください。

Linux ホスト コンピューターを使用している場合は、Robocopy のようなコピー ユーティリティを使用します。 Linux で使用できる代替手段は、[`rsync`](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/)、[Ultracopier](https://ultracopier.first-world.info/) などです。  

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

コピー処理中にエラーが発生すると、通知が表示されます。

![[接続とコピー] でエラーをダウンロードして表示する](media/data-box-deploy-copy-data/view-errors-1.png)

**[問題の一覧をダウンロードする]** を選択します。

![コピー エラーの問題一覧をダウンロードする](media/data-box-deploy-copy-data/view-errors-2.png)

一覧を開いてエラーの詳細を表示し、解決用 URL を選択して推奨される解決方法を確認します。

![コピー エラーの問題の一覧内の問題](media/data-box-deploy-copy-data/view-errors-3.png)

詳細については、「[View error logs during data copy to Data Box (Data Box へのデータのコピー中のエラー ログを表示する)](data-box-logs.md#view-error-log-during-data-copy)」を参照してください。 データのコピー中のエラーの詳細な一覧については、[Data Box の問題のトラブルシューティング](data-box-troubleshoot.md)に関するページを参照してください。

データの整合性を保証するため、データがコピーされるときにインラインでチェックサムが計算されます。 コピーが完了したら、デバイスで使用済み領域と空き領域を確認します。

   ![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * 前提条件
> * Data Box に接続する
> * Data Box にデータをコピーする

次のチュートリアルに進み、お客様の Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に発送する](./data-box-deploy-picked-up.md)
