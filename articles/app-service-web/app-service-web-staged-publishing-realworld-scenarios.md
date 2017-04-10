---
title: "Web アプリに対して DevOps 環境を効果的に使用する | Microsoft Docs"
description: "デプロイ スロットを使用してアプリケーションの複数の開発環境をセットアップおよび管理する方法を説明します"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Web アプリに対して DevOps 環境を効果的に使用する
この記事では、開発、ステージング、品質保証、運用などの各種環境 に複数のバージョンのアプリケーションがある場合に、Web アプリケーションのデプロイメントをどのようにセットアップし、管理すればよいかについて説明します。 アプリケーションの各バージョンは、デプロイ プロセスの特定の目的に適合する開発環境とみなすことができます。 たとえば QA 環境は、開発者が運用環境に変更をプッシュする前に、アプリケーションの品質をテストする目的で使用できます。
開発環境が複数あると、管理が煩雑になることもあります。コードを追跡したり、各種のリソース (コンピューティング、Web アプリ、データベース、キャッシュなど) を管理したり、コードを環境全体にデプロイしたりする必要があるからです。

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>非運用環境 (ステージング、開発、品質保証) のセットアップ
運用 Web アプリの準備ができたら、次にするべきことは非運用環境を作成することです。 デプロイ スロットを使用するには、実行環境が Standard または Premium の Azure App Service プラン モードであることを確認します。 デプロイ スロットは、独自のホスト名を持つライブ Web アプリです。 Web アプリのコンテンツと構成の各要素は、(運用スロットを含む) 2 つのデプロイ スロットの間でスワップすることができます。 デプロイ スロットにアプリケーションをデプロイすることには、次のメリットがあります。

- Web アプリを運用スロットにスワップする前に、ステージング デプロイ スロットでアプリへの変更を検証できます。
- 最初に Web アプリをスロットにデプロイし、その後運用サイトにスワップすると、スロットのすべてのインスタンスが、運用サイトへのスワップ前にウォームアップされます。 このプロセスにより、Web アプリをデプロイする際のダウンタイムがなくなります。 トラフィックのリダイレクトはシームレスであるため、スワップ操作によって破棄される要求はありません。 このワークフロー全体を自動化するには、[自動スワップ](web-sites-staged-publishing.md#configure-auto-swap)を構成します (スワップ前の検証が必要ない場合)。
- スワップ後には、Web アプリがステージングされたスロットに、以前の運用 Web アプリが配置されます。 運用スロットにスワップした変更が想定どおりでない場合は、同じスワップをすぐに実行することで、適切な動作が確認されている元の Web アプリに戻すことができます。

ステージング デプロイ スロットをセットアップするには、「[Azure App Service で Web アプリ用にステージング環境をセットアップする](web-sites-staged-publishing.md)」を参照してください。 各環境には、それぞれ独自のリソース セットを含める必要があります。 たとえば、Web アプリでデータベースを使用する場合は、運用 Web アプリとステージング Web アプリの両方にそれぞれ異なるデータベースを使用する必要があります。 開発環境のリソース (データベース、ストレージ、キャッシュなど) を追加して、ステージング開発環境を設定してください。

## <a name="examples-of-using-multiple-development-environments"></a>複数の開発環境を使用する例
どのようなプロジェクトでも、最低 2 つの環境 (開発環境と運用環境) でソース コード管理を行う必要があります。 コンテンツ管理システム (CMS) やアプリケーション フレームワークなどを使用する場合は、アプリケーションをこのシナリオに対応させるために、カスタマイズが必要になることもあります。 この必要性は、以下のセクションで説明されている一般的なフレームワークの一部で発生します。 CMS/フレームワークを使用するにあたっては、多くの確認事項があります。たとえば次のような点です。

- 各種の環境にコンテンツをどのように配置するか。
- フレームワークのバージョン更新に影響を与えることなく変更できるのはどのようなファイルか。
- 各環境の構成をどのように管理するか。
- モジュール、プラグイン、およびコア フレームワークのバージョン更新をどのように管理するか。

プロジェクトに複数の環境を設定する方法には、さまざまなものがあります。 以下の例では、アプリケーションごとに 1 つの方法を示します。

### <a name="wordpress"></a>WordPress
このセクションでは、WordPress 用のスロットを使用してデプロイ ワークフローをセットアップする方法を説明します。 WordPress では、多くの CMS ソリューションと同様、複数の開発環境をカスタマイズなしで使用することはできません。 Azure App Service の Web Apps には、構成設定をコードの外部に簡単に格納するための機能がいくつかあります。

1. ステージング スロットを作成する前に、アプリケーション コードをセットアップして、複数の環境をサポートできるようにします。 WordPress で複数の環境をサポートするには、ローカル開発 Web アプリの `wp-config.php` を編集し、ファイルの先頭に次のコードを追加する必要があります。 このプロセスにより、選択された環境に基づいてアプリケーションが適切な構成を利用できるようになります。

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. Web アプリのルートの下に `config` というフォルダーを作成し、`wp-config.azure.php` ファイルと `wp-config.local.php` ファイルを追加します。これらはそれぞれ、Azure 環境とローカル環境を表します。

3. `wp-config.local.php` に次のコードをコピーします。

    ```
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', 'yourdatabasename');

    /** MySQL database username */
    define('DB_USER', 'yourdbuser');

    /** MySQL database password */
    define('DB_PASSWORD', 'yourpassword');

    /** MySQL hostname */
    define('DB_HOST', 'localhost');
    /**
     * For developers: WordPress debugging mode.
     * * Change this to true to enable the display of notices during development.
     * It is strongly recommended that plugin and theme developers use WP_DEBUG
     * in their development environments.
     */
    define('WP_DEBUG', true);

    //Security key settings
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    上記のコードで設定しているセキュリティ キーは Web アプリをハッキングから守るためのものなので、必ず一意の値を使用してください。 コード内のセキュリティ キーの文字列を生成する必要がある場合は、[自動ジェネレーターに移動](https://api.wordpress.org/secret-key/1.1/salt)して、新しいキー/値ペアを作成できます。

4. `wp-config.azure.php`に次のコードをコピーします。

    ```    
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', getenv('DB_NAME'));

    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));

    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));

    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));

    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */

    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);

    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>相対パスの使用
もう 1 つ、WordPress アプリで構成する必要があるのが、相対パスです。 WordPress では、データベースに URL 情報を格納します。 この記憶域を使用することが、環境間でのコンテンツの移動を難しくしています。 ローカル環境からステージング環境に移動したり、ステージング環境から運用環境に移動したりするたびに、データベースを更新する必要があるからです。 環境間でのデプロイ時ごとにデータベースをデプロイすることで生じる問題発生リスクを軽減するには、[Relative Root リンク プラグイン](https://wordpress.org/plugins/root-relative-urls/)を使用します。このプラグインは、WordPress の管理者ダッシュボードを使用してインストールできます。

`wp-config.php` ファイルに、`That's all, stop editing!` コメントの前に次のエントリを追加します。

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

WordPress 管理者ダッシュボードの [`Plugins`] メニューから、プラグインをアクティブ化します。 WordPress アプリの固定リンク設定を保存します。

#### <a name="the-final-wp-configphp-file"></a>最終の `wp-config.php` ファイル
WordPress のコア更新プログラムはいずれも、`wp-config.php`、`wp-config.azure.php`、および `wp-config.local.php` ファイルに影響を与えません。 以下に示すのは、`wp-config.php` ファイルの最終バージョンです。

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>ステージング環境のセットアップ
1. Azure サブスクリプション上で WordPress Web アプリを既に実行している場合は、[Azure ポータル](http://portal.azure.com) にサインインし、WordPress Web アプリに移動します。 WordPress Web アプリがまだない場合は、Azure Marketplace から作成できます。 詳しくは、「[Azure App Service での WordPress Web アプリの作成](web-sites-php-web-site-gallery.md)」をご覧ください。
**[設定]**  >  **[デプロイメント スロット]**  >  **[追加]** をクリックし、*stage* という名前でデプロイメント スロットを作成します。 デプロイメント スロットは、以前に作成したプライマリ Web アプリと同じリソースを共有する、もう 1 つの Web アプリケーションです。

    ![ステージ デプロイメント スロットを作成する](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. もう 1 つの MySQL データベース (たとえば、`wordpress-stage-db`) を、リソース グループ (`wordpressapp-group`) に追加します。

    ![リソース グループに MySQL データベースを追加する](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. ステージ デプロイメント スロットの接続文字列を更新して、新しいデータベース (`wordpress-stage-db`) を指すようにします。 運用 Web アプリ (`wordpressprodapp`) とステージング Web アプリ (`wordpressprodapp-stage`) が、それぞれ別のデータベースを指すようにする必要があります。

#### <a name="configure-environment-specific-app-settings"></a>環境固有のアプリ設定の構成
開発者は、キー/値の文字列ペアを、Web アプリに関連付けられた構成情報 (**App Settings** と呼ばれる) の一部として、Azure に格納することができます。 Web アプリは、実行時にこれらの値を自動的に取得し、Web アプリで実行されているコードからそれらの値を利用できるようにします。 この機能を使用すると、パスワードを含んだ機密情報 (データベース接続文字列など) が `wp-config.php` などのファイルにクリア テキストとして現れなくなるので、セキュリティ上のメリットがあります。

このプロセス (以下の段落で説明しています) では、WordPress アプリのファイル変更とデータベース変更の両方を含めることができるので、非常に便利です。

* WordPress バージョンのアップグレード
* プラグインの新規追加、編集、またはアップグレード
* 新しいテーマの追加、テーマの編集またはアップグレード

アプリ設定の構成:

* データベース情報
* WordPress のログ記録のオン/オフ
* WordPress のセキュリティ設定

![Wordpress Web アプリ用のアプリ設定](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

運用 Web アプリとステージ スロットのそれぞれに、次のアプリ設定を追加してください。 運用 Web アプリとステージング Web アプリではそれぞれ別のデータベースが使用されるので注意してください。

1. WP_ENV を除くすべての設定パラメーターについて、**[スロット設定]** チェックボックスをオフにします。 このプロセスにより、Web アプリ、ファイル コンテンツ、およびデータベースの構成がスワップされます。 **[スロット設定]** がオンの場合は、**スワップ操作**を実行しても、Web アプリのアプリ設定と接続文字列構成は環境間で移動*されません*。 データベースの変更が存在していても、運用 Web アプリに障害をもたらすことはありません。

2. WebMatrix またはお好みのツール (FTP、Git、PhpMyAdmin など) を使用して、ローカル開発環境の Web アプリをステージ Web アプリとデータベースにデプロイします。

    ![WordPress Web アプリの [Web Matrix 公開] ダイアログ ボックス](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. ステージング Web アプリを参照し、テストします。 Web アプリのテーマが更新されるシナリオについて検討します。ここでは、ステージング Web アプリを扱います。

    ![スロットをスワップする前にステージング Web アプリを参照する](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. すべて問題なければ、ステージング Web アプリの **[スワップ]** ボタンをクリックして、コンテンツを運用環境に移動します。 この場合、**スワップ**操作を行うたびに、Web アプリとデータベースが環境間でスワップされることになります。

    ![Wordpress のスワップによる変更のプレビュー](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > ファイルのみをプッシュすればよい (データベースの更新はない) シナリオの場合は、**スワップ**を実行する前に、Azure ポータルの **Web アプリ設定**ブレードで、データベースに関連するすべての*アプリ設定*と*接続文字列設定*について、**[スロット設定]** をオンにします。 この場合、DB_NAME、DB_HOST、DB_PASSWORD、DB_USER、および既定の接続文字列設定は、**スワップ**の実行時に変更のプレビューに表示されません。 このとき、**スワップ**操作を完了すると、WordPress Web アプリには更新プログラム ファイルだけが配置されることになります。
    >
    >

    **スワップ**を実行する前は、運用環境の WordPress Web アプリは次のような状態です。
    ![スロットをスワップする前の運用 Web アプリ](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    **スワップ操作**を実行すると、運用 Web アプリでテーマが更新されます。

    ![スロットをスワップした後の運用 Web アプリ](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. ロールバックする必要がある場合は、運用 Web **アプリ設定**に移動し、**[スワップ]** ボタンをクリックして、Web アプリとデータベースを運用環境からステージング スロットにスワップします。 **スワップ**操作にデータベースの変更が含まれる場合は、ステージング Web アプリに次回デプロイする際に、ステージング Web アプリの現在のデータベースに対してデータベースの変更をデプロイする必要があります。 現在のデータベースは、以前の運用データベースである場合と、ステージ データベースである場合があります。

#### <a name="summary"></a>概要
次に示すのは、データベースを使用するアプリケーションの汎用的なプロセスです。

1. ローカル環境にアプリケーションをインストールします。
2. 環境固有の構成を含めます (ローカルおよび Azure Web Apps)。
3. Web Apps のステージング環境と運用環境を設定します。
4. 運用アプリケーションが Azure で既に実行されている場合は、運用コンテンツ (ファイル/コードおよびデータベース) をローカル環境とステージング環境に対して同期します。
5. ローカル環境でアプリケーションを開発します。
6. 運用 Web アプリをメンテナンス モードまたはロック モードに設定し、運用環境のデータベース コンテンツをステージング環境と開発環境に同期します。
7. ステージング環境にデプロイし、テストします。
8. 運用環境にデプロイします。
9. 手順 4 ～ 6 を繰り返します。

### <a name="umbraco"></a>Umbraco
このセクションでは、Umbraco CMS でカスタム モジュールを使用して、複数の DevOps 環境間でデプロイを行う方法について説明します。 この例では、複数の開発環境を管理するための別のアプローチを紹介します。

[Umbraco CMS](http://umbraco.com/) は、多くの開発者によって使用されている一般的な .NET CMS ソリューションです。 [Courier2](http://umbraco.com/products/more-add-ons/courier-2) モジュールを使用して、開発環境、ステージング環境、および運用環境でのデプロイを実行できます。 Umbraco CMS Web アプリ用のローカル開発環境は、Visual Studio または WebMatrix を使用して簡単に作成できます。

- [Create an Umbraco web app with Visual Studio](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget) (Visual Studio で Umbraco Web アプリを作成する)
- [Create an Umbraco web app with WebMatrix](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/) (WebMatrix で Umbraco Web アプリを作成する)

アプリケーションの `install` フォルダーは必ず削除してください。また、このフォルダーをステージ Web アプリや運用 Web アプリにアップロードすることは絶対にしないでください。 このチュートリアルでは、WebMatrix を使用します。

#### <a name="set-up-a-staging-environment"></a>ステージング環境のセットアップ
1. Umbraco CMS の Web アプリが準備できている場合は、前述の要領で Umbraco CMS Web アプリ用のデプロイメント スロットを作成します。 そうでない場合は、Marketplace から Web アプリを作成できます。
2. ステージ デプロイメント スロットの接続文字列を更新して、新しい **umbraco-stage-db** データベースを指すようにします。 運用 Web アプリ (umbraositecms-1) と ステージング Web アプリ (umbracositecms-1-stage) は、それぞれ別のデータベースを指す*必要があります*。

    ![新しいステージング データベースでステージング Web アプリの接続文字列を更新する](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. デプロイメント スロット **stage** の **[発行設定の取得]** をクリックします。 このプロセスにより、発行設定ファイルがダウンロードされます。このファイルには、Visual Studio や WebMatrix がアプリケーションをローカル開発 Web アプリから Azure Web アプリへ発行する際にに必要となる、すべての情報が格納されています。

    ![ステージング Web アプリの発行設定を取得する](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. WebMatrix または Visual Studio でローカル開発 Web アプリを開きます。 このチュートリアルでは、WebMatrix を使用します。 まずは、ステージング Web アプリの発行設定ファイルをインポートする必要があります。

    ![Web Matrix を使用して Umbraco の発行設定をインポートする](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. ダイアログ ボックスで変更を確認し、ローカル Web アプリを Azure Web アプリ (*umbracositecms-1-stage*) にデプロイします。 ファイルをステージング Web アプリに直接デプロイする場合は、`~/app_data/TEMP/` フォルダー内のファイルを除外します。これらのファイルは、ステージ Web アプリの初回起動時に再生成されます。 また、`~/app_data/umbraco.config` ファイルも除外します (これらも再生成されます)。

    ![Web Matrix での発行に関する変更を確認する](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. Umbraco ローカル Web アプリがステージング Web アプリに正常に発行されたら、ステージング Web アプリを参照し、いくつかのテストを実行して問題を排除します。

#### <a name="set-up-the-courier2-deployment-module"></a>Courier2 デプロイメント モジュールのセットアップ
[Courier2](http://umbraco.com/products/more-add-ons/courier-2) モジュールを使用すると、コンテンツ、スタイル シート、および開発モジュールを、右クリック操作だけでステージング Web アプリから運用 Web アプリにプッシュできます。 このプロセスにより、更新プログラムのデプロイ時に運用 Web アプリで障害が発生するリスクを軽減できます。
Courier2 のライセンスは、`*.azurewebsites.net` ドメインとカスタム ドメイン (たとえば http://abc.com) に対して購入できます。 ライセンスを購入したら、ダウンロードしたライセンス (.LIC file) を `bin` フォルダーに配置します。

![Bin フォルダーの下にライセンス ファイルをドロップする](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Courier2 パッケージをダウンロード](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/)します。 ステージ Web アプリ (たとえば http://umbracocms-site-stage.azurewebsites.net/umbraco) にサインインし、**[開発者]** メニューをクリックして、**[パッケージ]**  >  **[Install local package (ローカル パッケージのインストール)]** をクリックします。

    ![Umbraco パッケージ インストーラー](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. インストーラーを使用して Courier2 パッケージをアップロードします。

    ![Courier モジュールのパッケージをアップロードする](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. パッケージを構成するには、Web アプリの **Config** フォルダーにある courier.config ファイルを更新する必要があります。

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. `<repositories>`で、運用サイトの URL とユーザー情報を入力します。
    既定の Umbraco メンバーシップ プロバイダーを使用している場合は、&lt;[ユーザー]&gt; セクションに管理ユーザーの ID を追加します。
    カスタムの Umbraco メンバーシップ プロバイダーを使用している場合は、Courier2 モジュールで `<login>` と `<password>` を使用して、運用サイトに接続します。
    詳細については、[Courier2 モジュールのドキュメントを確認](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation)してください。

5. 同様に、運用サイトで Courier2 モジュールをインストールし、下記のように、それぞれの courier.config ファイルでステージ Web アプリを指すよう構成します。

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Umbraco CMS Web アプリのダッシュボードで **[Courier2]** タブをクリックし、**[場所]** をクリックします。 `courier.config`で説明したように、リポジトリ名を確認する必要があります。 このプロセスを、運用 Web アプリとステージング Web アプリの両方で行います。

    ![宛先 Web アプリ リポジトリを表示する](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. ステージング サイトからを運用サイトにコンテンツをデプロイするには、**[コンテンツ]** に移動し、既存のページを選択するか、新しいページを作成します。 この例では、Web アプリから既存のページ (ページのタイトルは **Getting Started – new**) を選択し、**[保存して発行]** をクリックします。

    ![ページのタイトルを変更して公開する](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. 変更されたページを右クリックして、すべてのオプションを表示します。 **[Courier]** をクリックして、**[デプロイメント]** ダイアログ ボックスを開きます。 **[デプロイ]** をクリックしてデプロイを開始します。

    ![Courier モジュールのデプロイメント ダイアログ ボックス](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. 変更を確認し、**[続行]** をクリックします。

    ![Courier モジュールのデプロイメント ダイアログ ボックスの確認対象の変更](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    デプロイメント ログに、デプロイが正常に完了したかどうかが示されます。

     ![Courier モジュールからのデプロイメント ログを表示する](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. 運用 Web アプリを参照して、変更が反映されているかどうかを確認します。

     ![運用 Web アプリを参照する](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Courier の詳細な使用方法については、ドキュメントを参照してください。

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Umbraco CMS バージョンのアップグレード方法
Courier には、Umbraco CMS のバージョン アップグレードを支援する機能はありません。 Umbraco CMS のバージョンをアップグレードする場合は、カスタム モジュールやサード パーティ モジュールとの互換性、および Umbraco のコア ライブラリとの互換性に問題がないことを確認する必要があります。 ベスト プラクティスを次に示します。

* アップグレードの前に、Web アプリとデータベースを必ずバックアップしてください。 Azure の Web アプリでは、バックアップ機能を使用して Web サイトの自動バックアップをセットアップできます。また、必要な場合は復元機能を使用してサイトを復元することもできます。 詳細については、「[Web アプリのバックアップ方法](web-sites-backup.md)」および「[Web アプリの復元方法](web-sites-restore.md)」を参照してください。
* パートナーのパッケージが、アップグレード後のバージョンと互換性があるかどうかを確認します。 パッケージのダウンロード ページで、プロジェクトが Umbraco CMS のバージョンと互換性があることを確認します。

Web アプリをローカルでアップグレードする方法について詳しくは、[一般的なアップグレード ガイダンス](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general)をご覧ください。

ローカル開発サイトがアップグレードされたら、変更内容をステージング Web アプリに発行します。 その後、アプリケーションをテストします。 結果がすべてが良好であれば、**[スワップ]** ボタンをクリックして、ステージング サイトを運用 Web アプリにスワップします。 **スワップ**操作を行う際には、Web アプリの構成で影響を受ける変更を確認できます。 この**スワップ**操作では、Web アプリとデータベースがスワップされます。 **スワップ後**、運用 Web アプリは umbraco-stage-db データベースを指すようになり、ステージング Web アプリは umbraco-prod-db データベースを指すようになります。

![Umbraco CMS をデプロイするためのスワップ プレビュー](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

次に示すのは、Web アプリとデータベースの両方をスワップすることのメリットです。

* アプリケーションに問題がある場合には、再度**スワップ**操作を行うことで、Web アプリの前バージョンにロールバックできます。
* アップグレードにあたっては、ステージング Web アプリのファイルとデータベースを、運用環境の Web アプリとデータベースにデプロイする必要があります。 ファイルやデータベースをデプロイする際には、問題が多数発生することがあります。 スロットの**スワップ**機能を使用すると、アップグレード中のダウンタイムを減らし、変更のデプロイ時に障害が発生するリスクを軽減することができます。
* [運用環境でのテスト](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)機能を使用して、**A/B テスト**を行うことができます。

この例では、Umbraco Courier モジュールに類似したカスタム モジュールを作成して複数の環境でデプロイメントを管理する場合のプラットフォームの柔軟性について示しています。

## <a name="references"></a>参照
[Azure App Service を使用したアジャイル ソフトウェア開発](app-service-agile-software-development.md)

[Azure App Service の Web アプリのステージング環境を設定する](web-sites-staged-publishing.md)

[運用環境以外のデプロイメント スロットへの Web アクセスを禁止する方法](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)



<!--HONumber=Dec16_HO3-->


