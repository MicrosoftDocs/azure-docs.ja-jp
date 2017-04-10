---
title: "Linux で Django を使用した Python Web アプリケーション | Microsoft Docs"
description: "Linux 仮想マシンを使用して Azure で Django ベースの Web アプリケーションをホストする方法について説明します。"
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 11/17/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 25a6507e3d7b473d9985114de4061da8c602e987
ms.lasthandoff: 03/25/2017


---
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Linux VM での Django Hello World Web アプリケーション
> [!div class="op_single_selector"]
> * [Windows](windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](virtual-machines-linux-python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

このチュートリアルでは、Linux 仮想マシンを使用して Microsoft Azure で Django ベースの Web サイトをホストする方法について説明します。 このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。 このチュートリアルを完了すると、クラウドで動作する Django ベースのアプリケーションが完成します。

学習内容:

* Django をホストするように Azure の仮想マシンを設定します。 このチュートリアルでは **Linux**で Azure の仮想マシンを設定する方法について説明しますが、Azure でホストされている Windows Server VM の場合も基本的な手順は同じです。 
* 新しい Django アプリケーションを Linux から作成します。

このチュートリアルを実行して、単純な Hello World Web アプリケーションを作成します。 このアプリケーションは Azure の仮想マシンでホストされます。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure で Hello World ページを表示するブラウザー ウィンドウ](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Django をホストする Azure の仮想マシンの作成と構成
1. [ここ](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) に記載されている手順に従って、*Ubuntu Server 14.04 LTS* ディストリビューションの Azure 仮想マシンを作成します。  必要に応じて、SSH 公開キーではなくパスワード認証を選択できます。
2. ポート 80 への受信 http トラフィックが許可されるように、 [ここ](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)にある手順に従って、ネットワーク セキュリティ グループを編集します。
3. 既定では、新しい仮想マシンに完全修飾ドメイン名 (FQDN) は含まれていません。  [ここ](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にある手順に従って、作成することができます。  この手順を省略しても、このチュートリアルを完了できます。

## <a id="setup"> </a>開発環境の設定
**注:** Python をインストールする、またはクライアント ライブラリを使用する場合は、 [Python インストール ガイド](../python-how-to-install.md)をご覧ください。

Ubuntu Linux VM はプレインストールされている Python 2.7 に付属していますが、Apache または Django はインストールされません。  VM に接続し、Apache および Django をインストールするには、次の手順に従います。

1. 新しい **ターミナル** ウィンドウを起動します。
2. 次のコマンドを入力して、Azure VM に接続します。  FQDN を作成していない場合は、Azure クラシック ポータルの仮想マシン概要に表示されるパブリック IP アドレスを使用して接続できます。
   
       $ ssh yourusername@yourVmUrl
3. 次のコマンドを入力して、Django をインストールします。
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. 次のコマンドを入力して、Apache と mod-wsgi をインストールします。
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="creating-a-new-django-application"></a>新しい Django アプリケーションの作成
1. 上のセクションで使用した **ターミナル** ウィンドウを開き、SSH キーを使用して仮想マシンにログインします。
2. 次のコマンドを入力して、新しい Django プロジェクトを作成します。
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   **django-admin.py** スクリプトによって、Django ベースの Web サイトの基本的な構造が生成されます。
   
   * **helloworld/manage.py** を使用すると、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
   * **helloworld/helloworld/settings.py** には、アプリケーション向けの Django の設定が含まれています。
   * **helloworld/helloworld/urls.py** には、各 URL とそのビューとの間のマッピング コードが含まれています。
3. **/var/www/helloworld/helloworld** ディレクトリに **views.py** という名前のファイルを新たに作成します。 このファイルには、"Hello World" ページをレンダリングするビューが含まれます。 エディターを起動し、次のコードを入力します。
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. ここで、 **urls.py** ファイルの内容を次のコードに置き換えます。
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="setting-up-apache"></a>Apache の設定
1. Apache 仮想ホスト構成ファイル **/etc/apache2/sites-available/helloworld.conf**を作成します。 下記にコンテンツを設定し、*yourVmName* を使用しているマシンの実際の名前に置き換えてください (例: *pyubuntu*)。
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. 次のコマンドでサイトを有効にします。
   
       $ sudo a2ensite helloworld
3. 次のコマンドで Apache を再起動します。
   
       $ sudo service apache2 reload
4. 最後に、ブラウザーで Web ページを読み込みます。
   
   ![Azure で Hello World ページを表示するブラウザー ウィンドウ](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shutting-down-your-azure-virtual-machine"></a>Azure の仮想マシンのシャットダウン
このチュートリアルが終了したら、新しく作成した Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Azure に対する利用料金の発生を回避します。


