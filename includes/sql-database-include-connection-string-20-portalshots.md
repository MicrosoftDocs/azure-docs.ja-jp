
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure ポータルから接続文字列を取得する
[Azure ポータル](https://portal.azure.com/) を使用して、クライアント プログラムが Azure SQL Database と対話するために必要な接続文字列を取得します。 

1. **[参照]** > **[SQL データベース]** の順にクリックします。
2. **[SQL データベース]** ブレードの左上近辺にあるフィルター テキスト ボックスにデータベースの名前を入力します。
3. データベースの行をクリックします。
4. データベース用のブレードが表示されたら、画面を見やすくするために、標準的な最小化コントロールをクリックして、参照とデータベースのフィルター処理を行うために使用したブレードを折りたたむことができます。 
   
    ![フィルター処理を行ってデータベースを分離する][10-FilterDatabase]
5. データベース用のブレードで、 **[データベース接続文字列の表示]**をクリックします。
6. ADO.NET 接続ライブラリを使用する場合は、 **ADO**というラベルが付いた文字列をコピーします。 
   
    ![データベース用の ADO 接続文字列をコピーする][20-CopyAdoConnectionString]
7. 特定の形式の接続文字列情報を、クライアント プログラム コードに貼り付けます。

詳細については、「<br/>[接続文字列と構成ファイル](http://msdn.microsoft.com/library/ms254494.aspx)の順にクリックします。

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->


<!--HONumber=Nov16_HO3-->


