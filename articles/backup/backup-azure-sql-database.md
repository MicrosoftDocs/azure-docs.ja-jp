---
title: Azure への SQL Server データベースのバックアップ | Microsoft Docs
description: このチュートリアルでは、SQL Server を Azure に バックアップする方法について説明します。 また、SQL Server の復旧についても説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58847301"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Azure VM での SQL Server Backup について

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 [Azure Backup](backup-overview.md) を使用して、Azure VM 上で稼働している SQL Server データベースをバックアップできます。

## <a name="backup-process"></a>バックアップ プロセス

このソリューションでは、SQL ネイティブ API シリーズを活用して、SQL データベースのバックアップを作成します。

* 保護し、その中のデータベースに対してクエリを実行する SQL Server VM を指定すると、Azure Backup サービスにより、`AzureBackupWindowsWorkload` 拡張機能という名前のワークロード バックアップ拡張機能が VM 内にインストールされます。
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
**サポートされる SQL Server のバージョン** | SQL Server 2017、SQL Server 2016、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。
**サポートされている .NET バージョン** | VM 内にインストールされている .NET Framework 4.5.2 以降

## <a name="feature-consideration-and-limitations"></a>機能の考慮事項と制限事項

- SQL Server のバックアップは、Azure portal または **PowerShell** を使用して構成できます。 CLI はサポートされていません。
- SQL Server を稼働している VM では、Azure パブリック IP アドレスにアクセスするためにインターネット接続を必要とします。
- SQL Server **フェールオーバー クラスター インスタンス (FCI)** と SQL Server Always on フェールオーバー クラスター インスタンスはサポートされません。
- ミラー データベースおよびデータベース スナップショットのバックアップ操作および復元操作はサポートされていません。
- スタンドアロンの SQL Server インスタンスまたは SQL Always On 可用性グループをバックアップするために複数のバックアップ ソリューションを使用すると、バックアップに失敗する可能性があるため、そうしないようにしてください。
- また、可用性グループの 2 つのノードを、同じまたは異なるソリューションを使用して個別にバックアップすると、バックアップに失敗する可能性があります。 Azure Backup では、コンテナーと同じリージョン内にあるすべてのノードを検出および保護できます。 お使いの SQL Server Always On 可用性グループが複数の Azure リージョンにまたがっている場合は、プライマリ ノードのあるリージョンからのバックアップを設定します。 Azure Backup では、バックアップ設定に従って、可用性グループ内のすべてのデータベースを検出および保護できます。  
- **読み取り専用**データベースについては、Azure Backup ではバックアップの種類として完全とコピーのみの完全だけをサポートしています。
- 多数のファイルがあるデータベースは保護できません。 サポートされるファイルの最大数は**約 1,000 個**です。  
- コンテナーあたり**約 2,000 個**の SQL Server データベースをバックアップできます。 データベースの数が多い場合、複数のコンテナーを作成できます。
- 一度に最大 **50** 個のデータベースのバックアップを構成できます。この制限により、バックアップの負荷が最適化されます。
- サポートされているデータベースの最大サイズは **2 TB** です。これを超えるサイズの場合は、パフォーマンスの問題が生じる可能性があります。
- サーバーあたりいくつのデータベースを保護できるかを把握するには、帯域幅、VM のサイズ、バックアップの頻度、データベースのサイズなどの要因を考慮する必要があります。Microsoft では、これらの数を自分で計算するのに役立つプランナーに取り組んでいます。 間もなく公開する予定です。
- 可用性グループの場合は、バックアップはいくつかの要因に基づいて異なるノードから作成されます。 可用性グループのバックアップ動作を以下にまとめています。

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>Always On 可用性グループの場合のバックアップ動作

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

## <a name="fix-sql-sysadmin-permissions"></a>SQL sysadmin 権限を修正する

  **UserErrorSQLNoSysadminMembership** エラーが理由でアクセス許可を修正する必要がある場合、以下を実行します。

  1. SQL Server sysadmin アクセス許可があるアカウントを使用して、SQL Server Management Studio (SSMS) にサインインします。 特殊な権限が必要でない限り、Windows 認証が機能するはずです。
  2. SQL Server で、**Security/Logins** フォルダーを開きます。

      ![セキュリティ/ログイン フォルダーを開いてアカウントを表示する](./media/backup-azure-sql-database/security-login-list.png)

  3. **[ログイン]** フォルダーを右クリックし、**[新しいログイン]** を選択します。 **[ログイン - 新規作成]** で **[検索]** を選択します。

      ![[ログイン - 新規作成] ダイアログ ボックスで [検索] を選択する](./media/backup-azure-sql-database/new-login-search.png)

  4. Windows 仮想サービス アカウント **NT SERVICE\AzureWLBackupPluginSvc** は、仮想マシンの登録と SQL 検出フェーズ中に作成済みです。 **[選択するオブジェクト名を入力します]** に示されているようにアカウント名を入力します。 **[名前の確認]** を選択して名前解決を行います。 Click **OK**.

      ![[名前の確認] を選択して不明なサービス名を解決する](./media/backup-azure-sql-database/check-name.png)

  5. **[サーバー ロール]** で、**sysadmin** ロールが選択されていることを確認します。 Click **OK**. この時点で、必要な権限が揃っているはずです。

      ![sysadmin サーバー ロールが選択されていることを確認する](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. 次にデータベースを Recovery Services コンテナーと関連付けます。 Azure portal の **[保護されたサーバー]** 一覧で、エラー状態にあるサーバーを右クリックし、**[DB の再検出]** を選択します。

      ![サーバーに適切な権限があることを確認する](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. **[通知]** 領域で進行状況をチェックします。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

      ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)


## <a name="next-steps"></a>次の手順

- SQL Server データベースのバックアップに[ついて学習します](backup-sql-server-database-azure-vms.md)。
- バックアップされた SQL Server データベースの復元に[ついて学習します](restore-sql-database-azure-vm.md)。
- バックアップされた SQL Server データベースの管理に[ついて学習します](manage-monitor-sql-database-backup.md)。
