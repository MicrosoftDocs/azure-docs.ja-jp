### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する

1. [SQL Server] ブレードで、[設定] の下にある **[ファイアウォール]** をクリックして、SQL Server のファイアウォール ブレードを開きます。

    ![SQL Server のファイアウォール](./media/sql-data-warehouse-server-firewall/sql-server-firewall.png)
    
2. 表示されたクライアント IP アドレスを確認し、任意のブラウザーを使用してこれがインターネット上の自分の IP アドレスであることを確認します ("what is my IP address" を検索します)。 場合によっては、さまざまな理由により一致しないことがあります。

    ![自分の IP アドレス](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. ID アドレスが一致したと仮定し、ツール バーの **[クライアント IP の追加]** をクリックします。

    ![クライアント IP の追加](./media/sql-data-warehouse-server-firewall/add-client-ip.png)

    > [!NOTE]
    > SQL サーバー (論理サーバー) 上のファイアウォールは、1 つの IP アドレスに対して開くことも、範囲全体のアドレスに対して開くこともできます。 ファイアウォールを開くと、SQL の管理者とユーザーは、有効な資格情報を持っているサーバー上の任意のデータベースにログインできます。
    >

4. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存し、**[OK]** をクリックします。

    ![クライアント IP の追加](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

<!--HONumber=Jan17_HO3-->


