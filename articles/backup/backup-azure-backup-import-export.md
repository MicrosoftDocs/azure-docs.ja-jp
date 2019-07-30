---
title: Azure Backup - Azure Import/Export サービスを使用したオフライン バックアップまたは初期シード処理
description: Azure Backup の Azure Import/Export サービスを使用してネットワークからデータを送信する方法について説明します。 この記事では、Azure Import Export サービスの使用による初期バックアップ データのオフライン シード処理について説明します。
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: e852e1595be5b564bd1a6326d41115496284506f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466788"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup でのオフライン バックアップのワークフロー
Azure Backup はさまざまな面で効率性に優れ、Azure への初回完全バックアップ時にネットワークとストレージのコストを抑えます。 初回完全バックアップでは通常、大量のデータが転送されます。その後の差分/増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 オフライン シード処理プロセスにより、Azure Backup はディスクを使ってオフライン バックアップ データを Azure にアップロードすることができます。

Azure Backup のオフライン シード処理プロセスは [Azure Import/Export サービス](../storage/common/storage-import-export-service.md) と緊密に統合されており、ディスクを使用して初期バックアップ データを Azure に転送できます。 待ち時間が長く、低帯域幅のネットワークで転送する必要がある初回バックアップ データが数 TB (テラバイト) ある場合は、オフライン シード処理ワークフローを使用して、1 台以上のハード ドライブ上にある初回バックアップ コピーを Azure データセンターに発送できます。 次の図では、ワークフローの手順の概要を示します。

  ![オフラインでのインポート ワークフロー プロセスの概要](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

オフライン バックアップ プロセスには次の手順があります。

1. ネットワーク経由でバックアップ データを送信する代わりに、ステージングの場所にバックアップ データを書き込みます。
2. AzureOfflineBackupDiskPrep ユーティリティを使って、ステージングの場所のデータを、1 つまたは複数の SATA ディスクに書き込みます。
3. 準備作業の一環として、AzureOfflineBackupDiskPrep ユーティリティは Azure Import ジョブを作成します。 SATA ドライブを最も近い Azure データセンターに送り、インポート ジョブを参照してアクティビティを接続します。
4. Azure データセンターでは、ディスク上のデータが Azure ストレージ アカウントにコピーされます。
5. Azure Backup によってストレージ アカウントから Recovery Services コンテナーにバックアップ データがコピーされ、増分バックアップがスケジュールされます。

## <a name="supported-configurations"></a>サポートされている構成 
Azure Backup の次の機能またはワークロードは、オフライン バックアップの使用をサポートします。

> [!div class="checklist"]
> * Microsoft Azure Recovery Services (MARS) エージェント (Azure Backup エージェントとも呼ばれます) によるファイルとフォルダーのバックアップ。 
> * System Center Data Protection Manager (SC DPM) でのすべてのワークロードとファイルのバックアップ 
> * Microsoft Azure Backup Server でのすべてのワークロードとファイルのバックアップ <br/>

   > [!NOTE]
   > Azure Backup エージェントを使用して行われたシステム状態のバックアップでは、オフライン バックアップはサポートされません。 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>前提条件

  > [!NOTE]
  > 次の前提条件およびワークフローは、[最新の MARS エージェント](https://aka.ms/azurebackup_agent)を使ったファイルとフォルダーのオフライン バックアップにのみ適用されます。 System Center DPM または Azure Backup Server を使ってワークロードのオフライン バックアップを実行する場合は、[こちらの記事](backup-azure-backup-server-import-export-.md)をご覧ください。 

オフライン バックアップ ワークフローを開始する前に、次の前提条件を完了します。 
* [Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)を作成します。 コンテナーの作成手順については、[こちらの記事](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)をご覧ください
* [最新バージョンの Azure Backup エージェント](https://aka.ms/azurebackup_agent)だけが Windows Server/Windows クライアントにインストールされ、Recovery Services コンテナーにコンピューターが登録されていることを確認します。
* Azure Backup エージェントを実行しているコンピューターには Azure PowerShell 3.7.0 が必要です。 [バージョン 3.7.0 の Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017) をダウンロードしてインストールすることをお勧めします。
* Azure Backup エージェントを実行しているコンピューターで、Microsoft Edge または Internet Explorer 11 がインストールされ、JavaScript が有効になっていることを確認します。 
* Recovery Services コンテナーと同じサブスクリプションを使って、Azure ストレージ アカウントを作成します。 
* Azure Active Directory アプリケーションを作成するのに[必要なアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md)があることを確認します。 オフライン バックアップ ワークフローは、Azure ストレージ アカウントに関連付けられているサブスクリプションに Azure Active Directory アプリケーションを作成します。 アプリケーションの目的は、オフライン バックアップ ワークフローに必要な、Azure インポート サービスに対するセキュリティで保護されて範囲を制限されたアクセスを、Azure Backup に提供することです。 
* Azure ストレージ アカウントを含むサブスクリプションに Microsoft.ImportExport リソース プロバイダーを登録します。 リソース プロバイダーを登録するには:
    1. メイン メニューで **[サブスクリプション]** をクリックします。
    2. 複数のサブスクリプションをサブスクライブしている場合は、オフライン バックアップに使っているサブスクリプションを選びます。 使っているサブスクリプションが 1 つだけの場合は、そのサブスクリプションが表示されます。
    3. サブスクリプションのメニューで、 **[リソース プロバイダー]** をクリックしてプロバイダーの一覧を表示します。
    4. プロバイダーの一覧で、Microsoft.ImportExport まで下にスクロールします。 状態が [NotRegistered]\(未登録\) の場合は、 **[登録]** をクリックします。
    ![リソース プロバイダーの登録](./media/backup-azure-backup-import-export/registerimportexport.png)
* ステージング場所 (ネットワーク共有、または最初のコピーを保持するのに十分なディスク領域がある内部または外部コンピューター上の追加ドライブ) が作成されていること。 ステージング場所には、初期コピーを保持するのに十分なディスク領域があることを確認します。 たとえば、500 GB のファイル サーバーをバックアップする場合は、ステージング領域が 500 GB 以上あることを確認します (圧縮処理により、使用量はこれよりも少なくなります)。
* ディスクを Azure に送付するときは、2.5 インチの SSD、あるいは 2.5 インチまたは 3.5 インチの SATA II/III 内蔵ハード ドライブのみを使用ってください。 最大 10 TB のハード ドライブを使用できます。 サービスでサポートされている最新のドライブについては、[Azure Import/Export サービスのドキュメント](../storage/common/storage-import-export-requirements.md#supported-hardware)をご覧ください。
* SATA ドライブは、"*ステージング場所*" から SATA ドライブへのバックアップ データのコピーが行われるコンピューター ("*コピー用コンピューター*" と呼ばれます) に接続されている必要があります。 "*コピー用コンピューター*" で BitLocker が有効になっていることを確認します。

## <a name="workflow"></a>ワークフロー
このセクションでは、データを Azure データセンターに送付したり、Azure Storage にアップロードしたりできるように、オフライン バックアップ ワークフローについて説明します。 インポート サービスやプロセスの他の側面について質問がある場合は、[インポート サービスの概要に関するページ](../storage/common/storage-import-export-service.md)をご覧ください。

## <a name="initiate-offline-backup"></a>オフライン バックアップを開始する
1. MARS エージェントでバックアップをスケジュールするときは、次の画面が表示されます。

    ![Import screen](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   この画面では、次の内容を入力します。

    * **ステージングの場所**:初回バックアップ コピーが書き込まれる一時的なストレージの場所。 ステージング場所には、ネットワーク共有またはローカル コンピューター上の場所を使用できます。 コピー用コンピューターとソース コンピューターが異なる場合は、ステージング場所の完全なネットワーク パスを指定することをお勧めします。
    * **Azure Resource Manager ストレージ アカウント**:Azure サブスクリプションでの Resource Manager タイプのストレージ アカウント (General Purpose v1 または General Purpose v2) の名前。
    * **Azure ストレージ コンテナー**:Recovery Services コンテナーにコピーする前に、バックアップ データをインポートする先の Azure ストレージ アカウントのストレージ BLOB の名前。
    * **Azure サブスクリプション ID**:Azure ストレージ アカウントを作成する Azure サブスクリプションの ID。
    * **Azure インポート ジョブ名**:ディスクを使用して Azure に送信されたデータを、Azure インポート サービスと Azure Backup が追跡するときに使用する一意の名前。 
  
   画面で情報を入力し、 **[次へ]** をクリックします。 ディスクの準備に必要な情報であるため、指定した "*ステージング場所*" と "*Azure インポート ジョブ名*" を保存します。

2. メッセージ表示されたら、Azure サブスクリプションにサインインします。 Azure Backup が Azure Active Directory アプリケーションを作成し、Azure インポート サービスへのアクセスに必要なアクセス許可を提供できるようにするため、サインインする必要があります。

    ![今すぐバックアップ](./media/backup-azure-backup-import-export/azurelogin.png)

3. ワークフローを完了し、Azure Backup エージェント コンソールで **[今すぐバックアップ]** をクリックします。

    ![今すぐバックアップ](./media/backup-azure-backup-import-export/backupnow.png)

4. ウィザードの [確認] ページで、 **[バックアップ]** をクリックします。 初期バックアップが、セットアップの一部としてステージング領域に書き込まれます。

   ![バックアップの準備ができたことを確認する](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    操作が完了すると、ステージング場所を使用してディスクを準備できるようになります。

   ![今すぐバックアップ](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA ドライブの準備と Azure への発送
*AzureOfflineBackupDiskPrep* ユーティリティは、最寄りの Azure データセンターに送る SATA ドライブを準備します。 このユーティリティは、Azure Backup エージェントのインストール ディレクトリにあります (次のパス)。

   *\Microsoft Azure Recovery Services Agent\Utils\\*

1. このディレクトリに移動し、**AzureOfflineBackupDiskPrep** ディレクトリを、SATA ドライブが接続されている別のコンピューターにコピーします。 SATA ドライブが接続されたコンピューターで、次のことを確認します。

   * コピー用コンピューターが、 **オフライン バックアップの開始** ワークフロー中に指定されたのと同じネットワーク パスを使用して、オフライン シード処理ワークフローのステージング場所にアクセスできる。
   * コピー用コンピューターで BitLocker が有効になっている。
   * Azure PowerShell 3.7.0 がインストールされている。
   * 最新の互換性のあるブラウザー (Microsoft Edge または Internet Explorer 11) がインストールされていて、JavaScript が有効になっている。 
   * コピー用コンピューターで Azure Portal にアクセスできる。 必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。
    
     > [!IMPORTANT] 
     > ソース コンピューターが仮想マシンの場合、コピー用コンピューターはソース コンピューターとは異なる物理サーバーまたはクライアント マシンである必要があります。

2. コピー用コンピューターで、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して管理者特権でのコマンド プロンプトを開き、次のコマンドを実行します。

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | パラメーター | 説明 |
    | --- | --- |
    | s:&lt;*ステージング場所のパス*&gt; |必須。**オフライン バックアップの開始**ワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; |オプション。**オフライン バックアップの開始**ワークフローで入力した **Azure 発行設定**ファイルへのパスを指定します。 |

    コマンドを実行すると、準備する必要があるドライブに対応する Azure インポート ジョブを選択するよう求められます。 指定されたステージング場所に関連付けられているインポート ジョブが 1 つのみの場合は、次のような画面が表示されます。

    ![Azure Disk Preparation tool input](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Azure への転送を準備する、マウント済みディスクのドライブ文字を入力します (末尾のコロンを除く)。 
4. メッセージが表示されたら、ドライブをフォーマットすることを確認します。
5. Azure サブスクリプションへのサインインを求められます。 資格情報を入力します。

    ![Azure Disk Preparation tool input](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    これにより、ディスクとバックアップ データのコピーの準備が開始されます。 指定したディスクにバックアップ データを格納するための領域が十分にない場合は、メッセージが表示されます。その場合は、追加のディスクを接続する必要があります。 <br/>

    ツールが正常に実行されると、最後に、コマンド プロンプトで 3 つの情報が提供されます。
   1. 提供した 1 つまたは複数のディスクが、Azure への発送用に準備されます。 
   2. インポート ジョブが作成されたことの確認が表示されます。 インポート ジョブには指定した名前が使われています。
   3. ツールで、Azure データセンターの発送先住所が表示されます。

      ![Azure disk preparation complete](./media/backup-azure-backup-import-export/console2.png)<br/>

6. コマンド実行の最後に、発送情報を更新できます。

7. 表示された住所にディスクを発送し、後で参照できるように追跡番号を控えておきます。

   > [!IMPORTANT] 
   > 2 つの Azure インポート ジョブが同じ追跡番号を持つことはできません。 単一の Azure インポート ジョブでユーティリティによって準備されたドライブが単一のパッケージで発送されることと、パッケージに 1 つの一意の追跡番号があることを確認します。 単一パッケージ内の異なる Azure インポート ジョブの一部として準備されたドライブは結合しないでください。



## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure インポート ジョブで発送の詳細を更新する

次の手順では、Azure インポート ジョブの送付の詳細を更新します。 この情報には、以下に関する詳細が含まれます。
* ディスクを Azure に配達する配送業者の名前
* ディスクの返送先の詳細

1. Azure サブスクリプションにサインインします。
2. メイン メニューで **[すべてのサービス]** をクリックし、[すべてのサービス] ダイアログで「インポート」と入力します。 **[インポート/エクスポート ジョブ]** が表示されたら、それをクリックします。
    ![発送情報の入力](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **[インポート/エクスポート ジョブ]** メニューの一覧が開き、選択したサブスクリプション内のすべてのインポート/エクスポート ジョブの一覧が表示されます。 

3. 複数のサブスクリプションがある場合は、バックアップ データのインポートに使ったサブスクリプションを選択します。 新しく作成されたインポート ジョブを選んで詳細を表示します。

    ![発送情報の確認](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. インポート ジョブの [設定] メニューで、 **[発送情報の管理]** をクリックして、返送の詳細を入力します。

    ![発送情報の格納](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. 配送業者の追跡番号がある場合は、Azure インポートジョブの概要ページでバナーをクリックして、次の詳細を入力します。

   > [!IMPORTANT] 
   > Azure インポート ジョブを作成してから 2 週間以内に、配送業者の情報と追跡番号が更新されたことを確認します。 2 週間以内にこの情報を確認しないと、ジョブが削除され、ドライブが処理されない可能性があります。

   ![発送情報の格納](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![発送情報の格納](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>ドライブの処理の所要時間 
Azure インポート ジョブの処理にかかる時間は、運送時間、ジョブの種類、コピーするデータの種類とサイズ、用意されたディスクのサイズなど、さまざまな要素によって変わります。 Azure Import/Export サービスに SLA はありませんが、ディスクの到着後 7 日から 10 日以内に Azure ストレージ アカウントへのバックアップ データ コピーが完了するよう努力しております。 

### <a name="monitoring-azure-import-job-status"></a>Azure インポート ジョブの状態の監視
Azure portal で **[インポート/エクスポート ジョブ]** ページに移動してジョブを選ぶことにより、インポート ジョブの状態を監視できます。 インポート ジョブの状態について詳しくは、[ストレージのインポート/エクスポート サービス](../storage/common/storage-import-export-service.md)に関するページをご覧ください。

### <a name="complete-the-workflow"></a>ワークフローを完了する
インポート ジョブが正常に完了すると、初回バックアップ データをストレージ アカウントで使用できます。 次回のスケジュール バックアップでは、Azure Backup は次のようにストレージ アカウントのデータのコンテンツを Recovery Services コンテナーにコピーします。 

   ![Recovery Services コンテナーへのデータのコピー](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

次回のスケジュール バックアップでは、Azure Backup は増分バックアップを実行します。

### <a name="cleaning-up-resources"></a>リソースのクリーンアップ
初回バックアップが完了したら、Azure ストレージ コンテナーにインポートしたデータおよびステージングの場所にあるバックアップ データ を安全に削除することができます。

## <a name="next-steps"></a>次の手順
* Azure Import/Export ワークフローについて質問がある場合は、「 [Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage/common/storage-import-export-service.md)」を参照してください。
* ワークフローについて質問がある場合は、Azure Backup [FAQ](backup-azure-backup-faq.md) のオフライン バックアップのセクションを参照してください。
