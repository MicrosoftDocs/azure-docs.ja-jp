---
title: DPM と Azure Backup Server のオフライン バックアップ
description: Azure Backup では、Azure Import/Export サービスを使用してネットワークからデータを送信できます。 この記事では、DPM と Azure Backup Server でのオフライン バックアップ ワークフローについて説明します。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98701815"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>DPM と Azure Backup Server (MABS) のオフライン バックアップのワークフロー

>[!IMPORTANT]
> これらの手順は、DPM 2019 UR1 (またはそれ以降) と MABS v3 UR1 (またはそれ以降) に適用されます。

System Center Data Protection Manager と Azure Backup Server (MABS) は、Azure Backup と統合し、組み込みの効率性を使って Azure への初回完全バックアップ時にネットワークとストレージのコストを抑えます。 初回完全バックアップでは通常、大量のデータが転送されます。その後の差分/増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 Azure Backup では、初回バックアップが圧縮されます。 オフライン シード処理プロセスによって、Azure Backup でディスクを使用し、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。

Azure Backup のオフライン シード処理プロセスは [Azure Import/Export サービス](../import-export/storage-import-export-service.md) と緊密に統合されています。 このサービスでは、ディスクを使用してデータを Azure に転送できます。 待ち時間が長く、低帯域幅のネットワークで転送する必要がある初回バックアップ データが数 TB (テラバイト) ある場合は、オフライン シード処理ワークフローを使用して、1 台以上のハード ドライブ上にある初回バックアップ コピーを Azure データセンターに発送できます。 この記事では、System Center Data Protection Manager (DPM) および Microsoft Azure Backup Server (MABS) のこのワークフローを完了するための概要と手順について説明します。

> [!NOTE]
> Microsoft Azure Recovery Services (MARS) エージェントのオフライン バックアップのプロセスは、DPM と MABS とは異なります。 MARS エージェントのオフライン バックアップの使用の詳細については、「[Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」を参照してください。 Azure Backup エージェントを使用して行われたシステム状態のバックアップでは、オフライン バックアップはサポートされていません。
>
> MABS UR1 のアップデートでは、MABS の Azure Data Box を使用したオフライン バックアップのプレビューも導入されます。 詳細については、[SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) にお問い合わせください。

## <a name="overview"></a>概要

Azure Backup のオフライン シード処理機能と Azure Import/Export サービスを使用すると、ディスクを使ってオフラインで Azure にデータを簡単にアップロードできます。 オフライン バックアップ プロセスには次の手順があります。

> [!div class="checklist"]
>
> * バックアップ データは、ネットワーク経由で送信されずにステージング場所に書き込まれます。
> * その後、ステージング場所のデータは *AzureOfflineBackupDiskPrep* ユーティリティを使用して、1 つまたは複数の SATA ディスクに書き込まれます。
> * Azure インポート ジョブがユーティリティによって自動的に作成されます。
> * SATA ドライブが最寄りの Azure データセンターに送付されます。
> * Azure へのバックアップ データのアップロードが完了した後、Azure Backup は、バックアップ データをバックアップ コンテナーにコピーし、増分バックアップをスケジュールします。

## <a name="prerequisites"></a>前提条件

オフライン バックアップ ワークフローを開始する前に、次の前提条件を満たしていることをご確認ください。

* [Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)が作成されていること。 作成するには、「[Recovery Services コンテナーを作成する](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)」にある手順を参照してください。tutorial-backup-windows-server-to-azure#create-a-recovery-services-vault).
* [最新バージョンの Microsoft Azure Recovery Services エージェント](https://aka.ms/azurebackup_agent)のみが SC DPM または MABS にインストールされ、Recovery Services コンテナーに登録されていることを確認してください。
* 更新プログラム ロールアップ 1 は、SC DPM 2019 または MABS v3 にインストールされています。

  > [!NOTE]
  > DPM 2019 UR1 と MABS v3 UR1 では、オフラインシード処理は Azure Active Directory を使用して認証されます。

* DPM または MABS サーバーで、Microsoft Edge または Internet Explorer 11 がインストールされ、JavaScript が有効になっていることを確認します。
* Recovery Services コンテナーと同じサブスクリプションを使って、Azure ストレージ アカウントを作成します。
* Azure Active Directory アプリケーションを作成するのに[必要なアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md)があることを確認します。 オフライン バックアップ ワークフローは、Azure ストレージ アカウントに関連付けられているサブスクリプションに Azure Active Directory アプリケーションを作成します。 アプリケーションの目的は、オフライン バックアップ ワークフローに必要な、Azure インポート サービスに対するセキュリティで保護されて範囲を制限されたアクセスを、Azure Backup に提供することです。
* Azure ストレージ アカウントを含むサブスクリプションに Microsoft.ImportExport リソース プロバイダーを登録します。 リソース プロバイダーを登録するには:
    1. メイン メニューで、 **[サブスクリプション]** を選択します。
    2. 複数のサブスクリプションをサブスクライブしている場合は、オフライン バックアップに使っているサブスクリプションを選びます。 使っているサブスクリプションが 1 つだけの場合は、そのサブスクリプションが表示されます。
    3. サブスクリプション メニューで、 **[リソース プロバイダー]** を選択してプロバイダーの一覧を表示します。
    4. プロバイダーの一覧で、Microsoft.ImportExport まで下にスクロールします。 状態が [未登録] である場合は、 **[登録]** を選択します。

       ![リソース プロバイダーの登録](./media/backup-azure-backup-server-import-export/register-import-export.png)

* ステージング場所 (ネットワーク共有、または最初のコピーを保持するのに十分なディスク領域がある内部または外部コンピューター上の追加ドライブ) が作成されていること。 たとえば、500 GB のファイル サーバーをバックアップする場合は、500 GB 以上のステージング領域を確保します。 たとえば、500 GB のファイル サーバーをバックアップする場合は、ステージング領域が 500 GB 以上あることを確認します (圧縮処理により、使用量はこれよりも少なくなります)。
* Azure に送信されるディスクに関して、2.5 インチ SSD、または 2.5 インチか 3.5 インチの SATA II/III 内蔵ハード ドライブが使用されていることを確認します。 最大 10 TB のハード ドライブを使用できます。 サービスでサポートされている最新のドライブについては、[Azure Import/Export サービスのドキュメント](../import-export/storage-import-export-requirements.md#supported-hardware)をご覧ください。
* SATA ドライブは、ステージング場所から SATA ドライブへのバックアップ データのコピーが行われるコンピューター ("*コピー用コンピューター*" と呼ばれます) に接続されている必要があります。 コピー用コンピューターで BitLocker を確実に有効にします。

## <a name="workflow"></a>ワークフロー

このセクションの情報は、オフライン バックアップ ワークフローを完了するためのものなので、このデータを Azure データセンターに配信したり、Azure Storage にアップロードしたりできます。 インポート サービスやプロセスの他の側面について質問がある場合は、上記で参照した [サービスの概要に関するページ](../import-export/storage-import-export-service.md) を参照してください。

## <a name="initiate-offline-backup"></a>オフライン バックアップを開始する

1. オンライン保護を使用して新しい保護グループを作成するか、既存の保護グループにオンライン保護を追加すると、次の画面が表示されます。 初期のオンライン レプリケーション方法を選択するには、 **[独自のディスクを使用して転送]** を選択し、 **[次へ]** を選択します。

    ![Import screen](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Azure のサインイン ページが開きます。 Azure のユーザー アカウントを使用してサインインします。このアカウントには、Azure サブスクリプションに対する *所有者* ロールの権限が付与されています。

    ![Azure サインイン ページ](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. **[独自のディスクの使用]** ページで入力を指定します。

   ![独自のディスクの使用の入力](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   この画面では、次の内容を入力します。

   * **ステージングの場所**:初回バックアップ コピーが書き込まれる一時的なストレージの場所。 ステージング場所には、ネットワーク共有またはローカル コンピューター上の場所を使用できます。 コピー用コンピューターとソース コンピューターが異なる場合は、ステージング場所の完全なネットワーク パスを指定します。
   * **Azure Resource Manager ストレージ アカウント**:Azure サブスクリプションでの Resource Manager タイプのストレージ アカウント (General Purpose v1 または General Purpose v2) の名前。
   * **Azure ストレージ コンテナー**:バックアップ データをインポートする先の Azure ストレージ アカウントの BLOB ストレージ コンテナーの名前。
   * **Azure サブスクリプション ID**:Azure ストレージ アカウントを作成する Azure サブスクリプションの ID。
   * **Azure インポート ジョブ名**:ディスクを使用して Azure に送信されたデータを、Azure インポート サービスと Azure Backup が追跡するときに使用する一意の名前。

    **ステージング場所** と、指定した **Azure Import ジョブの名前** を保存します。 ディスクを準備する必要があります。

4. ワークフローを完了して、保護を作成または更新します。 また、オフライン バックアップ コピーを開始するには、 **[保護グループ]** を右クリックし、 **[回復ポイントの作成]** オプションを選択します。 次に、 **[オンライン保護]** オプションを選択します。

   ![[回復ポイントの作成]](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. [監視] ウィンドウでオンライン レプリカの作成ジョブを監視します。 ジョブは、「*Azure Import ジョブの完了を待機しています*」という警告と共に成功するはずです。

   ![回復ポイントの完了](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. 操作が完了すると、ステージング場所を使用してディスクを準備できるようになります。

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA ドライブの準備と Azure への発送

*AzureOfflineBackupDiskPrep* ユーティリティは、最寄りの Azure データセンターに送る SATA ドライブを準備します。 このユーティリティは、Azure Backup エージェントのインストール ディレクトリにあります。(パスは次のとおり): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. このディレクトリに移動し、**AzureOfflineBackupDiskPrep** ディレクトリを、SATA ドライブが接続されている別のコンピューターにコピーします。 SATA ドライブが接続されたコンピューターで、次のことを確認します。

   * コピー用コンピューターが、「オフライン バックアップの開始」セクションのワークフロー中に指定されたのと同じネットワーク パスを使用して、オフライン シード処理ワークフローのステージング場所にアクセスできる。
   * コピー用コンピューターで BitLocker が有効になっている。
   * Azure PowerShell 3.7.0 がコピー コンピューターにインストールされます (DPM または MABS サーバーで AzureOfflineBackupDiskPrep ユーティリティを実行している場合は必要ありません)。
   * 最新の互換性のあるブラウザー (Microsoft Edge または Internet Explorer 11) がインストールされていて、JavaScript が有効になっている。
   * コピー用コンピューターで Azure Portal にアクセスできる。 必要に応じて、コピー用コンピューターをソース コンピューターと同じにすることができます。

     > [!IMPORTANT]
     > ソース コンピューターが仮想マシンの場合は、別の物理サーバーまたはクライアント マシンをコピー用コンピューターとして使用する必要があります。

1. コピー用コンピューターで、*AzureOfflineBackupDiskPrep* ユーティリティのディレクトリを現在のディレクトリとして使用して管理者特権でのコマンド プロンプトを開きます。 次のコマンドを実行します。

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | パラメーター | 説明 |
    | --- | --- |
    | s:&lt;*ステージング場所のパス*&gt; |必須の入力。「オフライン バックアップの開始」セクションのワークフローで入力したステージング場所へのパスを指定します。 |
    | p:&lt;*PublishSettingsFile へのパス*&gt; |この省略可能な入力は、Azure 発行設定ファイルへのパスを指定するために使用されます。 |

    コマンドを実行すると、準備する必要があるドライブに対応する Azure インポート ジョブを選択するよう求められます。 指定されたステージング場所に関連付けられているインポート ジョブが 1 つのみの場合は、次のような画面が表示されます。

      ![ディスク準備コンソール](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Azure への転送を準備する、マウント済みディスクのドライブ文字を入力します (末尾のコロンを除く)。
1. メッセージが表示されたら、ドライブをフォーマットすることを確認します。
1. Azure サブスクリプションへのサインインを求められます。 資格情報を入力します。

    ![Azure サインイン画面](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    これにより、ディスクとバックアップ データのコピーの準備が開始されます。 指定したディスクにバックアップ データを格納するための領域が十分にない場合は、ツールによりメッセージが表示されます。その場合は、追加のディスクを接続する必要があります。 <br/>

    ツールが正常に実行されると、最後に、コマンド プロンプトで 3 つの情報が提供されます。
    * 提供した 1 つまたは複数のディスクが、Azure への発送用に準備されます。
    * インポート ジョブが作成されたことの確認が表示されます。 インポート ジョブには指定した名前が使われています。
    * ツールで、Azure データセンターの発送先住所が表示されます。

     ![Azure disk preparation complete](./media/backup-azure-backup-server-import-export/console.png)

1. コマンドの実行の最後にも、発送情報を更新するオプションが表示されます。

1. 表示された住所にディスクを発送し、後で参照できるように追跡番号を控えておきます。

   > [!IMPORTANT]
   > 2 つの Azure インポート ジョブが同じ追跡番号を持つことはできません。 単一の Azure インポート ジョブでユーティリティによって準備されたドライブが単一のパッケージで発送されることと、パッケージに 1 つの一意の追跡番号があることを確認します。 単一パッケージ内の異なる Azure インポート ジョブの一部として準備されたドライブは結合しないでください。

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure インポート ジョブで発送の詳細を更新する

次の手順では、Azure インポート ジョブの送付の詳細を更新します。 この情報には、以下に関する詳細が含まれます。

* ディスクを Azure に配達する配送業者の名前
* ディスクの返送先の詳細

   1. Azure サブスクリプションにサインインします。
   2. メイン メニューで **[すべてのサービス]** を選択し、[すべてのサービス] ダイアログで「インポート」と入力します。 **[インポート/エクスポート ジョブ]** が表示されたら、それを選択します。
       ![発送情報の入力](./media/backup-azure-backup-server-import-export/search-import-job.png)

       **[インポート/エクスポート ジョブ]** メニューの一覧が開き、選択したサブスクリプション内のすべてのインポート/エクスポート ジョブの一覧が表示されます。

   3. 複数のサブスクリプションがある場合は、バックアップ データのインポートに使ったサブスクリプションを選択します。 新しく作成されたインポート ジョブを選んで詳細を表示します。

       ![発送情報の確認](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. インポート ジョブの [設定] メニューで、 **[配送情報の管理]** を選択し、返送の詳細を入力します。

       ![発送情報の格納](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. 配送業者の追跡番号がある場合は、Azure インポート ジョブの概要ページでバナーを選択し、次の詳細を入力します。

      > [!IMPORTANT]
      > Azure インポート ジョブを作成してから 2 週間以内に、配送業者の情報と追跡番号が更新されたことを確認します。 2 週間以内にこの情報を確認しないと、ジョブが削除され、ドライブが処理されない可能性があります。

      ![ジョブの概要](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![追跡情報](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>ドライブの処理の所要時間

Azure インポート ジョブの処理にかかる時間は状況に応じて異なります。 処理時間は、発送時刻、ジョブの種類、コピーするデータの種類とサイズ、提供されるディスクのサイズなどの要因によって異なります。 Azure Import/Export サービスには SLA はありません。 ディスクの到着後 7 日から 10 日以内に Azure ストレージ アカウントへのバックアップ データ コピーが完了するよう努力しております。 次のセクションでは、Azure インポート ジョブの状態を監視する方法について詳しく説明します。

### <a name="monitor-azure-import-job-status"></a>Azure インポート ジョブの状態の監視

Azure portal で **[インポート/エクスポート ジョブ]** ページに移動してジョブを選ぶことにより、インポート ジョブの状態を監視できます。 インポート ジョブの状態について詳しくは、[ストレージのインポート/エクスポート サービス](../import-export/storage-import-export-service.md)に関するページをご覧ください。

### <a name="complete-the-workflow"></a>ワークフローを完了する

インポート ジョブが完了すると、初回バックアップ データをストレージ アカウントで使用できます。 次回のスケジュール バックアップでは、Azure Backup はストレージ アカウントのデータのコンテンツを Recovery Services コンテナーにコピーします。

予定されている次回のオンライン レプリカ作成ジョブで、Data Protection Manager は、初回バックアップ コピーに対する増分バックアップを実行します。

## <a name="next-steps"></a>次のステップ

* Azure Import/Export サービス ワークフローについて質問がある場合は、[Microsoft Azure Import/Export サービスを使用した BLOB ストレージへのデータの転送](../import-export/storage-import-export-service.md)に関するページを参照してください。
