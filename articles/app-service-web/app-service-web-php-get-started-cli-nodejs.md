---
title: "Azure への PHP Web アプリの作成、構成、デプロイ | Microsoft Docs"
description: "Azure App Service で動作する PHP (Laravel) Web アプリの作成方法をわかりやすく説明しています。 ご利用の PHP フレームワークの要件を満たすように Azure App Service を構成する方法について説明します。"
services: app-service\web
documentationcenter: php
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: cb73859d-48aa-470a-b486-d984746d6d26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: fe1143a2c6966a547caab6b9bd35d78a91446e34
ms.lasthandoff: 03/21/2017


---
# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>Azure への PHP Web アプリの作成、構成、デプロイ
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

このチュートリアルでは、Azure 用に PHP Web アプリを作成、構成、デプロイする方法のほか、その PHP Web アプリの要件を満たすように Azure App Service を構成する方法について説明します。 このチュートリアルの最後には、実用的な [Laravel](https://www.laravel.com/) Web アプリが完成し、[Azure App Service](../app-service/app-service-value-prop-what-is.md) で実際に動作するようすを確認できます。

PHP 開発者の方は、お気に入りの PHP フレームワークを Azure に持ち込むことができます。 このチュートリアルでは、単にアプリの具体例として Laravel を使用しています。 学習内容: 

* Git を使用してデプロイする
* PHP バージョンを設定する
* ルート アプリケーション ディレクトリに存在しないスタート ファイルを使用する
* 環境固有の変数にアクセスする
* Azure でアプリを更新する

ここで覚えたことは、Azure にデプロイする他の PHP Web アプリにも応用することができます。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](app-service-web-php-get-started-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0](app-service-web-php-get-started.md) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="prerequisites"></a>前提条件
* [PHP 5.6.29](http://php.net/downloads.php)
* [[[Composer]]](https://getcomposer.org/download/)
* [Azure CLI](../cli-install-nodejs.md)
* [Git](http://www.git-scm.com/downloads)
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

> [!NOTE]
> Web アプリの動作を確認してください。 今すぐ [App Service を試用](https://azure.microsoft.com/try/app-service/)して、有効期間が短いスターター アプリを作成してみてください。このサービスの利用にあたり、クレジット カードや契約は必要ありません。
> 
> 

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>開発コンピューター上で PHP (Laravel) アプリを作成する
1. 新しい Windows コマンド プロンプト、PowerShell ウィンドウ、Linux のシェル、または OS X ターミナルを開きます。 必要なツールが適切にコンピューターにインストールされていることを次のコマンドを実行して確認します。 
   
        php --version
        composer --version
        azure --version
        git --version
   
    ツールをインストールしていない場合は、「 [前提条件](#Prerequisites) 」のダウンロード リンクを参照してください。

2. Laravel をインストールします。
   
        composer global require "laravel/installer"
3. `CD` で作業ディレクトリに移動し、新しい Laravel アプリケーションを作成します。
   
        cd <working_directory>
        laravel new <app_name>
4. 新しく作成した `<app_name>` ディレクトリに `CD` で移動し、アプリをテストします。
   
        cd <app_name>
        php artisan serve
   
    ブラウザーで http://localhost:8000 にアクセスすると、Laravel のスプラッシュ スクリーンが表示されます。
   
    ![Test your PHP (Laravel) app locally before deploying it to Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)

ここまでは、Laravel の標準的なワークフローです。<a href="https://laravel.com/docs/5.3" rel="nofollow">Laravel について</a>は本題から逸れるので、 これぐらいにして、次に進みましょう。

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Azure Web アプリの作成と Git デプロイの設定
> [!NOTE]
> FTP で デプロイしたいという方のために、 [FTP チュートリアル](web-sites-php-mysql-deploy-use-ftp.md)が用意されています。 
> 
> 

Azure App Service への Web アプリの作成と、Git デプロイに必要なセットアップは、Azure CLI から&1; 行のコマンドで実行できます。 以下に示したのは、そのための手順です。

1. ASM モードに変更し、Azure にログインします。
   
        azure config mode asm
        azure login
   
    ヘルプ メッセージに従って、ログイン プロセスを続行します。
   
    ![Log in to Azure to deploy your PHP (Laravel) app to Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

3. App Service のデプロイ ユーザーを設定します。 後で、資格情報を使用してコードをデプロイします。
   
        azure site deployment user set --username <username> --pass <password>

2. Git デプロイで Azure Web アプリを作成するためのコマンドを実行します。 メッセージが表示されたら、必要なリージョンの数を指定します。
   
        azure site create --git <app_name>
   
    ![Create the Azure resource for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/create-site-cli.png)
   
    このコマンドは、(`git init` で) 現在のディレクトリに新しい Git リポジトリを作成し、(`git remote add` で) Git リモートとして Azure 内のリポジトリに接続します。

<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>Azure Web アプリの構成
作成した Laravel アプリが Azure で正しく動作するためには、いくつかのことに注意する必要があります。 これと同様の作業は、すべての PHP フレームワークで必要となります。

* PHP 5.6.4 以降を構成します。 サーバーの全要件については、 [最新の Laravel 5.3 サーバーの要件](https://laravel.com/docs/5.3#server-requirements) を参照してください。 その他の要件は拡張機能に関するものであり、Azure の PHP 環境であらかじめ使用できる状態になっています。 
* アプリに必要な環境変数を設定します。 Laravel では、`.env` ファイルを使用して簡単に環境変数を設定できます。 ただし、このファイルをソース管理下に置くことは避けてください ([Laravel の環境構成](https://laravel.com/docs/5.3/configuration#environment-configuration)に関するページを参照)。Azure Web アプリに対するアプリ設定は自分で行うことになります。
* Laravel アプリのエントリ ポイント ( `public/index.php`) が最初に読み込まれることを確認します。 [Laravel のライフサイクルの概要](https://laravel.com/docs/5.3/lifecycle#lifecycle-overview)に関するページを参照してください。 つまり、Web アプリのルート URL が `public` ディレクトリを指すように設定する必要があります。
* composer.json があるので、Azure で Composer 拡張機能を有効にします。 これにより、 `git push`でのデプロイ時に必要なパッケージの入手に関連した面倒な処理を Composer に委ねることができます。 これは利便性の問題です。 
  Composer によるオートメーションを有効にしなかった場合、必要なことは、コードのコミットとデプロイ時に `vendor` ディレクトリの内容がすべて対象となる ("無視されない") ように、`/vendor` を `.gitignore` ファイルから削除するだけです。

では、これらの作業を順に行っていきます。

1. Laravel アプリに必要な PHP バージョンを設定します。
   
        azure site set --php-version 5.6
   
    これで PHP のバージョンが設定されました。 
2. Azure Web アプリ用の新しい `APP_KEY` を生成し、その Azure Web アプリ用のアプリ設定として指定します。
   
        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"
3. また、 `Whoops, looks like something went wrong.` という謎めいたページが表示されないようにするため、Laravel デバッグを有効にします。
   
        azure site appsetting add APP_DEBUG=true
   
    環境変数の設定はこれで完了です。
   
   > [!NOTE]
   > ここでひと息吐いて、Laravel と Azure がここで何をしているかについて説明します。 Laravel はルート ディレクトリにある `.env` ファイルを使用して、アプリに環境変数を渡します。`APP_DEBUG=true` や `APP_KEY=...` の行が該当します。 `config/app.php` では、`'debug' => env('APP_DEBUG', false),` というコードでこの変数にアクセスしています。 [env()](https://laravel.com/docs/5.3/helpers#method-env) は Laravel のヘルパー メソッドで、内部的には PHP の [getenv()](http://php.net/manual/en/function.getenv.php) が使われています。
   > 
   > ただし `.env` は、ルート ディレクトリの `.gitignore` ファイルによって除外指定されているため、Git によって無視されます。 つまり、ローカル Git リポジトリ内の `.env` だけは、他のファイルとは異なり、Azure にプッシュされません。 もちろん、この行を `.gitignore`から削除すればよいのですが、既に述べたように、このファイルをソース管理に追加することは推奨されません。 とはいえ Azure でこれらの環境変数を指定する手段は必要です。 
   > 
   > さいわい、Azure App Service におけるアプリ設定では、PHP の [getenv()](http://php.net/manual/en/function.getenv.php) がサポートされています。 FTP などの手段を使用して手動で `.env` ファイルを Azure にアップロードしてもかまいませんが、Azure では、`.env` を使わず単に必要な変数を Azure のアプリ設定として指定することができます。先ほど行った手順がまさにこれに該当します。 さらに、`.env` ファイルと Azure のアプリ設定との両方に同じ変数が存在した場合、Azure のアプリ設定が優先されます。     
   > 
   > 
4. 最後の&2; つのタスク (仮想ディレクトリの設定と Composer の有効化) には [Azure Portal](https://portal.azure.com) が必要となります。ご使用の Azure アカウントで [Azure Portal](https://portal.azure.com) にログインしましょう。
5. 左側のメニューから、**[App Services]**  >  **[&lt;app_name>]**  >  **[拡張機能]** の順にクリックします。
   
    ![Enable Composer for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
6. **[追加]** をクリックして拡張機能を追加します。
7. **[拡張機能の選択]** [ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)で **[Composer]** を選択します ("*ブレード*" = 横並びで表示されるポータル ページ)。
8. **[法律条項に同意する]** [ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)の **[OK]** をクリックします。 
9. **[拡張機能の追加]** [ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)の **[OK]** をクリックします。
   
    Azure で拡張機能の追加が完了すると、端の方に親切なポップアップ メッセージが表示され、**[拡張機能]** [ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)の一覧に  **Composer** が表示されます。
   
    ![Extensions blade after enabling Composer for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    Composer の有効化はこれで完了です。
10. Web アプリの[リソース ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)に戻り、**[アプリケーション設定]** をクリックします。
    
     ![Access Settings blade to set virtual directory for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     **[アプリケーション設定]** ブレードで、先ほど設定した PHP バージョンを確認します。
    
     ![PHP version in Settings blade for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)
    
     先ほど追加したアプリ設定も確認してください。
    
     ![App settings in Settings blade for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. [ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)の一番下までスクロールし、ルート仮想ディレクトリの基準となるパスを **site\wwwroot** から **site\wwwroot\public** に変更します。
    
     ![Set virtual directory for your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. [ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources)の上部にある **[保存]** をクリックします。
    
     仮想ディレクトリの設定はこれで完了です。 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>Git を使用した Web アプリのデプロイ (と環境変数の設定)
いよいよコードをデプロイする段階となりました。 デプロイ作業は、再びコマンド プロンプトまたは端末から行います。

1. Git リポジトリの場合と同様に、すべての変更をコミットし、Azure Web アプリにコードをデプロイします。
   
        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 
   
    メッセージが表示されたら、前に作成したユーザー資格情報を使用します。 

2. 次のコマンドを実行してブラウザーでの実行状態を確認してみましょう。
   
        azure site browse
   
    正常に実行されていれば、ブラウザーに Laravel のスプラッシュ スクリーンが表示されます。
   
    ![Laravel splash screen after deploying web app to Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
   
    この画面が表示されれば成功です。Laravel Web アプリが Azure で実行されています。

## <a name="troubleshoot-common-errors"></a>一般的なエラーのトラブルシューティング
ここでは、このチュートリアルで遭遇する可能性のあるエラーをいくつか取り上げます。

* [Azure CLI で "'site' is not an azure command ('サイト' は Azure コマンドではありません)" が表示される](#clierror)
* [Web アプリで HTTP 403 エラーが表示される](#http403)
* [Web アプリで "Whoops, looks like something went wrong. (問題が発生しました)" が表示される](#whoops)
* [Web アプリで "No supported encryptor found. (サポートされている暗号化機能が見つかりません)" が表示される](#encryptor)

<a name="clierror"></a>

### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>Azure CLI で "'site' is not an azure command ('サイト' は Azure コマンドではありません)" が表示される
コマンド ライン端末から `azure site *` を実行しているときに、`error:   'site' is not an azure command. See 'azure help'.` というエラーが表示されます。 

一般に、"ARM" (Azure Resource Manager) モードに切り替えた結果として、このエラーが発生します。 これを解決するには、「 `azure config mode asm`」を実行して "ASM" (Azure Service Management) モードに戻してください。

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>Web アプリで HTTP 403 エラーが表示される
Web アプリを正しく Azure にデプロイしたにもかかわらず、ブラウザーで Azure Web アプリにアクセスすると、"`HTTP 403`" や "`You do not have permission to view this directory or page.`" が表示されます。

その原因として、Laravel アプリのエントリ ポイントの検出に Web アプリが失敗していることがまず考えられます。 ルート仮想ディレクトリの基準となるパスが `site\wwwroot\public` に変更されていることを確認してください。この場所に Laravel の `index.php` が置かれます (「[Azure Web アプリの構成](#configure)」を参照)。

<a name="whoops"></a>

### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>Web アプリで "Whoops, looks like something went wrong. (問題が発生しました)" が表示される
Web アプリを正しく Azure にデプロイしたにもかかわらず、Azure Web アプリにブラウザーでアクセスすると、" `Whoops, looks like something went wrong.`

エラーの状況をもっと詳しく把握するために、`APP_DEBUG` 環境変数を `true` に設定して Laravel のデバッグを有効にします (「[Azure Web アプリの構成](#configure)」を参照)。

<a name="encryptor"></a>

### <a name="web-app-shows-no-supported-encryptor-found"></a>Web アプリで "No supported encryptor found. (サポートされている暗号化機能が見つかりません)" が表示される
Web アプリを正しく Azure にデプロイしたにもかかわらず、Azure Web アプリにブラウザーでアクセスすると、次のエラー メッセージが表示されます。

![APP_KEY missing in your PHP (Laravel) app in Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

わかりにくいエラーではありますが、Laravel のデバッグを有効にしたので、少なくとも最初の状態よりは問題を把握しやすくなりました。 Laravel のフォーラムでエラー文字列をざっと検索してみると、`.env` に APP_KEY が設定されていないことに原因があるようです。今回のケースでは、Azure には `.env` が送信されません。 この問題は、`APP_KEY` を Azure アプリ設定として追加することによって解決できます (「[Azure Web アプリの構成](#configure)」を参照)。

## <a name="next-steps"></a>次のステップ
[Azure に MySQL データベースを作成](../store-php-create-mysql-database.md)してアプリにデータを追加する方法について確認します。 また、Azure での PHP について解説された有益な情報は他にもあります。以下のリンクからアクセスしてください。

* [PHP デベロッパー センター](/develop/php/)
* [Azure Marketplace から Web アプリを作成する](app-service-web-create-web-app-from-marketplace.md)
* [Azure App Service Web Apps での PHP の構成方法](web-sites-php-configure.md)
* [Azure App Service での WordPress から Multisite への変換](web-sites-php-convert-wordpress-multisite.md)
* [Azure App Service のエンタープライズ クラスの WordPress](web-sites-php-enterprise-wordpress.md)


