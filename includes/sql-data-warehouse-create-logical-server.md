### <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Azure ポータルで新しい論理 SQL サーバーを作成します。

1. をクリックして**新規**、検索**論理サーバー**を入力し、 **ENTER**です。

    ![論理サーバーの検索](./media/sql-data-warehouse-create-logical-server/search-logical-server.png)
2. 選択**SQL server (論理サーバー)** 

    ![論理サーバーを選択します。](./media/sql-data-warehouse-create-logical-server/select-logical-server.png)
  
3. をクリックして**作成**を新しい SQL Server (論理サーバー) ブレードを開きます。

   <kbd>![論理サーバー ブレードを開きます](./media/sql-data-warehouse-create-logical-server/open-logical-server-blade.png) </kbd> <kbd>![論理サーバー ブレード](./media/sql-data-warehouse-create-logical-server/logical-server-blade.png)</kbd>
  
3. SQL Server (論理サーバー) ブレードのサーバー名 テキスト ボックスには、新しい論理サーバーの有効な名前を指定します。 緑のチェック マークは、有効な名前を入力したことを示します。
    
    ![新しいサーバー名](./media/sql-data-warehouse-create-logical-server/new-name-logical-server.png)

    > [!IMPORTANT]
    > 新しいサーバーの完全修飾名は < your_server_name > になります。..database.windows.net を付けています。
    >
    
4. サーバー管理者ログインのテキスト ボックスには、このサーバーの SQL 認証ログインのユーザー名を提供します。 このログインは、サーバー プリンシパルのログインと呼ばれます。 緑のチェック マークは、有効な名前を入力したことを示します。
    
    ![SQL 管理者のログイン](./media/sql-data-warehouse-create-logical-server/sql-admin-login.png)
5. **パスワード**と**パスワードの確認入力**テキスト ボックスのパスワードを指定、サーバー プリンシパルのログイン アカウントです。 緑のチェック マークは、有効なパスワードを入力したことを示します。
    
    ![SQL 管理者のパスワード](./media/sql-data-warehouse-create-logical-server/sql-admin-password.png)
6. オブジェクトを作成するアクセス許可があるサブスクリプションを選択します。

    ![サブスクリプション](./media/sql-data-warehouse-create-logical-server/subscription.png)
7. リソース グループのテキスト ボックスで選択**新規作成**し、リソース グループのテキスト ボックスに (できますも使用する既存のリソース グループ自分用のいずれかを既に作成した場合)、新しいリソース グループの有効な名前を指定します。 緑のチェック マークは、有効な名前を入力したことを示します。

    ![新しいリソース グループ](./media/sql-data-warehouse-create-logical-server/new-resource-group.png)

8. **場所**「オーストラリア東部」などの場所に適切なセンターのテキスト ボックスでデータを選択します。
    
    ![サーバーの場所](./media/sql-data-warehouse-create-logical-server/server-location.png)
    
    > [!TIP]
    > チェック ボックスを**アクセス サーバーに azure サービスを許可する**このブレードで変更することはできません。 サーバーのファイアウォール ブレードには、この設定を変更することができます。 詳細については、次を参照してください。[セキュリティの概要](../articles/sql-database/sql-database-manage-servers-portal.md)です。
    >
    
9. **[作成]** をクリックします。

    ![ボタンを作成します。](./media/sql-data-warehouse-create-logical-server/create.png)

