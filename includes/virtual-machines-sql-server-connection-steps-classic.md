### <a name="determine-the-dns-name-of-the-virtual-machine"></a>仮想マシンの DNS 名を特定する
別のコンピューターから SQL Server Database エンジンに接続するには、仮想マシンのドメイン ネーム システム (DNS) 名が必要になります (これは、仮想マシンを識別するためにインターネットで使用される名前です。 IP アドレスを使用することもできますが、Azure で冗長化またはメンテナンスのためにリソースが移動された場合、IP アドレスは変わる可能性があります。 DNS 名は、新しい IP アドレスにリダイレクトできるため、安定しています)。  

1. Azure ポータルで (または前の手順から)、 **[仮想マシン (クラシック)]**を選択します。
2. SQL VM を選択します。
3. **[仮想マシン]** ブレードで、仮想マシンの **DNS 名**をコピーします。
   
    ![DNS name](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>別のコンピューターからデータベース エンジンに接続する
1. インターネットに接続されたコンピューターで、SQL Server Management Studio を開きます。
2. **[サーバーへの接続]** または **[データベース エンジンへの接続]** ダイアログ ボックスで、**[サーバー名]** ボックスに、先のタスクで決定した仮想マシンの DNS 名とパブリック エンドポイントのポート番号を「*DNS 名,ポート番号*」という形式で入力します (例: **mysqlvm.cloudapp.net,57500**)。
   
    ![SSMS を使用した接続](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    以前に作成したパブリック エンドポイントのポート番号を覚えていない場合は、**[仮想マシン]** ブレードの**[エンドポイント]** 領域でポート番号を確認できます。
   
    ![パブリック ポート](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. **[認証]** ボックスで、**[SQL Server 認証]** を選択します。
4. **[ログイン]** ボックスに、前のタスクで作成したログインの名前を入力します。
5. **[パスワード]** ボックスに、前のタスクで作成したログインのパスワードを入力します。
6. **[接続]**をクリックします。



<!--HONumber=Nov16_HO3-->


