---
title: Microsoft Azure Backup Server v3 のリリース ノート
description: この記事では、Microsoft Azure Backup Server (MABS) v3 の既知の問題と回避策に関する情報を提供します。
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254263"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server のリリース ノート

この記事では、Microsoft Azure Backup Server (MABS) V3 の既知の問題と回避策を提供します。

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>クラスター化されたワークロードのバックアップと回復に失敗します

**説明:** 28MABS V2 から MABS V3 にアップグレードした後、HYPER-V クラスターや SQL クラスター (SQL Always On) などのクラスター化されたデータ ソース、または データベース可用性グループ (DAG) の Exchange のバックアップ/復元に失敗します。

**回避策:** これを防ぐには、SQL Server Management Studio (SSMS) を開き、DPM DB に対して次の SQL スクリプトを実行します。

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>ロシア語のロケールで MABS V3 へのアップグレードに失敗します

**説明:** ロシア語ロケールでの MABS V2 から MABS V3 へのアップグレードは、エラー コード **4387** で失敗します。

**回避策:** 次の手順に従い、ロシア語のインストール パッケージを使用して MABS V3 にアップグレードします。

1. [バックアップ](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) SQL データベースと MABS V2 のアンインストール (アンインストール中に、保護されたデータを保持すると選択)。
2. SQL 2017 (Enterprise) にアップグレードし、アップグレードの一環としてレポートをアンインストールします。
3. SQL Server Reporting Services (SSRS)[をインストール](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server)。
4. SQL Server Management Studio (SSMS) を[インストール](/sql/ssms/download-sql-server-management-studio-ssms)。
5. [SQL 2017 を使用した SSRS 構成](./backup-azure-microsoft-azure-backup.md#upgrade-mabs)に記載されているパラメーターを使用してレポートを構成します。
6. MABS V3を [インストール](backup-azure-microsoft-azure-backup.md)。
7. [ここ](/system-center/dpm/back-up-the-dpm-server#using-dpmsync)に記されている通り、SSMS と実行 DPM 同期ツールを使用して SQL を[復元](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)します。
8. 次のコマンドを使用して dbo.tbl_DLS_GlobalSetting テーブルの 'DataBaseVersion' プロパティを更新します：

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. MSDPM サービスを開始します。

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>UR1 をインストールした後、MABS レポートが新しい RDL ファイルで更新されない

**説明**:UR1 では、MABS レポートの書式設定の問題は、更新された RDL ファイルを使用して修正されます。 新しい RDL ファイルによって既存のファイルが自動的に置き換えられることはありません。

**回避策**:RDL ファイルを置き換えるには、次の手順に従います。

1. MABS コンピューター上で、SQL Reporting Services Web ポータルの URL を開きます。
1. Web ポータルの URL では、DPMReports フォルダーは **`DPMReports_<GUID>`** の形式で表示されます。

    >[!NOTE]
    >この名前付け規則を持つフォルダーは、常に 1 つだけ存在します。 MABS が前のバージョンからアップグレードされている場合は、別の古いフォルダーも存在する可能性がありますが、開くことはできません。

    ![DPMReports フォルダー](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. **`DPMReports_<GUID>`** フォルダーを選択して開きます。 個々のレポート ファイルが次のように一覧表示されます。

    ![個々のレポート ファイルの一覧](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. **Report** で終わっていないレポート ファイルを選択します。 **[オプション]** を右クリックし、 **[管理]** を選択します。

    ![レポートファイルの [管理] を選択する](./media/backup-mabs-release-notes-v3/manage-files.png)

1. 新しいページで、 **[置換]** オプションを選択して、ファイルを最新のレポート ファイルに置き換えます。

    最新のレポートファイルは、パス `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports` にあります。

    例: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![ファイルを最新のレポート ファイルに置き換える](./media/backup-mabs-release-notes-v3/replace-files.png)

    ファイルが置き換えられたら、 **[名前]** と **[説明]** に変化がないこと、空白ではないことを確認します。

1. ファイルが置き換えられたら、MABS サービスを再起動し、レポート ファイルを使用します。

## <a name="next-steps"></a>次のステップ

[MABS の新機能](backup-mabs-whats-new-mabs.md)
