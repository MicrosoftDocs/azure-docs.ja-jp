---
title: "Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする"
description: "Azure SQL Database にデータを保存する PHP Web アプリを作成し、Azure App Service への Git デプロイメントを使用する方法を示すチュートリアル。"
services: app-service\web, sql-database
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6b090bf6-31d8-4b74-81eb-050ef95929ca
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c7dbb3ab55607e377abc3c91c7ee3e938248a676


---
# <a name="create-a-php-sql-web-app-and-deploy-to-azure-app-service-using-git"></a>Azure App Service で PHP-MySQL Web アプリを作成して Git でデプロイする
このチュートリアルでは、Azure SQL Database に接続する [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の PHT Web アプリを作成する方法と、Git を使用してそれをデプロイする方法について説明します。 このチュートリアルは、コンピューターに [PHP][install-php]、[SQL Server Express][install-SQLExpress]、[Microsoft Drivers for SQL Server for PHP](http://www.microsoft.com/download/en/details.aspx?id=20098)、および [Git][install-git] がインストールされていることを前提としています。 このチュートリアルを完了すると、Azure で動作する PHP-SQL Web アプリが完成します。

> [!NOTE]
> [Microsoft Web プラットフォーム インストーラー](http://www.microsoft.com/web/downloads/platform.aspx)を使用すると、PHP、SQL Server Express、Microsoft Drivers for SQL Server for PHP をインストールおよび構成できます。
> 
> 

学習内容:

* [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して Azure Web アプリと SQL データベースを作成する方法。 App Service Web Apps では PHP が既定で有効になっているため、特に何もしなくても PHP コードを実行できます
* Git を使用して Azure にアプリケーションを発行および再発行する方法。

このチュートリアルでは、登録用の単純な Web アプリケーションを PHP で作成します。 このアプリケーションは Azure の Web サイトでホストします。 完成したアプリケーションのスクリーンショットは次のようになります。

![Azure PHP Web Site](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="create-an-azure-web-app-and-set-up-git-publishing"></a>Azure Web アプリの作成と Git 発行の設定
Azure の Web アプリと SQL Database を作成するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. ダッシュ ボードの左上の **[新規]** アイコンをクリックして Azure Marketplace を開き、Marketplace の横の **[すべて選択]** をクリックし、**[Web + モバイル]** を選択します。
3. Marketplace で **[Web + モバイル]**を選択します。
4. **[Web アプリ + SQL]** アイコンをクリックします。
5. Web アプリ + SQL アプリの説明を読み、 **[作成]**を選択します。
6. 各部分 (**[リソース グループ]**、**[Web アプリ]**、**[データベース]**、**[サブスクリプション]**) をクリックし、必要なフィールドに値を入力するか、値を選択します。
   
   * 好みの URL 名を入力します    
   * データベース サーバーの資格情報を構成します。
   * 最も近いリージョンを選択します
     
     ![configure your app](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)
7. Web アプリの定義が完了したら、 **[作成]**をクリックします。
   
    Web アプリが作成されると、**[通知]** ボタンが緑色の **[成功]** と点滅し、リソース グループ ブレードが開いてグループの Web アプリと SQL Database 双方が表示されます。
8. リソース グループ ブレードの Web アプリのアイコンをクリックして Web アプリのブレードを開きます。
   
    ![Web アプリ リソース グループ](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)
9. **[設定]** で **[継続的なデプロイメント]** > **[必要な設定の構成]** をクリックします。 **[ローカル Git リポジトリ]** を選択し、**[OK]** をクリックします。
   
    ![ソース コードの位置](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)
   
    Git リポジトリが未選択である場合は、ユーザー名とパスワードを指定する必要があります。 そのためには、Web アプリのブレードで **[設定]** > **[デプロイメント資格情報]** をクリックします。
   
    ![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)
10. **[設定]** で **[プロパティ]** をクリックして、後ほど PHP アプリのデプロイで使用する Git リモート URL を確認します。

## <a name="get-sql-database-connection-information"></a>SQL Database 接続情報を取得する
Web アプリにリンクされている SQL Database インスタンスに接続するには、データベースの作成時に指定した接続情報が必要になります。 SQL Database 接続情報を取得するには、次の手順に従います。

1. リソース グループのブレードに戻り、SQL データベース アイコンをクリックします。
2. SQL Database ブレードで **[設定]** > 、**[プロパティ]**、**[データベース接続文字列を表示]** の順にクリックします。 
   
    ![データベースのプロパティの表示](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
3. 表示されたダイアログの **[PHP]** セクションから、`Server`、`SQL Database`、および `User Name` の値をメモします。 この値は後ほど PHP Web アプリを Azure App Service に発行する際に使用します。

## <a name="build-and-test-your-application-locally"></a>アプリケーションの作成とローカル テスト
Registration アプリケーションは、名前と電子メール アドレスを入力してイベントに登録するための、単純な PHP アプリケーションです。 それまでの登録者情報がテーブルに表示されます。 登録情報は SQL Database インスタンスに保存されます。 アプリケーションは、次の&2; つのファイルで構成されます (下にあるコードをコピーし、貼り付けて使用できます)。

* **index.php**: 登録用のフォームと登録者情報が含まれたテーブルを表示します。
* **createtable.php**: アプリケーション用の SQL Database テーブルを作成します。 このファイルは&1; 度しか使用されません。

アプリケーションをローカルで実行するには、次の手順に従います。 これらの手順は、ローカル コンピューターに PHP および SQL Server Express がセットアップされており、[SQL Server 用 PDO 拡張機能][pdo-sqlsrv]が有効になっていることを前提としています。

1. `registration`という SQL Server データベースを作成します。 これには、 `sqlcmd` コマンド プロンプトで次のコマンドを実行します。
   
        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO    
2. アプリケーションのルート ディレクトリに、`createtable.php` というファイルと`index.php` というファイルを&2; つ作成します。
3. `createtable.php` ファイルをテキスト エディターまたは IDE で開き、次のコードを追加します。 このコードは、`registration_tbl` データベースに `registration` テーブルを作成するために使用します。
   
        <?php
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
            id INT NOT NULL IDENTITY(1,1) 
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
   
    <code>$user</code> と <code>$pwd</code> の値は、ローカルの SQL Server ユーザー名とパスワードに置き換える必要があります。
4. アプリケーションのルート ディレクトリのターミナルで、次のコマンドを入力します。
   
        php -S localhost:8000
5. Web ブラウザーを開いて、**http://localhost:8000/createtable.php** にアクセスします。 このコードは、データベースに `registration_tbl` テーブルを作成するために使用します。
6. **index.php** ファイルをテキスト エディターまたは IDE で開いて、ページの基本的な HTML コードおよび CSS コードを追加します (PHP コードは後で追加します)。
   
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
7. PHP タグ内に、データベースに接続するための PHP コードを追加します。
   
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }
   
    ここでも、<code>$user</code> と <code>$pwd</code>の値は、ローカルの MySQL ユーザー名とパスワードに置き換える必要があります。
8. データベース接続コードの次に、登録情報をデータベースに挿入するためのコードを追加します。
   
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
9. 上のコードの次に、データベースからデータを取得するためのコードを追加します。
   
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

これで、**http://localhost:8000/index.php** に移動してアプリケーションをテストできるようになりました。

## <a name="publish-your-application"></a>アプリケーションの発行
アプリケーションをローカルでテストした後、Git を使用してそのアプリケーションを App Service Web Apps に発行できます。 ただし、まずアプリケーション内のデータベース接続情報を更新する必要があります。 先ほど (「**SQL Database の接続情報の取得**」セクションで) 取得したデータベース接続情報を使用し、`createdatabase.php` ファイルと `index.php` ファイルの**両方**で、次の情報を適切な値に置き換えます。

    // DB connection info
    $host = "tcp:<value of Server>";
    $user = "<value of User Name>";
    $pwd = "<your password>";
    $db = "<value of SQL Database>";

> [!NOTE]
> <code>$host</code> では、Server の値を <code>tcp:</code> に付加する必要があります。
> 
> 

これで、Git 発行を設定してアプリケーションを発行する準備ができました。

> [!NOTE]
> これらは、上の「 **Azure の Web アプリの作成と Git 発行の設定** 」セクションの最後でメモした手順と同じです。
> 
> 

1. GitBash (Git が `PATH`にある場合はターミナル) を開き、ディレクトリをアプリケーションの **登録** ディレクトリに変更して、次のコマンドを実行します。
   
        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master
   
    先ほど作成したパスワードを入力するように求められます。
2. アプリケーション用の SQL データベースを作成するには、**http://[Web アプリ名].azurewebsites.net/createtable.php** に移動します。
3. アプリの使用を開始するには、**http://[Web アプリ名].azurewebsites.net/index.php** に移動します。

アプリケーションを発行した後、アプリケーションへの変更を開始し、Git を使用してその変更を発行することもできます。 

## <a name="publish-changes-to-your-application"></a>アプリケーションへの変更の発行
アプリケーションへの変更を発行するには、次のステップに従います。

1. ローカルでアプリケーションへの変更を行います。
2. GitBash (Git が `PATH`にある場合はターミナル) を開き、ディレクトリをアプリケーションのルート ディレクトリに変更して、次のコマンドを実行します。
   
        git add .
        git commit -m "comment describing changes"
        git push azure master
   
    先ほど作成したパスワードを入力するように求められます。
3. 変更内容を確認するには、**http://[web app name].azurewebsites.net/index.php** に移動します。

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv




<!--HONumber=Jan17_HO3-->


