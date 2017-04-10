次のステップに従って、MongoDB を CentOS Linux が実行されている仮想マシンにインストールして実行します。

> [!WARNING]
> 認証、IP アドレス バインドなどの MongoDB セキュリティ機能は既定では有効になっていません。 MongoDB を運用環境に展開する前に、セキュリティ機能を有効にすることをお勧めします。  詳細については、「 [Security and Authentication (セキュリティと認証)](http://www.mongodb.org/display/DOCS/Security+and+Authentication) 」を参照してください。
> 
> 

1. MongoDB をインストールできるようにパッケージ管理システム (YUM) を構成します。 */etc/yum.repos.d/10gen.repo* ファイルを作成し、リポジトリの情報を保存して、以下を追加します。
   
        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1
2. repo ファイルを保存し、次のコマンドを実行して、ローカル パッケージ データベースを更新します。
   
        $ sudo yum update
3. パッケージをインストールするには、次のコマンドを実行し、MongoDB の最新の安定バージョンと関連ツールをインストールします。
   
        $ sudo yum install mongo-10gen mongo-10gen-server
   
    MongoDB がダウンロードおよびインストールされるまで待ちます。
4. データ ディレクトリを作成します。 MongoDB では */data/db* ディレクトリにデータが保存されるように既定で設定されていますが、このディレクトリは作成する必要があります。 作成するには、次のように実行します。
   
        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data
   
    Linux への MongoDB のインストールの詳細については、[Unix のクイック スタート][QuickstartUnix]に関する記事を参照してください。
5. データベースを開始するには、次を実行します。
   
        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log
   
    MongoDB サーバーがジャーナル ファイルを開始して事前に割り当てると、すべてのログ メッセージが */srv/datadrive/data/mongod.log* ファイルにダイレクトされます。 MongoDB がジャーナル ファイルを事前に割り当てて、接続のリッスンを開始するには、数分かかる場合があります。
6. MongoDB 管理シェルを開始するには、個別の SSH または PuTTY ウィンドウを開いて実行します。
   
        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  
   
    データベースは挿入によって作成されます。
7. MongoDB をインストールしたら、MongoDB にリモートでアクセスできるように、エンドポイントを構成する必要があります。 管理ポータルで、**[仮想マシン]**、新しい仮想マシンの名前、**[エンドポイント]** の順にクリックします。
   
    ![エンドポイント][Image7]
8. ページの下部にある **[エンドポイントの追加]** をクリックします。
   
    ![[エンドポイント]][Image8]
9. 次の設定で、エンドポイントを追加します。
   
   * **名前**: Mongo
   * **プロトコル**: TCP
   * **パブリック ポート**: 27017
   * **プライベート ポート**: 27017
   
   これにより、MongoDB へのリモート アクセスが可能になります。

[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png


<!--HONumber=Jan17_HO3-->


