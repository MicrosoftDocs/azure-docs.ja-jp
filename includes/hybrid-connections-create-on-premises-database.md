
このセクションでは、SQL Server Express のインストール、TCP/IP の有効化、静的なポートの設定、およびハイブリッド接続で使用可能なデータベースを作成する方法を説明します。  

### <a name="install-sql-server-express"></a>SQL Server Express をインストールする
ハイブリッド接続でオンプレミスの SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。 SQL Server の既定のインスタンスは静的ポート 1433 を使用しますが、名前付きインスタンスは静的ポート 1433 を使用しません。 このため、既定のインスタンスをインストールします。 SQL Server Express の既定のインスタンスが既にインストールされている場合は、このセクションを省略できます。

1. SQL Server Express をインストールするには、ダウンロードした **SQLEXPRWT_x64_ENU.exe** または **SQLEXPR_x86_ENU.exe** ファイルを実行します。 SQL Server インストール センター ウィザードが表示されます。
2. **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加]** をクリックし、画面の指示に従い、**[インスタンスの構成]** ページまで、既定の選択と設定を受け入れて進みます。
3. **[インスタンスの構成]** ページで、**[既定のインスタンス]** を選択してから、**[サーバーの構成]** ページで既定の設定を受け入れます。
   
   > [!NOTE]
   > SQL Server の既定のインスタンスが既にインストールされている場合は、次のセクションにスキップして、ハイブリッド接続でこのインスタンスを使用できます。 
   > 
   > 
4. **[データベース エンジンの構成]** ページの **[認証モード]** で、**[混合モード (SQL Server 認証と Windows 認証)]** を選択して、組み込みの **sa** 管理者アカウント用の保護されたパスワードを入力します。
   
    このチュートリアルでは、SQL Server 認証を使用します。 後で必要になるため、入力したパスワードを忘れないでください。
5. ウィザードを終了して、インストールを完了します。

### <a name="enable-tcpip-and-setting-a-static-port"></a>TCP/IP を有効にして、静的ポートを設定する
このセクションでは、SQL Server Express をインストールした際にインストールされた SQL Server 構成マネージャーを使用して、TCP/IP を有効にし、静的ポートを設定します。 

1. 「 [SQL Server の TCP/IP ネットワーク プロトコルの有効化](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) 」の手順に従って、インスタンスに TCP/IP がアクセスできるようにします。
2. (省略可能) 既定のインスタンスを使用できない場合は、 [特定の TCP ポートで受信待ちするようにサーバーを構成する方法 ](https://msdn.microsoft.com/library/ms177440.aspx) に関するページの手順に従って、インスタンスの静的ポートを設定する必要があります。 この手順を完了すると、ポート 1433 ではなく、定義する新しいポートを使用して接続します。
3. (省略可能) 必要な場合は、SQL Server のプロセス (sqlservr.exe) へのリモート アクセスを許可するために、ファイアウォールに例外を追加します。

### <a name="create-a-new-database-in-the-on-premises-sql-server-instance"></a>オンプレミスの SQL Server インスタンスに新しいデータベースを作成する
1. SQL Server Management Studio で、インストールした SQL Server に接続します (**[サーバーへの接続]** ダイアログが自動的に表示されない場合は、左パネルの **[オブジェクト エクスプローラー]** に移動し、**[接続]** をクリックして、**[データベース エンジン]** をクリックします)。     
   
    ![[サーバーへの接続]](./media/hybrid-connections-create-on-premises-database/A04SSMSConnectToServer.png)
   
    **[サーバーの種類]** には、**[データベース エンジン]** を選択します。 **[サーバー名]** には「**localhost**」と入力するか、SQL Server をインストールしたコンピューターの名前を使用します。 **[SQL Server 認証]**を選択し、前に作成した sa のログインのパスワードを入力します。 
2. SQL Server Management Studio を使用して新しいデータベースを作成するには、オブジェクト エクスプローラーで **[データベース]** を右クリックしてから、**[新しいデータベース]** をクリックします。
3. **[新しいデータベース]** ダイアログ ボックスで、「`OnPremisesDB`」と入力し、**[OK]** をクリックします。 
4. オブジェクト エクスプローラーで、 **[データベース]**を展開すると、新しいデータベースが作成されたことが示されます。

### <a name="create-a-new-sql-server-login-and-set-permissions"></a>新しい SQL Server ログインを作成し、アクセス許可を設定する
最後に、アクセス許可を制限した新しい SQL Server ログインを作成します。 Azure サービスは、サーバーに対する完全なアクセス許可を持つ組み込みの sa ログインではなく、このログインを使用してオンプレミスの SQL Server に接続します。

1. SQL Server Management Studio オブジェクト エクスプローラーで、**[OnPremisesDB]** データベースを右クリックし、**[新しいクエリ]** をクリックします。
2. 次の TSQL クエリを、クエリ ウィンドウに貼り付けます。
   
       USE [master]
       GO
   
       /* Replace the PASSWORD in the following statement with a secure password. 
          If you save this script, make sure that you secure the file to 
          securely maintain the password. */ 
       CREATE LOGIN [HybridConnectionLogin] WITH PASSWORD=N'<**secure_password**>', 
           DEFAULT_DATABASE=[OnPremisesDB], DEFAULT_LANGUAGE=[us_english], 
           CHECK_EXPIRATION=OFF, CHECK_POLICY=ON
       GO
   
       USE [OnPremisesDB]
       GO
   
       CREATE USER [HybridConnectionLogin] FOR LOGIN [HybridConnectionLogin] 
       WITH DEFAULT_SCHEMA=[dbo]
       GO
   
       GRANT CONNECT TO [HybridConnectionLogin]
       GRANT CREATE TABLE TO [HybridConnectionLogin]
       GRANT CREATE SCHEMA TO [HybridConnectionLogin]
       GO  
3. 上記のスクリプトでは、文字列 `<**secure_password**>` を、新しい *HybridConnectionsLogin*用の保護されたパスワードに置き換えます。
4. **実行** して、新しいログインを作成し、オンプレミスのデータベースで必要なアクセス許可を付与します。



<!--HONumber=Jan17_HO3-->


