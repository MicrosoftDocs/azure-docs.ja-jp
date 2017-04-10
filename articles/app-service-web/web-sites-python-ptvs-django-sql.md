---
title: "Python Tools 2.2 for Visual Studio を使用した Azure 上の Django と SQL Database"
description: "Python Tools for Visual Studio を使用して、SQL database インスタンスにデータを保存する Django Web アプリを作成し、Azure App Service Web Apps にデプロイする方法について説明します。"
services: app-service\web
tags: python
documentationcenter: python
author: huguesv
manager: erikre
editor: 
ms.assetid: 3a677e64-b5a9-4d43-b9c0-66246368b483
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c5e01ef322f72eb5a704fd2273194315bf1a9f6f
ms.lasthandoff: 01/20/2017


---
# <a name="django-and-sql-database-on-azure-with-python-tools-22-for-visual-studio"></a>Python Tools 2.2 for Visual Studio を使用した Azure 上の Django と SQL Database
このチュートリアルでは、 [Python Tools for Visual Studio] のサンプル テンプレートを使用して、単純な投票 Web アプリを作成します。 このチュートリアルは、 [ビデオ](https://www.youtube.com/watch?v=ZwcoGcIeHF4)でもご覧いただけます。

ここでは、Azure 上でホストされる SQL Database を使用する方法、Web アプリが SQL Database を使用するよう構成する方法、および [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)に Web アプリを発行する方法を学習します。

Bottle、Flask、Django の各 Web フレームワークと、Azure Table Storage、MySQL、SQL Database の各サービスを使用した、PTVS での Azure App Service Web Apps の開発を取り上げたその他の記事については、「 [Python デベロッパー センター] 」をご覧ください。 この記事では App Service について重点的に説明していますが、 [Azure Cloud Services]の開発も同様の手順で行います。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2015
* [Python 2.7 (32 ビット)]
* [Python Tools 2.2 for Visual Studio]
* [Python Tools 2.2 for Visual Studio サンプル VSIX]
* [Azure SDK Tools for VS 2015]
* Django 1.9 以降

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
>
>

## <a name="create-the-project"></a>プロジェクトを作成する
このセクションでは、サンプル テンプレートを使用して Visual Studio プロジェクトを作成します。 仮想環境を作成し、必要なパッケージをインストールします。 sqlite を使用して、ローカル データベースを作成します。 Web アプリをローカルで実行します。

1. Visual Studio で、**[ファイル]****[新しいプロジェクト]** の順に選択します。
2. [Python Tools 2.2 for Visual Studio サンプル VSIX] のプロジェクト テンプレートは、**[Python]** の **[サンプル]** にあります。 **[投票 Django Web プロジェクト]** を選択し、[OK] をクリックしてプロジェクトを作成します。

     ![[新しいプロジェクト] ダイアログ](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)
3. 外部のパッケージをインストールするよう求めるメッセージが表示されます。 **[仮想環境にインストールする]**を選択します。

     ![External Packages ダイアログ](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)
4. 基になるインタープリターとして **Python 2.7** を選択します。

     ![Add Virtual Environment ダイアログ](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)
5. **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックして **[Python]** を選択し、**[Django Migrate (Django 移行)]** を選択します。  **[Django Create Superuser (Django でスーパーユーザーを作成する)]**を選択します。
6. これにより、Django Management Console が開き、プロジェクト フォルダーに SQLite データベースが作成されます。 プロンプトに従ってユーザーを作成します。
7. <kbd>F5</kbd> キーを押して、アプリケーションが動作することを確認します。
8. 上部にあるナビゲーション バーで、 **[ログイン]** をクリックします。

     ![Web ブラウザー](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)
9. データベースを同期したときに作成したユーザーの資格情報を入力します。

     ![Web ブラウザー](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)
10. **[サンプル投票の作成]**をクリックします。

      ![Web ブラウザー](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)
11. 投票をクリックして投票します。

      ![Web ブラウザー](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-sql-database"></a>SQL データベースの作成
データベースには、Azure SQL データベースを作成します。

データベースを作成するには、次のステップを実行します

1. [Azure ポータル]にログインします。
2. ナビゲーション ウィンドウの下部にある **[新規]**をクリックします。 **[データ + ストレージ]** > **SQL Database** をクリックします。
3. 新しいリソース グループを作成することで、新しい SQL Database を構成し、適切な場所を選択します。
4. SQL Database が作成されたら、データベース ブレードの **[Visual Studio で開く]** をクリックします。
5. **[Configure your firewall]**をクリックします。
6. **[ファイアウォールの設定]** ブレードで、**開始 IP** と **終了 IP** が開発用コンピューターのパブリック IP アドレスに設定されるファイアウォール規則を追加します。 **[保存]**をクリックします。

   これで、開発用のコンピューターからデータベース サーバーに接続できるようになります。
7. データベース ブレードに戻り、**[プロパティ]** をクリックし、**[データベース接続文字列の表示]** をクリックします。
8. [コピー] ボタンを使用して、 **ADO.NET** の値をクリップボードにコピーします。

## <a name="configure-the-project"></a>プロジェクトを構成する
このセクションでは、先ほど作成した SQL Database を使用するよう Web アプリを構成します。 Django と SQL データベースを使用するために必要な追加の Python パッケージもインストールします。 Web アプリをローカルで実行します。

1. Visual Studio で、 **ProjectName**フォルダーから *settings.py* を開きます。 エディターで、接続文字列を一時的に貼り付けます。 この接続文字列の形式を次に示します。

       Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

`DATABASES` の定義を編集して、上記の値を使用します。

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1. ソリューション エクスプローラーで、**[Python 環境]** の下にある仮想環境を右クリックし、**[Python パッケージのインストール]** を選択します。
2. **pip** を使用して `pyodbc` パッケージをインストールします。

     ![Install Python Package ダイアログ](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)
3. **pip** を使用して `django-pyodbc-azure` パッケージをインストールします。

     ![Install Python Package ダイアログ](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)
4. **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックして **[Python]** を選択し、**[Django Migrate (Django 移行)]** を選択します。  **[Django Create Superuser (Django でスーパーユーザーを作成する)]**を選択します。

   前のセクションで作成した SQL Database のテーブルが作成されます。 プロンプトに従ってユーザーを作成してください。最初のセクションで作成した sqlite データベースのユーザーと一致させる必要はありません。
5. `F5`キーでアプリケーションを実行します。 **[サンプル投票の作成]** で作成された投票と、投票によって送信されたデータは、SQL database でシリアル化されます。

## <a name="publish-the-web-app-to-azure-app-service"></a>Web アプリを Azure App Service に発行する
Azure .NET SDK を使用すると、Web アプリを  Azure App Service Web Apps に簡単にデプロイできます。

1. **ソリューション エクスプローラー**で、プロジェクト ノードを右クリックし、**[発行]** をクリックします。

     ![Publish Web ダイアログ](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)
2. **[Microsoft Azure Web Apps]**をクリックします。
3. **[新規]** をクリックして、新しい Web アプリを作成します。
4. 次のフィールドに入力し、 **[作成]**をクリックします。

   * **[Web アプリケーション名]**
   * **[App Service プラン]**
   * **[リソース グループ]**
   * **[リージョン]**
   * **[データベース サーバー]** は、**[データベースなし]** のままにしておきます。
5. 他のすべての既定値をそのまま使用し、 **[発行]**をクリックします。
6. Web ブラウザーが自動的に開いて、発行した Web アプリが表示されます。 想定どおりに Web アプリ が Azure でホストされている **SQL** データベースを使用して動作していることが確認できます。

   ご利用ありがとうございます。

     ![Web ブラウザー](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## <a name="next-steps"></a>次のステップ
Python Tools for Visual Studio、Django および SQL Database の詳細については、以下のリンクを参照してください。

* [Python Tools for Visual Studio のドキュメント]
  * [Web プロジェクト]
  * [クラウド サービス プロジェクト]
  * [Microsoft Azure でのリモート デバッグ]
* [Django のドキュメント]
* [SQL Database]

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Python デベロッパー センター]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->
[Azure ポータル]: https://portal.azure.com
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 for Visual Studio サンプル VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python Tools for Visual Studio のドキュメント]: http://aka.ms/ptvsdocs
[Microsoft Azure でのリモート デバッグ]: http://go.microsoft.com/fwlink/?LinkId=624026
[Web プロジェクト]: http://go.microsoft.com/fwlink/?LinkId=624027
[クラウド サービス プロジェクト]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django のドキュメント]: https://www.djangoproject.com/
[SQL Database]: /documentation/services/sql-database/

