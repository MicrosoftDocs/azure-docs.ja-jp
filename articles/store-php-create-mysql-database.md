---
title: "Azure で MySQL データベースを作成して接続する"
description: "Azure ポータルを使用して MySQL データベースを作成し、Azure の PHP Web アプリからそのデータベースに接続する方法について説明します。"
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5f828f90c73f5fd0356fde4e71c11d4d27ec794


---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Azure で MySQL データベースを作成して接続する
このチュートリアルでは、[Azure Portal](https://portal.azure.com) (プロバイダーは [ClearDB](http://www.cleardb.com/)) で MySQL データベースを作成する方法と、[Azure App Service](app-service/app-service-value-prop-what-is.md) で実行されている PHP Web アプリからそのデータベースに接続する方法について説明します。 

> [!NOTE]
> MySQL データベースは、 [Marketplace アプリ テンプレート](app-service-web/app-service-web-create-web-app-from-marketplace.md)の一部として作成することもできます。
> 
> 

## <a name="create-a-mysql-database-in-azure-portal"></a>Azure ポータルで MySQL データベースを作成する
Azure ポータルで MySQL データベースを作成するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 左側のメニューで、**[新規]** > **[データ + ストレージ]** > **[MySQL データベース]** の順にクリックします。
   
    ![Create a MySQL database in Azure - start](./media/store-php-create-mysql-database/create-db-1-start.png)
3. [新しい MySQL データベース] [ブレード](azure-portal-overview.md)で、次のように新しい MySQL データベースを構成します (*ブレード*: 横並びで表示されるポータル ページ)。
   
   * **[データベース名]**: 一意に識別できる名前を入力します。
   * **[サブスクリプション]**: 使用するサブスクリプションを選択します。
   * **[データベースの種類]**: 低コストまたは無料のレベルの場合は **[共有]** を選択し、専用リソースを取得する場合は **[専用]** を選択します。 
   * **[リソース グループ]**: MySQL データベースを既存の [リソース グループ](azure-resource-manager/resource-group-overview.md) に追加するか、新しいリソース グループに配置します。 同じグループ内のリソースは、まとめて簡単に管理できます。
   * **[場所]**: 近くの場所を選択します。 既存のリソース グループに追加する場合は、リソース グループの場所に固定されます。
   * **[価格レベル]**: **[価格レベル]** をクリックし、価格オプション (**Mercury** レベルは無料) を選択して、**[選択]** をクリックします。 
   * **[法律条項]**: **[法律条項]** をクリックし、購入の詳細を確認して、**[購入]** をクリックします。
   * **[ダッシュボードにピン留めする]**: ダッシュボードから直接アクセスする場合に選択します。 これは、ポータルのナビゲーションにまだ慣れていない場合に特に便利です。
     
     次のスクリーンショットは、MySQL データベースの構成方法のほんの一例です。  
     ![Create a MySQL database in Azure - configure](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. 構成が完了したら、 **[作成]**をクリックします。
   
    ![Create a MySQL database in Azure - create](./media/store-php-create-mysql-database/create-db-3-create.png)
   
    デプロイが開始されたことを知らせるポップアップ通知が表示されます。
   
    ![Create a MySQL database in Azure - in progress](./media/store-php-create-mysql-database/create-db-4-started-status.png)
   
    デプロイに成功すると、別のポップアップが表示されます。 ポータルでは、MySQL データベースのブレードも自動的に開かれます。

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>MySQL データベースに接続する
新しい MySQL データベースの接続情報を表示するには、Web アプリのブレードで **[プロパティ]** をクリックするだけです。

![Create a MySQL database in Azure - MySQL database blade](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

これで、どの Web アプリでもその接続情報を使用できるようになりました。 単純な PHP アプリから接続情報を使用する方法を示すサンプルは、 [こちら](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)で入手できます。

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Laravel Web アプリの接続 (PHP の入門チュートリアルから)
「[Azure への PHP Web アプリの作成、構成、デプロイ](app-service-web/app-service-web-php-get-started.md)」のチュートリアルを完了したばかりで、Azure で [Laravel](https://www.laravel.com/) Web アプリを実行しているとします。 その場合、Laravel アプリにデータベース機能を簡単に追加できます。 以下の手順に従ってください。

> [!NOTE]
> 以下の手順では、「 [Azure への PHP Web アプリの作成、構成、デプロイ](app-service-web/app-service-web-php-get-started.md)」のチュートリアルが完了していることを前提としています。
> 
> 

1. ローカル開発環境で、MySQL データベースを参照するように Laravel アプリを構成します。 そのためには、Laravel アプリのルート ディレクトリの `.env` を開き、MySQL データベース オプションを構成します。
   
        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>
   
   > [!NOTE]
   > **[プロパティ]** ブレードでは、MySQL データベースの名前が **[データベース名]** フィールドに表示されている場合と表示されていない場合があります。 **[接続文字列]** フィールドで Database パラメーターを確認することをお勧めします。    
   > 
   > ![Create a MySQL database in Azure - in progress](./media/store-php-create-mysql-database/connect-db-1-database-name.png)
   > 
   > 
2. 現時点で MySQL にアクセスできることを確認するための最も簡単な方法は、 [Laravel の既定の認証スキャフォールディング](https://laravel.com/docs/5.2/authentication#authentication-quickstart)を使用することです。 
   コマンド ライン ターミナルで、Laravel アプリのルート ディレクトリから以下のコマンドを実行します。
   
         php artisan migrate
         php artisan make:auth
   
    最初のコマンドは `database/migrations` ディレクトリの定義済みの移行に基づいて Azure にテーブルを作成し、2 番目のコマンドはユーザー登録と認証用の基本的なビューとルートをスキャフォールディングします。
3. ここで開発サーバーを実行します。
   
        php artisan serve
4. ブラウザーで http://localhost:8000 に移動し、次のように新しいユーザーを登録します。
   
    ![Connect to MySQL database in Azure - register user](./media/store-php-create-mysql-database/connect-db-2-development-server.png)
   
    UI の指示に従って、登録を完了します。 登録が完了すると、ログイン状態になります。
   
    ![Connect to MySQL database in Azure - register user](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)
   
    これで、Azure の MySQL データベースに対してアプリを開発できるようになりました。
5. ここでは、単に `.env` 設定を Azure Web アプリにレプリケートする必要があります。 以下の Azure CLI コマンドを実行します。
   
        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>
   
    これがどのように動作するかについては、「 [Azure Web アプリの構成](app-service-web/app-service-web-php-get-started.md#configure)」を参照してください。
6. 次に、以前 `php artisan make:auth`の実行中に行ったローカルの変更をコミットして Azure にプッシュします。
   
        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master
7. Azure Web アプリに移動します。
   
        azure site browse
8. 前に作成したユーザー資格情報を使用して、ログインします。
   
    ![Connect to MySQL database in Azure - browse to Azure web app](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)
   
    ログインすると、わかりやすいログイン後の画面が表示されます。
   
    ![Connect to MySQL database in Azure - logged in](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)
   
    これで、Azure の PHP Web アプリが MySQL データベースのデータにアクセスできるようになりました。 

## <a name="next-steps"></a>次のステップ
詳細については、 [PHP デベロッパー センター](/develop/php/)を参照してください。




<!--HONumber=Nov16_HO3-->


