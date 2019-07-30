---
title: Azure への SQL Server データベースのバックアップ | Microsoft Docs
description: このチュートリアルでは、SQL Server を Azure に バックアップする方法について説明します。 また、SQL Server の復旧についても説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 42a99c1be348a24a9325173dc275d0c416b975a2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465428"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM での SQL Server Backup について

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 [Azure Backup](backup-overview.md) を使用して、Azure VM 上で稼働している SQL Server データベースをバックアップできます。

## <a name="backup-process"></a>バックアップ プロセス

このソリューションでは、SQL ネイティブ API シリーズを活用して、SQL データベースのバックアップを作成します。

* 保護し、その中のデータベースに対してクエリを実行する SQL Server VM を指定すると、Azure Backup サービスにより、`AzureBackupWindowsWorkload`  拡張機能という名前のワークロード バックアップ拡張機能が VM 内にインストールされます。
* この拡張機能は、コーディネーターと SQL プラグインで構成されています。 コーディネーターは、バックアップの構成、バックアップ、復元など、さまざまな操作のワークフローのトリガーを処理し、プラグインは実際のデータ フローを処理します。
* この VM 上のデータベースを検出できるようにするために、Azure Backup により、アカウント  `NT SERVICE\AzureWLBackupPluginSvc` が作成されます。 このアカウントはバックアップと復元に使用され、SQL sysadmin アクセス許可を必要とします。 Azure Backup では、データベースの検出と照会に `NT AUTHORITY\SYSTEM` アカウントが利用されます。そのため、このアカウントは SQL 上でパブリック ログインである必要があります。 SQL Server VM を Azure Marketplace から作成しなかった場合、エラー  **UserErrorSQLNoSysadminMembership** が発生する可能性があります。 これが発生した場合、 [こちらの手順に従ってください](backup-azure-sql-database.md)。
* 選択したデータベースに対して保護の構成をトリガーすると、バックアップ サービスにより、コーディネーターに対してバックアップ スケジュールとその他のポリシーの詳細が設定されます。これにより、拡張機能が VM 内にローカルにキャッシュされます。 
* スケジュールされた時刻になると、コーディネーターがプラグインと通信し、VDI を使用して SQL サーバーからバックアップ データのストリーム配信を開始します。  
* プラグインは Recovery Services コンテナーに直接データを送信するため、ステージングの場所は必要ありません。 データは Azure Backup サービスによって暗号化され、ストレージ アカウント内に格納されます。
* データ転送が完了すると、コーディネーターはバックアップ サービスを使用してコミットを確認します。

  ![SQL のバックアップ アーキテクチャ](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>開始する前に

開始する前に、以下を確認します。

1. Azure で SQL Server インスタンスを稼働させていることを確認する。 マーケットプレースで [SQL Server インスタンスをすばやく作成](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)できます。
2. [機能の考慮事項](#feature-consideration-and-limitations)と[シナリオのサポート](#scenario-support)を確認する。
3. このシナリオに関する[一般的な質問を確認](faq-backup-sql-server.md)する。

## <a name="scenario-support"></a>シナリオのサポート

**サポート** | **詳細**
--- | ---
**サポートされているデプロイ** | SQL Marketplace の Azure VM と、Marketplace 以外の (SQL Server が手動でインストールされる) VM がサポートされています。
**サポートされている地域** | オーストラリア南東部 (ASE)、オーストラリア東部 (AE) <br> ブラジル南部 (BRS)<br> カナダ中部 (CNC)、カナダ東部 (CE)<br> 東南アジア (SEA)、東アジア (EA) <br> 米国東部 (EUS)、米国東部 2 (EUS2)、米国中西部 (WCUS)、米国西部 (WUS)、米国西部 2 (WUS 2)、米国中北部 (NCUS)、米国中部 (CUS)、米国中南部 (SCUS) <br> インド中部 (INC)、インド南部 (INS) <br> 東日本 (JPE)、西日本 (JPW) <br> 韓国中部 (KRC)、韓国南部 (KRS) <br> 北ヨーロッパ (NE)、西ヨーロッパ <br> 英国南部 (UKS)、英国西部 (UKW)
**サポートされているオペレーティング システム** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012<br/><br/> Linux は現在サポートされていません。
**サポートされる SQL Server のバージョン** | SQL Server 2017 (詳細は[こちら](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017))、SQL Server 2016 および SP (詳細は[こちら](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack))、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。
**サポートされている .NET バージョン** | VM 内にインストールされている .NET Framework 4.5.2 以降

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>SQL Server 2008 および SQL Server 2008 R2 のサポート

Azure Backup による [EOS SQL Sever](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) (SQL Server 2008 および SQL Server 2008 R2) のサポートが最近発表されました。 このソリューションは、現在、EOS SQL Server 用のプレビュー版であり、次の構成をサポートしています。

1. Windows 2008 R2 SP1 で実行されている SQL Server 2008 および SQL Server 2008 R2
2. .NET Framework 4.5.2 以降を VM にインストールする必要がある
3. FCI とミラー化されたデータベースのバックアップはサポートされない

この機能の一般提供が開始されるまで、ユーザーはこの機能について課金されません。 その他の[機能の考慮事項と制限事項](#feature-consideration-and-limitations)もすべてこれらのバージョンに適用されます。 [レジストリ キー](backup-sql-server-database-azure-vms.md#add-registry-key-to-enable-registration)の設定など、SQL Servers 2008 および 2008 R2 で保護を構成する前に、[前提条件](backup-sql-server-database-azure-vms.md#prerequisites)を参照してください (この機能が一般提供された場合、この手順は不要です)。


## <a name="feature-consideration-and-limitations"></a>機能の考慮事項と制限事項

- SQL Server のバックアップは、Azure portal または **PowerShell** を使用して構成できます。 CLI はサポートされていません。
- このソリューションは、Azure Resource Manager VM とクラシック VM のどちらの種類の[デプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)でもサポートされます。
- SQL Server を稼働している VM では、Azure パブリック IP アドレスにアクセスするためにインターネット接続を必要とします。
- SQL Server **フェールオーバー クラスター インスタンス (FCI)** と SQL Server Always on フェールオーバー クラスター インスタンスはサポートされません。
- ミラー データベースおよびデータベース スナップショットのバックアップ操作および復元操作はサポートされていません。
- スタンドアロンの SQL Server インスタンスまたは SQL Always On 可用性グループをバックアップするために複数のバックアップ ソリューションを使用すると、バックアップに失敗する可能性があるため、そうしないようにしてください。
- また、可用性グループの 2 つのノードを、同じまたは異なるソリューションを使用して個別にバックアップすると、バックアップに失敗する可能性があります。
- **読み取り専用**データベースについては、Azure Backup ではバックアップの種類として完全とコピーのみの完全だけをサポートしています。
- 多数のファイルがあるデータベースは保護できません。 サポートされるファイルの最大数は**約 1,000 個**です。  
- コンテナーあたり**約 2,000 個**の SQL Server データベースをバックアップできます。 データベースの数が多い場合、複数のコンテナーを作成できます。
- 一度に最大 **50** 個のデータベースのバックアップを構成できます。この制限により、バックアップの負荷が最適化されます。
- サポートされているデータベースの最大サイズは **2 TB** です。これを超えるサイズの場合は、パフォーマンスの問題が生じる可能性があります。
- サーバーあたりいくつのデータベースを保護できるかを把握するには、帯域幅、VM のサイズ、バックアップの頻度、データベースのサイズなどの要因を考慮する必要があります。VM リソースとバックアップ ポリシーに基づいて、サーバーごとに使用できるデータベースの概数を算出する、リソース プランナーを[ダウンロード](http://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)します。
- 可用性グループの場合は、バックアップはいくつかの要因に基づいて異なるノードから作成されます。 可用性グループのバックアップ動作を以下にまとめています。

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Always On 可用性グループの場合のバックアップ動作

バックアップの構成は、AG の 1 つのノードでのみ行うことをお勧めします。 バックアップは必ず、プライマリ ノードと同じリージョンに構成してください。 つまり、バックアップの構成場所のリージョンには必ず、プライマリ ノードが存在している必要があります。 AG のすべてのノードがバックアップの構成場所と同じリージョンにあれば問題ありません。

**複数リージョンにまたがる AG**
- バックアップの設定に関係なく、バックアップの構成場所とは異なるリージョン内のノードからバックアップが実行されることはありません。 複数リージョンにまたがるバックアップはサポートされないためです。 ノードが 2 つしか存在せず、なおかつ、セカンダリ ノードが別のリージョンに存在する場合、プライマリ ノード側からは引き続きがバックアップが実行されます (ただし、バックアップの設定が "セカンダリのみ" になっている場合を除く)。
- バックアップの構成場所とは異なるリージョンへのフェールオーバーが実行された場合、フェールオーバーされた側のリージョン内のノードでは、バックアップに失敗します。

バックアップ設定とバックアップの種類 (完全/差分/ログ/コピーのみの完全) に応じて、バックアップは特定のノード (プライマリ/セカンダリ) から作成されます。

- **バックアップの設定:プライマリ**

**バックアップの種類** | **Node**
    --- | ---
    完全 | プライマリ
    差分 | プライマリ
    ログ |  プライマリ
    コピーのみの完全 |  プライマリ

- **バックアップの設定:セカンダリのみ**

**バックアップの種類** | **Node**
--- | ---
完全 | プライマリ
差分 | プライマリ
ログ |  セカンダリ
コピーのみの完全 |  セカンダリ

- **バックアップの設定:セカンダリ**

**バックアップの種類** | **Node**
--- | ---
完全 | プライマリ
差分 | プライマリ
ログ |  セカンダリ
コピーのみの完全 |  セカンダリ

- **バックアップの設定なし**

**バックアップの種類** | **Node**
--- | ---
完全 | プライマリ
差分 | プライマリ
ログ |  セカンダリ
コピーのみの完全 |  セカンダリ

## <a name="set-vm-permissions"></a>VM のアクセス許可を設定する

  SQL Server で検出を実行すると、Azure Backup により以下が行われます。

* AzureBackupWindowsWorkload 拡張機能を追加します。
* 仮想マシン上のデータベースを検出するために、NT SERVICE\AzureWLBackupPluginSvc アカウントが作成されます。 このアカウントはバックアップと復元に使用され、SQL sysadmin アクセス許可を必要とします。
* VM 上で実行されているデータベースを検出します。Azure Backup は NT AUTHORITY\SYSTEM アカウントを使用します。 このアカウントは SQL でのパブリック サインインである必要があります。

Azure Marketplace で SQL Server VM を作成しなかった場合、または SQL 2008 および 2008 R2 を使用している場合、**UserErrorSQLNoSysadminMembership** エラーが発生する可能性があります。

Windows 2008 R2 で **SQL 2008** および **2008 R2** を実行している場合にアクセス許可を付与するには、[こちら](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)を参照してください。

その他のすべてのバージョンでは、次の手順を使用してアクセス許可を修正します。

  1. SQL Server sysadmin アクセス許可があるアカウントを使用して、SQL Server Management Studio (SSMS) にサインインします。 特殊な権限が必要でない限り、Windows 認証が機能するはずです。
  2. SQL Server で、**Security/Logins** フォルダーを開きます。

      ![セキュリティ/ログイン フォルダーを開いてアカウントを表示する](./media/backup-azure-sql-database/security-login-list.png)

  3. **[ログイン]** フォルダーを右クリックし、 **[新しいログイン]** を選択します。 **[ログイン - 新規作成]** で **[検索]** を選択します。

      ![[ログイン - 新規作成] ダイアログ ボックスで [検索] を選択する](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 仮想サービス アカウント **NT SERVICE\AzureWLBackupPluginSvc** は、仮想マシンの登録と SQL 検出フェーズ中に作成済みです。 **[選択するオブジェクト名を入力します]** に示されているようにアカウント名を入力します。 **[名前の確認]** を選択して名前解決を行います。 Click **OK**.

      ![[名前の確認] を選択して不明なサービス名を解決する](./media/backup-azure-sql-database/check-name.png)

  5. **[サーバー ロール]** で、**sysadmin** ロールが選択されていることを確認します。 Click **OK**. この時点で、必要な権限が揃っているはずです。

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
3. [ログイン] を右クリックし、 *[新しいログイン]* をクリックします

    ![SSMS を使用した新しいログイン](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. [全般] タブに移動し、ログイン名として「**NT AUTHORITY\SYSTEM**」と入力します。

    ![SSMS のログイン名](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. *[サーバー ロール]* に移動し、 *[public]* および *[sysadmin]* ロールを選択します。

    ![SSMS でのロールの選択](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. *[状態]* に移動します。 データベース エンジンに接続するためのアクセス許可を*付与*し、ログインを*有効*にします。

    ![SSMS でのアクセス許可の付与](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. [OK] をクリックします。
8. 同じ一連の手順 (上記の 1 から 7) を繰り返して、SQL Server インスタンスに NT Service\AzureWLBackupPluginSvc ログインを追加します。 ログインが既に存在する場合は、そのログインに sysadmin サーバー ロールがあることを確認します。また [状態] で、そのログインにデータベース エンジンに接続するためのアクセス許可が付与されていること、およびそのログインが有効になっていることを確認します。
9. アクセス許可を付与した後、ポータルで **DB を再検出**します: コンテナー **->** バックアップ インフラストラクチャ **->** Azure VM でのワークロード。

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


## <a name="next-steps"></a>次の手順

* SQL Server データベースのバックアップに[ついて学習します](backup-sql-server-database-azure-vms.md)。
* バックアップされた SQL Server データベースの復元に[ついて学習します](restore-sql-database-azure-vm.md)。
* バックアップされた SQL Server データベースの管理に[ついて学習します](manage-monitor-sql-database-backup.md)。
