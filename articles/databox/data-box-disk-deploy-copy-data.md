---
title: Azure Data Box Disk にデータをコピーするチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk にデータをコピーする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: c309a7cb18086526b23c875b41d9d4f4db4bc213
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70231384"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>チュートリアル:Azure Data Box Disk にデータをコピーして確認する

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Azure Data Box Disk にデータをコピーして検証する

ディスクが接続され、ロックが解除されたら、ソース データ サーバーからディスクにデータをコピーできます。 データのコピーが完了した後は、データを検証して、Azure へのアップロードが正常に行われることを確認する必要があります。

::: zone-end

::: zone target="docs"

このチュートリアルでは、ホスト コンピューターからデータをコピーして、データ整合性を確認するためのチェックサムを生成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * データを Data Box ディスクにコピーする
> * データの確認

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。
- [チュートリアル:Azure Data Box Disk のインストールと構成](data-box-disk-deploy-set-up.md)を完了していること。
- ディスクは、ロックが解除されてクライアント コンピューターに接続されます。
- ディスクにデータをコピーするために使用するクライアント コンピューターでは、[サポートされているオペレーティング システム](data-box-disk-system-requirements.md##supported-operating-systems-for-clients)が実行されている必要があります。
- データのための目的のストレージの種類が、[サポートされるストレージの種類](data-box-disk-system-requirements.md#supported-storage-types-for-upload)と一致することを確認します。
- [Azure オブジェクトのサイズ制限におけるマネージド ディスク制限](data-box-disk-limits.md#azure-object-size-limits)を確認します。


## <a name="copy-data-to-disks"></a>ディスクにデータをコピーする

ディスクにデータをコピーする前に、次の考慮事項を確認してください。

- データはお客様の責任で、適切なデータ形式に対応するフォルダーにコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用のフォルダーにコピーしてください。 データ形式が適切なフォルダー (ストレージの種類) と一致しない場合、後続の手順で、Azure へのデータのアップロードに失敗します。
- データのコピー中は、そのサイズが [Azure Storage と Data Box Disk の制限](data-box-disk-limits.md)に関するページに記載されたサイズ制限に準拠していることを確認してください。
- Data Box Disk によってアップロードされているデータが、Data Box Disk の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。

   > [!IMPORTANT]
   >  注文の作成時にマネージド ディスクを保管先の 1 つとして指定した場合は、次のセクションが該当します。

- 事前に作成されたすべてのフォルダーにわたり、また、すべての Data Box Disk にわたって 1 つのリソース グループ内で特定の名前を持つマネージド ディスクを 1 つだけ保持できます。 これは、事前に作成されたフォルダーにアップロードされた VHD は一意の名前でなければならないことを意味します。 指定した名前が、リソース グループ内の既存のマネージド ディスクと一致しないようにしてください。 VHD の名前が同じである場合、1 つの VHD のみがその名前を持つマネージド ディスクに変換されます。 その他の VHD は、ステージング ストレージ アカウントにページ BLOB としてアップロードされます。
- VHD は必ず、事前に作成されたフォルダーの 1 つにコピーします。 これらのフォルダー以外または自分で作成したフォルダーに VHD をコピーすると、VHD はマネージド ディスクではなく、ページ BLOB として Azure Storage アカウントにアップロードされます。
- マネージド ディスクを作成するためにアップロードできるのは、容量固定の VHD のみです。 動的 VHD、差分 VHD、VHDX ファイルはサポートされていません。


ご利用のコンピューターから Data Box Disk に接続してデータをコピーするには、次の手順を実行します。

1. ロックを解除したドライブの内容を表示します。 ドライブの事前に作成されたフォルダーとサブフォルダーのリストは、Data Box Disk の注文時に選択したオプションによって異なります。

    |選択された保存先  |ストレージ アカウントの種類|ステージング ストレージ アカウントの種類 |フォルダーとサブフォルダー  |
    |---------|---------|---------|------------------|
    |ストレージ アカウント     |GPv1 または GPv2                 | NA | BlockBlob <br> PageBlob <br> AzureFile        |
    |ストレージ アカウント     |BLOB ストレージ アカウント         | NA | BlockBlob        |
    |マネージド ディスク     |NA | GPv1 または GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |ストレージ アカウント <br> マネージド ディスク     |GPv1 または GPv2 | GPv1 または GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |ストレージ アカウント <br> マネージド ディスク    |BLOB ストレージ アカウント | GPv1 または GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    GPv2 ストレージ アカウントが指定された順序のスクリーンショット例を以下に示します。

    ![ディスク ドライブの内容](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. ブロック BLOB としてインポートする必要があるデータを、*BlockBlob* フォルダーにコピーします。 同様に、VHD/VHDX などのデータを *PageBlob* フォルダーと、*AzureFile* フォルダーにコピーします。

    Azure Storage アカウントには、BlockBlob フォルダー下および PageBlob フォルダー下のサブフォルダーごとにコンテナーが 1 つ作成されます。 BlockBlob フォルダー下のファイルと PageBlob フォルダー下のファイルはすべて、Azure Storage アカウントの既定のコンテナー `$root` にコピーされます。 `$root` コンテナー内のファイルは常にブロック BLOB としてアップロードされます。

   *AzureFile* フォルダー内のフォルダーにファイルをコピーします。 *AzureFile* フォルダー内のサブフォルダーでファイル共有が作成されます。 *AzureFile* フォルダーに直接コピーされたファイルは失敗し、ブロック BLOB としてアップロードされます。

    ルート ディレクトリにファイルやフォルダーが存在する場合は、それらを別のフォルダーに移動してからデータのコピーを開始してください。

    > [!IMPORTANT]
    > すべてのコンテナー、BLOB、およびファイル名は、[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)に準拠する必要があります。 これらの規則に従っていない場合、Azure へのデータのアップロードに失敗します。

3. ファイルをコピーするときに、ファイルが約 4.7 TiB (ブロック BLOB の場合)、約 8 TiB (ページ BLOB の場合)、および約 1 TiB (Azure Files の場合) を超えないようにしてください。 
4. エクスプローラーとの間で、ドラッグ アンド ドロップを使ってデータをコピーできます。 Robocopy などの SMB 互換ファイル コピー ツールを使用して、データをコピーすることもできます。 次の Robocopy コマンドを使用すると、複数のコピー ジョブを開始することができます。

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    次の表は、コマンドのパラメーターとオプションを一覧にしたものです。
    
    |パラメーター/オプション  |説明 |
    |--------------------|------------|
    |source            | コピー元ディレクトリのパスを指定します。        |
    |Destination       | コピー先ディレクトリのパスを指定します。        |
    |/E                  | サブディレクトリをコピーします (空のディレクトリを含む)。 |
    |/MT[:N]             | スレッド数 N のマルチスレッド コピーを作成します。N は 1 から 128 の整数です。 <br>N の既定値は 8 です。        |
    |/R: \<N>             | 失敗したコピーの再試行回数を指定します。 N の既定値は 1,000,000 です (再試行回数 100 万回)。        |
    |/W: \<N>             | 再試行間の待ち時間 (秒) を指定します。 N の既定値は 30 です (待ち時間 30 秒)。        |
    |/NFL                | ファイル名をログに記録しないように指定します。        |
    |/NDL                | ディレクトリ名をログに記録しないように指定します。        |
    |/FFT                | FAT ファイル時間 (2 秒の精度) を想定します。        |
    |/Log:\<ログ ファイル>     | 状態出力をログ ファイルに書き込みます (既存のログ ファイルを上書きします)。         |

    複数のディスクを並列に使用しながら、各ディスクで複数のジョブを実行することができます。

6. ジョブが進行しているときにコピーの状態をチェックします。 次のサンプルは、Data Box Disk にファイルをコピーする robocopy コマンドの出力を示しています。

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    パフォーマンスを最適化するには、データをコピーするときに、次の robocopy パラメーターを使用します。

    |    プラットフォーム    |    ほとんどが小さいファイル (< 512 KB)                           |    ほとんどが中規模のファイル (512 KB ～ 1 MB)                      |    ほとんどが大きいファイル (> 1 MB)                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 Robocopy セッション* <br> セッションあたり 16 スレッド    |    2 Robocopy セッション* <br> セッションあたり 16 スレッド    |    2 Robocopy セッション* <br> セッションあたり 16 スレッド    |
    
    * *各 Robocopy セッションには、最大で 7,000 ディレクトリと 1 億 5,000 万ファイルを含めることができます。* "
    
    >[!NOTE]
    > 上記で推奨されているパラメーターは、社内テストで使用された環境に基づいています。
    
    Robocopy コマンドについて詳しくは、「[Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)」(Robocopy といくつかの例) をご覧ください。

6. コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。 コピー処理中にエラーが発生した場合は、トラブルシューティングのためにログ ファイルをダウンロードしてください。 ログ ファイルは、robocopy コマンドで指定した場所に格納されます。
 
### <a name="split-and-copy-data-to-disks"></a>データを分割してディスクにコピーする

このオプションの手順は、複数のディスクを使用しており、大きなデータセットをそれらのすべてのディスクに分割してコピーする必要がある場合に使用できます。 Data Box 分割コピー ツールは、Windows コンピューター上でデータを分割してコピーするのに役立ちます。

>[!IMPORTANT]
> Data Box 分割コピー ツールでは、お客様のデータの検証も行われます。 Data Box 分割コピー ツールを使用してデータをコピーすると、[検証手順](#validate-data)をスキップできます。
> 分割コピー ツールはマネージド ディスクではサポートされません。

1. Windows コンピューターで、Data Box 分割コピー ツールがダウンロードされ、ローカル フォルダーに抽出されていることを確認します。 このツールは、Windows 用の Data Box Disk ツールセットをダウンロードしたときにダウンロードされました。
2. エクスプローラーを開きます。 Data Box Disk に割り当てられたデータ ソース ドライブと ドライブ文字をメモしておきます。 

     ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. コピーするソース データを特定します。 たとえば、この場合:
    - 以下のブロック BLOB データが特定されました。

         ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - 以下のページ BLOB データが特定されました。

         ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. ソフトウェアが抽出されるフォルダーに移動します。 そのフォルダーで `SampleConfig.json` ファイルを見つけます。 これは、変更して保存できる読み取り専用ファイルです。

   ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. `SampleConfig.json` ファイルを変更します。
 
   - ジョブ名を指定します。 これにより、Data Box Disk にフォルダーが作成され、最終的にこれらのディスクに関連付けられた Azure ストレージ アカウント内のコンテナーになります。 ジョブ名は、Azure コンテナーの名前付け規則に従う必要があります。 
   - ソース パスを指定して `SampleConfigFile.json` のパス形式をメモします。 
   - ターゲット ディスクに対応するドライブ文字を入力します。 データはソース パスから取得され、複数のディスクにコピーされます。
   - ログ ファイルのパスを指定します。 既定では、`.exe` がある現在のディレクトリに送信されます。

     ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. ファイル形式を検証するには、`JSONlint` に移動します。 ファイルを `ConfigFile.json`という名前で保存します。 

     ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. コマンド プロンプト ウィンドウを開きます。 

8. `DataBoxDiskSplitCopy.exe` を実行します。 Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Enter キーを押してスクリプトを続行します。

    ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. データセットを分割してコピーすると、コピー セッションの分割コピー ツールの概要が表示されます。 サンプル出力を次に示します。

    ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. データがターゲット ディスク間で分割されていることを確認します。 
 
    ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    `n:` ドライブの内容をさらに調べると、ブロック BLOB およびページ BLOB 形式データに対応して 2 つのサブフォルダーが作成されていることがわかります。
    
     ![データの分割コピー](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. コピー セッションが失敗した場合、復旧して再開するには、次のコマンドを使用します。

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

分割コピー ツールの使用中にエラーが発生した場合は、[分割コピー ツールのエラーのトラブルシューティングを行う](data-box-disk-troubleshoot-data-copy.md)方法をご覧ください。

データのコピーが完了した後、お客様のデータの検証に進むことができます。 分割コピー ツールを使用した場合は、検証をスキップして (分割コピー ツールで検証も行われます)、次のチュートリアルに進んでください。


## <a name="validate-data"></a>データの検証

データのコピーに分割コピー ツールを使用しなかった場合は、お客様のデータを検証する必要があります。 データを確認するには、次の手順を実行します。

1. ドライブの *DataBoxDiskImport* フォルダーで、チェックサムの検証のために `DataBoxDiskValidation.cmd` を実行します。
    
    ![Data Box Disk 検証ツールの出力](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. 適切なオプションを選択してください。 **オプション 2 を選択して、常にファイルの検証とチェックサムの生成を行うことをお勧めします**。 データのサイズによっては、この手順にしばらく時間がかかることがあります。 スクリプトが完了したら、コマンド ウィンドウを終了します。 検証とチェックサムの生成の間にエラーが発生した場合は、通知が表示され、エラー ログへのリンクも提供されます。

    ![チェックサムの出力](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - 2 回の実行の間にツールをリセットします。
    > - 小さなファイル (KB 単位まで) を含む大きなデータ セットを扱う場合、オプション 1 を使用します。 チェックサムの生成に非常に長い時間がかかり、パフォーマンスが非常に低くなる場合があるので、このオプションではファイルの検証だけを行います。

3. 複数のディスクを使用している場合は、ディスクごとにコマンドを実行します。

検証中にエラーが発生した場合は、[検証エラーのトラブルシューティング](data-box-disk-troubleshoot.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * データを Data Box ディスクにコピーする
> * データ整合性の確認

次のチュートリアルに進み、Data Box Disk を返送する方法および Azure へのデータのアップロードを確認する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に返送する](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>ディスクにデータをコピーする

ご利用のコンピューターから Data Box Disk に接続してデータをコピーするには、次の手順に従います。

1. ロックを解除したドライブの内容を表示します。 ドライブの事前に作成されたフォルダーとサブフォルダーのリストは、Data Box Disk の注文時に選択したオプションによって異なります。
2. 適切なデータ形式に対応するフォルダーにデータをコピーします。 たとえば、非構造化データを *BlockBlob* フォルダーのフォルダーに、VHD または VHDX データを *PageBlob* フォルダーに、ファイルを *AzureFile* にコピーします。 データ形式が適切なフォルダー (ストレージの種類) と一致しない場合、後続の手順で、Azure へのデータのアップロードに失敗します。

    - すべてのコンテナー、BLOB、およびファイルが、[Azure の名前付け規則](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)および [Azure オブジェクトのサイズ制限](data-box-disk-limits.md#azure-object-size-limits)に準拠していることを確認してください。 これらの規則および制限に従っていない場合、Azure へのデータのアップロードに失敗します。     
    - 注文で保管先の 1 つとしてマネージド ディスクがある場合は、[マネージド ディスク](data-box-disk-limits.md#managed-disk-naming-conventions)の命名規則を参照してください。
    - Azure Storage アカウントには、BlockBlob フォルダー下および PageBlob フォルダー下のサブフォルダーごとにコンテナーが 1 つ作成されます。 *BlockBlob* フォルダーの下のファイルと *PageBlob* フォルダーの下のファイルはすべて、Azure Storage アカウントの既定のコンテナー $root にコピーされます。 $root コンテナー内のファイルは、常にブロック BLOB としてアップロードされます。
    - *AzureFile* フォルダー内にサブフォルダーを作成します。 このサブフォルダーは、クラウド内のファイル共有にマップされます。 そのサブフォルダーにファイルをコピーします。 *AzureFile* フォルダーに直接コピーされたファイルは失敗し、ブロック BLOB としてアップロードされます。
    - ルート ディレクトリにファイルやフォルダーが存在する場合は、それらを別のフォルダーに移動してからデータのコピーを開始してください。

3. データをコピーするには、エクスプローラーでのドラッグ アンド ドロップや、Robocopy などの SMB 互換ファイル コピー ツールを使用します。 次のコマンドを使用すると、複数のコピー ジョブを開始することができます。

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。 コピー処理中にエラーが発生した場合は、トラブルシューティングのためにログ ファイルをダウンロードしてください。 ログ ファイルは、robocopy コマンドで指定した場所に格納されます。

複数のディスクを使用しており、大きなデータセットをそれらのすべてのディスクに分割してコピーする必要がある場合は、[分割とコピー](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks)のオプションの手順を使用できます。

### <a name="validate-data"></a>データの検証

次の手順に従って、データを検証します。

1. ドライブの *DataBoxDiskImport* フォルダーで、チェックサムの検証のために `DataBoxDiskValidation.cmd` を実行します。
2. オプション 2 を使用して、ファイルを検証し、チェックサムを生成します。 データのサイズによっては、この手順にしばらく時間がかかることがあります。 検証とチェックサムの生成の間にエラーが発生した場合は、通知が表示され、エラー ログへのリンクも提供されます。

    データ検証の詳細については、「[データの検証](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data)」を参照してください。 検証中にエラーが発生する場合は、[検証エラーのトラブルシューティング](data-box-disk-troubleshoot.md)に関する記事を参照してください。

::: zone-end
