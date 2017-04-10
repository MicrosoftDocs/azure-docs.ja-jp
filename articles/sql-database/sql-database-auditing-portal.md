---
title: "Azure Portal: SQL Database 監査の管理 | Microsoft Docs"
description: "Azure Portal で、データベース イベントを追跡して Azure ストレージ アカウントの監査ログに書き込むように、Azure SQL Database 監査を構成します。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e20a30a565198c01f213895a87fe8807a37272cb
ms.lasthandoff: 03/30/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>Azure Portal での SQL Database 監査の構成および管理

以下のセクションでは、Azure Portal を使用して監査を構成および管理する方法について説明します。 PowerShell を用いて監査を構成および管理する方法については、[PowerShell を使用した監査の構成](scripts/sql-database-create-and-configure-database-powershell.md)に関するページを参照してください。 

監査の概要については、[SQL Database 監査](sql-database-auditing.md)に関するページを参照してください。

## <a name="configure-blob-auditing"></a>BLOB 監査の構成

1. https://portal.azure.com で [Azure Portal](https://portal.azure.com) を起動します。
2. 監査する SQL Database または SQL Server の [設定] ブレードに移動します。 [設定] ブレードで、**[監査と脅威の検出]** を選択します。

    ![ナビゲーション ウィンドウ](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. データベース監査構成ブレードで、このデータベースをサーバーの設定に従い監査する場合は、**[サーバーから設定を継承]** チェックボックスをオンにします。 このオプションをオンにすると、このコンテキストでサーバーの監査設定を表示または変更する **[サーバーの監査設定を表示]** リンクが表示されます。

    ![ナビゲーション ウィンドウ][2]
4. (サーバーレベルの監査に加えて、あるいはサーバーレベルの監査の代わりに) データベースレベルでの BLOB 監査を有効にする場合、**[サーバーから監査設定を継承]** オプションを**オフ**にし、[監査] を**オン**にして、**BLOB** 監査タイプを選択します。

    ![ナビゲーション ウィンドウ][3]
5. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。 ログを保存する Azure ストレージ アカウントと古いログを削除するタイミングを指定するリテンション期間を選択し、一番下にある **[OK]** をクリックします。 **ヒント:** 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください。

    <a id="storage-screenshot"></a>
    ![ナビゲーション ウィンドウ][4]
6. 監査対象イベントをカスタマイズする場合は、PowerShell または REST API を使用して行います。
7. 監査設定を構成したら、新機能である脅威の検出 (プレビュー) をオンにし、セキュリティの警告を受信するための電子メールを構成します。 脅威の検出を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティに対するプロアクティブ アラートを受信できます。 詳細については、[脅威の検出](sql-database-threat-detection.md)に関するページを参照してください。
8. **[保存]**をクリックします。


## <a name="table-auditing"></a>テーブルの監査

> [!IMPORTANT]
> **テーブルの監査**をセットアップする前に、["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用しているかどうかを確認します。 また、ファイアウォールを厳密に設定している場合は、テーブル監査を有効にすると[データベースの IP エンドポイントが変更される](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)ことに注意してください。
>

1. https://portal.azure.com で [Azure Portal](https://portal.azure.com) を起動します。
2. 監査する SQL Database または SQL Server の [設定] ブレードに移動します。 [設定] ブレードで、**[監査と脅威検出]** を選択します (*[「BLOB の監査」セクションのスクリーンショットを参照してください](#auditing-screenshot)*)。
3. データベース監査構成ブレードで、このデータベースをサーバーの設定に従い監査する場合は、**[サーバーから設定を継承]** チェックボックスをオンにします。 このオプションをオンにすると、このコンテキストでサーバーの監査設定を表示または変更する **[サーバーの監査設定を表示]** リンクが表示されます。

    ![ナビゲーション ウィンドウ][2]
4. サーバーから監査設定を継承しない場合は、**[サーバーから監査設定を継承]** オプションを**オフ**にし、監査を**オン**にして、**テーブル**監査タイプを選択します。

    ![ナビゲーション ウィンドウ][3-tbl]
5. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。 ログを保存する Azure ストレージ アカウントと古いログを削除するタイミングを指定するリテンション期間を選択します。 **ヒント:** 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください (*[「BLOB の監査」セクションのスクリーンショットを参照してください](#storage-screenshot)*)。
6. 監査するイベントをカスタマイズするには、**[監査されたイベント]** をクリックします。 イベント別のログ記録ブレードで、すべてのイベントのログを記録する場合は **[成功]** および **[失敗]** をクリックします。または、イベント カテゴリを個別に選択します。

    ![ナビゲーション ウィンドウ][5]
7. 監査設定を構成したら、新機能である脅威の検出 (プレビュー) をオンにし、セキュリティの警告を受信するための電子メールを構成します。 脅威の検出を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティに対するプロアクティブ アラートを受信できます。 詳細については、[脅威の検出](sql-database-threat-detection.md)に関するページを参照してください。
8. **[保存]**をクリックします。

## <a name="auditing-geo-replicated-databases"></a>geo レプリケーション対応データベースの監査

geo レプリケーション対応データベースを使用する場合、監査の種類に応じてプライマリ データベースとセカンダリ データベースのいずれか (または両方) に監査を設定できます。

**テーブルの監査:** 2 つのデータベース (プライマリとセカンダリ) のそれぞれについて、データベース レベルまたはサーバー レベルで個別のポリシーを構成できます。

**BLOB 監査**: 次の指示に従ってください。

1. **プライマリ データベース:** サーバー自体またはデータベース自体で BLOB 監査を有効にします。
2. **セカンダリ データベース**: BLOB 監査のオン/オンは、プライマリ データベースの監査設定からのみ切り替えることができます。

   * プライマリ データベースで BLOB 監査を有効にします。 BLOB 監査は、サーバーではなく*プライマリ データベース自体*で有効にする必要があります。
   * プライマリ データベースで BLOB 監査を有効にすると、セカンダリ データベースでも有効になります。

    > [!IMPORTANT]
    > 既定では、セカンダリ データベースのストレージ設定はプライマリ データベースと同じであるため、リージョンをまたいだトラフィックが発生します。 これは、セカンダリ サーバーで BLOB 監査を有効にし、セカンダリ サーバーのストレージ設定でローカル ストレージを構成する (セカンダリ データベースのストレージ場所が上書きされ、結果的に各データベースが監査ログをローカル ストレージに保存します) ことで回避できます。  

## <a name="viewing-blob-auditing-logs"></a>BLOB 監査ログの確認

BLOB 監査のログは、"**sqldbauditlogs**" という名前のコンテナー内に BLOB ファイルのコレクションとして保存されます。

BLOB 監査ログのストレージ フォルダー階層、BLOB の命名規則、ログ形式の詳細については、[BLOB 監査ログ形式のリファレンス ](https://go.microsoft.com/fwlink/?linkid=829599)に関するダウンロード版 doc ファイルを参照してください。

BLOB 監査ログはいくつかの方法で表示できます。

* [Azure Portal](https://portal.azure.com) から関連するデータベースを開きます。 データベースの [監査と脅威検出] ブレードの上部にある **[監査ログの表示]** をクリックします。

    ![Navigation pane][10]

    [監査レコード] ブレードが開きます。ここでログを参照できます。

    - [監査レコード] ブレードの上の領域にある **[フィルター]** をクリックすると、特定の日付を表示できます。
    - サーバー ポリシーまたはデータベース ポリシー監査によって作成された監査レコードを切り替えることができます。

    ![Navigation pane][11]
* ポータル経由で、あるいは [Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを利用して、Azure Storage BLOB コンテナーからログ ファイルをダウンロードします。

    ログ ファイルをローカルでダウンロードすると、ファイルをダブルクリックし、SSMS でログを開き、表示し、分析できます。

* 他の方法:

   * Azure ストレージ エクスプローラーを利用すると、複数のファイルを同時にダウンロードできます。特定のサブフォルダー (特定の日付のログ ファイルをすべて含むサブフォルダーなど) を右クリックし、**[名前を付けて保存]** を選択してローカルのフォルダーに保存します。

       数個のファイル (あるいは上の説明にあるように丸 1 日分) をダウンロードしたら、それらをローカルで次のように結合できます。

       **[SSMS]、[ファイル]、[開く]、[Merge Extended Events (拡張イベントの結合)] の順に開き、結合するすべてのファイルを選択する**
   * プログラムを使用する:

     * [拡張イベント リーダー (C# ライブラリ)](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [PowerShell 使用した拡張イベント ファイルへのクエリ](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * Azure で実行され、OMS ダッシュボードで確認できるように SQL 監査ログを OMS パブリック API により OMS にプッシュする[サンプル アプリケーション](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)が用意されています。

## <a name="viewing-table-audit-logs"></a>テーブル監査ログの確認

テーブル監査ログは、**SQLDBAuditLogs** プレフィックスが付いた Azure Storage テーブルのコレクションとして保存されます。

テーブル監査ログ形式の詳細については、[テーブル監査ログ形式のリファレンス (doc ファイルのダウンロード)](http://go.microsoft.com/fwlink/?LinkId=506733) を参照してください。

テーブル監査ログはいくつかの方法で表示できます。

* [Azure Portal](https://portal.azure.com) から関連するデータベースを開きます。 データベースの [監査と脅威検出] ブレードの上部にある **[監査ログの表示]** をクリックします。

    ![Navigation pane][10]

    [監査レコード] ブレードが開きます。ここでログを参照できます。

    * [監査レコード] ブレードの上の領域にある **[フィルター]** をクリックすると、特定の日付を表示できます。
    * [監査レコード] ブレードの上の領域にある **[Excel で開く]** をクリックすると、Excel 形式で監査ログをダウンロードし、表示できます。

    ![Navigation pane][12]

* あるいは、事前に構成されたレポート テンプレートが[ダウンロードできる Excel スプレッドシート](http://go.microsoft.com/fwlink/?LinkId=403540)として用意されているので、ログ データをすぐに分析できます。 監査ログでテンプレートを使用するには、Excel 2013 以降および Power Query (http://www.microsoft.com/download/details.aspx?id=39379) が必要です。

* Power Query を使用すると、Azure ストレージ アカウントから直接 Excel テンプレートに監査ログをインポートすることもできます。 その後監査レコードを確認し、ログ データを使用しダッシュ ボードとレポートを作成できます。

    ![Navigation pane][9]

## <a name="storage-key-regeneration"></a>ストレージ キーの再生成
運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 キーを最新の情報に更新する場合は、監査ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。

1. [容量の詳細] ブレードで、**[ストレージ アクセス キー]** を *[プライマリ]* から *[セカンダリ]* に切り替え、一番下にある **[OK]** をクリックします。 [監査構成] ブレードの上部の **[保存]** をクリックします。

    ![Navigation pane][6]
2. ストレージ構成ブレードに移動し、**プライマリ アクセス キー**を*再生成*します。

    ![Navigation pane][8]
3. 監査の構成ブレードに戻り、**[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替え、一番下にある **[OK]** をクリックします。 監査の構成ブレードの上部にある **[保存]** をクリックします。
4. ストレージ構成ブレードに戻り、(次のキー更新サイクルの準備として) *セカンダリ アクセス キー*を**再生成**します。


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

## <a name="next-steps"></a>次のステップ

* PowerShell を用いて監査を構成および管理する方法については、[PowerShell を使用したデータベース監査の構成](scripts/sql-database-create-and-configure-database-powershell.md)に関するページを参照してください。
* 監査の概要については、[データベース監査](sql-database-auditing.md)に関するページを参照してください。


