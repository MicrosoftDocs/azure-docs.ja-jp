---
title: "Azure でのエンタープライズ クラスの WordPress | Microsoft Docs"
description: "Azure App Service にエンタープライズ クラスの WordPress サイトをホストする方法について説明します。"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 541dd7bed5a4a9e306642b2c36dd7c9d45aec690
ms.openlocfilehash: fcaff51b2897b1b0bce8b1c503adfd9425c9fbb4
ms.lasthandoff: 01/24/2017


---
# <a name="enterprise-class-wordpress-on-azure"></a>Azure でのエンタープライズ クラスの WordPress
Azure App Service は、基幹業務系の大規模な [WordPress][wordpress] サイト用に、拡張性のある、安全で使いやすい環境を提供します。 Microsoft も [Office][officeblog] ブログや [Bing][bingblog] ブログなどのエンタープライズ クラスのサイトを運営しています。 この記事では、Microsoft Azure App Service の Web Apps を使用して、大量の訪問者を処理できるエンタープライズ クラスの、クラウド ベースの WordPress サイトを確立し、維持する方法について説明しています。

## <a name="architecture-and-planning"></a>アーキテクチャと計画
WordPress インストールの基本的な要件は 2 つだけです。

* **MySQL データベース**: この要件は [Azure Marketplace の ClearDB][cdbnstore] 経由で使用できます。 また、[Windows][mysqlwindows] または [Linux][mysqllinux] のいずれかを使用して、Azure Virtual Machines で独自の MySQL インストールを管理することもできます。

  > [!NOTE]
  > ClearDB では、複数の MySQL 構成が可能です。 構成によってパフォーマンスの特性が異なります。 Azure ストアで提供されているオファリングの詳細については [Azure ストア][cdbnstore]をご覧ください。直接提供されているオファリングについては、[ClearDB の Web サイト](http://www.cleardb.com/pricing.view)をご覧ください。
  >
  >
* **PHP 5.2.4 以上**: Azure App Service は現在、[PHP バージョン 5.4、5.5、5.6][phpwebsite] を提供しています。

  > [!NOTE]
  > 最新のセキュリティ更新プログラムが適用されるよう、常に最新バージョンの PHP で実行することをお勧めします。
  >
  >

### <a name="basic-deployment"></a>基本的なデプロイ
基本要件のみを使用した場合は、Azure リージョン内に基本的なソリューションを作成できます。

![単一の Azure リージョンでホストされている Azure Web アプリと MySQL データベース][basic-diagram]

このデプロイでは、サイトの複数の Web Apps インスタンスを作成することによってアプリケーションをスケールアウトできますが、いずれも特定リージョンのデータセンターにホストされます。 このリージョン外部からアクセスすると、サイトの使用時に応答時間が長くなる場合があります。 このリージョン内のデータセンターがダウンすると、アプリケーションもダウンします。

### <a name="multi-region-deployment"></a>複数リージョンのデプロイ
Azure [Traffic Manager][trafficmanager] を使用すると、WordPress サイトを複数のリージョンに展開する一方で、アクセスするすべてのユーザーには同じ URL を提示できます。 すべてのユーザーは Traffic Manager を介してサイトにアクセスし、負荷分散構成に基づいて特定のリージョンにルーティングされます。

![Azure の Web アプリは CDBR の高可用性のルーターを使用して、地域間での MySQL にルーティングする複数の地域でホストされています。][multi-region-diagram]

各リージョン内でも、WordPress サイトは複数の Web Apps インスタンス間に展開されることがありますが、こうしたスケーリングはリージョンに固有です。 トラフィックの多いリージョンと少ないリージョンでは、異なるスケーリングを実施できます。

トラフィックを複製して複数の MySQL データベースにルーティングするには、[ClearDB の高可用性のルーター (CDBR)][cleardbscale] (左図) または [MySQL Cluster Carrier Grade Edition (CGE)][cge] を使用します。

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>メディア ストレージとキャッシュを用いる複数リージョンのデプロイ
サイトがアップロードを受け入れている場合、またはメディア ファイルをホストしている場合は、Azure Blob Storage を使用します。 キャッシュが必要な場合は、[Redis Cache][rediscache]、[Memcached Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)、または [Azure ストア](http://azure.microsoft.com/gallery/store/)で取り扱われている別のキャッシュ オファリングの使用を検討してください。

![Azure の Web アプリは、Managed Cache、Blob ストレージ、Content Delivery Network を使用した CDBR の高可用性のルーターを MySQL に使用し、複数のリージョンでホストされています。][performance-diagram]

BLOB ストレージは、既定では複数のリージョンにまたがって地理的に分散されるため、すべてのサイトにファイルをレプリケートしなくてもかまいません。 また、Blob ストレージ用に [Azure Content Delivery Network][cdn] を有効にして、ユーザーに近いエンド ノードへファイルを配信することもできます。

### <a name="planning"></a>計画
#### <a name="additional-requirements"></a>その他の要件
| 目的 | 方法 |
| --- | --- |
| **大きいファイルのアップロードまたは保存** |[WordPress plugin for using Blob storage (BLOB ストレージを使うための WordPress プラグイン)][storageplugin] |
| **電子メールの送信** |[SendGrid][storesendgrid]、[SendGrid を使うための WordPress プラグイン][sendgridplugin]についての説明 |
| **カスタム ドメイン名** |[Azure App Service のカスタム ドメイン名の構成][customdomain] |
| **HTTPS** |[Web アプリに対する HTTPS を Azure App Service で有効にする][httpscustomdomain] |
| **運用前検証** |[Azure App Service の Web アプリのステージング環境を設定する][staging] <p>Web アプリをステージングから運用に切り替えると、WordPress の構成も変化します。 ステージング中のアプリを運用段階に切り替える前に、すべての設定が本番アプリの要件に合わせて更新されていることを確認する必要があります。</p> |
| **監視とトラブルシューティング** |[Azure App Service で Web アプリの診断のログ記録を有効にし][log]、[Azure App Service で Web Apps を監視する][monitor] |
| **サイトのデプロイ** |[Azure App Service での Web アプリのデプロイ][deploy] |

#### <a name="availability-and-disaster-recovery"></a>高可用性と障害復旧
| 目的 | 方法 |
| --- | --- |
| **サイトの負荷分散**、**サイトの地理的な分散** |[Azure Traffic Manager によるトラフィックのルーティング][trafficmanager] |
| **バックアップおよび復元** |[Azure App Service での Web アプリのバックアップ][backup]と [Azure App Service での Web アプリの復元][restore] |

#### <a name="performance"></a>パフォーマンス
クラウドのパフォーマンスは、主にキャッシュとスケール アウトに依存します。 ただし、Web Apps ホスティングのメモリ、帯域幅、その他の属性も考慮する必要があります。

| 目的 | 方法 |
| --- | --- |
| **App Service インスタンス機能について学ぶ** |[App Service 階層 の機能と料金の詳細][websitepricing] |
| **キャッシュ リソース** |[Redis Cache][rediscache]、[Memcached Cloud](/gallery/store/garantiadata/memcached/)、[MemCachier](/gallery/store/memcachier/memcachier/)、または [Azure ストア](/gallery/store/)で取り扱われている別のキャッシュ オファリング |
| **アプリケーションのスケール** |[Azure App Service で Web アプリ][websitescale]と [ClearDB の高可用性ルーティング][cleardbscale]をスケールします。 独自の MySQL インストールをホストし、管理する場合は、[MySQL Cluster CGE][cge] によるスケールアウトを検討してください。 |

#### <a name="migration"></a>移行
既存の WordPress サイトを Azure App Service に移行する方法は、次の 2 種類です。

* **[WordPress のエクスポート][export]**: この方法を使用すると、ブログのコンテンツがエクスポートされます。 Azure App Service の新しい WordPress サイトにコンテンツをインポートする場合は、[WordPress Importer プラグイン][import]を使用します。

  > [!NOTE]
  > このプロセスではブログのコンテンツを移行できますが、プラグインやテーマ、その他のカスタマイズ内容は移行できません。 これらのコンポーネントは再度手動でインストールする必要があります。
  >
  >
* **手動による移行**: [サイト][wordpressbackup]と[データベースをバックアップ][wordpressdbbackup]し、それを Azure App Service の Web アプリや関連する MySQL データベースに手動で復元します。 このメソッドでは、プラグイン、テーマ、その他のカスタマイズ内容を手動でインストールする手間が省けるため、高度にカスタマイズされたサイトを移行する際に便利です。

## <a name="step-by-step-instructions"></a>詳細な手順
### <a name="create-a-wordpress-site"></a>WordPress サイトの作成
1. [Azure Marketplace][cdbnstore] を使用して、サイトをホストするリージョンに「[アーキテクチャと計画](#planning)」セクションで特定したサイズの MySQL データベースを作成します。
2. 「[Azure App Service での WordPress Web アプリの作成createwordpress][createwordpress]」の手順に従って、WordPress Web アプリを作成します。 Web アプリを作成する場合は、**[既存の MySQL データベースを使用します]** を選択し、手順 1 で作成したデータベースを選択します。

既存の WordPress サイトを移行する場合は、新しい Web アプリを作成した後、「[既存の WordPress サイトを Azure に移行する](#Migrate-an-existing-WordPress-site-to-Azure)」をご覧ください。

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>既存の WordPress サイトを Azure に移行する
「[アーキテクチャと計画](#planning)」セクションで説明したように、WordPress サイトは 2 つの方法で移行できます。

* **エクスポートとインポート** - カスタマイズの量が多くないサイトを移行する場合や、コンテンツだけを移行する場合に実行します。
* **バックアップと復元** - 数多くのカスタマイズが施されたサイトを丸ごと移行する場合に実行します。

以下のセクションのいずれかに従って、サイトを移行します。

#### <a name="the-export-and-import-method"></a>エクスポートとインポートによる移行
1. [WordPress][export] のエクスポートを実行して、既存のサイトをエクスポートします。
2. 「[WordPress サイトの作成](#Create-a-new-WordPress-site)」セクションの手順に従って、Web アプリを作成します。
3. [Azure Portal][mgmtportal] の WordPress サイトにサインインし、**[プラグイン]** > **[新規追加]** をクリックします。 **WordPress Importer** プラグインを検索し、インストールします。
4. WordPress Importer プラグインをインストールしたら、**[ツール]** > **[インポート]** の順にクリックして **WordPress** をクリックし、WordPress Importer プラグインを使用します。
5. **[WordPress のインポート]** ページで、**[ファイルの選択]** を選択します。 既存の WordPress サイトからエクスポートした WXR ファイルを探して、**[ファイルのアップロードとインポート]** をクリックします。
6. **[送信]**をクリックします。 インポートに成功したことを示すメッセージが表示されます。
7. これらのすべての手順が完了したら、[Azure Portal][mgmtportal] の **App Services** ブレードからサイトを再起動します。

サイトをインポートしたら、以下の手順を実行して、インポート ファイルに含まれない設定を有効にすることが必要になる場合があります。

| 使用している要素 | これを行うには、次の手順を実行します。 |
| --- | --- |
| **固定リンク** |新しいサイトの WordPress ダッシュボードで、**[設定]** > **[固定リンク]** の順にクリックし、固定リンクの構造を更新します。 |
| **イメージまたはメディア リンク** |リンク先を新しい場所に更新するには、検索置換ツールの [Velvet Blues Update URLs プラグイン][velvet]を使用するか、データベース内のリンクを直接修正します。 |
| **テーマ** |**[外観]** > **[テーマ]** の順に移動し、必要に応じてサイトのテーマを更新します。 |
| **メニュー** |使用しているテーマがメニューをサポートしている場合は、ホーム ページへのリンクに使用していないサブディレクトリが残っていることがあります。 **[外観]** > **[メニュー]** の順に移動して、更新します。 |

#### <a name="the-backup-and-restore-method"></a>バックアップと復元による移行
1. 「[WordPress Backups (WordPress のバックアップ)][wordpressbackup]」の情報を参照して、既存の WordPress サイトをバックアップします。
2. 「[Backing Up Your Database (データベースのバックアップ)][wordpressdbbackup]」の情報を参照して、既存のデータベースをバックアップします。
3. データベースを作成し、バックアップを復元します。

   1. [Azure Marketplace][cdbnstore]で新しいデータベースを購入するか、[Windows][mysqlwindows] または [Linux][mysqllinux] 仮想マシンで MySQL データベースを設定します。
   2. [MySQL Workbench][workbench] などの MySQL クライアントを使用して、新しいデータベースに接続し、WordPress データベースをインポートします。
   3. データベースを更新して、ドメイン エントリを "mywordpress.azurewebsites.net" などのように、新しい Azure App Service ドメインに変更します。 [WordPress データベースの検索と置換用スクリプト][searchandreplace]を使用すると、すべてのインスタンスを安全に変更できます。
4. Azure Portal で Web アプリを作成し、WordPress のバックアップを発行します。

   1. データベースを持つ Web アプリを作成するには、[Azure Portal][mgmtportal] で **[新規]** > **[Web + モバイル]** > **[Azure Marketplace]** > **[Web アプリ]** > **[Web アプリ + SQL]** \(または **[Web アプリ + MySQL]**) -> **[作成]** をクリックします。 必要なすべての設定を構成して、空の Web アプリを作成します。
   2. WordPress バックアップで **wp-config.php** ファイルを見つけて、エディターで開きます。 以下のエントリを新しい MySQL データベースの情報に変更します。

      * **DB_NAME**: データベースのユーザー名
      * **DB_USER**: データベースへのアクセスに使用するユーザー名
      * **DB_PASSWORD**: ユーザーのパスワード

        これらのエントリを変更したら、**wp-config.php** ファイルを保存して閉じます。
   3. 「[Azure App Service での Web アプリのデプロイ][deploy]」の情報を使用して、使用したいデプロイ方法を有効にし、WordPress バックアップを Azure App Service の Web アプリにデプロイします。
5. WordPress サイトがデプロイされると、サイトの *.azurewebsite.net という URL を使用して新しいサイト (App Service Web アプリとして) アクセスできるようになります。

### <a name="configure-your-site"></a>サイトの構成
WordPress サイトを作成、移行した後は、以下の情報を参照して、パフォーマンスの向上と追加機能の有効化を行います。

| 目的 | 方法 |
| --- | --- |
| **App Service プランのモード、サイズを設定し、スケーリングを有効化する** |[Azure App Service での Web アプリの拡張][websitescale] |
| **永続的なデータベース接続を有効にする** |既定では、WordPress は永続的なデータベース接続を使用しないため、複数接続を実行すると、データベースへの接続が制限される場合があります。 永続的な接続を有効にするには、[永続的な接続アダプター プラグイン](https://wordpress.org/plugins/persistent-database-connection-updater/installation/)をインストールします。 |
| **パフォーマンスの向上** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">ARR クッキーの無効化</a> - 複数の Web Apps のインスタンスで WordPress を実行している場合に、パフォーマンスを向上させる方法です。</p></li><li><p>キャッシュを有効にする。 <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis Cache</a> (プレビュー) は、<a href="https://wordpress.org/plugins/redis-object-cache/">WordPress プラグインの Redis Object Cache</a> で使用できます。また、<a href="/gallery/store/">Azure ストア</a>から入手できる他のキャッシュ オファリングも使用できます。</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">WordPress を Wincache で高速化します</a>。 Wincache は Web アプリに対して既定で有効化されています。</p></li><li><p>[Azure App Service で Web アプリを拡張][websitescale]して <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB の高可用性ルーティング</a>または <a href="http://www.mysql.com/products/cluster/">MySQL クラスター CGE</a> を使用します。</p></li></ul> |
| **ストレージ用 BLOB の使用** |<ol><li><p>[Azure Storage アカウント](../storage/storage-create-storage-account.md)を作成します。</p></li><li><p>[コンテンツ配信ネットワークの使用](../cdn/cdn-create-new-endpoint.md)方法を理解し、BLOB を用いて地理的に分散したデータ保存を実施します。</p></li><li><p><a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress プラグイン</a>をインストールし、構成します。</p><p>このプラグインに関する詳細な設定と構成の情報については、<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">ユーザー ガイド</a>を参照してください。</p> </li></ol> |
| **電子メールの有効化** |Azure ストアで <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> を有効にします。 WordPress 用の <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">SendGrid プラグイン</a>をインストールします。 |
| **カスタム ドメイン名の構成** |[Azure App Service のカスタム ドメイン名の構成します][customdomain]。 |
| **カスタム ドメイン名に対する HTTPS の有効化** |[Web アプリに対する HTTPS を Azure App Service で有効にします][httpscustomdomain]。 |
| **サイトの負荷分散、または地理的な分散** |[Azure Traffic Manager によるトラフィックのルーティング][trafficmanager]。 カスタム ドメインを使用する場合は、カスタム ドメイン名と Traffic Manager の使用の詳細について、[Azure App Service でのカスタム ドメイン名の構成][customdomain]に関するページをご覧ください。 |
| **自動化されたバックアップを有効にする** |[Azure App Service で Web アプリをバックアップします][backup]。 |
| **診断ログの有効化** |[Azure App Service の Web アプリの診断ログを有効にします][log]。 |

## <a name="next-steps"></a>次のステップ
* [WordPress の最適化](http://codex.wordpress.org/WordPress_Optimization)
* [Azure App Service での WordPress から Multisite への変換](web-sites-php-convert-wordpress-multisite.md)
* [Azure 用の ClearDB アップグレード ウィザード](http://www.cleardb.com/store/azure/upgrade)
* [Azure App Service で Web アプリのサブフォルダーに WordPress をホストする (ブログの投稿)](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [Step-By-Step: Create a WordPress site using Azure (ステップ バイ ステップ: Azure で WordPress サイトを作成する)](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [Host your existing WordPress blog on Azure (Azure で既存の WordPress ブログをホストする)](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [Enabling pretty permalinks in WordPress (WordPress で見やすい固定リンクを有効にする)](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [How to migrate and run your WordPress blog on Azure Websites (Azure App Service の WordPress ブログを Azure Websites に移行し、運用する方法)](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [WordPress を無料の Azure App Service で実行する方法](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [WordPress on Azure in 2 minutes or less (Azure で 2 分以内に WordPress を稼動)](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [Moving a WordPress blog to Azure - Part 1: Creating a WordPress blog on Azure (WordPress ブログを Azure に移転する - パート 1: Azure で WordPress ブログを作成)](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [Moving a WordPress blog to Azure - Part 2: Transferring your content (WordPress ブログを Azure に移転する - パート 2: コンテンツの転送)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [Moving a WordPress blog to Azure - Part 3: Setting up your custom domain (WordPress ブログを Azure に移転する - パート 3: カスタム ドメインの設定)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [Moving a WordPress blog to Azure - Part 4: Pretty permalinks and URL Rewrite rules (WordPress ブログを Azure に移転する - パート 4: 見やすい固定リンクと URL 書き換えのルール)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [Moving a WordPress blog to Azure - Part 5: Moving from a subfolder to the root (WordPress ブログを Azure に移転する - パート 5: サブフォルダーからルートへの移動)](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [Azure アカウントに WordPress Web アプリを設定する方法](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [Propping up WordPress on Azure (Azure で WordPress を設置する)](http://www.johnpapa.net/wordpress-on-azure/)
* [Tips for WordPress on Azure (Azure で WordPress を運用するヒント)](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページにアクセスすると、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 試用にあたり、クレジット カードや契約は必要ありません。
>
>

## <a name="whats-changed"></a>変更内容
Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」をご覧ください。

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

