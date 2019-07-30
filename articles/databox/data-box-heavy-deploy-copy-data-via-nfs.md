---
title: Azure Data Box Heavy に NFS 経由でデータをコピーするチュートリアル | Microsoft Docs
description: Azure Data Box Heavy に NFS 経由でデータをコピーする方法について学習します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595811"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>チュートリアル:NFS 経由で Azure Data Box Heavy にデータをコピーする

このチュートリアルでは、ローカル Web UI を使用してホスト コンピューターに接続し、そこからデータをご利用の Azure Data Box Heavy にコピーする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件
> * Data Box Heavy に接続する
> * データを Data Box Heavy にコピーする

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [チュートリアル:Azure Data Box Heavy の設定](data-box-heavy-deploy-set-up.md)。
2. Data Box Heavy の受け取りが済んでいて、ポータル内での注文の状態が **[配送済み]** であること。
3. Data Box Heavy にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-heavy-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 最速のコピー速度を得るため、2 つの 40 GbE 接続 (ノードごとに 1 つずつ) を並列で利用できます。 40 GbE 接続を使用できない場合は、少なくとも 2 つの 10 GbE 接続 (ノードごとに 1 つずつ) を使用することをお勧めします。 

## <a name="connect-to-data-box-heavy"></a>Data Box Heavy に接続する

選択したストレージ アカウントに基づいて、Data Box Heavy では最大で次のものが作成されます。
- GPv1 および GPv2 に対して関連付けられているストレージ アカウントごとに 3 つの共有。
- Premium ストレージに対して 1 つの共有。
- BLOB ストレージ アカウントに対して 1 つの共有。

これらの共有はデバイスの両方のノード上に作成されます。

ブロック BLOB およびページ BLOB の共有の場合:
- 第 1 レベルのエンティティはコンテナーです。
- 第 2 レベルのエンティティは BLOB です。

Azure Files の共有の場合:
- 第 1 レベルのエンティティは共有です。
- 第 2 レベルのエンティティはファイルです。

次の表は、Data Box Heavy 上の共有への UNC パスと、データのアップロード先である Azure Storage のパスの URL を示しています。 Azure Storage の最終的なパスの URL は、UNC 共有パスから導き出すことができます。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure ブロック BLOB | <li>共有への UNC パス: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure ページ BLOB  | <li>共有への UNC パス: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>共有への UNC パス: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Linux ホスト コンピューターを使用している場合は、次の手順を行って、NFS クライアントへのアクセスを許可するように、ご利用のデバイスを構成します。

1. 共有にアクセスできる許可するクライアントの IP アドレスを指定します。 ローカル Web UI で、 **[接続とコピー]** ページに移動します。 **[NFS の設定]** で、 **[NFS のクライアント アクセス]** をクリックします。 

    ![NFS のクライアント アクセスを構成する 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. NFS クライアントの IP アドレスを指定して、 **[追加]** をクリックします。 この手順を繰り返すことにより、複数の NFS クライアントに対するアクセスを構成できます。 Click **OK**.

    ![NFS のクライアント アクセスを構成する 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux ホスト コンピューターに[サポートされているバージョン](data-box-system-requirements.md)の NFS クライアントがインストールされていることを確認します。 お使いの Linux ディストリビューションの特定のバージョンを使用します。 

3. NFS クライアントをインストールした後、次のコマンドを使用して、Data Box デバイスに NFS 共有をマウントします。

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    NFS 経由で Data Box Heavy の共有に接続する方法の例を次に示します。 Data Box Heavy の IP は `10.161.23.130` で、共有 `Mystoracct_Blob` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxheavyubuntuhost/databoxheavy` です。

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Mac クライアントの場合は、次のように、さらにオプションを追加する必要があります。 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。

## <a name="copy-data-to-data-box-heavy"></a>データを Data Box Heavy にコピーする

Data Box Heavy 共有に接続したら、次の手順はデータのコピーです。 データのコピーを開始する前に、次の考慮事項を確認してください。

- 適切なデータ形式に対応する共有にデータをコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用の共有にコピーしてください。 VHD をページ BLOB にコピーします。 データ形式が適切な共有の種類と一致しない場合、後続の手順で、Azure へのデータのアップロードに失敗します。
-  データのコピー中は、そのサイズが [Azure Storage と Data Box Heavy の制限](data-box-heavy-limits.md)に関するページに記載されたサイズ制限に準拠していることを確実にしてください。 
- Data Box Heavy によってアップロードされているデータが、Data Box Heavy の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。
- SMB と NFS の両方を同時に使用しないこと、または同じデータを Azure 上の同じ最終コピー先にコピーしないことをお勧めします。 このような場合は、最終的な結果がわからなくなります。
- **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。
- 大文字と小文字を区別するディレクトリとファイル名を NFS 共有から Data Box Heavy 上の NFS に取り込む場合: 
    - 名前の大文字と小文字の区別は保持されます。
    - ファイルの大文字と小文字は区別されません。
    
    たとえば、`SampleFile.txt` と `Samplefile.Txt` をコピーする場合、デバイスにコピーされるときに名前の大文字と小文字の区別は保持されますが、2 番目のファイルによって最初のファイルは上書きされます。これは、これらが同じファイルと見なされるためです。


Linux ホスト コンピューターを使用している場合は、Robocopy のようなコピー ユーティリティを使用します。 Linux で使用できる代替手段は、[rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/)、[Ultracopier](https://ultracopier.first-world.info/) などです。  

`cp` コマンドは、ディレクトリをコピーするのに最適なオプションの 1 つです。 使用方法について詳しくは、[cp の man ページ](http://man7.org/linux/man-pages/man1/cp.1.html)をご覧ください。

マルチスレッドのコピーに rsync オプションを使用する場合は、以下のガイドラインに従ってください。

 - Linux クライアントで使用されているファイル システムに応じて、**CIFS Utils** または **NFS Utils** パッケージをインストールします。

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  **Rsync** および **Parallel** をインストールします (Linux ディストリビューションのバージョンによって異なります)。

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - マウント ポイントを作成します。

    `sudo mkdir /mnt/databoxheavy`

 - ボリュームをマウントします。

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - フォルダーのディレクトリ構造をミラー化します。  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - ファイルをコピーします。 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     j は並列化の数を指定し、X は並列コピーの数です。

     16 並列コピーから始めて、使用可能なリソースに応じてスレッドの数を増やすことをお勧めします。

> [!IMPORTANT]
> Linux ファイルの種類のうち、シンボリック リンク、文字ファイル、ブロック ファイル、ソケット、パイプはサポートされていません。 これらのファイルの種類を使用すると、**発送準備**手順でエラーが発生します。

コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。 コピー処理中にエラーが発生した場合は、トラブルシューティングのためにエラー ファイルをダウンロードします。 詳細については、[データを Data Box Heavy にコピーしている間のエラー ログの表](data-box-logs.md#view-error-log-during-data-copy)に関するページを参照してください。 データ コピー中のエラーの詳細な一覧については、[Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)に関するページを参照してください。

データの整合性を保証するため、データがコピーされるときにインラインでチェックサムが計算されます。 コピーが完了したら、デバイスで使用済み領域と空き領域を確認します。
    
   ![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Heavy に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件
> * Data Box Heavy に接続する
> * データを Data Box Heavy にコピーする


次のチュートリアルに進み、お客様の Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box Heavy を Microsoft に発送する](./data-box-heavy-deploy-picked-up.md)

