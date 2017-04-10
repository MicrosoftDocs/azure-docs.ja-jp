---
title: "Azure Backup - Azure Import/Export サービスを使用したオフライン バックアップまたは初期シード処理 | Microsoft Docs"
description: "Azure Backup の Azure Import/Export サービスを使用してネットワークからデータを送信する方法について説明します。 この記事では、Azure Import Export サービスの使用による初期バックアップ データのオフライン シード処理について説明します。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2016
ms.author: saurse;nkolli;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 2876f3a7e8e83dc05801d914c7582a4f1fd92e98


---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup でのオフライン バックアップのワークフロー
Azure Backup はさまざまな面で効率性に優れ、Azure への初回完全バックアップ時にネットワークとストレージのコストを抑えます。 初回完全バックアップでは通常、大量のデータが転送されます。その後の差分/増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 Azure Backup では、初回バックアップが圧縮されます。 オフライン シード処理プロセスによって、Azure Backup でディスクを使用し、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。  

Azure Backup のオフライン シード処理プロセスは [Azure Import/Export サービス](../storage/storage-import-export-service.md) と緊密に統合されており、ディスクを使用してデータを Azure に転送できます。 待ち時間が長く、低帯域幅のネットワークで転送する必要がある初回バックアップ データが数 TB (テラバイト) ある場合は、オフライン シード処理ワークフローを使用して、1 台以上のハード ドライブ上にある初回バックアップ コピーを Azure データセンターに発送できます。 この記事では、このワークフローを完了するための手順の概要について説明します。

## <a name="overview"></a>Overview
Azure Backup のオフライン シード処理機能と Azure Import/Export を使用すると、ディスクを使ってオフラインで Azure にデータを簡単にアップロードできます。 ネットワーク経由で完全な初期コピーを転送する代わりに、 *ステージング場所*にバックアップ データを書き込みます。 Azure Import/Export ツールを使用したステージング場所へのコピーが完了すると、このデータは、データの量に応じて 1 台以上の SATA ドライブに書き込まれます。 最終的に、これらのドライブは最も近くにある Azure データセンターに発送されます。

[Azure Backup の更新プログラム 2016 年 8 月 (以降)](http://go.microsoft.com/fwlink/?LinkID=229525) には、AzureOfflineBackupDiskPrep という名前の "*Azure Disk Preparation ツール*" が付属しています。このツールでは、次の操作を行うことができます。

* Azure Import/Export ツールを使用して、Azure Import 用のドライブの準備を容易にする。
* [Azure クラシック ポータル](https://manage.windowsazure.com)で、Azure Import/Export サービスの Azure インポート ジョブを自動的に作成する。以前の Azure Backup では手動で作成する必要がありました。

Azure へのバックアップ データのアップロードが完了した後、Azure Backup は、バックアップ データをバックアップ コンテナーにコピーし、増分バックアップをスケジュールします。

> [!NOTE]
> Azure Disk Preparation ツールを使用するために、Azure Backup の更新プログラム 2016 年 8 月 (以降) を必ずインストールしておきます。ワークフローの手順はすべて、その更新プログラムを使用して実行します。 以前のバージョンの Azure Backup を使用している場合は、この記事の後のセクションで詳しく説明するように、Azure Import/Export ツールを使って SATA ドライブを準備できます。
>
>

## <a name="prerequisites"></a>前提条件
* [Azure Import/Export のワークフローについて理解を深めます](../storage/storage-import-export-service.md)。
* ワークフローを開始する前に、次のことを確認します。
  * Azure Backup コンテナーが作成されている。
  * コンテナー資格情報がダウンロードされている。
  * Windows Server、Windows クライアント、または System Center Data Protection Manager サーバーに Azure Backup エージェントがインストール済みであり、Azure Backup コンテナーにコンピューターが登録されている。
* [Azure 発行ファイル設定をダウンロード](https://manage.windowsazure.com/publishsettings) します。
* ステージング場所を準備します。 これには、コンピューター上のネットワーク共有や追加ドライブを使用できます。 ステージング場所は一時ストレージであり、このワークフロー中に一時的に使用されます。 ステージング場所には、初期コピーを保持するのに十分なディスク領域があることを確認します。 たとえば、500 GB のファイル サーバーをバックアップする場合は、ステージング領域が 500 GB 以上あることを確認します (圧縮処理により、使用量はこれよりも少なくなります)。
* サポートされているドライブを使用していることを確認します。 Import/Export サービスで使用できるのは、3.5 インチ SATA II/III ハード ドライブのみです。 8 TB を超えるハード ドライブはサポートされていません。 大部分のコンピューターには、SATA II/III USB アダプターを使用して SATA II/III ディスクを外部接続できます。 サービスでサポートされている最新のドライブについては、Azure Import/Export のドキュメントを参照してください。
* SATA ドライブ ライターが接続されているコンピューターで BitLocker を有効にします。
* [Azure Import/Export ツールをダウンロード](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) します。 Azure Backup の更新プログラム 2016 年 8 月 (以降) をインストールした場合、この手順は不要です。

## <a name="workflow"></a>ワークフロー
このセクションの情報は、オフライン バックアップ ワークフローを完了するためのものなので、このデータを Azure データセンターに配信したり、Azure Storage にアップロードしたりできます。 インポート サービスやプロセスの他の側面について質問がある場合は、上記で参照した [サービスの概要に関するページ](../storage/storage-import-export-service.md) を参照してください。

### <a name="initiate-offline-backup"></a>オフライン バックアップを開始する
1. バックアップのスケジュールを設定するとき、次の画面が表示されます (Windows Server、Windows クライアント、または System Center Data Protection Manager)。

    ![Import screen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    System Center Data Protection Manager の対応する画面を次に示します。 <br/>
    ![DPM のインポート画面](./media/backup-azure-backup-import-export/dpmoffline.png)

    この画面では、次の内容を入力します。

    * **ステージングの場所**: 初回バックアップ コピーが書き込まれる一時的なストレージの場所。 これには、ネットワーク共有またはローカル コンピューター上の場所を使用できます。 コピー用コンピューターとソース コンピューターが異なる場合は、ステージング場所の完全なネットワーク パスを指定することをお勧めします。
    * **Azure インポート ジョブ名**: ディスクを使用して Azure に送信されたデータを、Azure インポート サービスと Azure Backup が追跡するときに使用する一意の名前。
    * **Azure 発行設定**: サブスクリプション プロファイルに関する情報が含まれる XML ファイル。 サブスクリプションに関連付けられている、セキュリティで保護された資格情報も含まれています。 [ファイルをダウンロード](https://manage.windowsazure.com/publishsettings)することができます。 発行設定ファイルへのローカル パスを指定します。
    * **Azure サブスクリプション ID**: Azure インポート ジョブを開始するサブスクリプションの Azure サブスクリプション ID。 複数の Azure サブスクリプションを使用している場合は、インポート ジョブに関連付けるサブスクリプションの ID を使用します。
    * **Azure Storage アカウント**: 指定された Azure サブスクリプションのクラシック タイプのストレージ アカウント。Azure インポート ジョブに関連付けられます。
    * **Azure ストレージ コンテナー**: このジョブのデータをインポートする先の Azure ストレージ アカウントのストレージ BLOB の名前。

    > [!NOTE]
    > バックアップの [Azure Portal](https://portal.azure.com) から Azure Recovery Services コンテナーにサーバーを登録していて、クラウド ソリューション プロバイダー (CSP) サブスクリプションがない場合は、引き続き Azure Portal からクラシック タイプのストレージ アカウントを作成して、オフライン バックアップ ワークフローで使用できます。
    >
    >

     後の手順でもう一度入力する必要があるため、この情報はすべて保存しておいてください。 Azure Disk Preparation ツールを使用してディスクを準備した場合は、" *ステージングの場所* " のみが必須です。    
2. ワークフローが完了したら、Azure Backup 管理コンソールで **[今すぐバックアップ]** を選択して、オフラインのバックアップ コピーを開始します。 初期バックアップが、この手順の一部としてステージング領域に書き込まれます。

    ![今すぐバックアップ](./media/backup-azure-backup-import-export/backupnow.png)

    System Center Data Protection Manager で対応するワークフローを完了するには、**[保護グループ]** を右クリックし、**[回復ポイントの作成]** を選択します。 次に、 **[オンライン保護]** オプションを選択します。

    ![DPM backup now](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    操作が完了すると、ステージング場所を使用してディスクを準備できるようになります。

    ![Backup progress](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>Azure Disk Preparation ツールを使用して SATA ドライブを準備し、Azure インポート ジョブを作成する
Azure Disk Preparation ツールは、次のパスにある Recovery Services エージェント (更新プログラム 2016 年 8 月以降) のインストール ディレクトリに含まれています。

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. このディレクトリに移動し、 **AzureOfflineBackupDiskPrep** ディレクトリを、準備するドライブがマウントされているコピー用コンピューターにコピーします。 コピー用コンピューターについて、次を確認します。

    * コピー用コンピューターが、 **オフライン バックアップの開始** ワークフロー中に指定されたのと同じネットワーク パスを使用して、オフライン シード処理ワークフローのステージング場所にアクセスできる。
    * コンピューターで BitLocker が有効になっている。
    * コンピューターで Azure Portal にアクセスできる。

    必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。
2. コピー用コンピューターで、Azure Disk Preparation ツールのディレクトリを現在のディレクトリとして使用して管理者特権のコマンド プロンプトを開き、次のコマンドを実行します。

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | パラメーター | 説明 |
    | --- | --- |
    | s:&lt;*ステージング場所のパス*&gt; |必須。**オフライン バックアップの開始**ワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; |オプション。**オフライン バックアップの開始**ワークフローで入力した **Azure 発行設定**ファイルへのパスを指定します。 |

    > [!NOTE]
    > コピー用コンピューターとソース コンピューターが異なる場合は、&lt;PublishSettingsFile へのパス&gt;の値を必ず入力する必要があります。
    >
    >

    コマンドを実行すると、準備する必要があるドライブに対応する Azure インポート ジョブを選択するよう求められます。 指定されたステージング場所に関連付けられているインポート ジョブが 1 つのみの場合は、次のような画面が表示されます。

    ![Azure Disk Preparation tool input](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. Azure への転送を準備する、マウント済みディスクのドライブ文字を入力します (末尾のコロンを除く)。 メッセージが表示されたら、ドライブをフォーマットすることを確認します。

    これにより、バックアップ データでのディスクの準備が開始されます。 指定したディスクにバックアップ データを格納するための領域が十分にない場合は、メッセージが表示されます。その場合は、追加のディスクを接続する必要があります。 <br/>

    ツールが適切に実行されたら、指定した 1 つ以上のディスクを Azure に発送する準備は完了です。 さらに、 **オフライン バックアップの開始** ワークフローで指定した名前のインポート ジョブが Azure クラシック ポータルに作成されます。 最後に、ディスクの送付先である Azure データセンターの住所と、Azure クラシック ポータルでインポート ジョブを特定するためのリンクが表示されます。

    ![Azure disk preparation complete](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. 表示された住所にディスクを発送し、後で参照できるように追跡番号を控えておきます。<br/>

5. ツールに表示されたリンクに移動すると、 **オフライン バックアップの開始** ワークフロー中に指定した Azure ストレージ アカウントが表示されます。 このストレージ アカウントの **[インポート/エクスポート]** タブで、新しく作成されたインポート ジョブを確認できます。

    ![Created import job](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. ページ下部の **[発送情報]** をクリックすると、次の画面に示すように、連絡先の詳細が更新されます。 インポート ジョブが完了した後、Microsoft はこの情報を使用してディスクを返送します。

    ![連絡先情報](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. 次の画面で発送に関する詳細情報を入力します。 **[配送業者]** と **[問い合わせ番号]** に、Azure データセンターに発送したディスクの詳細情報を入力します。

    ![[発送情報]](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>ワークフローを完了する
インポート ジョブが完了すると、初回バックアップ データをストレージ アカウントで使用できます。 その後、Recovery Services エージェントは、データの内容を、このアカウントから、使用可能なバックアップ コンテナーまたは Recovery Services コンテナーにコピーします。 次回のスケジュール バックアップでは、Azure Backup エージェントは、初回バックアップ コピーに対する増分バックアップを実行します。

> [!NOTE]
> 以降のセクションは、Azure Disk Preparation ツールを利用できない、以前の Azure Backup のユーザーに適用されます。
>
>

### <a name="prepare-a-sata-drive"></a>SATA ドライブを準備する
1. [Microsoft Azure Import/Export ツール](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) をコピー用コンピューターにダウンロードします。 ステージング場所が、次の一連のコマンドを実行するコンピューターからアクセスできることを確認します。 必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。

2. WAImportExport.zip ファイルを解凍します。 WAImportExport ツールを実行すると、SATA ドライブがフォーマットされ、SATA ドライブにバックアップ データが書き込まれて暗号化されます。 次のコマンドを実行する前に、BitLocker がコンピューターで有効になっていることを確認します。 <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > Azure Backup の更新プログラム 2016 年 8 月 (以降) をインストールした場合は、入力したステージング場所が、 **[今すぐバックアップ]** 画面のものと同じであること、また、このステージング場所に AIB と基本 BLOB が含まれていることを確認します。
    >
    >

| パラメーター | Description |
| --- | --- |
| /j:<*ジャーナル ファイル*> |ジャーナル ファイルへのパス。 各ドライブには、ジャーナル ファイルが必ず 1 つあります。 ターゲット ドライブには、ジャーナル ファイルは存在しません。 ジャーナル ファイルの拡張子は .jrn で、このコマンドの実行の一部として作成されます。 |
| /id:<*セッション ID*> |セッション ID はコピー セッションを識別します。 中断されたコピー セッションを正しく復元するために使用されます。 コピー セッションでコピーされるファイルは、ターゲット ドライブ上のセッション ID の名前が付いたディレクトリに格納されます。 |
| /sk:<*ストレージ アカウント キー*> |データのインポート先ストレージ アカウントのアカウント キー。 キーは、バックアップ ポリシー/保護グループ作成時に入力されたものと同じにする必要があります。 |
| /BlobType |BLOB の種類。 このワークフローは、**PageBlob** が指定されている場合にのみ成功します。 これは既定のオプションではありません。このコマンドに記述する必要があります。 |
| /t:<*ターゲット ドライブ文字*> |現在のコピー セッションのターゲットのハード ドライブのドライブ文字 (末尾のコロンは含めません)。 |
| /format |ドライブをフォーマットするオプション。 ドライブをフォーマットする必要がある場合は、このパラメーターを指定します。それ以外の場合は省略します。 ツールによってドライブがフォーマットされる前に、確認を求めるメッセージがコンソールに表示されます。 確認メッセージを非表示にするには、/silentmode パラメーターを指定します。 |
| /encrypt |ドライブを暗号化するオプション。 ドライブが BitLocker で暗号化されていないため、ツールで暗号化する必要がある場合は、このパラメーターを指定します。 ドライブが既に BitLocker で暗号化されている場合は、このパラメーターを省略して /bk パラメーターを指定し、既存の BitLocker キーを入力します。 /format パラメーターを指定する場合も、/encrypt パラメーターを指定する必要があります。 |
| /srcdir:<*ソース ディレクトリ*> |ターゲット ドライブにコピーされるファイルを含むソース ディレクトリ。 ディレクトリ名は相対パスではなく完全パスで指定してください。 |
| /dstdir:<*コピー先 BLOB 仮想ディレクトリ*> |Azure ストレージ アカウントのコピー先の仮想ディレクトリへのパス。 コピー先の仮想ディレクトリや BLOB を指定する場合は、有効なコンテナー名を使用してください。 コンテナー名は小文字にする必要があります。  このコンテナー名は、バックアップ ポリシー/保護グループの作成時に入力したコンテナー名である必要があります。 |

> [!NOTE]
> ジャーナル ファイルは WAImportExport フォルダーに作成され、ワークフロー全体の情報を収集します。 このファイルは、Azure Portal でインポート ジョブを作成するときに必要です。
>
>

  ![PowerShell の出力](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>Azure Portal でインポート ジョブを作成する
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)でストレージ アカウントに移動し、作業ウィンドウで **[インポート/エクスポート]**、**[インポート ジョブの作成]** の順にクリックします。

    ![Import/export tab in the Azure portal](./media/backup-azure-backup-import-export/azureportal.png)

2. ウィザードのステップ 1. で、ドライブの準備ができており、ドライブのジャーナル ファイルが使用可能であることを示します。

3. ウィザードのステップ 2. で、このインポート ジョブの担当者の連絡先情報を入力します。

4. ステップ 3. で、前のセクションで取得したドライブのジャーナル ファイルをアップロードします。

5. ステップ 4. で、バックアップ ポリシー/保護グループの作成時に入力したインポート ジョブにわかりやすい名前を入力します。 名前に含めることができるのは、アルファベットの小文字、数字、ハイフン、アンダースコアだけです。また、先頭の文字はアルファベットにします。スペースを含めることはできません。 選択した名前は、ジョブの進行中と完了後にそのジョブを追跡するために使用されます。

6. 次に、データセンターのリージョンを一覧から選択します。 データセンターのリージョンに、パッケージの発送先となるデータセンターとその住所が示されます。

    ![Select datacenter region](./media/backup-azure-backup-import-export/dc.png)

7. ステップ 5. で、返送に利用する宅配業者を一覧から選択し、業者のアカウント番号を入力します。 Microsoft では、インポート ジョブの完了後、このアカウントを使用してドライブを返送します。

8. ディスクを発送して問い合わせ番号を入力し、出荷の状態を追跡します。 ディスクがデータセンターに到着すると、ストレージ アカウントにコピーされ、状態が更新されます。

    ![Completed status](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>ワークフローを完了する
ストレージ アカウントで初回バックアップ データが使用可能になると、Microsoft Azure Recovery Services エージェントは、データの内容を、このアカウントから、使用可能なバックアップ コンテナーまたは Recovery Services コンテナーにコピーします。 次回のスケジュール バックアップで、Azure Backup エージェントは初期バックアップ コピーの上に増分バックアップを実行します。

## <a name="next-steps"></a>次のステップ
* Azure Import/Export ワークフローについて質問がある場合は、「 [Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage/storage-import-export-service.md)」を参照してください。
* ワークフローについて質問がある場合は、Azure Backup [FAQ](backup-azure-backup-faq.md) のオフライン バックアップのセクションを参照してください。



<!--HONumber=Nov16_HO3-->


