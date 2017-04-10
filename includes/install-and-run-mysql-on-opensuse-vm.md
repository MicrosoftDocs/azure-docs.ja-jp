
1. 権限をエスカレートするには、次のように入力します。
   
        sudo -s
   
    パスワードを入力します。
2. MySQL Community Server エディションをインストールするには、次のように入力します。
   
        zypper install mysql-community-server
   
    MySQL がダウンロードおよびインストールされるまで待ちます。
3. 次回システム起動時に MySQL を起動するように設定するには、次のように入力します。
   
        insserv mysql
4. 次のコマンドで MySQL デーモン (mysqld) を手動で開始します。
   
        rcmysql start
   
    MySQL デーモンの状態を確認するには、次のように入力します。
   
        rcmysql status
   
    MySQL デーモンを停止するには、次のように入力します。
   
        rcmysql stop
   
   > [!IMPORTANT]
   > インストール後、既定では MySQL ルート パスワードは空になっています。 MySQL の保護に役立つスクリプト、**mysql\_secure\_installation** を実行することをお勧めします。 スクリプトを実行すると、MySQL ルート パスワードの変更、匿名のユーザー アカウントの削除、リモート ルート ログインの無効化、テスト データベースの削除、および権限テーブルの再読み込みを行うように求められます。 これらのオプションすべてに対して "はい" と答えて、ルート パスワードを変更することをお勧めします。
   > 
   > 
5. 次のように入力して、MySQL インストール スクリプトを実行します。
   
        mysql_secure_installation
6. MySQL にログインします。
   
        mysql -u root -p
   
    先ほど変更した MySQL ルート パスワードを入力すると、データベースを操作するための SQL ステートメントを実行できるプロンプトが表示されます。
7. 新しい MySQL ユーザーを作成するには、**mysql>** プロンプトで次のコマンドを実行します。
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    行末のセミコロン (;) は、コマンドの終わりを示すために必要です。
8. データベースを作成し、このデータベースに対するアクセス許可を `mysqluser` ユーザーに付与するには、次のコマンドを実行します。
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    データベースのユーザー名とパスワードは、データベースに接続するスクリプトのみが使用します。  データベース ユーザー アカウントは、システム上の実際のユーザー アカウントを表しているとは限りません。
9. 別のコンピューターからログインするには、次のように入力します。
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    `ip-address` は、MySQL への接続元コンピューターの IP アドレスです。
10. MySQL データベース管理ユーティリティを終了するには、次のように入力します。
    
        quit

## <a name="add-an-endpoint"></a>エンドポイントの追加
1. MySQL をインストールしたら、MySQL にリモートでアクセスできるように、エンドポイントを構成する必要があります。 [Azure クラシック ポータル][AzurePortal]にログインします。 **[仮想マシン]**、新しい仮想マシンの名前、**[エンドポイント]** の順にクリックします。
2. ページの下部にある **[追加]** をクリックします。
3. エンドポイントを追加します。名前に「MySQL」、プロトコルに **[TCP]** を指定し、**[パブリック ポート]** と **[プライベート ポート]** に「3306」を指定します。
4. お使いのコンピューターから仮想マシンにリモートで接続するには、次のように入力します。
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    たとえば、このチュートリアルで作成した仮想マシンを使用する場合、次のコマンドを入力します。
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png


<!--HONumber=Nov16_HO3-->


