---
title: SMB 経由でデータを Azure Data Box Heavy にコピーするチュートリアル | Microsoft Docs
description: SMB 経由でデータを Azure Data Box Heavy にコピーする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 1c45e06159e4c2850efa2d3ab3290647961fb7e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592428"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>チュートリアル:SMB 経由で Azure Data Box Heavy にデータをコピーする

このチュートリアルでは、ローカル Web UI を使用してホスト コンピューターに接続し、そこからデータをコピーする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box Heavy に接続する
> * データを Data Box Heavy にコピーする


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box の設定に関するAzure Data Box Heavy の設定](data-box-deploy-set-up.md)。
2. Data Box Heavy の受け取りが済んでいて、ポータル内での注文の状態が **[配送済み]** であること。
3. Data Box Heavy にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 最速のコピー速度を得るため、2 つの 40 GbE 接続 (ノードごとに 1 つずつ) を並列で利用できます。 40 GbE 接続を使用できない場合は、少なくとも 2 つの 10 GbE 接続 (ノードごとに 1 つずつ) を使用することをお勧めします。

## <a name="connect-to-data-box-heavy-shares"></a>Data Box Heavy 共有に接続する

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
| Azure ブロック BLOB | <li>共有への UNC パス: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure ページ BLOB  | <li>共有への UNC パス: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>共有への UNC パス: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

接続する手順は、クライアントに Windows と Linux のいずれを使用するかによって異なります。

> [!NOTE]
> デバイスの両方のノードに並列して接続するには、同じ手順に従ってください。

### <a name="connect-on-a-windows-system"></a>Windows システムで接続する

Windows Server ホスト コンピューターを使用している場合は、これらの手順に従って Data Box Heavy に接続します。

1. 最初に、認証を行ってセッションを開始します。 **[接続とコピー]** に移動します。 **[資格情報の取得]** をクリックして、ストレージ アカウントに関連付けられている共有用のアクセス資格情報を取得します。

    ![共有の資格情報を取得する 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. [共有にアクセスしてデータをコピーする] ダイアログ ボックスで、共有に対応する **[ユーザー名]** と **[パスワード]** をコピーします。 Click **OK**.
    
    ![共有の資格情報を取得する 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. 自分のストレージ アカウントに関連付けられている共有 (次の例の場合は、*databoxe2etest*) にホスト コンピューターからアクセスするために、コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    データ形式に応じて、共有パスは次のようになっています。
    - Azure ブロック BLOB - `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure ページ BLOB - `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Files - `\\10.100.10.100\databoxe2etest_AzFile`
    
4. メッセージに従って共有のパスワードを入力します。 上記のコマンドを使用して共有に接続する例を次に示します。

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Windows キーを押しながら R キーを押します。 **[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>`」と入力します。 **[OK]** をクリックして、エクスプローラーを開きます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    共有がフォルダーとして表示されます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。
    
### <a name="connect-on-a-linux-system"></a>Linux システムで接続する

Linux クライアントを使用している場合は、次のコマンドを使用して、SMB 共有をマウントします。

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` パラメーターは、自分の Linux ホストがサポートしている SMB のバージョンです。 上記のコマンドで適切なバージョンを指定します。

Data Box Heavy でサポートされている SMB のバージョンについては、「[Supported file systems for Linux clients (Linux クライアントでサポートされるファイル システム)](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients)」を参照してください。

## <a name="copy-data-to-data-box-heavy"></a>データを Data Box Heavy にコピーする

Data Box Heavy 共有に接続したら、次の手順はデータのコピーです。

### <a name="copy-considerations"></a>コピーに関する考慮事項

データのコピーを開始する前に、次の考慮事項を確認してください。

- 適切なデータ形式に対応する共有にデータをコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用の共有にコピーしてください。 VHD をページ BLOB にコピーします。

    データ形式が適切な共有の種類と一致しない場合は、後続の手順で、Azure へのデータのアップロードに失敗します。
-  データをコピーするときは、データのサイズが、[Azure Storage と Data Box Heavy の制限](data-box-heavy-limits.md)に関する記事に記載されているサイズ制限に従っていることを確認してください。
- Data Box Heavy によってアップロードされているデータが、Data Box Heavy の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。
- 推奨事項は次のとおりです。
    - SMB と NFS の両方を同時に使用しません。
    - 同じデータを Azure 上の同じ宛先にコピーします。
     
  このようにした場合は、最終的な結果が不確定になります。
- 必ず、コピーするファイル用のフォルダーを共有の下に作成してから、ファイルをそのフォルダーにコピーしてください。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。

SMB 共有に接続した後、データのコピーを開始します。

1. Robocopy などの SMB 互換ファイル コピー ツールを使用して、データをコピーできます。 Robocopy を使用して、複数のコピー ジョブを開始できます。 次のコマンドを使用します。
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    属性については次の表で説明します。
    
    |Attribute  |説明  |
    |---------|---------|
    |/e      |サブディレクトリをコピーします (空のディレクトリを含む)。         |
    |/r:     |失敗したコピーの再試行回数を指定します。         |
    |/w:     |再試行間の待ち時間 (秒) を指定します。         |
    |/is     |同じファイルを含めます。         |
    |/nfl    |ファイル名をログに記録しないことを指定します。         |
    |/ndl    |ディレクトリ名をログに記録しないことを指定します。        |
    |/np     |コピー操作の進行状況 (それまでにコピーされたファイルまたはディレクトリの数) が表示されないように指定します。 進行状況を表示すると、パフォーマンスが大幅に低下します。         |
    |/MT     | マルチスレッドを使用します (推奨は 32 または 64 スレッド)。 このオプションは、暗号化されたファイルでは使用されません。 暗号化されたファイルと暗号化されていないファイルを分離することが必要な場合があります。 ただし、シングルスレッドのコピーでは、パフォーマンスが大幅に低下します。           |
    |/fft    | 任意のファイル システムのタイムスタンプの細分性を下げるために使用します。        |
    |/b      | バックアップ モードでファイルをコピーします。        |
    |/z      | 再起動モードでファイルをコピーします。環境が不安定な場合はこれを使用します。 このオプションでは、追加ログによりスループットが低下します。      |
    | /zb    | 再起動モードを使用します。 アクセスが拒否された場合、このオプションはバックアップ モードを使用します。 このオプションでは、チェックポイント処理のためにスループットが低下します。         |
    |/efsraw | すべての暗号化されたファイルを EFS raw モードでコピーします。 暗号化されたファイルでのみ使用します。         |
    |log+:\<LogFile>| 既存のログ ファイルに出力を追加します。|
    
 
    次のサンプルは、Data Box Heavy にファイルをコピーする robocopy コマンドの出力を示しています。

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
    ```       

2. パフォーマンスを最適化するには、データをコピーするときに、次の robocopy パラメーターを使用します。 (以下の数字は最良のシナリオを表します。)

    | プラットフォーム    | ほとんどが小さいファイル (< 512 KB)    | ほとんどが中規模のファイル (512 KB から 1 MB)  | ほとんどが大きいファイル (> 1 MB)                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy セッション <br> セッションあたり 24 スレッド | 6 Robocopy セッション <br> セッションあたり 16 スレッド | 6 Robocopy セッション <br> セッションあたり 16 スレッド |


    Robocopy コマンドについて詳しくは、「[Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)」(Robocopy といくつかの例) をご覧ください。

3. コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。

    ![コピーされたファイルの表示](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. データのコピー時:

    - Azure のオブジェクトおよびストレージの制限のほか、Azure のファイルおよびコンテナーの名前付け規則を満たすように、ファイル名、サイズ、形式が検証されます。
    - データの整合性を確保するために、インラインでチェックサムも計算されます。

    コピー処理中にエラーが発生した場合は、トラブルシューティングのためにエラー ファイルをダウンロードします。 エラー ファイルをダウンロードするには、矢印アイコンを選択します。

    ![エラー ファイルのダウンロード](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    詳細については、[データを Data Box Heavy にコピーしている間のエラー ログの表](data-box-logs.md#view-error-log-during-data-copy)に関するページを参照してください。 データ コピー中のエラーの詳細な一覧については、[Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)に関するページを参照してください。

5. メモ帳でエラー ファイルを開きます。 次のエラー ファイルは、データが正しくアラインされていないことを示しています。

    ![エラー ファイルのオープン](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    ページ BLOB の場合、データは 512 バイトでアラインされる必要があります。 このデータを削除すると、次のスクリーンショットに示すようにエラーが解決します。

    ![解決したエラー](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. コピーが完了した後、 **[ダッシュボードの表示]** ページに移動します。 自分のデバイス上の使用済み領域と空き領域を確認します。
    
    ![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

上記の手順を繰り返して、デバイスの第 2 のノードにデータをコピーします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Heavy に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box Heavy に接続する
> * データを Data Box Heavy にコピーする


次のチュートリアルに進み、ご自分の Data Box Heavy を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box Heavy を Microsoft に発送する](./data-box-heavy-deploy-picked-up.md)

