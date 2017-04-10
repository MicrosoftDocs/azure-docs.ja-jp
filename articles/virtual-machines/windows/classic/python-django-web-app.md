---
title: "Azure VM で Django アプリをビルドする | Microsoft Docs"
description: "このチュートリアルでは、従来のデプロイ モデルで Windows Server 2012 R2 Datacenter 仮想マシンを使用して Azure で Django ベースの Web サイトをホストする方法について説明します。"
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 08/04/2015
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a55a2c069df1717bc49199589abf280cced307af
ms.lasthandoff: 03/27/2017


---
# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Windows Server VM での Django Hello World Web アプリケーション
> [!div class="op_single_selector"]
> * [Windows](python-django-web-app.md)
> * [Mac/Linux](../../virtual-machines-linux-python-django-web-app.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Django をデプロイするための Resource Manager テンプレートについては、[こちら](https://azure.microsoft.com/documentation/templates/django-app/)をご覧ください。

このチュートリアルでは、Windows Server 仮想マシンを使用して Microsoft Azure で Django ベースの Web サイトをホストする方法について説明します。 このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。 このチュートリアルを完了すると、クラウド内で動作する Django ベースのアプリケーションが完成します。

学習内容:

* Django をホストするように Azure の仮想マシンを設定します。 このチュートリアルでは Windows Server で Azure の仮想マシンを設定する方法について説明しますが、Azure でホストされている Linux VM の場合も基本的な手順は同じです。
* 新しい Django アプリケーションを Windows から作成します。

このチュートリアルを実行して、単純な Hello World Web アプリケーションを作成します。 このアプリケーションは Azure の仮想マシンでホストされます。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure で Hello World ページを表示するブラウザー ウィンドウ][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Django をホストする Azure の仮想マシンの作成と構成
1. [こちら](tutorial.md) に記載されている手順に従って、Windows Server 2012 R2 Datacenter ディストリビューションの Azure の仮想マシンを作成します。
2. Azure で、ポート 80 トラフィックを Web から仮想マシン上のポート 80 に転送します。
   
   * Azure クラシック ポータルで新しく作成した仮想マシンに移動し、 **[エンドポイント]** タブをクリックします。
   * 画面の下部にある **[追加]** ボタンをクリックします。
     ![エンドポイントの追加](./media/python-django-web-app/django-helloworld-addendpoint.png)
   * **TCP** プロトコルの**パブリック ポート 80** を**プライベート ポート 80** として開きます。
     ![][port80]
3. **[ダッシュボード]** タブで **[接続]** をクリックし、新たに作成した Azure の仮想マシンに**リモート デスクトップ**を使ってリモートでログインします。  

**重要:** 以下に示しているすべての手順では、仮想マシンに正しくログインしており、ローカル コンピューターではなく仮想マシンでコマンドを発行することを前提としています。

## <a id="setup"> </a>Python、Django、WFastCGI のインストール
**注:** Internet Explorer を使ってダウンロードするには、IE ESC 設定の構成が必要になる場合があります。具体的には、[スタート]、[管理ツール]、[サーバー マネージャー]、[ローカル サーバー] の順にクリックしてから、**[IE セキュリティ強化の構成]** をクリックし、[オフ] に設定します。

1. [python.org][python.org] から最新の Python 2.7 または 3.4 をインストールします。
2. pip を使用して wfastcgi パッケージおよび django パッケージをインストールします。
   
    Python 2.7 の場合、以下のコマンドを使用します。
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Python 3.4 の場合、以下のコマンドを使用します。
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>FastCGI を使用した IIS のインストール
1. FastCGI のサポートを利用して IIS をインストールします。  この操作には数分かかる可能性があります。
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>新しい Django アプリケーションの作成
1. *C:\inetpub\wwwroot* から次のコマンドを入力して、新しい Django プロジェクトを作成します。
   
   Python 2.7 の場合、以下のコマンドを使用します。
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Python 3.4 の場合、以下のコマンドを使用します。
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![New-AzureService コマンドの結果](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. **django-admin** コマンドによって、Django ベースの Web サイトの基本的な構造が生成されます。
   
   * **helloworld\manage.py** を使用すると、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
   * **helloworld\helloworld\settings.py** には、アプリケーション向けの Django の設定が含まれています。
   * **helloworld\helloworld\urls.py** には、各 URL とそのビューとの間のマッピング コードが含まれています。
3. **views.py** という名前の新しいファイルを *C:\inetpub\wwwroot\helloworld\helloworld* ディレクトリに作成します。 このファイルには、"Hello World" ページをレンダリングするビューが含まれます。 エディターを起動し、次のコードを入力します。
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. urls.py ファイルの内容を次のコードに置き換えます。
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="configuring-iis"></a>IIS の構成
1. グローバルの applicationhost.config で handlers セクションのロックを解除します。  これにより、web.config で python ハンドラーを使用できるようになります。
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. WFastCGI を有効化します。  これにより、グローバルの applicationhost.config に、Python インタープリターの実行可能ファイルと wfastcgi.py スクリプトを参照するアプリケーションが追加されます。
   
    Python 2.7:
   
        c:\python27\scripts\wfastcgi-enable
   
    Python 3.4:
   
        c:\python34\scripts\wfastcgi-enable
3. *C:\inetpub\wwwroot\helloworld* に web.config ファイルを作成します。  `scriptProcessor` 属性の値は、前の手順の出力と一致させる必要があります。  wfastcgi の設定の詳細については、PyPI の [wfastcgi][wfastcgi] に関するページを参照してください。
   
    Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
    Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. django のプロジェクト フォルダを参照するように、IIS の既定の Web サイトの場所を更新します。
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. 最後に、ブラウザーで Web ページを読み込みます。

![Azure で Hello World ページを表示するブラウザー ウィンドウ][1]

## <a name="shutting-down-your-azure-virtual-machine"></a>Azure の仮想マシンのシャットダウン
このチュートリアルが終了したら、新しく作成した Azure の仮想マシンをシャットダウンまたは削除して、他のチュートリアル用にリソースを解放し、Azure に対する利用料金の発生を回避します。

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

