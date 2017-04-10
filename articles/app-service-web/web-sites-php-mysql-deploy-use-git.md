---
title: "Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする"
description: "MySQL にデータを保存する PHP Web アプリを作成し、Azure への Git デプロイを使用する方法を説明するチュートリアル。"
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
tags: mysql
ms.assetid: 7454475f-e275-4bc7-9f09-1ef07382e5da
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 1b1da346a253443c0b5eaf6f8a9a5a399923e760


---
# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする
このチュートリアルでは、PHP-MySQL Web アプリを作成する方法と、Git を使用してそれを [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法について説明します。 コンピューターにインストールされている [PHP][install-php]、MySQL コマンド ライン ツール ([MySQL][install-mysql] の一部)、および [Git][install-git] を使います。 このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。 このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web アプリが完成します。

学習内容:

* [Azure Portal][management-portal] を使って Web アプリと MySQL データベースを作成する方法。 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) では PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます
* Git を使用して Azure にアプリケーションを発行および再発行する方法。
* Composer 拡張機能を有効にして `git push`ごとに Composer のタスクを自動化する方法。

このチュートリアルでは、登録用の単純な Web アプリを PHP で作成します。 アプリケーションは、Web Apps でホストされます。 完成したアプリケーションのスクリーンショットは次のようになります。

![Azure の PHP Web サイト][running-app]

## <a name="set-up-the-development-environment"></a>開発環境を設定する
このチュートリアルは、コンピューターに [PHP][install-php]、MySQL コマンド ライン ツール ([MySQL][install-mysql] の一部)、および [Git][install-git] がインストールされていることを前提としています。

<a id="create-web-site-and-set-up-git"></a>

## <a name="create-a-web-app-and-set-up-git-publishing"></a>Web アプリの作成と Git 発行の設定
Web アプリと MySQL データベースを作成するには、次のステップに従います。

1. [Azure Portal][management-portal] にログインします。
2. **[次へ]** アイコンをクリックします。
3. **[Marketplace]** の横の **[すべて表示]** をクリック します。 
4. **[Web + モバイル]** をクリックし、**[Web アプリ + MySQL]** をクリックします。 **[作成]**をクリックします。
5. リソース グループの有効な名前を入力します。
   
    ![リソース グループ名の設定][resource-group]
6. 新しい Web アプリについての値を入力します。
   
    ![Web アプリの作成][new-web-app]
7. 法律条項への同意も含めて、新しいデータベースについての値を入力します。
   
    ![新しい MySQL データベースの作成][new-mysql-db]
8. Web アプリが作成されると、新しい Web アプリのブレードが表示されます。
9. **[設定]** で **[継続的なデプロイメント]** をクリックして、*[必要な設定の構成]* をクリックします。
   
    ![Git 発行の設定][setup-publishing]
10. ソース コード用の **[ローカル Git リポジトリ]** を選択します。
    
     ![Git リポジトリの設定][setup-repository]
11. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。 作成するユーザー名とパスワードはメモしておいてください (Git リポジトリを設定したことがある場合は、この手順をスキップできます)。
    
     ![発行資格情報の作成][credentials]

## <a name="get-remote-mysql-connection-information"></a>MySQL のリモート接続情報の取得
Web Apps で実行されている MySQL データベースに接続するには、接続情報が必要になります。 MySQL の接続情報を取得するには、次の手順に従います。

1. リソース グループで、データベースをクリックします。
   
    ![データベースの選択][select-database]
2. データベースの **[設定]** で、**[プロパティ]** を選択します。
   
    ![プロパティの選択][select-properties]
3. `Database`、`Host`、`User Id`、`Password` の各値をメモします。
   
    ![プロパティへの注記][note-properties]

## <a name="build-and-test-your-app-locally"></a>アプリケーションの作成とローカル テスト
Web アプリを作成したので、アプリケーションをローカルで作成し、それをテストした後にデプロイすることができます。

Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。 それまでの登録者情報がテーブルに表示されます。 登録情報は MySQL データベースに保存されます。 アプリケーションを構成するファイルは&1; つです (下にあるコードをコピーし、貼り付けて使用できます)。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。

アプリケーションを作成してローカルで実行するには、次の手順に従います。 ここに示す手順は、ローカル コンピューターに PHP、MySQL コマンド ライン ツール (MySQL の一部) がセットアップされており、[MySQL 用 PDO 拡張機能][pdo-mysql]が有効になっていることを前提としています。

1. 先ほどメモしておいた `Data Source`、`User Id`、`Password`、`Database` の各値を使用して、リモートの MySQL サーバーに接続します。
   
        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]
2. MySQL コマンド プロンプトが表示されます。
   
        mysql>
3. 次の `CREATE TABLE` コマンドを貼り付けます。これにより、データベース内に `registration_tbl` テーブルが作成されます。
   
        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);
4. ローカル アプリケーション フォルダーのルートに **index.php** ファイルを作成します。
5. テキスト エディターまたは IDE で **index.php** ファイルを開き、次のコードを追加します。`//TODO:` コメントの箇所を必要に応じて変更します。

        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
            if(!empty($_POST)) {
            try {
                $name = $_POST['name'];
                $email = $_POST['email'];
                $date = date("Y-m-d");
                // Insert data
                $sql_insert = "INSERT INTO registration_tbl (name, email, date)
                           VALUES (?,?,?)";
                $stmt = $conn->prepare($sql_insert);
                $stmt->bindValue(1, $name);
                $stmt->bindValue(2, $email);
                $stmt->bindValue(3, $date);
                $stmt->execute();
            }
            catch(Exception $e) {
                die(var_dump($e));
            }
            echo "<h3>Your're registered!</h3>";
            }
            // Retrieve data
            $sql_select = "SELECT * FROM registration_tbl";
            $stmt = $conn->query($sql_select);
            $registrants = $stmt->fetchAll();
            if(count($registrants) > 0) {
                echo "<h2>People who are registered:</h2>";
                echo "<table>";
                echo "<tr><th>Name</th>";
                echo "<th>Email</th>";
                echo "<th>Date</th></tr>";
                foreach($registrants as $registrant) {
                    echo "<tr><td>".$registrant['name']."</td>";
                    echo "<td>".$registrant['email']."</td>";
                    echo "<td>".$registrant['date']."</td></tr>";
                }
                 echo "</table>";
            } else {
                echo "<h3>No one is currently registered.</h3>";
            }
        ?>
        </body>
        </html>

1. ターミナルで、アプリケーション フォルダーに移動し、次のコマンドを入力します。
   
       php -S localhost:8000

これで、**http://localhost:8000/** に移動してアプリケーションをテストできるようになりました。

## <a name="publish-your-app"></a>アプリケーションの発行
アプリケーションをローカルでテストした後、Git を使用してそのアプリケーションを Web Apps に発行できます。 ローカルの Git リポジトリを初期化して、アプリケーションを発行します。

> [!NOTE]
> これらは、上の「Web アプリの作成と Git 発行の設定」セクションの最後で Azure ポータルに示された手順と同じです。
> 
> 

1. (省略可能) Git リモート リポジトリの URL を忘れた場合やスペルを誤った場合は、Azure Portal の Web アプリ プロパティに移動します。
2. GitBash (Git が `PATH`にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。
   
        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master
   
    先ほど作成したパスワードを入力するように求められます。
   
    ![Git 経由による Azure への初期プッシュ][git-initial-push]
3. アプリケーションの使用を開始できるように、**http://[サイト名].azurewebsites.net/index.php** に移動します (この情報はアカウント ダッシュボードに保存されます)。
   
    ![Azure の PHP Web サイト][running-app]

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。

## <a name="publish-changes-to-your-app"></a>アプリケーションへの変更の発行
アプリケーションへの変更を発行するには、次のステップに従います。

1. ローカルでアプリケーションへの変更を行います。
2. GitBash (Git が `PATH`にある場合はターミナル) を開き、ディレクトリをアプリのルート ディレクトリに変更して、次のコマンドを実行します。
   
        git add .
        git commit -m "comment describing changes"
        git push azure master
   
    先ほど作成したパスワードを入力するように求められます。
   
    ![サイト変更の Git 経由による Azure へのプッシュ][git-change-push]
3. アプリケーションとその変更内容を確認できるように、**http://[サイト名].azurewebsites.net/index.php** に移動します。
   
    ![Azure の PHP Web サイト][running-app]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

<a name="composer"></a>

## <a name="enable-composer-automation-with-the-composer-extension"></a>Composer 拡張機能を使用した Composer 自動化の有効化
既定では、PHP プロジェクトに composer.json があっても、App Service の git デプロイ プロセスで処理されません。 `git push` で composer.json の処理を有効にするには、Composer 拡張機能を有効にします。

1. [Azure Portal][management-portal] の PHP Web アプリのブレードで、**[ツール]** > **[拡張機能]** をクリックします。
   
    ![Composer Extension Settings][composer-extension-settings]
2. **[追加]**、**[Composer]** の順にクリックします。
   
    ![Composer Extension Add][composer-extension-add]
3. **[OK]** をクリックして法律条項に同意します。 もう一度 **[OK]** をクリックすると、拡張機能が追加されます。
   
    これで、 **[インストールされている拡張機能]** ブレードに Composer 拡張機能が表示されるようになります。  
    ![Composer Extension View][composer-extension-view]
4. 前のセクションと同様に、`git add`、`git commit`、`git push` を実行します。 composer.json で定義されている依存関係が Composer によってインストールされていることを確認できます。
   
    ![Composer Extension Success][composer-extension-success]

## <a name="next-steps"></a>次のステップ
詳細については、 [PHP デベロッパー センター](/develop/php/)を参照してください。

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png



<!--HONumber=Jan17_HO3-->


