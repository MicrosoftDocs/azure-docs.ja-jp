---
title: Azure SQL Data Warehouse での監査 | Microsoft Docs
description: 監査と、Azure SQL Data Warehouse で監査を設定する方法について説明します。
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 85693ec6aa67dc69cd65aae8e66e66e2118672ef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898484"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse での監査

監査と、Azure SQL Data Warehouse で監査を設定する方法について説明します。

## <a name="what-is-auditing"></a>監査とは
SQL Data Warehouse の監査により、Azure Storage アカウントの監査ログにデータベースのイベントを記録できます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。 標準準拠をサポートする Azure プログラムの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。

## <a id="subheading-1"></a>監査の基本
SQL Data Warehouse データベース監査により、以下のことが可能になります。

* **保持** 。 監査するデータベース活動のカテゴリを定義できます。
* **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
* **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

監査ログは、Azure ストレージ アカウントに格納されます。 監査ログの保有期間を定義することができます。


## <a id="subheading-4"></a>サーバー レベルおよびデータベース レベルの監査ポリシーを定義する

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。

* サーバー ポリシーが、サーバー上にある**既存と新規作成のすべてのデータベースに適用されます**。

* "*サーバー BLOB 監査が有効*" な場合は、"*常にデータベースに適用*" されます。 データベース監査設定に関係なく、データベースが監査されます。

* サーバー上だけでなくデータベース上でも監査を有効にした場合であっても、サーバーの BLOB 監査の設定がオーバーライドされたり変更されたりすることは "*ありません*"。 どちらの監査も並行して存在します。 つまり、データベースは並行して 2 回監査されることになります (1 回はサーバー ポリシー、もう 1 回はデータベース ポリシーによって監査されます)。

> [!NOTE]
> **サーバー レベルの BLOB 監査のみ**を有効にし、すべてのデータベースに対してデータベース レベルの監査を無効にすることをお勧めします。
> 次の場合を除き、サーバー監査とデータベース監査の両方を有効にすることは避けてください。
> * 特定のデータベースに対して異なる "*ストレージ アカウント*" または "*リテンション期間*" を使用する場合。
> * 特定のデータベースの監査対象とするイベントの種類またはカテゴリが、このサーバー上の他のデータベースの管理対象と異なる場合。 たとえば、特定のデータベースに対してのみ監査が必要なテーブルの挿入がある場合など。
> * 脅威検出を使用する場合 (現在はデータベースレベルの監査でのみサポートされています)。

> [!IMPORTANT]
>Azure SQL Data Warehouse 上で、または Azure SQL Data Warehouse が稼働しているサーバー上で監査を有効にすると、以前に一時停止されていた場合でも **Data Warehouse が再開されます**。 **監査を有効にした後、必ず Data Warehouse を再び一時停止してください**。

## <a id="subheading-5"></a>すべてのデータベースにサーバーレベルの監査を設定する

サーバー監視ポリシーが、サーバー上にある**既存と新規作成のすべてのデータベース**に適用されます。

以下のセクションでは、Azure Portal を使用した監査の構成について説明します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 監査する **SQL Server** に移動します (特定のデータベース/DW ではなく、SQL サーバーが表示されていることを確認してください)。 **[セキュリティ]** メニューで、**[監査と脅威の検出]** を選択します。

    ![ナビゲーション ウィンドウ][6]
4. *[監査と脅威検出]* ブレードの **[監査]** で **[オン]** を選択します。 この監視ポリシーは、このサーバー上にある既存のデータベースと新規作成されたデータベースのすべてに適用されます。

    ![ナビゲーション ウィンドウ][7]
5. **[監査ログの容量]** ブレードを開くには、**[容量の詳細]** を選択します。 ログを保存する Azure ストレージ アカウントを選択または作成してから、保有期間 (古いログを削除するまでの期間) を選択します。 次に、 **[OK]** をクリックします

    ![ナビゲーション ウィンドウ][8]

    > [!IMPORTANT]
    > 監査ログは Azure サブスクリプションの Azure Blob Storage 内にある**追加 BLOB** に書き込まれます。
    >
    > - すべてのストレージの種類 (v1、v2、BLOB) がサポートされています。
    > - すべてのストレージ レプリケーション構成がサポートされています。
    > - **Premium Storage** は現在**サポートされていません**。
    > - **VNet 内の Storage** は現在**サポートされていません**。
    > - **ファイアウォールの背後にある Storage** は現在**サポートされていません**。

8. **[Save]** をクリックします。



## <a id="subheading-2"></a>単一のデータベースに対してデータベースレベルの監査を設定する

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。

「[サーバー レベルおよびデータベース レベルの監査ポリシーを定義する](#subheading-4)」の説明に従って、データベース レベルの監査ではなく、サーバー レベルの監査を使用することを強くお勧めします。

監査をセットアップする前に、 ["ダウンレベル クライアント"](sql-data-warehouse-auditing-downlevel-clients.md)を使用しているかどうかを確認します。


1. [Azure ポータル](https://portal.azure.com)を開きます。
2. 監査する SQL Data Warehouse の **[設定]** に移動します。 **[監査と脅威検出]** を選びます。

    ![][1]
3. 次に、 **[オン]** ボタンをクリックして監査を有効にします。

    ![][3]
4. 監査構成パネルで、**[容量の詳細]** を選んで [監査ログの容量] パネルを開きます。 ログの Azure ストレージ アカウントと、リテンション期間を選びます。
    >[!TIP]
    >すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。

    ![][4]

5. **[OK]** ボタンをクリックして、記憶域の詳細構成を保存します。
6. **[イベントごとのログ記録]** で、**[成功]** および **[失敗]** を、すべてのイベントについて、または個別のイベント カテゴリについて記録します。
7. データベースの監査を構成している場合は、データの監査が正しくキャプチャされるように、クライアントの接続文字列を変更する必要がある場合があります。 ダウンレベル クライアントの接続については、[接続文字列でのサーバーの FQDN の変更](sql-data-warehouse-auditing-downlevel-clients.md)に関するトピックを参照してください。
8. Click **OK**.

## <a id="subheading-3"></a>監査ログとレポートを分析する

### <a name="server-level-policy-audit-logs"></a>サーバーレベルのポリシーの監査ログ
サーバーレベルの監査ログは Azure サブスクリプションの Azure Blob Storage 内にある**追加 BLOB** に書き込まれます。 これらは **sqldbauditlogs** という名前のコンテナー内に BLOB ファイルのコレクションとして保存されます。

ストレージ フォルダーの階層、命名規則、およびログ形式の詳細については、[BLOB 監査ログ形式のリファレンス](https://go.microsoft.com/fwlink/?linkid=829599)を参照してください。

BLOB 監査ログを表示するには、いくつかの方法が使用できます。

* SQL Server Management Studio (SSMS 17 以降) で **[監査ファイルの統合]** を使用します。
    1. SSMS のメニューから、**[ファイル]** > **[開く]** > **[監査ファイルの統合]** を選択します。

    2. **[監査ファイルの追加]** ダイアログ ボックスが表示されます。 **[追加]** オプションのいずれかを選択して、ローカル ディスクから監査ファイルをマージするか、Azure Storage からインポートするかを選択します。 Microsoft Azure Storage の詳細とアカウント キーを提供する必要があります。

    3. 統合するすべてのファイルを追加した後に、**[OK]** をクリックして統合の操作を完了します。

    4. 統合されたファイルを SSMS で開くと、ファイルを表示および分析し、XEL または CSV ファイルまたはテーブルにエクスポートすることができます。

* 作成した[同期アプリケーション](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)を使用します。 Azure 内で実行され、ログ分析のパブリック API を使用して SQL 監査ログを Azure Monitor ログにプッシュします。 同期アプリケーションはログ分析ダッシュボード経由で、Azure Monitor ログに SQL 監査ログをプッシュして使用します。

* Power BI を使用します。 Power BI で監査ログのデータを表示および分析できます。 [Power BI の詳細を確認し、ダウンロード可能なテンプレートにアクセスします](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)。

* ポータル経由で、あるいは [Azure ストレージ エクスプローラー](https://storageexplorer.com/)などのツールを利用して Azure Storage BLOB コンテナーからログ ファイルをダウンロードします。
    * ログ ファイルをローカルでダウンロードした後に、ファイルをダブルクリックし、SSMS でログを開き、表示し、分析できます。
    * また、Azure ストレージ エクスプ ローラーを使用して、同時に複数のファイルをダウンロードすることもできます。 特定のサブフォルダーを右クリックし、**[名前を付けて保存]** を選択してローカル フォルダーに保存します。

* 他の方法:
   * 複数のファイルまたはログ ファイルが含まれるサブフォルダーをダウンロードした後、前述の SSMS 監査ファイルの統合の指示に従って、ローカルでマージすることができます。

   * BLOB 監査ログをプログラムで表示します。

     * [拡張イベント リーダー](https://blogs.msdn.microsoft.com/extended_events/20../../introducing-the-extended-events-reader/) (C# ライブラリ) を使用します。
     * PowerShell を使用して[拡張イベント ファイルにクエリを実行します](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/)。



<br>

### <a name="database-level-policy-audit-logs"></a>データベースレベルのポリシーの監査ログ
データベースレベルの監査ログは、設定時に選択した Azure ストレージ アカウントで、**SQLDBAuditLogs** というプレフィックスが付いたストア テーブルのコレクションに集計されます。 [Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com)などのツールを使用してログ ファイルを表示できます。

事前に構成されたダッシュボード レポート テンプレートが[ダウンロードできる Excel スプレッドシート](https://go.microsoft.com/fwlink/?LinkId=403540)として用意されているので、ログ データをすぐに分析できます。 監査ログでテンプレートを使用するには、Excel 2013 以降および Power Query ([ここ](https://www.microsoft.com/download/details.aspx?id=39379)でダウンロードできます) が必要です。

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。

## <a id="subheading-4"></a>ストレージ キーの再生成
運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 鍵を最新の情報に更新する場合は、ポリシーを保存する必要があります。 このプロセスは次のとおりです。

1. 監査構成パネル (前の監査設定セクションで説明されています) で、**[ストレージ アクセス キー]** を *[プライマリ]* から *[セカンダリ]* に変更して、**[保存]** をクリックします。

   ![][4]
2. ストレージ構成パネルに移動し、"*プライマリ アクセス キー*" を**再生成**します。
3. 監査構成パネルに戻り、
4. **[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替えて、**[保存]** をクリックします。
4. ストレージの UI に戻り、 **セカンダリ アクセス キー** を *再生成* (次のキー更新サイクルの準備として) します。

## <a id="subheading-5"></a>Automation (PowerShell / REST API)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の自動化ツールを利用し、Azure SQL Data Warehouse で監査を構成することもできます。

* **PowerShell コマンドレット**:

<!-- None of the following links exist anymore 3-12-2019
   * [Get-AzSqlDatabaseAuditingPolicy](/powershell/module/az.sql/get-azsqldatabaseauditingpolicy)
   * [Get-AzSqlServerAuditingPolicy](/powershell/module/az.sql/Get-azSqlServerAuditingPolicy)
   * [Remove-AzSqlDatabaseAuditing](/powershell/module/az.sql/Remove-azSqlDatabaseAuditing)
   * [Remove-AzSqlServerAuditing](/powershell/module/az.sql/Remove-azSqlServerAuditing)
   * [Set-AzSqlDatabaseAuditingPolicy](/powershell/module/az.sql/Set-azSqlDatabaseAuditingPolicy)
   * [Set-AzSqlServerAuditingPolicy](/powershell/module/az.sql/Set-azSqlServerAuditingPolicy)
   * [Use-AzSqlServerAuditingPolicy](/powershell/module/az.sql/Use-azSqlServerAuditingPolicy) -->

   * [Get-AzSqlDatabaseAuditing](/powershell/module/az.sql/get-azsqldatabaseauditing)
   * [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseauditing)
   * [Get-AzSqlServerAuditing](/powershell/module/az.sql/get-azsqlserverauditing)
   * [Set-AzSqlServerAuditing](/powershell/module/az.sql/set-azsqlserverauditing)

## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>監査と動的データ マスキングのためのダウンレベル クライアントのサポート
監査は TDS リダイレクションに対応する SQL クライアントと連動します。

TDS 7.4 を実装するクライアントもリダイレクトをサポートします。 この例外には一部のリダイレクション機能に対応していない JDBC 4.0 とリダイレクションが実装されていない Tedious for Node.JS があります。

TDS バージョン 7.3 以前をサポートする "ダウンレベル クライアント" の場合、次のようにして接続文字列のサーバー FQDN を変更します。

- 接続文字列の元のサーバー FQDN: <*server name*>.database.windows.net
- 接続文字列の変更後のサーバー FQDN: <*server name*>.database.**secure**.windows.net

「ダウンレベル クライアント」には次が含まれます。

* .NET 4.0 以前
* ODBC 10.0 以前
* JDBC (JDBC は TDS 7.4 対応ですが、一部の TDS リダイレクション機能に対応していません。)
* Tedious (Node.JS 用)

**注釈:** 上のサーバー FDQN 変更は SQL サーバー レベル監査ポリシーの適用にも役に立ちます。データベースごとの構成が必要ありません (一時的な軽減)。     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
