---
title: "Azure での Bottle を使用した Python Web アプリ"
description: "Azure App Service Web Apps での Python Web アプリの実行について説明したチュートリアルです。"
services: app-service\web
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: 84f043b8-9d05-479f-a9d0-d0d9a32a0bb9
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: f7b54a1a6266d6728ac81e0b709febb1d6f00f9f
ms.lasthandoff: 02/16/2017


---
# <a name="creating-web-apps-with-bottle-in-azure"></a>Azure での Bottle を使用した Web アプリの作成
このチュートリアルでは、Azure App Service Web Apps で Python を実行するための基本的な方法について説明します。 Web Apps では、制限付きの無料のホスティングや迅速なデプロイを実行できます。また、Python も使用できます。 アプリケーションの拡張に合わせて、有料のホスティングに切り替えることができます。また、他のすべての Azure サービスと統合することもできます。

Bottle Web フレームワークを使用して Web アプリを作成します ([Django](web-sites-python-create-deploy-django-app.md) と [Flask](web-sites-python-create-deploy-flask-app.md) のこのチュートリアルの代替バージョンをご覧ください)。 Azure Marketplaceから Web アプリを作成し、Git デプロイを設定して、リポジトリをローカルで複製します。 その後、ローカルで Web アプリを実行し、変更を行い、それらをコミットして [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)にプッシュします。 チュートリアルでは、Windows または Mac/Linux での手順を紹介します。

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="prerequisites"></a>前提条件
* Windows、Mac、または Linux
* Python 2.7 または 3.4
* setuptools、pip、virtualenv (Python 2.7 のみ)
* Git
* [Python Tools 2.2 for Visual Studio][Python Tools 2.2 for Visual Studio] (PTVS) - 注: これはオプションです

**注**: 現在、TFS 発行は Python プロジェクトではサポートされていません。

### <a name="windows"></a>Windows
Python 2.7 または 3.4 (32 ビット) がインストールされていない場合は、Web Platform Installer を使用して [Azure SDK for Python 2.7] か [Azure SDK for Python 3.4] をインストールすることをお勧めします。 いずれかをインストールすると、Python の 32 ビット バージョン、setuptools、pip、virtualenv などがインストールされます (32 ビット Python は Azure ホスト コンピューターにインストールするものです)。 [python.org]から Python を取得することもできます。

Git には、[Git for Windows] または [GitHub for Windows] をお勧めします。 Visual Studio を使用する場合は、Git の統合のサポートを使用できます。

さらに、 [Python Tools 2.2 for Visual Studio]のインストールもお勧めします。 これはオプションですが、無料版の Visual Studio Community 2013 または Visual Studio Express 2013 for Web を含む [Visual Studio]を使用すれば、Python IDE を有効に活用できます。

### <a name="maclinux"></a>Mac/Linux
Python と Git は既にインストールされていると思いますが、Python 2.7 または 3.4 であることをご確認ください。

## <a name="web-app-creation-on-the-azure-portal"></a>Azure ポータルでの Web アプリの作成
アプリを作成する最初の手順では、 [Azure ポータル](https://portal.azure.com)で Web アプリを作成します。  

1. Azure ポータルの画面左下にある **[新規]** ボタンをクリックします。 
2. 検索ボックスに、「python」と入力します。
3. 検索結果で **[Bottle]** を選択し、**[作成]** をクリックします。
4. 新しい App Service プランやそのリソース グループ名の作成など、新しい Bottle アプリを構成します。 **[作成]**をクリックします。
5. 「 [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)」の指示に従い、新しく作成した Web アプリで Git 発行を構成します。

## <a name="application-overview"></a>アプリケーションの概要
### <a name="git-repository-contents"></a>Git リポジトリのコンテンツ
ここでは、初期の Git リポジトリにあるファイルの概要を説明します。次のセクションではこれらのファイルを複製します。

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

アプリケーションのメイン ソース。 マスター レイアウトを使用した 3 つのページ (index、about、contact) で構成されます。  静的コンテンツとスクリプト (bootstrap、jquery、modernizr、respond など)。

    \app.py

ローカル開発サーバーのサポート。 これを使用すると、アプリケーションをローカルで実行できます。

    \BottleWebProject.pyproj
    \BottleWebProject.sln

[Python Tools for Visual Studio]で使用するプロジェクト ファイル。

    \ptvs_virtualenv_proxy.py

仮想環境と PTVS リモート デバッグのサポートのための IIS プロキシ。

    \requirements.txt

このアプリケーションで必要な外部パッケージ。 デプロイ スクリプトにより、このファイルに記載してあるパッケージが pip インストールされます。

    \web.2.7.config
    \web.3.4.config

IIS 構成ファイル。 デプロイ スクリプトは適切な web.x.y.config を使用して web.config としてコピーします。

### <a name="optional-files---customizing-deployment"></a>オプションのファイル - デプロイのカスタマイズ
[!INCLUDE [web-sites-python-customizing-deployment](../../includes/web-sites-python-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>オプションのファイル - Python ランタイム
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>サーバー上の追加のファイル
サーバー上にはいくつかのファイルが存在しますが、Git リポジトリには追加されません。 これらのファイルはデプロイ スクリプトによって作成されるものです。

    \web.config

IIS 構成ファイル。 デプロイごとに web.x.y.config から作成されます。

    \env\

Python 仮想環境。 互換性のある仮想環境がまだ Web アプリ上にない場合に、デプロイ時に作成されます。  requirements.txt に示されているパッケージは pip インストールされますが、パッケージを既にインストールしている場合は pip でインストールがスキップされます。

次の 3 つのセクションでは、以下の 3 つの異なる環境における Web アプリ開発を使用した処理方法を説明します。

* Windows、Python Tools for Visual Studio を使用
* Windows、コマンド ラインを使用
* Mac/Linux、コマンド ラインを使用

## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Web アプリの開発 - Windows - Python Tools for Visual Studio
### <a name="clone-the-repository"></a>リポジトリの複製
最初に、Azure ポータルで提供される URL を使用して、リポジトリを複製します。 詳細については、「 [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)」を参照してください。

リポジトリのルートにあるソリューション ファイル (.sln) を開きます。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### <a name="create-virtual-environment"></a>仮想環境の作成
次にローカル開発用の仮想環境を作成します。 **[Python 環境]** を右クリックし、**[仮想環境の追加]** を選択します。

* 環境名が、 `env`となっていることを確認します。
* ベース インタープリターを選択します。 Web アプリで選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt または Azure ポータルにある Web アプリの **[アプリケーションの設定]** ブレード)。
* パッケージのダウンロードとインストールのオプションが選択されていることを確認します。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

**[作成]**をクリックします。 これにより、仮想環境が作成され、requirements.txt に指定されている依存関係がインストールされます。

### <a name="run-using-development-server"></a>開発サーバーを使用した実行
F5 キーを押してデバッグを開始します。ローカルに実行されているページが Web ブラウザーで自動的に開きます。

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

ソースにブレークポイントを設定したり、ウォッチ ウィンドウを使用したりできます。各種機能の詳細については、[Python Tools for Visual Studio のドキュメント]を参照してください。

### <a name="make-changes"></a>変更を加える
次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### <a name="install-more-packages"></a>その他のパッケージのインストール
アプリケーションが、Python と Bottle 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。 パッケージをインストールするには、仮想環境を右クリックし、 **[Install Python Package]**を選択します。

たとえば、Azure Storage、Service Bus などの他の Azure サービスにアクセスできるようになる Azure SDK for Python をインストールするには、 `azure`を入力します。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

仮想環境を右クリックし、 **[Generate requirements.txt]** を選択して requirements.txt を更新します。

その後、requirements.txt への変更内容を Git リポジトリにコミットします。

### <a name="deploy-to-azure"></a>Azure へのデプロイ
デプロイを開始するには、**[同期]** または **[プッシュ]** をクリックします。 同期ではプッシュとプルの両方が実行されます。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

仮想環境を作成したり、パッケージをインストールしたりするため、最初のデプロイには時間がかかります。

Visual Studio にはデプロイの進捗状況は表示されません。 出力を確認する場合は、「 [トラブルシューティング - デプロイ](#troubleshooting-deployment)」セクションを参照してください。

Azure URL を参照して、変更内容を表示します。

## <a name="web-app-development---windows---command-line"></a>Web アプリの開発 - Windows - コマンド ライン
### <a name="clone-the-repository"></a>リポジトリの複製
最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。 詳細については、「 [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)」を参照してください。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>仮想環境の作成
開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。 Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

Web アプリで選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt または Azure ポータルにある Web アプリの [アプリケーションの設定] ブレード)。

Python 2.7 の場合:

    c:\python27\python.exe -m virtualenv env

Python 3.4 の場合:

    c:\python34\python.exe -m venv env

アプリケーションに必要な外部パッケージをインストールします。 リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env\scripts\pip install -r requirements.txt

### <a name="run-using-development-server"></a>開発サーバーを使用した実行
次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env\scripts\python app.py

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### <a name="make-changes"></a>変更を加える
次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>その他のパッケージのインストール
アプリケーションが、Python と Bottle 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。 たとえば、Azure Storage、Service Bus などの他の Azure サービスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env\scripts\pip install azure

requirements.txt が更新されていることを確認します。

    env\scripts\pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Azure へのデプロイ
デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。

## <a name="web-app-development---maclinux---command-line"></a>Web アプリの開発 - Mac と Linux - コマンド ライン
### <a name="clone-the-repository"></a>リポジトリの複製
最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。 詳細については、「 [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)」を参照してください。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### <a name="create-virtual-environment"></a>仮想環境の作成
開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。 Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

Web アプリで選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt または Azure ポータルにある Web アプリの [アプリケーションの設定] ブレード)。

Python 2.7 の場合:

    python -m virtualenv env

Python 3.4 の場合:

    python -m venv env
または pyvenv env

アプリケーションに必要な外部パッケージをインストールします。 リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env/bin/pip install -r requirements.txt

### <a name="run-using-development-server"></a>開発サーバーを使用した実行
次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env/bin/python app.py

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### <a name="make-changes"></a>変更を加える
次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>その他のパッケージのインストール
アプリケーションが、Python と Bottle 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。 たとえば、Azure Storage、Service Bus などの他の Azure サービスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env/bin/pip install azure

requirements.txt が更新されていることを確認します。

    env/bin/pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Azure へのデプロイ
デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。

## <a name="troubleshooting---package-installation"></a>トラブルシューティング - パッケージのインストール
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>トラブルシューティング - 仮想環境
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>次のステップ
Bottle と Python Tools for Visual Studio の詳細については、次のリンクをご覧ください。 

* [Bottle のドキュメント]
* [Python Tools for Visual Studio のドキュメント]

Azure Table Storage と MongoDB の使用方法については、次のリンクをご覧ください。

* [Python Tools for Visual Studio を使用した Azure 上の Bottle と MongoDB]
* [Python Tools for Visual Studio を使用した Azure 上の Bottle と Azure Table Storage]

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Python Tools for Visual Studio を使用した Azure 上の Bottle と MongoDB]: web-sites-python-ptvs-bottle-table-storage.md
[Python Tools for Visual Studio を使用した Azure 上の Bottle と Azure Table Storage]: web-sites-python-ptvs-bottle-table-storage.md

<!--External Link references-->
[Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git for Windows]: http://msysgit.github.io/
[GitHub for Windows]: https://windows.github.com/
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Python Tools for Visual Studio のドキュメント]: http://aka.ms/ptvsdocs 
[Bottle のドキュメント]: http://bottlepy.org/docs/dev/index.html


