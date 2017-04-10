---
title: "Memcache プロトコルを経由して App Service Web アプリを Redis に接続する - Azure | Microsoft Docs"
description: "Memcache プロトコルを使用して Azure App Service の Web アプリを Redis Cache に接続する"
services: app-service\web
documentationcenter: php
author: SyntaxC4
manager: erikre
editor: riande
ms.assetid: 0fcdf9fa-2995-4839-ba4d-cfa389c4ba06
ms.service: app-service-web
ms.devlang: php
ms.topic: get-started-article
ms.tgt_pltfrm: windows
ms.workload: na
ms.date: 02/29/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 550b4154f0fe510cb37ec4f8bdef251b06f0b495
ms.openlocfilehash: 4d867f80cfbe5360962550fbda0091f88784b4af
ms.lasthandoff: 01/27/2017


---
# <a name="connect-a-web-app-in-azure-app-service-to-redis-cache-via-the-memcache-protocol"></a>Memcache プロトコルを経由して Azure App Service の Web アプリを Redis Cache に接続する
この記事では、[Memcache][13] プロトコルを使用して [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の WordPress Web アプリを [Azure Redis Cache][12] に接続する方法について説明します。 メモリ内のキャッシュに Memcached サーバーを使用する Web アプリがすでにある場合、Azure App Service に移行して、アプリケーション コードへの変更がほとんど、もしくはまったくない状態で Microsoft Azure でファースト パーティーのキャッシュ ソリューションが使用できます。 さらに、.NET、PHP、Node.js、Java、Python などの一般的なアプリケーション フレームワークを使用しながら、既存の Memcache の知識を利用してメモリ内のキャッシュ用に Azure Redis Cache を使い、Azure App Service で拡張性の高い分散型アプリを作成できます。  

App Service Web Apps により、Web Apps の Memcache shim でこのアプリケーション シナリオが有効になります。Memcache shim はローカルの Memcached サーバーで、Azure Redis Cache への呼び出しをキャッシュする Memcache プロキシの役割を果たします。 これにより、Memcache プロトコルを使用して通信するアプリでは Redis Cache を使用してデータをキャッシュできるようになります。 この Memcache shim はプロトコル レベルで動作するので、Memcache プロトコルを使用して通信する限り、あらゆるアプリケーションやアプリケーション フレームワークで使用できます。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="prerequisites"></a>前提条件
Web Apps の Memcache shim は、Memcache プロトコルを使用して通信することを条件に、あらゆるアプリケーションで使用できます。 この特定の例では、参照アプリケーションは、Azure Marketplace からプロビジョニングできるスケーラブルな WordPress サイトです。

次の記事で説明されている手順に従います。

* [Azure Redis Cache Service のインスタンスをプロビジョニングする][0]
* [Azure でスケーラブルな WordPress サイトをデプロイする][1]

スケーラブルな WordPress サイトをデプロイして、Redis Cache インスタンスをプロビジョニングしたら、Azure App Service Web Apps で Memcache shim を有効にできるようになります。

## <a name="enable-the-web-apps-memcache-shim"></a>Web Apps の Memcache shim を有効にする
Memcache shim を構成するためには&3; つのアプリ設定を作成する必要があります。 これは、[Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715)、[クラシック ポータル][3]、[Azure PowerShell コマンドレット][5]、[Azure コマンド ライン インターフェイス][5]などのさまざまな手法を使用して実行できます。 この記事では、[Azure Portal][4] を使用してアプリ設定を行います。 次の値は Redis Cache インスタンスの **[設定]** ブレードから取得できます。

![Azure Redis Cache の設定ブレード](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### <a name="add-redishost-app-setting"></a>REDIS_HOST アプリ設定の追加
最初に、**REDIS\_HOST** アプリ設定を作成する必要があります。 この設定では、shim がキャッシュ情報を転送する場所を設定します。 REDIS_HOST アプリ設定に必要な値は、Redis Cache インスタンスの **[プロパティ]** ブレードから取得できます。

![Azure Redis Cache のホスト名](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

アプリ設定のキーを **REDIS\_HOST** に設定し、アプリ設定の値を Redis Cache インスタンスの**ホスト名**に設定します。

![Web App AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### <a name="add-rediskey-app-setting"></a>REDIS_KEY アプリ設定の追加
次に、**REDIS\_KEY** アプリ設定を作成する必要があります。 この設定では、Redis Cache インスタンスに安全にアクセスするために必要な認証トークンを提供します。 REDIS_KEY アプリ設定に必要な値は、Redis Cache インスタンスの **[アクセス キー]** ブレードから取得できます。

![Azure Redis Cache の主キー](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

アプリ設定のキーを **REDIS\_KEY** に設定し、アプリ設定の値を Redis Cache インスタンスの **Primary Key** に設定します。

![Azure Websites のアプリ設定の REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### <a name="add-memcacheshimredisenable-app-setting"></a>MEMCACHESHIM_REDIS_ENABLE アプリ設定の追加
最後のアプリ設定は Web Apps で Memcache Shim を有効にするために使用します。REDIS_HOST と REDIS_KEY を使用して Azure Redis Cache に接続し、キャッシュの呼び出しを転送します。 アプリ設定のキーを **MEMCACHESHIM\_REDIS\_ENABLE** に設定し、値を **true** に設定します。

![Web アプリのアプリ設定の MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

3 つのアプリ設定の追加が完了したら、 **[保存]**をクリックします。

## <a name="enable-memcache-extension-for-php"></a>PHP で Memcache 拡張機能を有効にする
アプリケーションで Memcache プロトコルを使用するには、PHP (WordPress サイトの言語フレームワーク) に Memcache 拡張機能をインストールする必要があります。

### <a name="download-the-phpmemcache-extension"></a>php_memcache 拡張機能のダウンロード
[PECL][6] を参照します。 キャッシュ カテゴリで [[memcache]][7] をクリックします。 [Downloads] 列で [DLL] のリンクをクリックします。

![PHP PECL の Website](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Web Apps で有効な PHP バージョンの 非スレッド セーフ (NTS) x86 のリンクをダウンロードします (既定は PHP 5.4)。

![PHP PECL の Website の Memcache パッケージ](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### <a name="enable-the-phpmemcache-extension"></a>php_memcache 拡張機能を有効にする
ファイルをダウンロードしたら、**php\_memcache.dll** を解凍して **D:\\home\\site\\wwwroot\\bin\\ext\\** ディレクトリにアップロードします。 php_memcache.dll を Web アプリにアップロードしたら、PHP ランタイムで拡張機能を有効にする必要があります。 Azure Portal で Memcache 拡張機能を有効にするには、Web アプリの **[アプリケーション設定]** ブレードを開き、**PHP\_EXTENSIONS** のキーと **bin\\ext\\php_memcache.dll** という値を持つ新しいアプリ設定を追加します。

> [!NOTE]
> Web アプリで複数の PHP 拡張機能をロードする必要がある場合、PHP_EXTENSIONS の値は、DLL ファイルへの相対パスをコンマ区切りのリストにする必要があります。
> 
> 

![Web アプリのアプリ設定の PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

完了したら、 **[保存]**をクリックします。

## <a name="install-memcache-wordpress-plugin"></a>Memcache WordPress プラグインのインストール
> [!NOTE]
> [Memcached Object Cache プラグイン](https://wordpress.org/plugins/memcached/) を WordPress.org からダウンロードすることもできます。
> 
> 

WordPress プラグインのページで、 **[新規追加]**をクリックします。

![WordPress プラグインのページ](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

検索ボックスに「**memcached**」と入力し、**Enter** キーを押します。

![WordPress の Add New プラグイン](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

一覧で **[Memcached Object Cache]** を見つけ、**[今すぐインストール]** をクリックします。

![WordPress の Install Memcache プラグイン](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### <a name="enable-the-memcache-wordpress-plugin"></a>Memcache WordPress プラグインを有効にする
> [!NOTE]
> ブログ記事の「[How to enable a Site Extension in Web Apps (Web Apps でサイト拡張機能を有効にする方法)][8]」の手順に従って Visual Studio Team Services をインストールします。
> 
> 

`wp-config.php` ファイルで、ファイルの最後にある編集の終了コメントの上に次のコードを追加します。

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

このコードが貼り付けられると、モナコではドキュメントを自動的に保存します。

次のステップは、オブジェクト キャッシュのプラグインを有効にすることです。 これを行うには、**object-cache.php** を **wp-content/plugins/memcached** フォルダーから **wp-content** フォルダーにドラッグ アンド ドロップして、Memcache オブジェクト キャッシュ機能を有効にします。

![memcache object-cache.php プラグインの場所を指定](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

これで **object-cache.php** ファイルが **wp-content** フォルダーに格納され、Memcached オブジェクト キャッシュが有効になりました。

![memcache object-cache.php プラグインの場所を有効化](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## <a name="verify-the-memcache-object-cache-plugin-is-functioning"></a>Memcache オブジェクト キャッシュ プラグインが機能していることを確認する
Web Apps の Memcache shim を有効にするためのすべての手順が完了しました。 最後にデータが Redis Cache インスタンスに入力されていることを確認します。

### <a name="enable-the-non-ssl-port-support-in-azure-redis-cache"></a>Azure Redis Cache で非 SSL ポートのサポートを有効にする
> [!NOTE]
> この記事を書いている時点では、Redis CLI では SSL 接続がサポートされていないため、次の手順が必要になります。
> 
> 

Azure ポータルで、この Web アプリ用に作成した Redis Cache インスタンスを参照します。 キャッシュのブレードが開いたら、 **[設定]** アイコンをクリックします。

![Azure Redis Cache の [設定] ボタン](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

リストから **[アクセス ポート]** を選択します。

![Azure Redis Cache のアクセス ポート](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

**[SSL 経由のみでアクセスを許可]** で **[いいえ]** をクリックします。

![Azure Redis Cache のアクセス ポート (SSL のみ)](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

非 SSL ポートが設定されたことが確認できます。 **[保存]**をクリックします。

![Azure Redis Cache の Redis アクセス ポータル (非 SSL)](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### <a name="connect-to-azure-redis-cache-from-redis-cli"></a>redis-cli から Azure Redis Cache に接続する
> [!NOTE]
> この手順では、ｒedis が開発用コンピューターにローカルでインストールされていることを前提としています。 [こちらの手順に従って Redis をインストールします][9]。
> 
> 

任意のコマンドライン コンソールを開き、次のコマンドを入力します。

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

**&lt;hostname-for-redis-cache&gt;** を実際の xxxxx.redis.cache.windows.net というホスト名に、**&lt;primary-key-for-redis-cache&gt;** をキャッシュ用のアクセス キーにそれぞれ置き換え、**Enter** キーを押します。 CLI が Redis Cache インスタンスに接続されたら、任意の redis コマンドを発行します。 次のスクリーンショットでは、キーの一覧表示を選択しています。

![Redis-CLI 端末から Azure Redis Cache に接続](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

キーを一覧表示する呼び出しは、値を返す必要があります。 返さない場合は、Web アプリに移動してもう一度試します。

## <a name="conclusion"></a>まとめ
ご利用ありがとうございます。 これで、WordPress アプリには一元的なメモリ内キャッシュが導入され、増加するスループットをサポートできるようになりました。 Web Apps の Memcache Shim は、プログラミング言語やアプリケーション フレームワークに関係なく、あらゆる Memcache クライアントで使用できることを忘れないでください。 Web Apps の Memcache shim に関するフィードバックや質問は、[MSDN フォーラム][10]や [Stackoverflow][11] に投稿してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: /powershell/azureps-cmdlets-docs
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org

