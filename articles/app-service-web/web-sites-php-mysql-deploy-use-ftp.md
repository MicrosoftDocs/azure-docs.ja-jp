---
title: "Azure App Service で PHP-MySQL Web アプリを作成して FTP でデプロイする"
description: "MySQL にデータを保存する PHP Web アプリを作成し、Azure への FTP デプロイを使用する方法を説明するチュートリアル。"
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6d9d1de5-5868-48fd-8bad-decb4979cd65
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 134239990df959fe2657340eca4f2a8bf646a7b0


---
# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Azure App Service で PHP-MySQL Web アプリを作成して FTP でデプロイする
このチュートリアルでは、PHP-MySQL Web アプリを作成する方法と、FTP を使用してそれをデプロイする方法について説明します。 このチュートリアルは、コンピューターに [PHP][install-php]、[MySQL][install-mysql]、Web サーバー、および FTP クライアントがインストールされていることを前提としています。 このチュートリアルの手順は、Windows、Mac、Linux など、任意のオペレーティング システムで使用できます。 このチュートリアルを完了すると、Azure で動作する PHP/MySQL Web アプリが完成します。

学習内容:

* Azure ポータルを使用して Web アプリと MySQL データベースを作成する方法。 Web Apps では PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます。
* FTP を使用して Azure にアプリケーションを発行する方法。

このチュートリアルでは、登録用の単純な Web アプリを PHP で作成します。 このアプリケーションは Web Apps でホストされます。 完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site][running-app]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページを参照してください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。 
> 
> 

## <a name="create-a-web-app-and-set-up-ftp-publishing"></a>Web アプリの作成と FTP 発行の設定
Web アプリと MySQL データベースを作成するには、次のステップに従います。

1. [Azure Portal][management-portal] にログインします。
2. Azure ポータルの左上にある **[+ 新規]** アイコンをクリックします。
   
    ![新しい Azure の Web サイトの作成][new-website]
3. 検索に「**Web アプリ + MySQL**」と入力し、**[Web アプリ + MySQL]** をクリックします。
   
    ![Custom Create a new Web Site][custom-create]
4. **[作成]**をクリックします。 一意のアプリ サービス名、リソース グループの有効な名前と新しいサービス プランを入力します。
   
    ![リソース グループ名の設定][resource-group]
5. 法律条項への同意も含めて、新しいデータベースについての値を入力します。
   
    ![新しい MySQL データベースの作成][new-mysql-db]
6. Web アプリが作成されると、新しいアプリ サービスのブレードが表示されます。
7. **[設定]** > **[デプロイ資格情報]** をクリックします。 
   
    ![デプロイ資格情報の設定][set-deployment-credentials]
8. FTP 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。 資格情報を保存します。作成したユーザー名とパスワードはメモしておいてください。
   
    ![発行資格情報の作成][portal-ftp-username-password]

## <a name="build-and-test-your-app-locally"></a>アプリケーションの作成とローカル テスト
Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。 それまでの登録者情報がテーブルに表示されます。 登録情報は MySQL データベースに保存されます。 アプリケーションは、次の&2; つのファイルで構成されます。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。
* **createtable.php**: アプリケーション用の MySQL テーブルを作成します。 このファイルは&1; 度しか使用されません。

アプリケーションを作成してローカルで実行するには、次の手順に従います。 ここに示す手順は、ローカル コンピューターに PHP、MySQL、および Web サーバーがセットアップされており、[MySQL 用 PDO 拡張機能][pdo-mysql]が有効になっていることを前提としています。

1. " `registration`" という MySQL データベースを作成します。 これには、MySQL コマンド プロンプトで次のコマンドを実行します。
   
        mysql> create database registration;
2. Web サーバーのルート ディレクトリで、`registration` というフォルダーを作成し、その中に&2; つのファイル (`createtable.php` と `index.php`) を作成します。
3. `createtable.php` ファイルをテキスト エディターまたは IDE で開き、次のコードを追加します。 このコードは、`registration_tbl` データベースに `registration` テーブルを作成するために使用します。
   
        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>
   
   > [!NOTE]
   > <code>$user</code> と <code>$pwd</code> の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。
   > 
   > 
4. Web ブラウザーを開いて、[http://localhost/registration/createtable.php][localhost-createtable] にアクセスします。 このコードは、データベースに `registration_tbl` テーブルを作成するために使用します。
5. **index.php** ファイルをテキスト エディターまたは IDE で開いて、ページの基本的な HTML コードおよび CSS コードを追加します (PHP コードは後で追加します)。
   
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
   
        ?>
        </body>
        </html>
6. PHP タグ内に、データベースに接続するための PHP コードを追加します。
   
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }
   
   > [!NOTE]
   > ここでも、<code>$user</code> と <code>$pwd</code>の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。
   > 
   > 
7. データベース接続コードの次に、登録情報をデータベースに挿入するためのコードを追加します。
   
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
8. 上のコードの次に、データベースからデータを取得するためのコードを追加します。
   
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

これで、[http://localhost/registration/index.php][localhost-index] にアクセスしてアプリをテストできます。

## <a name="get-mysql-and-ftp-connection-information"></a>MySQL と FTP の接続情報の取得
Web Apps で実行されている MySQL データベースに接続するには、接続情報が必要になります。 MySQL の接続情報を取得するには、次の手順に従います。

1. App Service Web アプリのブレードで、次のリソース グループのリンクをクリックします。
   
    ![リソース グループの選択][select-resourcegroup]
2. リソース グループで、データベースをクリックします。
   
    ![データベースの選択][select-database]
3. データベース サマリーから、**[設定]** > **[プロパティ]** を選択します。
   
    ![プロパティの選択][select-properties]
4. `Database`、`Host`、`User Id`、`Password` の各値をメモします。
   
    ![プロパティへの注記][note-properties]
5. Web アプリで、ページの右下にある **[発行プロファイルのダウンロード]** リンクをクリックします。
   
    ![[発行プロファイルのダウンロード]][download-publish-profile]
6. XML エディターで `.publishsettings` ファイルを開きます。 
7. 次のように `<publishProfile >` が指定されている `publishMethod="FTP"` 要素を確認します。
   
        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>

`publishUrl`、`userName`、`userPWD` の各属性を記録します。

## <a name="publish-your-app"></a>アプリケーションの発行
アプリケーションをローカルでテストした後、FTP を使用してそのアプリケーションを Web アプリに発行できます。 ただし、まずアプリケーション内のデータベース接続情報を更新する必要があります。 先ほど (「**MySQL と FTP の接続情報の取得」**セクションで) 取得したデータベース接続情報を使用し、`createdatabase.php` ファイルと `index.php` ファイルの**両方**で、次の情報を適切な値に置き換えます。

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

これで、FTP を使用してアプリケーションを発行する準備ができました。

1. 好みの FTP クライアントを開きます。
2. 先ほどメモしておいた `publishUrl` 属性の*ホスト名部分*を FTP クライアントに入力します。
3. 先ほどメモしておいた `userName` 属性および `userPWD` 属性をそのまま FTP クライアントに入力します。
4. 接続を確立します。

接続した後、必要に応じて、ファイルをアップロードおよびダウンロードすることができます。 ファイルのアップロード先は、必ずルート ディレクトリ ( `/site/wwwroot`) にしてください。

`index.php` と `createtable.php` の両方をアップロードした後、**http://[サイト名].azurewebsites.net/createtable.php** に移動してアプリケーション用の MySQL テーブルを作成し、**http://[サイト名].azurewebsites.net/index.php** に移動してアプリケーションの使用を開始します。

## <a name="next-steps"></a>次のステップ
詳細については、 [PHP デベロッパー センター](/develop/php/)を参照してください。

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png




<!--HONumber=Jan17_HO3-->


