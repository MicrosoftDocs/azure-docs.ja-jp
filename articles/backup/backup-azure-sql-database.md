---
title: Azure への SQL Server データベースのバックアップ
description: この記事では、SQL Server を Azure に バックアップする方法について説明します。 また、SQL Server の復旧についても説明します。
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316845"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM での SQL Server Backup について

[Azure Backup](backup-overview.md) からは、Azure VM で実行されている SQL Server をバックアップする、ストリームベースの特別なソリューションがあります。 このソリューションは、ゼロインフラストラクチャ バックアップ、長期間保存、中央管理といった Azure Backup の長所と足並みをそろえるものです。 さらに、SQL Server だけには次の長所があります。

1. 完全、差分、ログという全種類のバックアップに対応するワークロード対応バックアップ
2. 15 分間の RPO (回復ポイントの目標) と頻繁に行われるログのバックアップ
3. 特定の時点に復旧 (1 秒まで)
4. 個々のデータベース レベルのバックアップと復旧

現在サポートされているバックアップと復元のシナリオを確認するには、[サポートマトリックス](sql-support-matrix.md#scenario-support)を参照してください。

## <a name="backup-process"></a>バックアップ プロセス

このソリューションでは、SQL ネイティブ API シリーズを活用して、SQL データベースのバックアップを作成します。

* SQL Server VM (保護対象で、その中のデータベースに対してクエリを実行するもの) を指定すると、Azure Backup サービスにより、`AzureBackupWindowsWorkload` 拡張機能という名前のワークロード バックアップ拡張機能が VM 上にインストールされます。
* この拡張機能は、コーディネーターと SQL プラグインで構成されています。 コーディネーターは、バックアップの構成、バックアップ、復元など、さまざまな操作のワークフローのトリガーを処理し、プラグインは実際のデータ フローを処理します。
* この VM 上のデータベースを検出できるようにするために、Azure Backup により、アカウント `NT SERVICE\AzureWLBackupPluginSvc` が作成されます。 このアカウントはバックアップと復元に使用され、SQL sysadmin アクセス許可を必要とします。 `NT SERVICE\AzureWLBackupPluginSvc` アカウントは[仮想サービス アカウント](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)であるため、パスワードの管理は不要です。 Azure Backup では、データベースの検出と照会に `NT AUTHORITY\SYSTEM` アカウントが使用されます。そのため、このアカウントは SQL 上でパブリック ログインである必要があります。 SQL Server VM を Azure Marketplace から作成しなかった場合、エラー **UserErrorSQLNoSysadminMembership** が発生する可能性があります。 これが発生した場合、[こちらの手順に従ってください](#set-vm-permissions)。
* 選択したデータベースに対して保護の構成をトリガーすると、バックアップ サービスにより、コーディネーターに対してバックアップ スケジュールとその他のポリシーの詳細が設定されます。これにより、拡張機能が VM 内にローカルにキャッシュされます。
* スケジュールされた時刻になると、コーディネーターがプラグインと通信し、VDI を使用して SQL サーバーからバックアップ データのストリーム配信を開始します。  
* プラグインは Recovery Services コンテナーに直接データを送信するため、ステージングの場所が必要ありません。 データは Azure Backup サービスによって暗号化され、ストレージ アカウント内に格納されます。
* データ転送が完了すると、コーディネーターはバックアップ サービスを使用してコミットを確認します。

  ![SQL のバックアップ アーキテクチャ](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>開始する前に

開始する前に、次の要件を確認します。

1. Azure で SQL Server インスタンスを稼働させていることを確認する。 マーケットプレースで [SQL Server インスタンスをすばやく作成](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md)できます。
2. [機能に関する考慮事項](sql-support-matrix.md#feature-considerations-and-limitations)と[シナリオのサポート](sql-support-matrix.md#scenario-support)を確認する。
3. このシナリオに関する[一般的な質問を確認](faq-backup-sql-server.md)する。

## <a name="set-vm-permissions"></a>VM のアクセス許可を設定する

  SQL Server で検出を実行すると、Azure Backup により以下が行われます。

* AzureBackupWindowsWorkload 拡張機能を追加します。
* 仮想マシン上のデータベースを検出するために、NT SERVICE\AzureWLBackupPluginSvc アカウントが作成されます。 このアカウントはバックアップと復元に使用され、SQL sysadmin アクセス許可を必要とします。
* VM 上で実行されているデータベースを検出します。Azure Backup は NT AUTHORITY\SYSTEM アカウントを使用します。 このアカウントは SQL でのパブリック サインインである必要があります。

Azure Marketplace で SQL Server VM を作成しなかった場合、または SQL 2008 または 2008 R2 を使用している場合、**UserErrorSQLNoSysadminMembership** エラーが発生する可能性があります。

Windows 2008 R2 で **SQL 2008** および **2008 R2** を実行している場合にアクセス許可を付与するには、[こちら](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)を参照してください。

その他のすべてのバージョンでは、次の手順を使用してアクセス許可を修正します。

  1. SQL Server sysadmin アクセス許可があるアカウントを使用して、SQL Server Management Studio (SSMS) にサインインします。 特殊な権限が必要でない限り、Windows 認証が機能するはずです。
  2. SQL Server で、**Security/Logins** フォルダーを開きます。

      ![セキュリティ/ログイン フォルダーを開いてアカウントを表示する](./media/backup-azure-sql-database/security-login-list.png)

  3. **[ログイン]** フォルダーを右クリックし、 **[新しいログイン]** を選択します。 **[ログイン - 新規作成]** で **[検索]** を選択します。

      ![[ログイン - 新規作成] ダイアログ ボックスで [検索] を選択する](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 仮想サービス アカウント **NT SERVICE\AzureWLBackupPluginSvc** は、仮想マシンの登録と SQL 検出フェーズ中に作成済みです。 **[選択するオブジェクト名を入力します]** に示されているようにアカウント名を入力します。 **[名前の確認]** を選択して名前解決を行います。 **[OK]** を選択します。

      ![[名前の確認] を選択して不明なサービス名を解決する](./media/backup-azure-sql-database/check-name.png)

  5. **[サーバー ロール]** で、**sysadmin** ロールが選択されていることを確認します。 **[OK]** を選択します。 この時点で、必要な権限が揃っているはずです。

      ![sysadmin サーバー ロールが選択されていることを確認する](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 次にデータベースを Recovery Services コンテナーと関連付けます。 Azure portal の **[保護されたサーバー]** 一覧で、エラー状態にあるサーバーを右クリックし、 **[DB の再検出]** を選択します。

      ![サーバーに適切な権限があることを確認する](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. **[通知]** 領域で進行状況をチェックします。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

      ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> SQL Server に SQL Server の複数のインスタンスがインストールされている場合は、すべての SQL インスタンスに **NT Service\AzureWLBackupPluginSvc** アカウントに対する sysadmin アクセス許可を追加する必要があります。

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin に SQL 2008 および SQL 2008 R2 のアクセス許可を付与する

SQL Server インスタンスに **NT AUTHORITY\SYSTEM** および **NT Service\AzureWLBackupPluginSvc** ログインを追加します。

1. オブジェクト エクスプローラーで SQL Server インスタンスにアクセスします。
2. [セキュリティ] -> [ログイン] に移動します
3. [ログイン] を右クリックし、 *[新しいログイン]* を選択します

    ![SSMS を使用した新しいログイン](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. [全般] タブに移動し、ログイン名として「**NT AUTHORITY\SYSTEM**」と入力します。

    ![SSMS のログイン名](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. *[サーバー ロール]* に移動し、 *[public]* および *[sysadmin]* ロールを選択します。

    ![SSMS でのロールの選択](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. *[状態]* に移動します。 データベース エンジンに接続するためのアクセス許可を *付与* し、ログインを *有効* にします。

    ![SSMS でのアクセス許可の付与](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. [OK] を選択します。
8. 同じ一連の手順 (上記の 1 から 7) を繰り返して、SQL Server インスタンスに NT Service\AzureWLBackupPluginSvc ログインを追加します。 ログインが既に存在する場合は、そのログインに sysadmin サーバー ロールがあることを確認します。また [状態] で、そのログインにデータベース エンジンに接続するためのアクセス許可が付与されていること、およびそのログインが有効になっていることを確認します。
9. アクセス許可を付与した後、ポータルで **DB を再検出** します: コンテナー **->** バックアップ インフラストラクチャ **->** Azure VM でのワークロード。

    ![Azure portal での DB の再検出](media/backup-azure-sql-database/sql-rediscover-dbs.png)

または、管理者モードで次の PowerShell コマンドを実行して、アクセス許可の付与を自動化できます。 インスタンス名は、既定では MSSQLSERVER に設定されます。 必要に応じて、スクリプトでインスタンス名引数を変更します。

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>次のステップ

* SQL Server データベースのバックアップに[ついて学習します](backup-sql-server-database-azure-vms.md)。
* バックアップされた SQL Server データベースの復元に[ついて学習します](restore-sql-database-azure-vm.md)。
* バックアップされた SQL Server データベースの管理に[ついて学習します](manage-monitor-sql-database-backup.md)。
