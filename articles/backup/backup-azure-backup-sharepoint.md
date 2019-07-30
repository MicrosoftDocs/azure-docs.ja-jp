---
title: Azure への SharePoint ファームの DPM/Azure Backup サーバー保護
description: この記事では、Azure への SharePoint ファームの DPM/Azure Backup サーバー保護の概要について説明します
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: kasinh
ms.openlocfilehash: 91b3105bc2aafba10f4bf105bee851743dcf5282
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466668"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>SharePoint ファームの Azure へのバックアップ
System Center Data Protection Manager (DPM) を使用して SharePoint ファームを Microsoft Azure にバックアップする方法は、他のデータ ソースのバックアップとよく似ています。 Azure Backup ではバックアップのスケジュールを柔軟に設定して日、週、月、年の単位でバックアップ ポイントを作成でき、さまざまなバックアップ ポイントに対応する保有ポリシー オプションがあります。 DPM では、目標復旧時間 (RTO) 短縮のためにはローカル ディスク コピーを保存でき、コスト効率に優れた長期リテンション期間のためには Azure にコピーできます。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint のサポートされるバージョンと関連する保護シナリオ
DPM 用 Azure Backup は、次のシナリオをサポートします。

| ワークロード | バージョン | SharePoint のデプロイ | DPM のデプロイの種類 | DPM - System Center 2012 R2 | 保護と回復 |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013、SharePoint 2010、SharePoint 2007、SharePoint 3.0 |物理サーバーまたは Hyper-V/VMware 仮想マシンとしてデプロイされた SharePoint <br> -------------- <br> SQL AlwaysOn |物理サーバーまたはオンプレミスの Hyper-V 仮想マシン |更新プログラム ロールアップ 5 から、Azure へのバックアップをサポートする |SharePoint ファームの保護の回復オプション: ディスク復旧ポイントからのファーム、データベース、およびファイルまたはリスト項目の回復。  Azure の回復ポイントからのファームとデータベースの回復。 |

## <a name="before-you-start"></a>開始する前に
SharePoint ファームを Azure にバックアップする前に、確認する必要がある点がいくつかあります。

### <a name="prerequisites"></a>前提条件
続行する前に、 [Microsoft Azure Backup を使用してワークロードを保護するための前提条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) がすべて満たされていることを確認します。 前提条件を満たすための作業として、バックアップ コンテナーの作成、コンテナー資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへの DPM/Azure Backup Server の登録などのがあります。

### <a name="dpm-agent"></a>DPM エージェント
DPM エージェントを、SharePoint を実行するサーバー、SQL Server を実行するサーバー、および SharePoint ファームを構成するその他のすべてのサーバーにインストールする必要があります。 保護エージェントのセットアップ方法の詳細については、「[保護エージェントの設定](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx)」をご覧ください。  唯一の例外は、1 台の Web フロント エンド (WFE) サーバーにだけエージェントをインストールすることです。 保護のエントリ ポイントとして使用するためにエージェントをインストールする必要がある WFE サーバーは 1 台だけです。

### <a name="sharepoint-farm"></a>SharePoint ファーム
DPM フォルダーが存在するボリュームには、ファーム内の 1,000 万項目ごとに 2 GB 以上の容量が必要です。 この容量はカタログ生成のために必要です。 DPM が特定の項目 (サイト コレクション、サイト、リスト、ドキュメント ライブラリ、フォルダー、個々のドキュメント、リスト項目) を回復できるよう、カタログ生成では各コンテンツ データベースに含まれる URL のリストが作成されます。 DPM 管理者コンソールの **回復** タスク領域の [回復可能な項目] ウィンドウで、URL の一覧を確認できます。

### <a name="sql-server"></a>SQL Server
DPM は、LocalSystem アカウントとして実行されます。 SQL Server データベースをバックアップする場合、DPM はそのアカウントに SQL Server を実行しているサーバーに対するsysadmin 権限が必要です。 バックアップする前に、SQL Server を実行しているサーバーで NT AUTHORITY\SYSTEM を *sysadmin* に設定します。

SharePoint ファームの SQL Server データベースが SQL Server エイリアスで構成されている場合は、DPM によって保護されるフロント エンド Web サーバーに SQL Server クライアント コンポーネントをインストールします。

### <a name="sharepoint-server"></a>SharePoint Server
パフォーマンスは SharePoint ファームのサイズなどのさまざまな要因に依存しますが、一般的なガイダンスとしては、1 つの DPM サーバーで 25 TB の SharePoint ファームを保護できます。

### <a name="dpm-update-rollup-5"></a>DPM の更新プログラム ロールアップ 5
Azure への SharePoint ファームの保護を開始するには、DPM の更新プログラム ロールアップ 5 以降をインストールする必要があります。 ファームが SQL AlwaysOn を使用して構成されている場合、更新プログラム ロールアップ 5 を使用すると SharePoint ファームを保護できます。
詳細については、 [DPM の更新プログラム ロールアップ 5](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>サポートされていないもの
* SharePoint ファームを保護する DPM では、検索インデックスまたはアプリケーション サービス データベースは保護されません。 これらのデータベースの保護は別に構成する必要があります。
* DPM では、スケールアウト ファイル サーバー (SOFS) 共有でホストされている SharePoint SQL Server データベースのバックアップはサポートしていません。

## <a name="configure-sharepoint-protection"></a>SharePoint の保護の構成
DPM を使用して SharePoint を保護する前に、 **ConfigureSharePoint.exe**を使用して SharePoint VSS ライター サービス (WSS ライター サービス) を構成する必要があります。

**ConfigureSharePoint.exe** は、フロント エンド Web サーバー上の [DPM のインストール パス]\bin フォルダーにあります。 このツールは、保護エージェントに SharePoint ファームに対する資格情報を提供します。 1 つの WFE サーバーでこのツールを実行します。 複数の WFE サーバーがある場合は、保護グループを構成するときに 1 つだけ選択します。

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>SharePoint VSS ライター サービスを構成するには
1. WFE サーバーのコマンド プロンプトで、[DPM のインストール場所]\bin\ に移動します。
2. ConfigureSharePoint -EnableSharePointProtection を入力します。
3. ファーム管理者の資格情報を入力します。 このアカウントは、WFE サーバーのローカル管理者グループのメンバーである必要があります。 ファーム管理者がローカル管理者ではない場合は、WFE サーバーで次の権限を付与します。
   * DPM フォルダー (%Program Files%\Microsoft Data Protection Manager\DPM) に対するフル コントロールを WSS_Admin_WPG グループに付与します。
   * DPM レジストリ キー (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager) に対する読み取りアクセスを WSS_Admin_WPG グループに付与します。

> [!NOTE]
> SharePoint ファーム管理者の資格情報に変更がある場合は必ず、ConfigureSharePoint.exe を再実行する必要があります。
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>DPM を使用した SharePoint ファームのバックアップ
前述のように DPM と SharePoint ファームを構成した後は、DPM で SharePoint を保護できます。

### <a name="to-protect-a-sharepoint-farm"></a>SharePoint ファームを保護するには
1. DPM 管理者コンソールの **[保護]** タブで **[新規]** をクリックします。
    ![新しい [保護] タブ](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. **新しい保護グループの作成**ウィザードの **[保護グループの種類の選択]** ページで **[サーバー]** を選択し、 **[次へ]** をクリックします。

    ![保護グループの種類の選択](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. **[グループ メンバーの選択]** 画面で、保護する SharePoint サーバーのチェック ボックスをオンにして、 **[次へ]** をクリックします。

    ![グループ メンバーの選択](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > DPM エージェントがインストールされていると、ウィザードにサーバーが表示されます。 DPM ではその構造も示されます。 ConfigureSharePoint.exe を実行したので、DPM は SharePoint VSS ライター サービスおよびその対応する SQL Server Database と通信し、SharePoint ファームの構造、関連するコンテンツ データベース、および対応する項目を認識します。
   >
   >
4. **[データの保護方法の選択]** ページで、**保護グループ**の名前を入力し、適切な "*保護方法*" を選択します。 **[次へ]** をクリックします。

    ![データ保護方法の選択](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > ディスク保護方法は、短い目標復旧時間の達成に役立ちます。 Azure は、テープと比較してコスト効果の高い長期的な保護対象です。 詳細については、「 [Azure Backup を使用してテープのインフラストラクチャを置換する](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   >
   >
5. **[短期的な目標値の指定]** ページで、適切な**リテンション期間**を選択し、バックアップを行うタイミングを指定します。

    ![短期的な目標の指定](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > 回復が最も頻繁に必要になるのは作成されてから 5 日間未満のデータなので、この例では、ディスクへの保有期間として 5 日を選択し、業務時間外にバックアップが行われるようにします。
   >
   >
6. 保護グループに割り当てられているストレージ プール ディスクの容量を確認し、 **[次へ]** をクリックします。
7. すべての保護グループについて、DPM はレプリカを格納および管理するためのディスク領域を割り当てます。 この時点で、DPM は選択されたデータのコピーを作成する必要があります。 レプリカを作成する方法とタイミングを選択し、 **[次へ]** をクリックします。

    ![レプリカ作成方法の選択](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > ネットワーク トラフィックが影響を受けないように、業務時間外の時刻を選択します。
   >
   >
8. DPM はレプリカに対して整合性チェックを実行することにより、データの整合性を保証します。 次の 2 つのオプションを使用できます。 整合性チェックを実行するスケジュールを定義することも、レプリカが不整合になった場合に必ず DPM に自動的にレプリカの整合性チェックを実行させることもできます。 適切なオプションを選択し、 **[次へ]** をクリックします。

    ![整合性チェック](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. **[オンライン保護するデータの指定]** ページで、保護する SharePoint ファームを選択し、 **[次へ]** をクリックします。

    ![DPM の SharePoint 保護 1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. **[オンライン バックアップ スケジュールの指定]** ページで、適切なスケジュールを選択して、 **[次へ]** をクリックします。

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > DPM では、一日当たり最大で 2 回、それぞれ異なる時刻に Azure へのバックアップを行うことができます。 Azure Backup では、 [Azure Backup ネットワーク調整](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling)を使用することで、ピーク時間帯とピーク外の時間帯のバックアップに使用できる WAN 帯域幅の量を制御することもできます。
    >
    >
11. 選択したバックアップ スケジュールに応じて、 **[オンライン保持ポリシーの指定]** ページで、日、週、月、年単位のバックアップ ポイントの保持ポリシーを選択します。

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > DPM が使用する祖父 - 父 - 子の保有方式では、異なるバックアップ ポイントに対して異なる保持ポリシーを選択できます。
    >
    >
12. ディスクと同様に、Azure でも最初の参照ポイント レプリカを作成する必要があります。 Azure に対する初期バックアップ コピーの適切な作成オプションを選択して、 **[次へ]** をクリックします。

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. **[概要]** ページで選択した設定を確認し、 **[グループの作成]** をクリックします。 保護グループが作成されると、成功メッセージが表示されます。

    ![まとめ](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>DPM を使用したディスクからの SharePoint アイテムの復元
次の例では、 *Recovering SharePoint item* が誤って削除され、回復する必要があります。
![DPM の SharePoint 保護 4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. **DPM 管理者コンソール**を開きます。 DPM によって保護されているすべての SharePoint ファームが、 **[保護]** タブに表示されます。

    ![DPM の SharePoint 保護 3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. アイテムの回復を始めるには、 **[回復]** タブを選択します。

    ![DPM の SharePoint 保護 5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. 回復ポイントの範囲内でワイルドカード ベースの検索を使用して、SharePoint で *Recovering SharePoint item* を検索できます。

    ![DPM の SharePoint 保護 6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. 検索結果から適切な回復ポイントを選択し、アイテムを右クリックして、 **[回復]** を選択します。
5. さまざまな回復ポイントを参照して、回復するデータベースまたはアイテムを選択できます。 **日付、回復時刻**の順に選択した後、正しい**データベース、SharePoint ファーム、回復ポイント、アイテム**を順番に選択します。

    ![DPM の SharePoint 保護 7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. アイテムを右クリックして **[回復]** を選択し、**回復ウィザード**を開きます。 **[次へ]** をクリックします。

    ![回復の選択の確認](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. 実行する回復の種類を選択して、 **[次へ]** をクリックします。

    ![回復の種類](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > 上図のように **[元のサイトに回復する]** を選択すると、アイテムは元の SharePoint サイトに回復します。
   >
   >
8. 使用する **回復プロセス** を選択します。

   * SharePoint ファームは変更されていなくて、復元する回復ポイントと同じである場合、 **[回復ファームを使用しないで回復する]** を選択します。
   * 回復ポイントの作成後に SharePoint ファームが変更された場合は、 **[回復ファームを使用して回復する]** を選択します。

     ![回復プロセス](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. 一時的にデータベースを回復するためのステージング SQL Server インスタンスの場所を指定し、DPM サーバー上と SharePoint を実行するサーバー上でアイテムを回復するためのステージング ファイル共有を指定します。

    ![ステージングの場所 1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM は、SharePoint アイテムをホストしているコンテンツ データベースを、一時的な SQL Server インスタンスにアタッチします。 DPM サーバーは、コンテンツ データベースからアイテムを回復し、DPM サーバー上のステージング ファイルの場所に格納します。 次に、DPM サーバーのステージングの場所に回復されたアイテムを、SharePoint ファーム上のステージング場所にエクスポートする必要があります。

    ![ステージングの場所 2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **[回復オプションの指定]** を選択し、SharePoint ファームに対するセキュリティ設定を適用するか、または回復ポイントのセキュリティ設定を適用します。 **[次へ]** をクリックします。

    ![回復オプション](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > ネットワーク帯域幅の使用量を調整できます。 これにより、業務時間中の運用サーバーへの影響を最小限にします。
    >
    >
11. 概要情報を確認し、 **[回復]** をクリックしてファイルの回復を開始します。

    ![回復の概要](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. **DPM 管理者コンソール**の **[監視]** タブを選択して、回復の**状態**を確認できます。

    ![回復の状態](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > ファイルが復元されます。 SharePoint サイトを更新して、復元されたファイルを確認すできます。
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>DPM を使用した Azure からの SharePoint データベースの復元
1. SharePoint コンテンツ データベースを回復するには、さまざまな回復ポイントを参照して (前述したように)、復元する回復ポイントを選択します。

    ![DPM の SharePoint 保護 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint の回復ポイントをダブルクリックして、使用可能な SharePoint カタログ情報を表示します。

   > [!NOTE]
   > SharePoint ファームは Azure では長期保有期間用に保護されているので、DPM サーバーには使用可能なカタログ情報 (メタデータ) がありません。 その結果、特定時点の SharePoint コンテンツ データベースを回復する必要があるときは常に、SharePoint ファームを再カタログ化する必要があります。
   >
   >
3. **[再カタログ化]** をクリックします。

    ![DPM の SharePoint 保護 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **[クラウドの再カタログ化]** ステータス ウィンドウが開きます。

    ![DPM の SharePoint 保護 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    カタログ化が完了すると、ステータスが *[成功]* に変わります。 **[閉じる]** をクリックします。

    ![DPM の SharePoint 保護 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. DPM の **[回復]** タブに表示される SharePoint オブジェクトをクリックして、コンテンツ データベースの構造を取得します。 該当する項目を右クリックし、 **[回復]** をクリックします。

    ![DPM の SharePoint 保護 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. この時点で、この記事で前述した回復手順に従ってディスクから SharePoint コンテンツ データベースを回復します。

## <a name="next-steps"></a>次の手順
* SharePoint の DPM 保護に関する詳細 - [Video シリーズ「DPM Protection of SharePoint (SharePoint の DPM 保護)」](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* 「 [System Center 2012 - Data Protection Manager リリース ノート](https://technet.microsoft.com/library/jj860415.aspx)
* 「 [System Center 2012 SP1 - Data Protection Manager リリース ノート](https://technet.microsoft.com/library/jj860394.aspx)
