### <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure SQL Database](../articles/sql-database/sql-database-get-started.md) とその接続情報 (サーバー名、データベース名、ユーザー名とパスワードなど)。 この情報は、SQL Database 接続文字列に含まれています。
  
    Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  
    Azure SQL Database の詳細については、[こちら](https://azure.microsoft.com/services/sql-database)をご覧ください。

> [!NOTE]
> Azure SQL Database を作成するときに、SQL に含まれるサンプル データベースを作成することもできます。 
> 
> 

ロジック アプリで Azure SQL Database を使用する前に、SQL Database に接続します。 これは、Azure ポータルのロジック アプリ内で簡単に実行できます。  

次の手順に従って、Azure SQL Database に接続します。  

1. ロジック アプリを作成します。 Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。 ドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「sql」と入力します。 いずれかの操作を選択します。  
   
    ![SQL Azure の接続の作成手順](./media/connectors-create-api-sqlazure/sql-actions.png)
2. これまで SQL Database への接続を行っていない場合は、接続の詳細情報を求められます。  
   
    ![SQL Azure の接続の作成手順](./media/connectors-create-api-sqlazure/connection-details.png) 
3. SQL Database の詳細を入力します。 アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | Connect via Gateway (ゲートウェイ経由で接続) |オフのままにしておきます。 このプロパティは、オンプレミスの SQL Server に接続する場合に使用します。 |
   | 接続名 * |接続の任意の名前を入力します。 |
   | SQL Server Name (SQL Server 名) * |サーバー名 (*servername.database.windows.net* など) を入力します。 サーバー名は、Azure ポータルの SQL Database のプロパティに表示されます。また、接続文字列にも表示されます。 |
   | SQL Database Name (SQL Database 名) * |SQL Database に付けた名前を入力します。 これは、SQL Database のプロパティの接続文字列に表示されます (Initial Catalog=*yoursqldbname*)。 |
   | ユーザー名 * |SQL Database の作成時に作成したユーザー名を入力します。 これは、Azure ポータルの SQL Database のプロパティに表示されます。 |
   | パスワード * |SQL Database の作成時に作成したパスワードを入力します。 |
   
    これらの資格情報を使用して、接続するロジック アプリの承認と、SQL データへのアクセスが行われます。 入力が完了すると、接続の詳細は次のようになります。  
   
    ![SQL Azure の接続の作成手順](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. **[作成]**を選択します。 
5. 接続が作成されたことを確認します。 これで、ロジック アプリで他の手順に進むことができます。 
   
    ![SQL Azure の接続の作成手順](./media/connectors-create-api-sqlazure/table.png)



<!--HONumber=Nov16_HO3-->


