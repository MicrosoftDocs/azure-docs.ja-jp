---
title: チュートリアル:VHD からマネージド ディスクにコピーする
titleSuffix: Azure Data Box
description: オンプレミスの VM ワークロードの VHD から Azure Data Box にデータをコピーする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2019
ms.author: alkohli
ms.openlocfilehash: 1394cf6511a65a0e406e51229953e8666d4d4d8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337680"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>チュートリアル:Data Box を使用して Azure のマネージド ディスクとしてデータをインポートする

このチュートリアルでは、Azure Data Box を使用して、オンプレミスの VHD を Azure のマネージド ディスクに移行する方法について説明します。 オンプレミス VM の VHD は、ページ BLOB として Data Box にコピーされ、マネージド ディスクとして Azure にアップロードされます。 その後、これらのマネージド ディスクは Azure VM に接続できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 前提条件を確認する
> * Data Box に接続する
> * Data Box にデータをコピーする

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box の設定に関するチュートリアル](data-box-deploy-set-up.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータル内での注文の状態が **[配信済み]** であること。
3. 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用します。ただし、コピー速度に影響があります。
4. 次の事項を確認済みであること。

    - [「Azure オブジェクトのサイズ制限」のマネージド ディスク サイズ](data-box-limits.md#azure-object-size-limits)のサポート状況。
    - [Azure マネージド ディスクの概要](../virtual-machines/managed-disks-overview.md)。 

5. Data Box によって Azure Storage にデータが転送されたことを確認するまでソース データのコピーを保持していること。

## <a name="connect-to-data-box"></a>Data Box に接続する

Data Box は、指定されたリソース グループに基づいて、関連付けられているリソース グループごとに 1 つの共有を作成します。 たとえば、注文するときに `mydbmdrg1` と `mydbmdrg2` が作成された場合は、次の共有が作成されます。

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

各共有内に、ストレージ アカウントのコンテナーに対応する次の 3 つのフォルダーが作成されます。

- Premium SSD
- Standard HDD
- Standard SSD

Data Box 上の共有への UNC パスを次の表に示します。
 
|        接続プロトコル           |             共有への UNC パス                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Data Box の共有への接続に SMB と NFS のどちらを使用するかに応じて、接続する手順は異なります。

> [!NOTE]
> この機能では、REST 経由の接続はサポートされていません。

### <a name="connect-to-data-box-via-smb"></a>SMB 経由で Data Box に接続する

Windows Server ホスト コンピューターを使用している場合は、次の手順に従って Data Box に接続します。

1. 最初に、認証を行ってセッションを開始します。 **[接続とコピー]** に移動します。 **[資格情報の取得]** をクリックして、お使いのリソース グループに関連付けられている共有用のアクセス資格情報を取得します。 Azure portal の **[デバイスの詳細]** からアクセス資格情報を取得することもできます。

    > [!NOTE]
    > マネージド ディスクのすべての共有の資格情報は同じです。

    ![[接続とコピー]、共有の資格情報を取得する](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. **[共有にアクセスしてデータをコピーする]** ダイアログ ボックスで、共有の **[ユーザー名]** と **[パスワード]** をコピーします。 **[OK]** をクリックします。
    
    ![[接続とコピー]、共有の資格情報をコピーする](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. ご自分のリソースに関連付けられている共有 (以下の例の場合は、*mydbmdrg1*) にホスト コンピューターからアクセスするため、コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    この例の UNC 共有パスは次のとおりです。

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. メッセージに従って共有のパスワードを入力します。 上記のコマンドを使用して共有に接続する例を次に示します。

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

5. Windows キーを押しながら R キーを押します。 **[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>\<ShareName>`」と入力します。 **[OK]** をクリックして、エクスプローラーを開きます。
    
    ![エクスプローラーで共有に接続する](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    各共有内に、事前に作成された次のフォルダーが表示されます。
    
    ![エクスプローラーで共有に接続する。共有のフォルダー](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>NFS 経由で Data Box に接続する

Linux ホスト コンピューターを使用している場合は、次の手順を実行して、NFS クライアントへのアクセスを許可するように Data Box を構成します。

1. 共有にアクセスできる許可するクライアントの IP アドレスを指定します。 ローカル Web UI で、 **[接続とコピー]** ページに移動します。 **[NFS の設定]** で、 **[NFS のクライアント アクセス]** をクリックします。

    ![NFS のクライアント アクセスを構成する](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. NFS クライアントの IP アドレスを指定して、 **[追加]** をクリックします。 この手順を繰り返すことにより、複数の NFS クライアントに対するアクセスを構成できます。 **[OK]** をクリックします。

    ![NFS クライアントの IP アドレスを構成する](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Linux ホスト コンピューターに[サポートされているバージョン](data-box-system-requirements.md)の NFS クライアントがインストールされていることを確認します。 お使いの Linux ディストリビューションの特定のバージョンを使用します。

3. NFS クライアントをインストールした後、次のコマンドを使用して、Data Box デバイスに NFS 共有をマウントします。

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    NFS 経由で Data Box の共有に接続する方法の例を次に示します。 Data Box デバイスの IP は `169.254.250.200` で、共有 `mydbmdrg1_MDisk` は ubuntuVM にマウントされており、マウント ポイントは `/home/databoxubuntuhost/databox` です。

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Data Box にデータをコピーする

データ サーバーに接続したら、次にデータをコピーします。 VHD ファイルは、ページ BLOB としてステージング用のストレージ アカウントにコピーされます。 その後、ページ BLOB はマネージド ディスクに変換され、リソース グループに移動されます。

データのコピーを開始する前に、次の考慮事項を確認してください。

- VHD は必ず、事前に作成されたフォルダーの 1 つにコピーします。 これらのフォルダー以外または自分で作成したフォルダーに VHD をコピーすると、VHD はマネージド ディスクではなく、ページ BLOB として Azure ストレージ アカウントにアップロードされます。
- マネージド ディスクを作成するためにアップロードできるのは、容量固定の VHD のみです。 VHDX ファイル、動的および差分 VHD はサポートされていません。
- 事前に作成されたすべてのフォルダーにわたって 1 つのリソース グループ内で特定の名前を持つマネージド ディスクを 1 つだけ保持できます。 これは、事前に作成されたフォルダーにアップロードされた VHD は一意の名前でなければならないことを意味します。 指定した名前が、リソース グループ内の既存のマネージド ディスクと一致しないようにしてください。
- 「[Azure オブジェクトのサイズ制限](data-box-limits.md#azure-object-size-limits)」のマネージド ディスク制限を確認します。

SMB と NFS のどちらを経由して接続するかに応じて、次の方法を使用できます。

- [SMB 経由でのデータ コピー](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [NFS 経由でのデータ コピー](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

コピー ジョブが完了するのを待ちます。 次の手順に進む前に、コピー ジョブがエラーなしで完了したことを確認してください。

![エラーがない **[接続とコピー]** ページ](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

コピー プロセス中にエラーがある場合は、 **[接続とコピー]** ページからログをダウンロードします。

- 512 バイトにアラインされていないファイルをコピーした場合、そのファイルはステージング用のストレージ アカウントにページ BLOB としてアップロードされません。 ログにエラーが表示されます。 そのファイルを削除し、512 バイトにアラインされているファイルをコピーします。

- 名前が長いVHDX (これらのファイルはサポートされていません) をコピーした場合は、ログにエラーが表示されます。

    ![ログにエラーがある **[接続とコピー]** ページ](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    次の手順に進む前に、エラーを解決してください。

データの整合性を保証するため、データがコピーされるときにインラインでチェックサムが計算されます。 コピーが完了したら、デバイスで使用済み領域と空き領域を確認します。
    
![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

コピー ジョブが完了したら、**発送準備** に進むことができます。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件を確認する
> * Data Box に接続する
> * Data Box にデータをコピーする


次のチュートリアルに進み、お客様の Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に発送する](./data-box-deploy-picked-up.md)