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
5. データベース用のブレードで、 **[データベース接続文字列の表示]**をクリックします。
6. ADO.NET 接続ライブラリを使用する場合は、 **ADO.NET**というラベルが付いた文字列をコピーします。
   
    ![データベース用の ADO.NET 接続文字列をコピーする][3-get-connection-string]
7. 接続文字列情報を、クライアント プログラム コードに貼り付けます。  {your_password_here} を実際のパスワードに置き換える必要があります。

詳細については、「 [接続文字列と構成ファイル](http://msdn.microsoft.com/library/ms254494.aspx)」を参照してください。

<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png


[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-dotnet.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Dec16_HO2-->


