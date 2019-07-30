---
title: Azure への SQL Server データベースのバックアップ | Microsoft Docs
description: このチュートリアルでは、SQL Server を Azure に バックアップする方法について説明します。
author: dcurwin
manager: ''
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 31549ddbc0fbe237b9cca1b2b7a4893bc98abaf9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467099"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Azure VM での SQL Server データベースのバックアップ



この記事では、Azure VM 上で稼働している SQL Server データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * コンテナーの作成と構成。
> * データベースの検出とバックアップの設定。
> * データベースに対する自動保護の設定。
> * アドホック バックアップの実行。


## <a name="prerequisites"></a>前提条件

SQL Server データベースをバックアップする前に、次の条件を確認してください。

1. SQL Server インスタンスをホストする VM として、同じリージョンまたはロケールの Recovery Services コンテナーを特定または[作成](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)する。
2. SQL データベースをバックアップするのに必要な [VM のアクセス許可をチェック](backup-azure-sql-database.md#set-vm-permissions)する。
3. VM が[ネットワーク接続](backup-sql-server-database-azure-vms.md#establish-network-connectivity)を備えていることを確認する。
4. Azure Backup の[命名ガイドライン](#verify-database-naming-guidelines-for-azure-backup)に従って SQL Server データベースが名付けられていることをチェックする。
5. データベースに対して有効になっているバックアップ ソリューションが他にないことを確認する。 このシナリオを設定する前に、他のすべての SQL Server バックアップを無効にします。 競合が発生することなく、VM 上で稼働している SQL Server データベースに対して Azure Backup を有効にするのと同時に、Azure VM に対して Azure Backup を有効にできます。


### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

すべての操作において、SQL Server VM 仮想マシンでは、Azure パブリック IP アドレスへの接続を必要とします。 パブリック IP アドレスへの接続がないと、VM の操作 (データベースの検出、バックアップの構成、バックアップのスケジュール、復旧ポイントの復元など) が失敗します。 以下のいずれかのオプションを使用して接続を確立します。

- **Azure データセンターの IP 範囲を許可する**。ダウンロードにおける [IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。 ネットワーク セキュリティ グループ (NSG) にアクセスするには、**Set-AzureNetworkSecurityRule** コマンドレットを使用します。
- **トラフィックをルーティングするために HTTP プロキシ サーバーをデプロイする**。Azure VM 上の SQL Server データベースをバックアップする場合、VM 上のバックアップ拡張機能によって HTTPS API が使用され、管理コマンドが Azure Backup に送信されてデータが Azure Storage に送信されます。 また、バックアップ拡張機能では、認証に Azure Active Directory (Azure AD) を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 パブリック インターネットにアクセスできるように構成されたコンポーネントはバックアップ拡張機能のみです。

各オプションには長所と短所があります

**オプション** | **長所** | **短所**
--- | --- | ---
IP 範囲を許可する | 追加のコストが発生しない。 | IP アドレス範囲が時間の経過と共に変化するため、管理が複雑である。 <br/><br/> Azure Storage だけでなく、Azure 全体へのアクセスを提供することになる。
HTTP プロキシを使用する   | 許可するストレージ URL をプロキシで詳細に制御可能。 <br/><br/> VM に対するインターネット アクセスを単一の場所で実現。 <br/><br/> Azure の IP アドレスの変更の影響を受けない。 | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。

### <a name="set-vm-permissions"></a>VM のアクセス許可を設定する

SQL Server データベースのバックアップを構成するとき、Azure Backup によって多数の処理が行われます。

- **AzureBackupWindowsWorkload** 拡張機能を追加します。
- 仮想マシン上のデータベースを検出するために、Azure Backup では **NT SERVICE\AzureWLBackupPluginSvc** というアカウントが作成されます。 このアカウントはバックアップと復元に使用され、SQL sysadmin アクセス許可を必要とします。
- Azure Backup では、データベースの検出と照会に **NT AUTHORITY\SYSTEM** アカウントが利用されます。そのため、このアカウントは SQL 上でパブリック ログインである必要があります。

SQL Server VM を Azure Marketplace から作成しなかった場合、エラー **UserErrorSQLNoSysadminMembership** が発生する可能性があります。 これが発生した場合、[こちらの手順に従ってください](backup-azure-sql-database.md#set-vm-permissions)。

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Azure Backup のデータベース命名ガイドラインを確認する

データベース名には以下を使用しないでください。

  * 末尾/先頭のスペース
  * 末尾の "!"
  * 右大かっこ "]"
  * "F:\" で始まるデータベース名

Azure テーブルでサポートされていない文字のエイリアス処理は用意されていますが、これらは使用しないことをお勧めします。 [詳細情報](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)。


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server データベースを検出する

VM 上で稼働しているデータベースを検出します。

1. [Azure portal](https://portal.azure.com) で、データベースをバックアップするために使用する Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** で、 **[ワークロードはどこで実行されていますか?]** を **[Azure]** (既定値) に設定します。

4. **[何をバックアップしますか?]** で、 **[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **[バックアップの目標]**  >  **[VM 内のデータベースを検出]** で、 **[検出の開始]** を選択して、サブスクリプション内にある保護されていない VM を検索します。 サブスクリプション内にある保護されていない仮想マシンの数によっては、少し時間がかかる場合があります。

   - 保護されていない VM は検出後、名前およびリソース グループ別に一覧に表示されます。
   - VM が予想どおりに一覧表示されない場合、それが既にコンテナーにバックアップされていないかをチェックしてください。
   - 名前の同じ複数の VM が、異なるリソース グループに含まれる可能性があります。

     ![VM 内にある DB の検索中はバックアップが保留される](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM の一覧で、SQL Server データベースを稼働している VM を選択し、 **[Discover DBs]\(DB の検出\)** を選択します。

7. **[通知]** 領域でデータベース検出を追跡します。 VM 上にあるデータベースの数によっては、ジョブが完了するまで少し時間がかかる場合があります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup によって、VM 上のすべての SQL Server データベースが検出されます。 検出中、バックグラウンドでは以下が行われます。

    - Azure Backup によって、ワークロード バックアップ用のコンテナーに VM が登録されます。 登録された VM 上のすべてのデータベースは、このコンテナーに対してのみバックアップできます。
    - Azure Backup によって、**AzureBackupWindowsWorkload** 拡張機能が VM にインストールされます。 エージェントは SQL データベースにインストールされません。
    - Azure Backup によって、サービス アカウント **NT Service\AzureWLBackupPluginSvc** が VM 上に作成されます。
      - すべてのバックアップおよび復元操作に、サービス アカウントを使用します。
      - **NT Service\AzureWLBackupPluginSvc** には、SQL sysadmin 権限が必要です。 Azure Marketplace で作成されたすべての SQL Server VM には、**SqlIaaSExtension** が元からインストールされています。 **AzureBackupWindowsWorkload** 拡張機能は **SQLIaaSExtension** を使用して自動的に必要な権限を取得します。
    - VM をマーケットプレースから作成しなかった場合、VM には **SqlIaaSExtension** がインストールされておらず、検出操作はエラー メッセージ **UserErrorSQLNoSysAdminMembership** と共に失敗します。 [手順](backup-azure-sql-database.md#set-vm-permissions)に従ってこの問題を修正します。

        ![VM とデータベースを選択する](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>バックアップの構成  

次のようにバックアップを構成します。

1. **[バックアップの目標]** で **[バックアップの構成]** を選択します。

   ![[バックアップの構成] を選択する](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **[バックアップの構成]** をクリックすると、 **[バックアップする項目の選択]** ブレードが表示されます。 ここには、登録されている可用性グループとスタンドアロン SQL Server すべてが一覧表示されます。 行の左側のシェブロンを展開すると、そのインスタンスまたは Always On AG 内の保護されていないすべてのデータベースが表示されます。  

    ![スタンドアロン データベースであるすべての SQL Server インスタンスの表示](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 保護したいデータベースをすべて選択して、 **[OK]** を選択します。

   ![データベースの保護](./media/backup-azure-sql-database/select-database-to-protect.png)

   バックアップの負荷を最適化するために、Azure Backup では、1 つのバックアップ ジョブにおけるデータベースの最大数が 50 個に設定されています。

    
     * または、 **[AUTOPROTECT]\(自動保護\)** 列の対応するドロップダウンの **[オン]** オプションを選択することにより、インスタンス全体または Always On 可用性グループで自動保護を有効にすることができます。 自動保護機能では、既存のすべてのデータベースの保護が一度に有効になるだけでなく、それ以降にそのインスタンスまたは可用性グループに追加されるすべての新しいデータベースも自動的に保護されます。  

4. **[OK]** をクリックすると、 **[バックアップ ポリシー]** ブレードが開きます。

    ![Always On 可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

5. **[バックアップ ポリシーの選択]** でポリシーを選択し、 **[OK]** をクリックします。

   - 既定のポリシーを選択します:HourlyLogBackup
   - SQL 用に以前に作成された既存のバックアップ ポリシーを選択する。
   - RPO とリテンション期間の範囲に基づいて新しいポリシーを定義する。

     ![[バックアップ ポリシー] を選択する](./media/backup-azure-sql-database/select-backup-policy.png)

6. **[バックアップ]** メニューで、 **[バックアップの有効化]** を選択します。

    ![選択したバックアップ ポリシーを有効にする](./media/backup-azure-sql-database/enable-backup-button.png)

7. ポータルの **[通知]** 領域で構成の進行状況を追跡します。

    ![[通知] 領域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

- ポリシーはコンテナー レベルで作成されます。
- 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。
- バックアップ ポリシーの作成時には、日次での完全バックアップが既定値になっています。
- 差分バックアップを追加することは可能ですが、週次で実行するように完全バックアップを構成する場合だけです。
- さまざまな種類のバックアップ ポリシー[について学習してください](backup-architecture.md#sql-server-backup-types)。

バックアップ ポリシーを作成するには:

1. コンテナーで、 **[バックアップ ポリシー]**  >  **[追加]** の順にクリックします。
2. **[追加]** メニューで **[Azure VM 内の SQL Server]** をクリックします。 これでポリシーの種類が定義されます。

   ![新しいバックアップ ポリシーのポリシーの種類を選択する](./media/backup-azure-sql-database/policy-type-details.png)

3. **[ポリシー名]** に新しいポリシーの名前を入力します。
4. **完全バックアップのポリシー**で **[バックアップ頻度]** を選択し、 **[毎日]** または **[毎週]** を選びます。

   - **[毎日]** を選択する場合は、バックアップ ジョブが開始されるときに、時刻とタイム ゾーンを選択します。
   - **[完全バックアップ]** オプションをオフにすることはできないため、完全バックアップを実行する必要があります。
   - **[完全バックアップ]** をクリックし、ポリシーを表示します。
   - 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。
   - **[毎週]** を選択する場合は、バックアップ ジョブが開始されるときに、曜日、時刻、およびタイム ゾーンを選択します。

     ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **[リテンション期間]** では、すべてのオプションが既定で選択されています。 使用したくない不要なリテンション期間の制限を解除して、使用する間隔を設定します。

    - バックアップのすべての種類 (完全/差分/ログ) の最小保持期間は 7 日間です。
    - 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
    - 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間の範囲と週次でのリテンション期間の設定に基づいて保持されます。
    - 月次および年次のリテンション期間の範囲でも、同様の動作になります。

   ![[リテンション期間] の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. **完全バックアップのポリシー** メニューで、 **[OK]** を選択して設定を確定します。
7. 差分バックアップ ポリシーを追加するには、 **[差分バックアップ]** を選択します。

   ![リテンション期間の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![差分バックアップのポリシー メニューを開く](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **差分バックアップのポリシー**で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。

    - 最多で、1 日に 1 回の差分バックアップをトリガーできます。
    - 差分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。

9. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。

10. トランザクション ログ バックアップ ポリシーを追加するには、 **[ログ バックアップ]** を選択します。
11. **[ログ バックアップ]** で、 **[有効]** を選択して、頻度とリテンション期間の制御を設定します。 ログ バックアップは、毎 15 分の頻度で実行でき、最大 35 日間保持することが可能です。
12. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。

    ![ログ バックアップ ポリシーを編集する](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **[バックアップ ポリシー]** メニューで、**SQL バックアップの圧縮**を有効にするかどうかを選択します。
    - 圧縮は既定で、無効になっています。
    - バックエンドでは、Azure Backup は SQL ネイティブ バックアップの圧縮を使用します。

14. バックアップ ポリシーに対する編集が完了したら、 **[OK]** を選択します。

## <a name="run-an-ad-hoc-backup"></a>アドホック バックアップの実行

1. Recovery Services コンテナーで、[バックアップ アイテム] を選択します。
2. [SQL in Azure VM]\(Azure VM 内の SQL\) をクリックします。
3. データベースを右クリックし、[今すぐバックアップ] を選択します。
4. バックアップの種類 (完全、差分、ログ、コピーのみの完全) と圧縮の有効/無効を選択します
5. [OK] を選択して、バックアップを開始します。
6. Recovery Services コンテナーに移動し、[バックアップ ジョブ] を選択して、バックアップ ジョブを監視します。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Portal で次の作業を行いました。

> [!div class="checklist"]
> * コンテナーの作成と構成。
> * データベースの検出とバックアップの設定。
> * データベースに対する自動保護の設定。
> * アドホック バックアップの実行。

ディスクから Azure 仮想マシンを復元するには、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure VM 上の SQL Server データベースを復元する](./restore-sql-database-azure-vm.md)
 

