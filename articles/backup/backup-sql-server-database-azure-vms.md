---
title: Azure VM での SQL Server データベースのバックアップ | Microsoft Docs
description: Azure VM で SQL Server データベースをバックアップする方法について説明します
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: vijayts
ms.openlocfilehash: 9870acdebaa4bf700bb18b5fa1d3a26f6338a10b
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464983"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Azure VM での SQL Server データベースのバックアップ

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 [Azure Backup](backup-overview.md) を使用して、Azure 仮想マシン (VM) 上で稼働している SQL Server データベースをバックアップできます。

この記事では、Azure VM 上で稼働している SQL Server データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。

この記事では、以下の方法について説明します。

> [!div class="checklist"]
> * コンテナーの作成と構成。
> * データベースの検出とバックアップの設定。
> * データベースに対する自動保護の設定。


## <a name="prerequisites"></a>前提条件

SQL Server データベースをバックアップする前に、次の基準を確認してください。

1. SQL Server インスタンスをホストする VM として、同じリージョンまたはロケールの [Recovery Services コンテナー](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)を特定または作成する。
2. VM が[ネットワーク接続](backup-sql-server-database-azure-vms.md#establish-network-connectivity)を備えていることを確認する。
3. SQL Server データベースが、[Azure Backup のためのデータベースの命名に関するガイドライン](#database-naming-guidelines-for-azure-backup)に従っていることを確認する。
4. 特に SQL 2008 および 2008 R2 のためには、[レジストリ キーを追加](#add-registry-key-to-enable-registration)してサーバーの登録を有効にする。 機能が一般提供されている場合、この手順は必要ありません。
5. データベースに対して有効になっているバックアップ ソリューションが他にないことをチェックする。 データベースをバックアップする前に、他のすべての SQL Server バックアップを無効にします。

> [!NOTE]
> Azure VM に対してだけでなく、VM 上で稼働している SQL Server データベースに対しても、競合を発生させることなく Azure Backup を有効にすることができます。


### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

すべての操作において、SQL Server VM では、Azure パブリック IP アドレスへの接続を必要とします。 Azure パブリック IP アドレスへの接続がないと、VM の操作 (データベースの検出、バックアップの構成、バックアップのスケジュール、復旧ポイントの復元など) が失敗します。

次のオプションのいずれかを使用して接続を確立します。

- **Azure データセンターの IP 範囲を許可する**。 このオプションは、ダウンロードで [IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。 ネットワーク セキュリティ グループ (NSG) にアクセスするには、Set-AzureNetworkSecurityRule コマンドレットを使用します。 安全な受信者がリージョン固有 IP のみをリストに登録する場合は、認証を有効にするために Azure Active Directory (Azure AD) サービス タグで信頼できる宛先のリストを更新する必要があります。

- **NSG タグを使用してアクセスを許可する**。 NSG を使用して接続を制限する場合、このオプションは、AzureBackup タグを使用して、Azure Backup への発信アクセスを許可する規則を NSG に追加します。 認証とデータ転送のための接続を許可するには、このタグに加えて、Azure AD と Azure Storage に対応する[規則](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)も必要です。 AzureBackup タグは現在、PowerShell でのみ使用可能です。 AzureBackup タグを使用して規則を作成するには:

    - Azure アカウントの資格情報を追加して各国のクラウドを更新する<br/>
    `Add-AzureRmAccount`

    - NSG サブスクリプションを選択する<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - NSG を選択する<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Azure Backup サービス タグの発信許可規則を追加する<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - NSG を保存する<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Azure Firewall タグを使用してアクセスを許可する**。 Azure Firewall を使用している場合は、AzureBackup [FQDN タグ](https://docs.microsoft.com/azure/firewall/fqdn-tags)を使用してアプリケーション規則を作成します。 これにより、Azure Backup への発信アクセスを許可します。
- **トラフィックをルーティングするために HTTP プロキシ サーバーをデプロイする**。 Azure VM 上の SQL Server データベースをバックアップする場合、VM 上のバックアップ拡張機能によって HTTPS API が使用され、管理コマンドが Azure Backup に送信されてデータが Azure Storage に送信されます。 また、バックアップ拡張機能では、認証に Azure AD を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 パブリック インターネットにアクセスできるように構成されたコンポーネントはバックアップ拡張機能のみです。

接続オプションには、次の長所と短所があります。

**オプション** | **長所** | **短所**
--- | --- | ---
IP 範囲を許可する | 追加のコストが発生しない | IP アドレス範囲が時間の経過と共に変化するため、管理が複雑である <br/><br/> Azure Storage だけでなく、Azure 全体へのアクセスを提供することになる
NSG サービス タグを使用する | 範囲の変更が自動的にマージされるため管理しやすい <br/><br/> 追加のコストが発生しない <br/><br/> | NSG でのみ使用可能 <br/><br/> サービス全体へのアクセスを提供する
Azure Firewall の FQDN タグを使用する | 必要な FQDN が自動的に管理されるため管理しやすい | Azure Firewall でのみ使用可能
HTTP プロキシを使用する | 許可するストレージ URL をプロキシで詳細に制御可能 <br/><br/> VM に対するインターネット アクセスを単一の場所で実現 <br/><br/> Azure の IP アドレスの変更の影響を受けない | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure Backup のためのデータベースの命名に関するガイドライン

データベースの名前に次の要素を使用しないでください。

  * 末尾および先頭のスペース
  * 末尾の感嘆符 (!)
  * 終わり角かっこ (])
  * セミコロン ';'
  * スラッシュ '/'

サポートされていない文字のエイリアス処理は用意されていますが、これらは使用しないことをお勧めします。 詳細については、「 [Table サービス データ モデルについて](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)」を参照してください。

### <a name="add-registry-key-to-enable-registration"></a>レジストリ キーを追加して登録を有効にする

1. Regedit を開きます
2. 次のレジストリ ディレクトリ パスを作成します: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (Microsoft の下に WorkloadBackup を作成してから、その下に TestHook というキーを作成する必要があります)。
3. このレジストリ ディレクトリ パスの下に、新しい "文字列値" を作成する必要があります。文字列名は **AzureBackupEnableWin2K8R2SP1** に、値は **True** にします

    ![登録を有効にするため RegEdit](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

または、次のコマンドを使用して .reg ファイルを実行し、この手順を自動化できます。

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server データベースを検出する

VM 上で稼働しているデータベースを検出する方法:

1. [Azure portal](https://portal.azure.com) で、データベースをバックアップするために使用する Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** で、 **[ワークロードはどこで実行されていますか?]** を **[Azure]** に設定します。

4. **[何をバックアップしますか?]** で、 **[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **[バックアップの目標]**  >  **[VM 内のデータベースを検出]** で、 **[検出の開始]** を選択して、サブスクリプション内にある保護されていない VM を検索します。 サブスクリプション内にある保護されていない VM の数によっては、この検索に少し時間がかかる場合があります。

   - 保護されていない VM は検出後、名前およびリソース グループ別に一覧に表示されます。
   - VM が予想どおりに一覧表示されない場合、それが既にコンテナーにバックアップされていないかを確認してください。
   - 名前の同じ複数の VM が、異なるリソース グループに含まれる可能性があります。

     ![VM 内にある DB の検索中はバックアップが保留される](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM の一覧で、SQL Server データベースを稼働している VM を選択し、 **[Discover DBs]\(DB の検出\)** を選択します。

7. **[通知]** でデータベース検出を追跡します。 この操作に必要な時間は、VM のデータベースの数によって異なります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup によって、VM 上のすべての SQL Server データベースが検出されます。 検出中、バックグラウンドで以下の要素が発生します。

    - Azure Backup によって、ワークロード バックアップ用のコンテナーに VM が登録されます。 登録された VM 上のすべてのデータベースは、このコンテナーのみに対してバックアップできます。
    - Azure Backup によって、AzureBackupWindowsWorkload 拡張機能が VM にインストールされます。 エージェントは SQL データベースにインストールされません。
    - Azure Backup によって、サービス アカウント NT Service\AzureWLBackupPluginSvc が VM 上に作成されます。
      - すべてのバックアップおよび復元操作に、サービス アカウントを使用します。
      - NT Service\AzureWLBackupPluginSvc には、SQL sysadmin 権限が必要です。 Marketplace で作成されたすべての SQL Server VM には、SqlIaaSExtension が元からインストールされています。 AzureBackupWindowsWorkload 拡張機能は SQLIaaSExtension を使用して自動的に必要な権限を取得します。
    - VM を Marketplace から作成しなかった場合や、SQL 2008 および 2008 R2 を実行している場合、VM に SqlIaaSExtension がインストールされないことあり、検出操作はエラー メッセージ UserErrorSQLNoSysAdminMembership を示して失敗します。 この問題を修正するには、「[VM のアクセス許可を設定する](backup-azure-sql-database.md#set-vm-permissions)」の手順に従います。

        ![VM とデータベースを選択する](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>バックアップの構成  

1. **[バックアップの目標]**  >  **[手順 2: バックアップの構成]** で、 **[バックアップの構成]** を選択します。

   ![[バックアップの構成] を選択する](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **[バックアップする項目の選択]** には、登録されている可用性グループおよびスタンドアロン SQL Server インスタンスがすべて表示されます。 行の左側にある矢印を選択して、そのインスタンスまたは Always On 可用性グループ内の保護されていないデータベースの全一覧を展開します。  

    ![スタンドアロン データベースであるすべての SQL Server インスタンスの表示](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 保護したいデータベースをすべて選択してから、 **[OK]** を選択します。

   ![データベースの保護](./media/backup-azure-sql-database/select-database-to-protect.png)

   バックアップの負荷を最適化するために、Azure Backup では、1 つのバックアップ ジョブにおけるデータベースの最大数が 50 個に設定されています。

     * 50 個を超えるデータベースを保護するには、複数のバックアップを構成します。
     * [](#enable-auto-protection)インスタンス全体または Always On 可用性グループを有効にします。 **[AUTOPROTECT]\(自動保護\)** ドロップダウン リストで、 **[ON]\(オン\)** を選択してから **[OK]** を選択します。

    > [!NOTE]
    > [自動保護](#enable-auto-protection)機能では、既存のすべてのデータベースの保護が一度に有効になるだけでなく、そのインスタンスまたは可用性グループに追加されたすべての新しいデータベースも自動的に保護されます。  

4. **[OK]** を選択して **[バックアップ ポリシー]** を開きます。

    ![Always On 可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

5. **[バックアップ ポリシー]** で、ポリシーを選択してから  **[OK]** を選択します。

   - 既定のポリシーを HourlyLogBackup として選択します。
   - SQL 用に以前に作成された既存のバックアップ ポリシーを選択する。
   - RPO とリテンション期間の範囲に基づいて新しいポリシーを定義する。

     ![[バックアップ ポリシー] を選択する](./media/backup-azure-sql-database/select-backup-policy.png)

6.  **[バックアップ]** で、 **[バックアップの有効化]** を選択します。

    ![選択したバックアップ ポリシーを有効にする](./media/backup-azure-sql-database/enable-backup-button.png)

7. ポータルの  **[通知]**   領域で構成の進行状況を追跡します。

    ![[通知] 領域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

- ポリシーはコンテナー レベルで作成されます。
- 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。
- バックアップ ポリシーの作成時には、日次での完全バックアップが既定値になっています。
- 差分バックアップを追加することは可能ですが、週次で実行するように完全バックアップを構成する場合だけです。
- [さまざまな種類のバックアップ ポリシー](backup-architecture.md#sql-server-backup-types)について学習してください。

バックアップ ポリシーを作成するには:

1. コンテナーで、 **[バックアップ ポリシー]**  >  **[追加]** の順に選択します。
2. **[追加]** で、 **[Azure VM 内の SQL Server]** を選択してポリシーの種類を定義します。

   ![新しいバックアップ ポリシーのポリシーの種類を選択する](./media/backup-azure-sql-database/policy-type-details.png)

3. **[ポリシー名]** に新しいポリシーの名前を入力します。
4. **[Full Backup policy]\(完全バックアップのポリシー\)** で、 **[バックアップ頻度]** を選択します。 **[毎日]** または **[毎週]** を選択します。

   - **[毎日]** を選択する場合は、バックアップ ジョブが開始されるときに、時刻とタイム ゾーンを選択します。
   - **[毎週]** を選択する場合は、バックアップ ジョブが開始されるときに、曜日、時刻、およびタイム ゾーンを選択します。
   - **[完全バックアップ]** オプションをオフにすることはできないため、完全バックアップを実行します。
   - **[完全バックアップ]** を選択し、ポリシーを表示します。
   - 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。

     ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **[リテンション期間]** では、すべてのオプションが既定で選択されています。 使用しないリテンション期間の制限を解除してから、使用する間隔を設定します。

    - バックアップのすべての種類 (完全、差分、ログ) の最小リテンション期間は 7 日間です。
    - 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
    - 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間の範囲と週次でのリテンション期間の設定に基づいて保持されます。
    - 月次および年次のリテンション期間の範囲でも、同様の動作になります。

       ![[リテンション期間] の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. **完全バックアップのポリシー** メニューで、 **[OK]** を選択して設定を確定します。
7. 差分バックアップ ポリシーを追加するには、 **[差分バックアップ]** を選択します。

   ![リテンション期間の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![差分バックアップのポリシー メニューを開く](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **差分バックアップのポリシー**で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。

    - 差分バックアップは 1 日に 1 回のみトリガーできます。
    - 差分バックアップは、最大 180 日間保持できます。 リテンション期間をさらに長くするには、完全バックアップを使用します。

9. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。

10. トランザクション ログ バックアップ ポリシーを追加するには、 **[ログ バックアップ]** を選択します。
11. **[ログ バックアップ]** で、 **[有効]** を選択して、頻度とリテンション期間の制御を設定します。 ログ バックアップは、15 分ごとの頻度で実行でき、最大 35 日間保持することが可能です。
12. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。

    ![ログ バックアップ ポリシーを編集する](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **[バックアップ ポリシー]** メニューで、**SQL バックアップの圧縮**を有効にするかどうかを選択します。
    - 圧縮は既定で、無効になっています。
    - バックエンドでは、Azure Backup は SQL ネイティブ バックアップの圧縮を使用します。

14. バックアップ ポリシーに対する編集が完了したら、 **[OK]** を選択します。


### <a name="modify-policy"></a>ポリシーを変更する
バックアップの頻度や保有期間の範囲を変更するためにポリシーを変更します。

> [!NOTE]
> 保持期間の変更は、新しい復旧ポイントだけでなく古い復旧ポイントすべてにもさかのぼって適用されます。

コンテナーのダッシュボードで、 **[管理]**  >  **[バックアップ ポリシー]** に移動し、編集するポリシーを選択します。

  ![バックアップ ポリシーの管理](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>自動保護を有効にする  

自動保護を有効にして、すべての既存および将来のデータベースを、スタンドアロンの SQL Server インスタンスに、または Always On 可用性グループに自動的にバックアップすることができます。

- 自動保護のために一度に選択できるデータベースの数に制限はありません。
- 自動保護を有効化するときに、インスタンス内のデータベースを選んで保護したり保護から除外したりすることはできません。
- 保護されているデータベースが既にインスタンスに含まれる場合は、自動保護を有効にした後でも、それぞれのポリシーに従って保護されたままです。 後から追加された、保護されていないすべてのデータベースには、自動保護の有効化の時点で定義し、 **[バックアップの構成]** で一覧に表示される 1 つのポリシーのみが適用されます。 ただし、自動保護データベースに関連付けたポリシーは後から変更できます。  

自動保護を有効にするには:

  1. **[Items to backup]\(バックアップする項目\)** で、自動保護を有効にしたいインスタンスを選択します。
  2. **[AUTOPROTECT]\(自動保護\)** の下のドロップダウン リストを選択し、 **[ON]\(オン\)** を選択してから **[OK]** を選択します。

      ![可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. バックアップはすべてのデータベースに対して一括で構成され、 **[バックアップ ジョブ]** で追跡できます。

自動保護を無効にする必要がある場合、 **[バックアップの構成]** のインスタンス名を選択してから、インスタンスについて **[自動保護を無効にする]** を選択します。 すべてのデータベースは引き続きバックアップされますが、今後のデータベースは自動的に保護されなくなります。

![そのインスタンスの自動保護を無効にする](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>次の手順

以下の項目について説明します。

- [バックアップした SQL Server データベースの復元](restore-sql-database-azure-vm.md)
- [バックアップした SQL Server データベースの管理](manage-monitor-sql-database-backup.md)
