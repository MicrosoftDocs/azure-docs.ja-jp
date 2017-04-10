
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>新しい Azure SQL データベースを作成します。
次の手順に従って、Azure ポータルで新規または既存の Azure SQL Database 論理サーバーに新しい Azure SQL Database を作成します。

1. [Azure Portal](http://portal.azure.com)に接続していない場合は接続します。
2. **[新規]** をクリックし、「**SQL Database**」と入力し、**[SQL Database (new database) (SQL Database (新しいデータベース))]** をクリックします。
   
     ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)
3. **[SQL Database (new database) (SQL Database (新しいデータベース))]**をクリックします。
   
     ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
4. **[作成]** をクリックして、SQL Database サービスに新しいデータベースを作成します。
   
     ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)
5. 次のサーバー プロパティの値を入力します。
   
   * データベース名
   * サブスクリプション: 複数のサブスクリプションがある場合にのみ該当します。
   * リソース グループ: 始めたばかりの場合は、論理サーバーのリソース グループを使用します。
   * ソースの選択: 空のデータベース、サンプル データ、または Azure データベースのバックアップを選択できます。 BCP コマンド ライン ツールを使用してオンプレミスの SQL Server データベースを移行したり、データを読み込んだりするには、この記事の最後にあるリンクを参照してください。
   * サーバー: 新規または既存の論理サーバー。
   * サーバー管理者のログイン
   * パスワード
   * 価格レベル: 始めたばかりの場合は、既定値の S0 を使用します。
   * 照合順序: 空のデータベースが選択された場合にのみ該当します。
     
        ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)
6. ページの下部にある **[Create]**」を参照してください。 通知領域にデプロイの開始が表示されます。
   
    ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)
7. デプロイが完了するまで待ってから、次の手順に進みます。
   
     ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)



<!--HONumber=Jan17_HO3-->


