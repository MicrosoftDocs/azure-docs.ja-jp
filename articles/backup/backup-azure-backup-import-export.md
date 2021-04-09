---
title: Azure Import/Export サービスを使用したオフライン バックアップのシード処理
description: Azure Import/Export サービスを使用してネットワークからデータを送信するための、Azure Backup の使用方法について説明します。 この記事では、Azure Import/Export サービスの使用による初期バックアップ データのオフライン シード処理について説明します。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704365"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup でのオフライン バックアップのワークフロー

Azure Backup はさまざまな面で効率性に優れ、Azure への初回完全バックアップ時にネットワークとストレージのコストを抑えます。 初回完全バックアップでは通常、大量のデータが転送されます。その後の差分/増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 オフライン シード処理プロセスにより、Azure Backup はディスクを使ってオフライン バックアップ データを Azure にアップロードすることができます。

Azure Backup のオフライン シード処理プロセスは [Azure Import/Export サービス](../import-export/storage-import-export-service.md)と緊密に統合されています。 このサービスを使用すると、ディスクを使用して初期バックアップ データを Azure に転送できます。 待ち時間が長く、低帯域幅のネットワークで転送する必要がある初回バックアップ データが数 TB (テラバイト) ある場合は、オフライン シード処理ワークフローを使用して、1 台以上のハード ドライブ上にある初回バックアップ コピーを Azure データセンターに発送できます。 次の図では、ワークフローの手順の概要を示します。

  ![オフラインでのインポート ワークフロー プロセスの概要](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

オフライン バックアップ プロセスには次の手順があります。

1. ネットワーク経由でバックアップ データを送信する代わりに、ステージングの場所にバックアップ データを書き込みます。
1. *AzureOfflineBackupDiskPrep* ユーティリティを使って、ステージングの場所のデータを、1 つ以上の SATA ディスクに書き込みます。
1. 準備作業の一環として、*AzureOfflineBackupDiskPrep* ユーティリティで Azure インポート ジョブを作成します。 SATA ドライブを最も近い Azure データセンターに送り、インポート ジョブを参照してアクティビティを接続します。
1. Azure データセンターでは、ディスク上のデータが Azure ストレージ アカウントにコピーされます。
1. Azure Backup によってストレージ アカウントから Recovery Services コンテナーにバックアップ データがコピーされ、増分バックアップがスケジュールされます。

## <a name="supported-configurations"></a>サポートされている構成

Azure Backup の次の機能またはワークロードでは、オフライン バックアップの使用がサポートされています。

> [!div class="checklist"]
>
> * Microsoft Azure Recovery Services (MARS) エージェント (Azure Backup エージェントとも呼ばれます) によるファイルとフォルダーのバックアップ。
> * System Center Data Protection Manager (DPM) でのすべてのワークロードとファイルのバックアップ。
> * Microsoft Azure Backup Server でのすべてのワークロードとファイルのバックアップ。

   > [!NOTE]
   > Azure Backup エージェントを使用して行われたシステム状態のバックアップでは、オフライン バックアップはサポートされていません。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>前提条件

  > [!NOTE]
  > 次の前提条件およびワークフローは、[最新の Azure Recovery Services エージェント](https://aka.ms/azurebackup_agent)を使ったファイルとフォルダーのオフライン バックアップにのみ適用されます。 System Center DPM または Azure Backup Server を使ってワークロードのオフライン バックアップを実行する場合は、「[DPM と Azure Backup Server のオフライン バックアップのワークフロー](backup-azure-backup-server-import-export.md)」をご覧ください。

オフライン バックアップ ワークフローを開始する前に、次の前提条件を完了します。

* [Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)を作成します。 コンテナーを作成するには、「[Recovery Services コンテナーを作成する](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)」の手順に従います。
* [最新バージョンの Azure Backup エージェント](https://aka.ms/azurebackup_agent)だけが Windows Server または Windows クライアントにインストールされ、Recovery Services コンテナーにそのコンピューターが登録されていることを確認します。
* Azure Backup エージェントが実行されているコンピューターには Azure PowerShell 3.7.0 が必要です。 [Azure PowerShell のバージョン 3.7.0 をダウンロードしてインストールします](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。
* Azure Backup エージェントを実行しているコンピューターで、Microsoft Edge または Internet Explorer 11 がインストールされ、JavaScript が有効になっていることを確認します。
* Recovery Services コンテナーと同じサブスクリプションを使って、Azure ストレージ アカウントを作成します。
* Azure Active Directory アプリケーションを作成するのに[必要なアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md)があることを確認します。 オフライン バックアップ ワークフローは、Azure ストレージ アカウントに関連付けられているサブスクリプションに Azure Active Directory アプリケーションを作成します。 アプリケーションの目的は、オフライン バックアップ ワークフローに必要な、Azure Import/Export サービスに対するセキュリティで保護されて範囲を制限されたアクセスを、Azure Backup に提供することです。
* Azure ストレージ アカウントを含むサブスクリプションに *Microsoft.ImportExport* リソース プロバイダーを登録します。 リソース プロバイダーを登録するには:
    1. メイン メニューで、 **[サブスクリプション]** を選択します。
    1. 複数のサブスクリプションをサブスクライブしている場合は、オフライン バックアップに使用する予定のサブスクリプションを選びます。 使っているサブスクリプションが 1 つだけの場合は、そのサブスクリプションが表示されます。
    1. サブスクリプションのメニューで、 **[リソース プロバイダー]** を選択してプロバイダーの一覧を表示します。
    1. プロバイダーの一覧で、*Microsoft.ImportExport* まで下にスクロールします。 **[状態]** が **[NotRegistered]\(未登録\)** の場合は、 **[登録]** を選択します。

        ![リソース プロバイダーの登録](./media/backup-azure-backup-import-export/registerimportexport.png)

* ステージング場所 (ネットワーク共有、または最初のコピーを保持するのに十分なディスク領域がある内部または外部コンピューター上の追加ドライブ) が作成されていること。 たとえば、500 GB のファイル サーバーをバックアップする場合は、500 GB 以上のステージング領域を確保します。 たとえば、500 GB のファイル サーバーをバックアップする場合は、ステージング領域が 500 GB 以上あることを確認します (圧縮処理により、使用量はこれよりも少なくなります)。
* ディスクを Azure に送付するときは、2.5 インチの SSD、あるいは 2.5 インチまたは 3.5 インチの SATA II/III 内蔵ハード ドライブのみを使用してください。 最大 10 TB のハード ドライブを使用できます。 サービスでサポートされている最新のドライブについては、[Azure Import/Export サービスのドキュメント](../import-export/storage-import-export-requirements.md#supported-hardware)をご覧ください。
* SATA ドライブは、ステージング場所から SATA ドライブへのバックアップ データのコピーが行われるコンピューター ("*コピー用コンピューター*" と呼ばれます) に接続されている必要があります。 コピー用コンピューターで BitLocker を確実に有効にします。

## <a name="workflow"></a>ワークフロー

このセクションでは、データを Azure データセンターに送付したり、Azure Storage にアップロードしたりできるように、オフライン バックアップ ワークフローについて説明します。 インポート サービスやプロセスの他の側面について質問がある場合は、[Azure Import/Export サービスの概要に関するドキュメント](../import-export/storage-import-export-service.md)をご覧ください。

## <a name="initiate-offline-backup"></a>オフライン バックアップを開始する

1. Recovery Services エージェントでバックアップをスケジュールする場合は、このページが表示されます。

    ![インポート ページ](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. **[Transfer using my own disks]\(独自のディスクを使用して転送\)** オプションを選択します。

    > [!NOTE]
    > 初期バックアップ データをオフラインで転送するには、Azure Data Box オプションを使用します。 このオプションを使用すると、Azure と互換性のある独自のディスクを調達するために必要な作業量を節約できます。 これにより、Recovery Services エージェントがバックアップ データを直接書き込むことができる、Microsoft 独自のセキュリティで保護された改ざん防止機能を備えた Azure Data Box デバイスが提供されます。

1. **[次へ]** を選択し、注意深くボックスに入力します。

    ![ディスクの詳細を入力する](./media/backup-azure-backup-import-export/your-disk-details.png)

   入力するボックスは次のとおりです。

    * **ステージングの場所**:初回バックアップ コピーが書き込まれる一時的なストレージの場所。 ステージング場所には、ネットワーク共有またはローカル コンピューター上の場所を使用できます。 コピー用コンピューターとソース コンピューターが異なる場合は、ステージング場所の完全なネットワーク パスを指定します。
    * **Azure Resource Manager ストレージ アカウント**:Azure サブスクリプションでの Resource Manager タイプのストレージ アカウント (General Purpose v1 または General Purpose v2) の名前。
    * **Azure ストレージ コンテナー**:Recovery Services コンテナーにコピーする前に、バックアップ データをインポートする先の Azure ストレージ アカウントの Blob Storage コンテナーの名前。
    * **Azure サブスクリプション ID**:Azure ストレージ アカウントを作成する Azure サブスクリプションの ID。
    * **Azure インポート ジョブ名**:ディスクを使用して Azure に送信されたデータを、Azure Import/Export サービスと Azure Backup が追跡するときに使用する一意の名前。
  
   ボックスに入力したら、 **[次へ]** を選択します。 **[ステージングの場所]** と **[Azure インポート ジョブ名]** の情報を保存します。 ディスクを準備する必要があります。

1. 要求されたら、Azure サブスクリプションにサインインします。 Azure Backup が Azure Active Directory アプリケーションを作成できるように、サインインする必要があります。 Azure Import/Export サービスにアクセスするために必要なアクセス許可を入力します。

    ![Azure サブスクリプションのサインイン ページ](./media/backup-azure-backup-import-export/azure-login.png)

1. ワークフローを終了します。 Azure Backup エージェント コンソールで、 **[今すぐバックアップ]** を選択します。

    ![今すぐバックアップ](./media/backup-azure-backup-import-export/backupnow.png)

1. ウィザードの **[確認]** ページで、 **[バックアップ]** を選択します。 初期バックアップが、セットアップの一部としてステージング領域に書き込まれます。

   ![バックアップの準備ができたことの確認](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    操作が完了すると、ステージング場所を使用してディスクを準備できるようになります。

   ![今すぐバックアップ ウィザード ページ](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA ドライブの準備と Azure への発送

*AzureOfflineBackupDiskPrep* ユーティリティは、最寄りの Azure データセンターに送る SATA ドライブを準備します。 このユーティリティは、次のパスの Azure Backup エージェントのインストール ディレクトリにあります。

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. このディレクトリに移動し、*AzureOfflineBackupDiskPrep* ディレクトリを、SATA ドライブが接続されている別のコンピューターにコピーします。 SATA ドライブが接続されたコンピューターで、次のことを確実にします。

   * コピー用コンピューターが、「オフライン バックアップの開始」セクションのワークフロー中に指定されたのと同じネットワーク パスを使用して、オフライン シード処理ワークフローのステージング場所にアクセスできる。
   * コピー用コンピューターで BitLocker が有効になっている。
   * Azure PowerShell 3.7.0 がインストールされている。
   * 最新の互換性のあるブラウザー (Microsoft Edge または Internet Explorer 11) がインストールされていて、JavaScript が有効になっている。
   * コピー用コンピューターで Azure Portal にアクセスできる。 必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。

     > [!IMPORTANT]
     > ソース コンピューターが仮想マシンの場合、コピー用コンピューターはソース コンピューターとは異なる物理サーバーまたはクライアント マシンである必要があります。

1. コピー用コンピューターで、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して管理者特権でのコマンド プロンプトを開きます。 次のコマンドを実行します。

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | パラメーター | 説明 |
    | --- | --- |
    | s:&lt;*ステージング場所のパス*&gt; |必須の入力。「オフライン バックアップの開始」セクションのワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; |この省略可能な入力は、Azure 発行設定ファイルへのパスを指定するために使用されます。  |

    コマンドを実行すると、準備する必要があるドライブに対応する Azure インポート ジョブを選択するよう求められます。 指定されたステージング場所に関連付けられているインポート ジョブが 1 つのみの場合は、このようなページが表示されます。

    ![Azure Disk Preparation ツールの入力](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Azure への転送を準備する、マウント済みディスクのドライブ文字を入力します (末尾のコロンを除く)。
1. メッセージが表示されたら、ドライブをフォーマットすることを確認します。
1. Azure サブスクリプションへのサインインを求められます。 資格情報を入力します。

    ![Azure サブスクリプションのサインイン](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    これにより、ディスクとバックアップ データのコピーの準備が開始されます。 指定したディスクにバックアップ データを格納するための領域が十分にない場合は、ツールによりメッセージが表示されます。その場合は、追加のディスクを接続する必要があります。 <br/>

    ツールが正常に実行されると、最後に、コマンド プロンプトで 3 つの情報が提供されます。

   1. 提供した 1 つまたは複数のディスクが、Azure への発送用に準備されます。
   1. インポート ジョブが作成されたことの確認が表示されます。 インポート ジョブには指定した名前が使われています。
   1. ツールで、Azure データセンターの発送先住所が表示されます。

      ![Azure ディスクの準備完了](./media/backup-azure-backup-import-export/console2.png)<br/>

1. コマンド実行の最後に、発送情報を更新できます。

1. ツールから提供された住所にディスクを発送します。 後で参照できるよう、追跡番号を控えておきます。

   > [!IMPORTANT]
   > 2 つの Azure インポート ジョブが同じ追跡番号を持つことはできません。 単一の Azure インポート ジョブでユーティリティによって準備されたドライブが単一のパッケージで発送され、パッケージに 1 つの一意の追跡番号があることを確実にします。 単一パッケージ内の異なる Azure インポート ジョブの一部として準備されたドライブは結合しないでください。

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure インポート ジョブで発送の詳細を更新する

次の手順では、Azure インポート ジョブの発送の詳細を更新します。 この情報には、以下に関する詳細が含まれます。

* ディスクを Azure に配達する配送業者の名前
* ディスクの返送先の詳細

1. Azure サブスクリプションにサインインします。
1. メイン メニューで、 **[すべてのサービス]** を選びます。 **[すべてのサービス]** ダイアログ ボックスに、「**インポート**」と入力します。 **[インポート/エクスポート ジョブ]** が表示されたら、それを選択します。

    ![発送情報の入力](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **[インポート/エクスポート ジョブ]** メニューが開き、選択したサブスクリプション内のすべてのインポート/エクスポート ジョブの一覧が表示されます。

1. 複数のサブスクリプションがある場合は、バックアップ データのインポートに使ったサブスクリプションを選択します。 次に、新しく作成されたインポート ジョブを選択して詳細を表示します。

    ![発送情報の確認](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. インポート ジョブの **[設定]** メニューで、 **[発送情報の管理]** を選択します。 差出人住所に関する詳細を入力します。

    ![発送情報の保存](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 配送業者の追跡番号がある場合は、Azure インポート ジョブの概要ページでバナーを選択して、次の詳細を入力します。

   > [!IMPORTANT]
   > Azure インポート ジョブを作成してから 2 週間以内に、配送業者の情報と追跡番号が更新されたことを確認します。 2 週間以内にこの情報を確認しないと、ジョブが削除され、ドライブが処理されない可能性があります。

   ![追跡情報の更新アラート](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![運送業者情報と追跡番号](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>ドライブの処理の所要時間

Azure インポート ジョブの処理にかかる時間は状況に応じて異なります。 処理時間は、発送時刻、ジョブの種類、コピーするデータの種類とサイズ、提供されるディスクのサイズなどの要因に基づいています。 Azure Import/Export サービスには SLA はありません。 ディスクの到着後 7 日から 10 日以内に Azure ストレージ アカウントへのバックアップ データ コピーが完了するよう努力しております。

### <a name="monitor-azure-import-job-status"></a>Azure インポート ジョブの状態の監視

Azure portal からインポート ジョブの状態を監視できます。 **[インポート/エクスポート ジョブ]** ページにアクセスして、目的のジョブを選択します。 インポート ジョブの状態について詳しくは、「[Azure Import/Export サービスとは](../import-export/storage-import-export-service.md)」をご覧ください。

### <a name="finish-the-workflow"></a>ワークフローの完了

インポート ジョブが正常に完了すると、初回バックアップ データをストレージ アカウントで使用できます。 次回のスケジュール バックアップで、Azure Backup によってストレージ アカウントからデータのコンテンツが Recovery Services コンテナーにコピーされます。

   ![Recovery Services コンテナーへのデータのコピー](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

次回のスケジュール バックアップでは、Azure Backup は増分バックアップを実行します。

### <a name="clean-up-resources"></a>リソースをクリーンアップする

最初のバックアップが完了したら、Azure Storage コンテナーにインポートされたデータと、ステージング場所のバックアップ データを安全に削除することができます。

## <a name="next-steps"></a>次のステップ

* Azure Import/Export サービス ワークフローについて質問がある場合は、[Microsoft Azure Import/Export サービスを使用した BLOB ストレージへのデータの転送](../import-export/storage-import-export-service.md)に関するページを参照してください。
