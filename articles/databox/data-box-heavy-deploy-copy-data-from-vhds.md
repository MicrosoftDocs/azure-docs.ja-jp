---
title: 'チュートリアル: Azure Data Box Heavy を使用して VHD からマネージド ディスクにデータをコピーする | Microsoft Docs'
description: オンプレミスの VM ワークロードの VHD から Azure Data Box Heavy にデータをコピーする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a29cd142b3322c958f70aad8d5cad2bc30b87d76
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670837"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>チュートリアル:Data Box Heavy を使用して Azure のマネージド ディスクとしてデータをインポートする

このチュートリアルでは、Azure Data Box Heavy を使用して、オンプレミスの VHD を Azure のマネージド ディスクに移行する方法について説明します。 オンプレミス VM の VHD は、ページ BLOB として Data Box Heavy にコピーされ、マネージド ディスクとして Azure にアップロードされます。 その後、これらのマネージド ディスクは Azure VM に接続できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件を確認する
> * Data Box Heavy に接続する
> * データを Data Box Heavy にコピーする


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box の設定に関するAzure Data Box Heavy の設定](data-box-heavy-deploy-set-up.md)。
2. Data Box Heavy の受け取りが済んでいて、ポータル内での注文の状態が **[配送済み]** であること。
3. 高速ネットワークに接続していること。 最速のコピー速度を得るため、2 つの 40 GbE 接続 (ノードごとに 1 つずつ) を並列で利用できます。 40 GbE 接続を使用できない場合は、少なくとも 2 つの 10 GbE 接続 (ノードごとに 1 つずつ) を使用することをお勧めします。 
4. 次の事項を確認済みであること。

    - [Azure オブジェクトのサイズ制限におけるサポート対象のマネージド ディスク サイズ](data-box-heavy-limits.md#azure-object-size-limits)。
    - [Azure マネージド ディスクの概要](/azure/virtual-machines/windows/managed-disks-overview)。 

## <a name="connect-to-data-box-heavy"></a>Data Box Heavy に接続する

Data Box Heavy では、指定されたリソース グループに基づいて、ノードごとに関連付けられた各リソース グループに対して 1 つの共有が作成されます。 たとえば、注文するときに `mydbmdrg1` と `mydbmdrg2` が作成された場合は、次の共有が作成されます。

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

各共有内に、ストレージ アカウントのコンテナーに対応する次の 3 つのフォルダーが作成されます。

- Premium SSD
- Standard HDD
- Standard SSD

ご利用の Data Box Heavy 上の共有への UNC パスを次の表に示します。
 
|        接続プロトコル           |             共有への UNC パス                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Data Box Heavy の共有への接続に SMB と NFS のどちらを使用するかに応じて、接続する手順は異なります。

> [!NOTE]
> - この機能では、REST 経由の接続はサポートされていません。
> - 接続手順を繰り返して、Data Box Heavy の 2 番目のノードに接続します。

### <a name="connect-to-data-box-heavy-via-smb"></a>SMB を介して Data Box Heavy に接続する

Windows Server ホスト コンピューターを使用している場合は、これらの手順に従って Data Box Heavy に接続します。

1. 最初に、認証を行ってセッションを開始します。 **[接続とコピー]** に移動します。 **[資格情報の取得]** をクリックして、リソース グループに関連付けられている共有用のアクセス資格情報を取得します。 Azure portal の **[デバイスの詳細]** からアクセス資格情報を取得することもできます。

    > [!NOTE]
    > マネージド ディスクのすべての共有の資格情報は同じです。

    ![共有の資格情報を取得する 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. [共有にアクセスしてデータをコピーする] ダイアログ ボックスで、共有の **[ユーザー名]** と **[パスワード]** をコピーします。 Click **OK**.
    
    ![共有の資格情報を取得する 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. リソースに関連付けられている共有 (以下の例の場合は、*mydbmdrg1*) にホスト コンピューターからアクセスするため、コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    この例の UNC 共有パスは次のとおりです。

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. メッセージに従って共有のパスワードを入力します。 上記のコマンドを使用して共有に接続する例を次に示します。

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Windows キーを押しながら R キーを押します。 **[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>\<ShareName>`」と入力します。 **[OK]** をクリックして、エクスプローラーを開きます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    各共有内に、事前に作成された次のフォルダーが表示されます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>NFS を介して Data Box Heavy に接続する

Linux ホスト コンピューターを使用している場合は、次の手順を行って、NFS クライアントへのアクセスを許可するように、ご利用のデバイスを構成します。

1. 共有にアクセスできる許可するクライアントの IP アドレスを指定します。 ローカル Web UI で、 **[接続とコピー]** ページに移動します。 **[NFS の設定]** で、 **[NFS のクライアント アクセス]** をクリックします。

    ![NFS のクライアント アクセスを構成する 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. NFS クライアントの IP アドレスを指定して、 **[追加]** をクリックします。 この手順を繰り返すことにより、複数の NFS クライアントに対するアクセスを構成できます。 Click **OK**.

    ![NFS のクライアント アクセスを構成する 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Linux ホスト コンピューターに[サポートされているバージョン](data-box-system-requirements.md)の NFS クライアントがインストールされていることを確認します。 お使いの Linux ディストリビューションの特定のバージョンを使用します。

3. NFS クライアントをインストールした後、次のコマンドを使用して、ご利用のデバイスに NFS 共有をマウントします。

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    NFS 経由で Data Box または Data Box Heavy の共有に接続する方法の例を次に示します。 Data Box または Data Box Heavy デバイスの IP は `169.254.250.200` であり、共有 `mydbmdrg1_MDisk` は ubuntuVM にマウントされていて、マウント ポイントは `/home/databoxubuntuhost/databox` です。

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>データを Data Box Heavy にコピーする

データ サーバーに接続したら、次にデータをコピーします。 VHD ファイルは、ページ BLOB としてステージング用のストレージ アカウントにコピーされます。 その後、ページ BLOB はマネージド ディスクに変換され、リソース グループに移動されます。

データのコピーを開始する前に、次の考慮事項を確認してください。

- VHD は必ず、事前に作成されたフォルダーの 1 つにコピーします。 これらのフォルダー以外または自分で作成したフォルダーに VHD をコピーすると、VHD はマネージド ディスクではなく、ページ BLOB として Azure Storage アカウントにアップロードされます。
- マネージド ディスクを作成するためにアップロードできるのは、容量固定の VHD のみです。 VHDX ファイル、動的 VHD、差分 VHD はサポートされていません。
- 事前に作成されたすべてのフォルダーにわたって 1 つのリソース グループ内で特定の名前を持つマネージド ディスクを 1 つだけ保持できます。 これは、事前に作成されたフォルダーにアップロードされた VHD は一意の名前でなければならないことを意味します。 指定した名前が、リソース グループ内の既存のマネージド ディスクと一致しないようにしてください。
- [Azure オブジェクトのサイズ制限](data-box-heavy-limits.md#azure-object-size-limits)におけるマネージド ディスク制限を確認します。

SMB と NFS のどちらを経由して接続するかに応じて、次の方法を使用できます。

- [SMB 経由でのデータ コピー](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [NFS 経由でのデータ コピー](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

コピー ジョブが完了するのを待ちます。 次の手順に進む前に、コピー ジョブがエラーなしで完了したことを確認してください。

![エラーがない **[接続とコピー]** ページ](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

コピー プロセス中にエラーがある場合は、 **[接続とコピー]** ページからログをダウンロードします。

- 512 バイトにアラインされていないファイルをコピーした場合、そのファイルはステージング用のストレージ アカウントにページ BLOB としてアップロードされません。 ログにエラーが表示されます。 そのファイルを削除し、512 バイトにアラインされているファイルをコピーします。

- 名前が長いVHDX (これらのファイルはサポートされていません) をコピーした場合は、ログにエラーが表示されます。

    ![ログにエラーがある **[接続とコピー]** ページ](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    次の手順に進む前に、エラーを解決してください。

データの整合性を保証するため、データがコピーされるときにインラインでチェックサムが計算されます。 コピーが完了したら、デバイスで使用済み領域と空き領域を確認します。
    
![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

コピー ジョブが完了したら、**発送準備**に進むことができます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Heavy に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件を確認する
> * Data Box Heavy に接続する
> * データを Data Box Heavy にコピーする


次のチュートリアルに進み、ご自分の Data Box Heavy を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box Heavy を Microsoft に発送する](./data-box-heavy-deploy-picked-up.md)

