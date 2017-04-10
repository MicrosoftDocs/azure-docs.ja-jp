<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure ポータルから接続文字列を取得する
[Azure ポータル](https://portal.azure.com/) を使用して、クライアント プログラムが Azure SQL Database と対話するために必要な接続文字列を取得します。

1. **[参照]** > **[SQL データベース]** の順にクリックします。
   
    ![Select SQL][1-select-sql]
2. **[SQL データベース]** ブレードの左上近辺にあるフィルター テキスト ボックスにデータベースの名前を入力します。
   
    ![データベースを選択する][2-select-database]
3. データベースの行をクリックします。
4. データベース用のブレードが表示されたら、画面を見やすくするために、標準的な最小化コントロールをクリックして、参照とデータベースのフィルター処理を行うために使用したブレードを折りたたむことができます。
5. **SQL Database** の名前と**サーバー名**を書き留めます。  ユーザー名は yourusername@yourserver.
   
    ![接続の詳細を取得する][3-get-connection-details]
6. 接続の詳細をクライアント プログラム コードに貼り付けます。  {your_password_here} を実際のパスワードに置き換える必要があります。

<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-details]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-details.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Dec16_HO2-->


