---
title: オープンソース テクノロジに関する FAQ
description: Azure App Service のオープン ソース テクノロジに関する FAQ への回答をご覧ください。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831841"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Azure の Web アプリのオープン ソース テクノロジに関する FAQ

この記事では、[Azure App Service の Web Apps 機能](https://azure.microsoft.com/services/app-service/web/)のオープン ソース テクノロジの問題に関するよく寄せられる質問 (FAQ) への回答を掲載しています。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>PHP ロギングをオンにして PHP の問題をトラブルシューティングするにはどうすればよいですか?

PHP ログを有効にするには、次の手順を実行します。

1. **Kudu の Web サイト** (`https://*yourwebsitename*.scm.azurewebsites.net`) にサインインします。
2. 上部のメニューから、 **[デバッグ コンソール]**  >  **[CMD]** の順に選択します。
3. **Site** フォルダーを選択します。
4. **wwwroot** フォルダーを選択します。
5. **+** アイコンをクリックして、 **[新しいファイル]** を選択します。
6. **.user.ini** というファイル名を設定します。
7. **. user.ini** の横にある鉛筆のアイコンを選択します。
8. ファイルで、コード `log_errors=on` を追加します。
9. **[保存]** を選択します。
10. **wp-config.php** の横にある鉛筆のアイコンを選択します。
11. テキストを次のコードに変更します。
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Azure ポータルの Web アプリのメニューで、Web アプリを再起動します。

詳細については、[WordPress エラー ログを有効にする](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2)に関するページを参照してください。

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>App Service にホストされているアプリでの Python アプリケーション エラーをログする方法を教えてください?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>App Service にホストされている Node.js アプリケーションのバージョンを変更する方法を教えてください?

Node.js アプリケーションのバージョンを変更するために、次のオプションのいずれかを使用できます。

* Azure ポータルで、 **[アプリ設定]** を使用します。
  1. Azure Portal で、Web アプリに移動します。
  2. **[設定]** ブレードで、 **[アプリケーション設定]** を選択します。
  3. **[アプリ設定]** で、キーとして WEBSITE_NODE_DEFAULT_VERSION を含め、値として希望する Node.js のバージョンを指定できます。
  4. **Kudu コンソール** (`https://*yourwebsitename*.scm.azurewebsites.net`) に移動します。
  5. Node.js バージョンを確認するには、次のコマンドを入力します。  
     ```
     node -v
     ```
* Iisnode.yml ファイルを変更します。 iisnode.yml ファイルで Node.js バージョンを変更しても、iisnode が使用するランタイム環境が設定されるだけです。 Kudu cmd などは、Azure ポータルの **[アプリ設定]** で設定されている Node.js バージョンを引き続き使用します。

  Iisnode.yml を手動で設定するには、アプリのルート フォルダーに iisnode.yml ファイルを作成します。 ファイルに次の行を含めます。
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* ソース管理デプロイ中に package.json を使用して Iisnode.yml ファイルを設定します。
  Azure のソース管理デプロイ プロセスには、次の手順があります。
  1. コンテンツを Azure Web アプリに移動します。
  2. Web アプリのルート フォルダーにデプロイ スクリプト (deploy.cmd、デプロイメント ファイル) がない場合、既定のデプロイメント スクリプトを作成します。
  3. package.json ファイルで Node.js バージョンを指定する場合、iisnode.yml ファイルを作成した場所でデプロイメント スクリプトを実行します。> engine `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Iisnode.yml ファイルには、次のコード行があります。
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>App Service にホストされている WordPress アプリに "Error establishing a database connection"(データベースの接続を確立中にエラーが発生しました) というメッセージが表示されます。 どのようにトラブルシューティングすればよいですか?

Azure WordPress アプリでこのエラーが発生する場合、php_errors.log と debug.log を有効にし、[WordPress エラー ログを有効にする](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2)に関するページで詳しく説明している手順を実行します。

ログを有効にしてエラーを再現し、ログをチェックして接続を使い尽くしていないかどうかを確認します。
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

debug.log ファイルまたは php_errors.log ファイルにエラーが表示される場合、アプリが接続数を超えています。 ClearDB でホストしている場合、[サービス プラン](https://www.cleardb.com/pricing.view)で利用可能な接続の数を確認します。

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>App Service にホストされている Node.js アプリをデバッグする方法は?

1.  **Kudu コンソール** (`https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole`) に移動します。
2.  アプリケーション ログ フォルダー (D:\home\LogFiles\Application) に移動します。
3.  logging_errors.txt ファイルの内容を確認します。

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>App Service Web アプリまたは API アプリでネイティブ Python モジュールをインストールする方法を教えてください?

Azure で pip を使用すると一部のパッケージがインストールされない場合があります。 そのパッケージが Python Package Index で使用できない、またはコンパイラが必要な可能性があります (App Service の Web アプリを実行しているコンピューターにはコンパイラはありません)。 App Service Web アプリおよび API アプリでネイティブ モジュールをインストールする詳細については、[App Service での Python モジュールのインストール](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps)に関するページを参照してください。

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Git および Python の新しいバージョンを使用して Django アプリを App Service にデプロイする方法を教えてください?

Django のインストール方法の詳細については、[App Service に Django アプリをデプロイする](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python)に関するページを参照してください。

## <a name="where-are-the-tomcat-log-files-located"></a>Tomcat のログ ファイルはどこにありますか?

Azure Marketplace とカスタム デプロイの場合:

* フォルダーの場所: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* 関連ファイル:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


ポータルの **[アプリ設定]** デプロイの場合:

* フォルダーの場所: D:\home\LogFiles
* 関連ファイル:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>JDBC ドライバー接続エラーのトラブルシューティング方法を教えてください?

Tomcat のログに次のメッセージが表示される場合があります。

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

このエラーを解決するには:

1. app/lib フォルダーから sqljdbc*.jar ファイルを削除します。
2. カスタム Tomcat または Azure Marketplace の Tomcat Web サーバーを使用している場合は、Tomcat lib フォルダーにこの .jar ファイルをコピーします。
3. Azure ポータルから Java を有効にしている場合 ( **[Java 1.8]**  >  **[Tomcat サーバー]** の順に選択)、アプリに並列のフォルダーに sqljdbc.* jar ファイルをコピーします。 次に、web.config ファイルに次のクラスパスの設定を追加します。

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>ライブのログ ファイルをコピーしようとするときにエラーが発生するのはなぜですか?

Java アプリ (たとえば、Tomcat) のライブのログ ファイルをコピーしようとすると、この FTP エラーが表示される場合があります。

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

エラー メッセージは、FTP クライアントによって異なります。

すべての Java アプリに、このロックの問題があります。 Kudu のみが、アプリ実行中のこのファイルをダウンロードをサポートしています。

アプリの停止すると、FTP はこれらのファイルにアクセスできるようになります。

別の回避策は、スケジュールで実行されてこれらのファイルを別のディレクトリにコピーする Web ジョブを書き込むことです。 サンプル プロジェクトについては、[CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) プロジェクトを参照してください。

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Jetty のログ ファイルはどこにありますか?

Marketplace とカスタム デプロイでは、ログ ファイルは D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs フォルダーにあります。 フォルダーの場所は、使用している Jetty のバージョンによって異なります。 たとえば、ここで示したパスは Jetty 9.1.2 用です。 jetty_ *YYYY_MM_DD*.stderrout.log を探してください。

ポータルのアプリ設定デプロイでは、ログ ファイルは D:\home\LogFiles にあります。 jetty_ *YYYY_MM_DD*.stderrout.log を探してください。

## <a name="can-i-send-email-from-my-azure-web-app"></a>Azure Web アプリから電子メールを送信できますか?

App Service には組み込みの電子メール機能はありません。 アプリから電子メールを送信するための適切な代替手段は、この[スタック オーバーフローのディスカッション](https://stackoverflow.com/questions/17666161/sending-email-from-azure)に関するページを参照してください。

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>WordPress サイトが別の URL をリダイレクトするのはなぜですか?

Azure に最近移行した場合は、WordPress が古いドメイン URL にリダイレクトする可能性があります。 これは、MySQL データベースの設定が原因です。

WordPress Buddy + は、データベースで直接リダイレクト URL の更新に使用できる、Azure サイトの拡張機能です。 WordPress Buddy + の使用に関する詳細については、[WordPress Buddy + を使用した WordPress ツールおよび MySQL の移行](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)に関するページを参照してください。

また、SQL クエリや PHPMyAdmin を使用してリダイレクト URL を手動で更新する場合、[WordPress: 間違った URL へのリダイレクト](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url)に関するページを参照してください。

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>WordPress のサインイン パスワードを変更する方法を教えてください?

WordPress のサインイン パスワードを忘れた場合、WordPress Buddy + を使用して更新できます。 パスワードをリセットするには、WordPress Buddy + Azure サイト拡張機能をインストールし、[WordPress Buddy + を使用した WordPress ツールおよび MySQL の移行](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)に関するページに記載されている手順を実行します。

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>WordPress にサインインできません。 解決するにはどうすればよいですか?

プラグインを最近インストールした後に WordPress からロックアウトされた場合、そのプラグインに欠陥がある可能性があります。 WordPress Buddy + は、WordPress のプラグインを無効化できる Azure のサイトの拡張機能です。 詳細については、[WordPress Buddy + を使用した WordPress ツールおよび MySQL の移行](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)に関するページを参照してください。

## <a name="how-do-i-migrate-my-wordpress-database"></a>WordPress データベースを移行する方法は?

WordPress Web サイトに接続されている MySQL データベースを移行する方法は複数あります。

* 開発者 : [コマンド プロンプトまたは PHPMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service) を使用
* 開発者以外 : [WordPress Buddy +](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows) を使用

## <a name="how-do-i-help-make-wordpress-more-secure"></a>WordPress をより安全にする方法を教えてください?

WordPress 用のセキュリティのベスト プラクティスについては、[Azure で WordPress セキュリティのベスト プラクティス](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure)に関するページを参照してください。

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>PHPMyAdmin を使用しようとして、「アクセスが拒否されました」というメッセージが表示されます。 解決するにはどうすればよいですか?

この問題は、この App Service のインスタンスで MySQL アプリ内の機能がまだ実行されていない場合に発生する可能性があります。 問題を解決するには、Web サイトにアクセスしてください。 MySQL アプリ内のプロセスを含む必要なプロセスが始まります。 MySQL アプリ内プロセスが実行していることを確認するには、プロセス エクスプローラーで、その mysqld.exe がプロセスに表示されていることを確認します。

その MySQL アプリ内プロセスが実行されていることを確認した後、PHPMyAdmin を使用してください。

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>PHPMyadmin を使用して、MySQL アプリ内データベースをインポートまたはエクスポートしようとすると、HTTP 403 エラーが表示されます。 解決するにはどうすればよいですか?

以前のバージョンの Chrome を使用している場合、既知のバグが発生している可能性があります。 この問題を解決するには、Chrome の新しいバージョンにアップグレードします。 また、この問題が発生しない別のブラウザー (Internet Explorer や Microsoft Edge など) の使用を試してください。