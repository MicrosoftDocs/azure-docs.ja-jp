---
title: Azure Backup - DPM と Azure Backup Server のオフライン バックアップ
description: Azure Backup の Azure Import/Export サービスを使用してネットワークからデータを送信する方法について説明します。 この記事では、Azure Import Export サービスの使用による初期バックアップ データのオフライン シード処理について説明します。
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 8a8571230b24d76482c505ec22d6faaa0caec5e6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466715"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>DPM と Azure Backup Server のオフライン バックアップのワークフロー
Azure Backup はさまざまな面で効率性に優れ、Azure への初回完全バックアップ時にネットワークとストレージのコストを抑えます。 初回完全バックアップでは通常、大量のデータが転送されます。その後の差分/増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 Azure Backup では、初回バックアップが圧縮されます。 オフライン シード処理プロセスによって、Azure Backup でディスクを使用し、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。

Azure Backup のオフライン シード処理プロセスは [Azure Import/Export サービス](../storage/common/storage-import-export-service.md) と緊密に統合されており、ディスクを使用してデータを Azure に転送できます。 待ち時間が長く、低帯域幅のネットワークで転送する必要がある初回バックアップ データが数 TB (テラバイト) ある場合は、オフライン シード処理ワークフローを使用して、1 台以上のハード ドライブ上にある初回バックアップ コピーを Azure データセンターに発送できます。 この記事では、DPM と Azure Backup Server のこのワークフローを完了するための概要と詳しい手順について説明します。

> [!NOTE]
> Microsoft Azure Recovery Services (MARS) エージェントのオフライン バックアップのプロセスは、System Center DPM と Azure Backup Server とは異なります。 MARS エージェントでのオフライン バックアップの使用については、[こちらの記事](backup-azure-backup-import-export.md)を参照してください。 Azure Backup エージェントを使用して行われたシステム状態のバックアップでは、オフライン バックアップはサポートされません。
>

## <a name="overview"></a>概要
Azure Backup のオフライン シード処理機能と Azure Import/Export を使用すると、ディスクを使ってオフラインで Azure にデータを簡単にアップロードできます。 オフライン バックアップ プロセスには次の手順があります。

> [!div class="checklist"]
> * バックアップ データは、ネットワーク経由で送信されずに "*ステージング場所*" に書き込まれます
> * *AzureOfflineBackupDiskPrep*ユーティリティを使用して、"*ステージング場所*" のデータが 1 つまたは複数の SATA ディスクに書き込まれます
> * Azure インポート ジョブがユーティリティによって自動的に作成されます
> * SATA ドライブが最寄りの Azure データセンターに送付されます
> * Azure へのバックアップ データのアップロードが完了した後、Azure Backup は、バックアップ データをバックアップ コンテナーにコピーし、増分バックアップをスケジュールします。

## <a name="supported-configurations"></a>サポートされている構成
オフライン バックアップは、オンプレミスから Microsoft Cloud にデータをオフサイトでバックアップする Azure Backup のすべてのデプロイ モデルでサポートされます。 次のバックアップがあります

> [!div class="checklist"]
> * Microsoft Azure Recovery Services (MARS) エージェントまたは Azure Backup エージェントでのファイルとフォルダーのバックアップ。
> * System Center Data Protection Manager (SC DPM) でのすべてのワークロードとファイルのバックアップ
> * Microsoft Azure Backup Server でのすべてのワークロードとファイルのバックアップ <br/>

## <a name="prerequisites"></a>前提条件
オフライン バックアップ ワークフローを開始する前に、次の前提条件を満たしていることをご確認ください
* [Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)が作成されていること。 作成するには、[こちらの記事](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)の手順をご覧ください
* Azure Backup エージェント、Azure Backup Server または SC DPM が Windows Server/Windows クライアントに適宜インストールされ、Recovery Services コンテナーにコンピューターが登録されていること。 [最新バージョンの Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) のみが使用されていることを確認します。
* データのバックアップを計画しているコンピューターで [Azure 発行設定ファイルをダウンロード](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)します。 発行設定ファイルをダウンロードするサブスクリプションは、Recovery Services コンテナーを含むサブスクリプションとは別のサブスクリプションにすることができます。 サブスクリプションがソブリン Azure クラウドにある場合は、Azure 発行設定ファイルのダウンロードに適した次のリンクを使用します。

    | ソブリン クラウドのリージョン | Azure 発行設定ファイルのリンク |
    | --- | --- |
    | 米国 | [リンク](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 中国 | [リンク](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* "*クラシック*" デプロイ モデルの Azure Storage アカウントが、次に示すように発行設定ファイルをダウンロードしたサブスクリプションに作成されていること。

  ![クラシック ストレージ アカウントの作成](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* ステージング場所 (ネットワーク共有、または最初のコピーを保持するのに十分なディスク領域がある内部または外部コンピューター上の追加ドライブ) が作成されていること。 ステージング場所には、初期コピーを保持するのに十分なディスク領域があることを確認します。 たとえば、500 GB のファイル サーバーをバックアップする場合は、ステージング領域が 500 GB 以上あることを確認します (圧縮処理により、使用量はこれよりも少なくなります)。
* Azure に送信されるディスクに関して、2.5 インチ SSD、または 2.5 インチか 3.5 インチの SATA II/III 内蔵ハード ドライブが使用されていることを確認します。 最大 10 TB のハード ドライブを使用できます。 サービスでサポートされている最新のドライブについては、[Azure Import/Export サービスのドキュメント](../storage/common/storage-import-export-requirements.md#supported-hardware)をご覧ください。
* SATA ドライブは、"*ステージング場所*" から SATA ドライブへのバックアップ データのコピーが行われるコンピューター ("*コピー用コンピューター*" と呼ばれます) に接続されている必要があります。 "*コピー用コンピューター*" で BitLocker が有効になっていることを確認します

## <a name="workflow"></a>ワークフロー
このセクションの情報は、オフライン バックアップ ワークフローを完了するためのものなので、このデータを Azure データセンターに配信したり、Azure Storage にアップロードしたりできます。 インポート サービスやプロセスの他の側面について質問がある場合は、上記で参照した [サービスの概要に関するページ](../storage/common/storage-import-export-service.md) を参照してください。

### <a name="initiate-offline-backup"></a>オフライン バックアップを開始する
1. バックアップのスケジュールを設定するとき、次の画面が表示されます (Windows Server、Windows クライアント、または System Center Data Protection Manager)。

    ![Import screen](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    System Center Data Protection Manager の対応する画面を次に示します。 <br/>
    ![SC DPM および Azure Backup Server のインポート画面](./media/backup-azure-backup-import-export/dpmoffline.png)

    この画面では、次の内容を入力します。

   * **ステージングの場所**:初回バックアップ コピーが書き込まれる一時的なストレージの場所。 ステージング場所には、ネットワーク共有またはローカル コンピューター上の場所を使用できます。 コピー用コンピューターとソース コンピューターが異なる場合は、ステージング場所の完全なネットワーク パスを指定することをお勧めします。
   * **Azure インポート ジョブ名**:ディスクを使用して Azure に送信されたデータを、Azure インポート サービスと Azure Backup が追跡するときに使用する一意の名前。
   * **Azure 発行設定**:発行設定ファイルへのローカル パスを指定します。
   * **Azure サブスクリプション ID**:Azure 発行設定ファイルをダウンロードしたサブスクリプションの Azure サブスクリプション ID。
   * **Azure Storage アカウント**:Azure 発行設定ファイルに関連付けられる Azure サブスクリプションのストレージ アカウントの名前。
   * **Azure ストレージ コンテナー**:バックアップ データをインポートする先の Azure ストレージアカウントのストレージ BLOB の名前。

     ディスクの準備に必要であるため、指定した "*ステージング場所*" と "*Azure インポート ジョブ名*" を保存します。  

2. ワークフローを完了し、オフラインのバックアップ コピーを開始するには、Azure Backup エージェント管理コンソールで **[今すぐバックアップ]** をクリックします。 初期バックアップが、この手順の一部としてステージング領域に書き込まれます。

    ![今すぐバックアップ](./media/backup-azure-backup-import-export/backupnow.png)

    System Center Data Protection Manager または Azure Backup Server で対応するワークフローを完了するには、 **[保護グループ]** を右クリックし、 **[回復ポイントの作成]** オプションを選択します。 次に、 **[オンライン保護]** オプションを選択します。

    ![SC DPM および Azure Backup Server の [今すぐバックアップ]](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    操作が完了すると、ステージング場所を使用してディスクを準備できるようになります。

    ![Backup progress](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA ドライブの準備と Azure への発送
*AzureOfflineBackupDiskPrep* ユーティリティを使用して、最寄りの Azure データセンターに送る SATA ドライブを準備します。 このユーティリティは、次のパスの Recovery Services エージェントのインストール ディレクトリにあります。

*\\Microsoft Azure Recovery Services Agent\\Utils\\*

1. このディレクトリに移動し、**AzureOfflineBackupDiskPrep** ディレクトリを、準備する SATA ドライブが接続されているコピー用コンピューターにコピーします。 コピー用コンピューターについて、以下を確認します。

   * コピー用コンピューターが、 **オフライン バックアップの開始** ワークフロー中に指定されたのと同じネットワーク パスを使用して、オフライン シード処理ワークフローのステージング場所にアクセスできる。
   * コピー用コンピューターで BitLocker が有効になっている。
   * コピー用コンピューターで Azure Portal にアクセスできる。

     必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。

     > [!IMPORTANT]
     > ソース コンピューターが仮想マシンの場合は、別の物理サーバーまたはクライアント マシンをコピー用コンピューターとして使用する必要があります。


2. コピー用コンピューターで、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して管理者特権でのコマンド プロンプトを開き、次のコマンドを実行します。

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | パラメーター | 説明 |
    | --- | --- |
    | s:&lt;*ステージング場所のパス*&gt; |必須。**オフライン バックアップの開始**ワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; |オプション。**オフライン バックアップの開始**ワークフローで入力した **Azure 発行設定**ファイルへのパスを指定します。 |

    > [!NOTE]
    > コピー用コンピューターとソース コンピューターが異なる場合は、&lt;Azure PublishSettingsFile へのパス&gt;の値を必ず入力する必要があります。
    >
    >

    コマンドを実行すると、準備する必要があるドライブに対応する Azure インポート ジョブを選択するよう求められます。 指定されたステージング場所に関連付けられているインポート ジョブが 1 つのみの場合は、次のような画面が表示されます。

    ![Azure Disk Preparation tool input](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Azure への転送を準備する、マウント済みディスクのドライブ文字を入力します (末尾のコロンを除く)。 メッセージが表示されたら、ドライブをフォーマットすることを確認します。

    これにより、ディスクとバックアップ データのコピーの準備が開始されます。 指定したディスクにバックアップ データを格納するための領域が十分にない場合は、メッセージが表示されます。その場合は、追加のディスクを接続する必要があります。 <br/>

    ツールが適切に実行されたら、指定した 1 つ以上のディスクを Azure に発送する準備は完了です。 さらに、**オフライン バックアップの開始**ワークフローで指定した名前のインポート ジョブが Azure に作成されます。 最後に、ディスクの送付先である Azure データセンターの住所が表示されます。

    ![Azure disk preparation complete](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. コマンドの実行の最後にも、次に示すように発送情報を更新するオプションが表示されます。

    ![発送情報の更新オプション](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. すぐに詳細を入力することができます。 このツールでは、一連の入力に関連するプロセスがガイドされます。 ただし、追跡番号やその他の発送関連の詳細などの情報がない場合は、セッションを終了することができます。 発送の詳細を後で更新する手順をこの記事で示します。

6. 表示された住所にディスクを発送し、後で参照できるように追跡番号を控えておきます。

   > [!IMPORTANT]
   > 2 つの Azure インポート ジョブが同じ追跡番号を持つことはできません。 単一の Azure インポート ジョブでユーティリティによって準備されたドライブが単一のパッケージで発送されることと、パッケージに 1 つの一意の追跡番号があることを確認します。 単一パッケージ内の**異なる** Azure インポート ジョブの一部として準備されたドライブは結合しないでください。

5. 追跡番号情報がある場合は、インポート ジョブの完了を待機しているソース コンピューターに移動し、管理者特権でのコマンド プロンプトで *AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して次のコマンドを実行します。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   オプションで、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して、"*コピー用コンピューター*" などの別のコンピューターから次のコマンドを実行することもできます。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | パラメーター | 説明 |
    | --- | --- |
    | u: | Azure インポート ジョブの発送詳細の更新に使用される必須の入力 |
    | s:&lt;*ステージング場所のパス*&gt; | ソース コンピューターでコマンドを実行していない場合に必須の入力。 **オフライン バックアップの開始**ワークフローで入力したステージング場所へのパスを指定するために使用します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; | ソース コンピューターでコマンドを実行していない場合に必須の入力。 **オフライン バックアップの開始**ワークフローで入力した **Azure 発行設定**ファイルへのパスを指定するために使用します。 |

    ユーティリティは、ソース コンピューターが待機しているインポート ジョブまたはコマンドが別のコンピューターで実行されているときにステージング場所に関連付けられているインポート ジョブを自動的に検出します。 次に示すように、一連の入力を通じて発送情報を更新するオプションが提供されます。

    ![発送情報の入力](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. すべての入力を指定したら、詳細情報を十分に確認し、「*yes*」と入力して指定した発送情報をコミットします。

    ![発送情報の確認](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. 発送情報が正常に更新されると、入力した発送情報の詳細が格納されるローカルの場所がユーティリティによって次のように提供されます

    ![発送情報の格納](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > *AzureOfflineBackupDiskPrep* ユーティリティを使用して発送情報を指定してから 2 週間以内にドライブが Azure データセンターに届くようにしてください。 そうしないと、ドライブが処理されないことがあります。  

上記の手順を完了すると、Azure データセンターはドライブを受け取る準備ができ、さらにドライブからユーザーが作成したクラシック タイプの Azure ストレージ アカウントにバックアップ データを転送する処理を行います。

### <a name="time-to-process-the-drives"></a>ドライブの処理の所要時間
Azure インポート ジョブの処理にかかる時間は、運送時間、ジョブの種類、コピーするデータの種類とサイズ、用意されたディスクのサイズなど、さまざまな要素によって変わります。 Azure Import/Export サービスに SLA はありませんが、ディスクの到着後 7 日から 10 日以内に Azure ストレージ アカウントへのバックアップ データ コピーが完了するよう努力しております。 次のセクションでは、Azure インポート ジョブの状態を監視する方法について詳しく説明します。

### <a name="monitoring-azure-import-job-status"></a>Azure インポート ジョブの状態の監視
ストレージ アカウントにコピーするドライブが輸送中または Azure データセンターに保管されている間、ソース コンピューター上の Azure Backup エージェント、SC DPM または Azure Backup Server コンソールには、スケジュールされたバックアップについて次のジョブ状態が表示されます。

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

インポート ジョブの状態を確認するには、次の手順に従います。
1. ソース コンピューターで管理者特権でのコマンド プロンプトを開き、次のコマンドを実行します。

     `AzureOfflineBackupDiskPrep.exe u:`

2.  出力に、次に示すようにインポート ジョブの現在の状態が示されます。

    ![インポート ジョブの状態の確認](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Azure インポート ジョブの各種の状態について詳しくは、[こちらの記事](../storage/common/storage-import-export-view-drive-status.md)をご覧ください

### <a name="complete-the-workflow"></a>ワークフローを完了する
インポート ジョブが完了すると、初回バックアップ データをストレージ アカウントで使用できます。 次回のスケジュール バックアップでは、Azure Backup は次のようにストレージ アカウントのデータのコンテンツを Recovery Services コンテナーにコピーします。

   ![Recovery Services コンテナーへのデータのコピー](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

次回のスケジュール バックアップでは、Azure Backup は、初回バックアップ コピーに対する増分バックアップを実行します。

## <a name="next-steps"></a>次の手順
* Azure Import/Export ワークフローについて質問がある場合は、「 [Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage/common/storage-import-export-service.md)」を参照してください。
* ワークフローについて質問がある場合は、Azure Backup [FAQ](backup-azure-backup-faq.md) のオフライン バックアップのセクションを参照してください。
