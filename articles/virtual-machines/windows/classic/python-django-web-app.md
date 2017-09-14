---
title: "Windows Server Azure VM 上の Django Web アプリ | Microsoft Docs"
description: "クラシック デプロイ モデルの Windows Server 2012 R2 Datacenter VM を使って Azure で Django ベースの Web サイトをホストする方法について説明します。"
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
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 283a296fb39863c2801be1093cc4f56904786abd
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Windows Server VM での Django Hello World Web アプリ

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager デプロイ モデルとクラシックデプロイ モデル](../../../resource-manager-deployment-model.md)の 2 種類があります。 この記事では、クラシック デプロイ モデルについて説明します。 ほとんどの新しいデプロイでは、Resource Manager モデルを使用することをお勧めします。

このチュートリアルでは、Azure Virtual Machines の Windows Server で Django ベースの Web サイトをホストする方法を説明します。 このチュートリアルは Azure の使用経験がなくても読むことができます。 このチュートリアルを最後まで読むと、Django ベースのアプリケーションをクラウドで稼働させることができます。

以下の項目について説明します。

* Django をホストするように Azure の仮想マシンを設定します。 このチュートリアルでは **Windows Server** での方法について説明しますが、Azure でホストされている Linux VM にも同じ方法を使うことができます。
* Windows で新しい Django アプリケーションを作成します。

このチュートリアルでは、基本的な Hello World Web アプリケーションをビルドする方法を示します。 このアプリケーションを Azure の仮想マシンでホストします。

次に示すのは完成したアプリケーションのスクリーンショットです。

![Azure の Hello World ページが表示されているブラウザー ウィンドウ][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Django をホストするための Azure 仮想マシンを作成して設定する

1. Windows Server 2012 R2 Datacenter ディストリビューションで Azure 仮想マシンを作成する方法については、「[Windows を実行する仮想マシンを Azure ポータルで作成する](tutorial.md)」をご覧ください。
2. ポート 80 で受信した Web からのトラフィックを仮想マシンのポート 80 に転送するように Azure を設定します。
   
   1. Azure Portal でダッシュボードに移動し、新しく作成した仮想マシンを選びます。
   2. **[エンドポイント]** をクリックし、**[追加]** をクリックします。

     ![エンドポイントの追加](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. **[エンドポイントの追加]** ページで、**[名前]** に「**HTTP**」と入力します。 パブリックおよびプライベートの TCP ポートを、**80** に設定します。

     ![名前を入力し、パブリック ポートとプライベート ポートを設定する](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. **[OK]**をクリックします。
     
3. ダッシュボードで VM を選びます。 新しく作成した Azure 仮想マシンにリモート デスクトップ プロトコル (RDP) を使ってリモートでサインインするには、**[接続]** をクリックします。  

> [!IMPORTANT] 
> 以下の手順では、仮想マシンに正しくサインインしているものとします。 また、ローカル コンピューターではなく仮想マシンでコマンドを発行しているものとします。

## <a id="setup"> </a>Python、Django、WFastCGI をインストールする
> [!NOTE]
> Internet Explorer を使ってダウンロードするには、Internet Explorer の **[セキュリティ強化の構成]** の設定を構成することが必要な場合があります。 そのためには、**[スタート]** > **[管理ツール]** > **[サーバー マネージャー]** > **[ローカル サーバー]** の順にクリックします。 **[IE セキュリティ強化の構成]** をクリックし、**[オフ]** を選びます。

1. [python.org][python.org] から Python 2.7 または Python 3.4 の最新バージョンをインストールします。
2. pip を使用して wfastcgi パッケージおよび django パッケージをインストールします。
   
    Python 2.7 の場合は、次のコマンドを使います。
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Python 3.4 の場合は、次のコマンドを使います。
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>FastCGI を有効にして IIS をインストールする
* FastCGI をサポートするインターネット インフォメーション サービス (IIS) をインストールします。 これには数分かかる場合があります。
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>新しい Django アプリケーションを作成する
1. C:\inetpub\wwwroot に新しい Django プロジェクトを作成するには、以下のコマンドを入力します。
   
   Python 2.7 の場合は、次のコマンドを使います。
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Python 3.4 の場合は、次のコマンドを使います。
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![New-AzureService コマンドの結果](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. `django-admin` コマンドによって、Django ベースの Web サイトの基本的な構造が生成されます。
   
   * `helloworld\manage.py` を使うと、Django ベースの Web サイトに対するホスティングの開始や停止を実行できます。
   * `helloworld\helloworld\settings.py` には、アプリケーションの Django の設定が含まれています。
   * `helloworld\helloworld\urls.py` には、各 URL とそのビューとの間のマッピング コードが含まれています。
3. C:\inetpub\wwwroot\helloworld\helloworld ディレクトリに、views.py という名前の新しいファイルを作成します。 このファイルには、"Hello World" ページをレンダリングするビューが含まれます。 コード エディターで、次のコマンドを入力します。
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. urls.py ファイルの内容を次のコマンドに置き換えます。
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>IIS を設定する
1. グローバルの applicationhost.config ファイルで、handlers セクションのロックを解除します。  これにより、web.config ファイルで Python ハンドラーを使うことができるようになります。 次のコマンドを追加します。
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. WFastCGI をアクティブにします。 これにより、グローバルの applicationhost.config ファイルに、Python インタープリターの実行可能ファイルと wfastcgi.py スクリプトを参照するアプリケーションが追加されます。
   
    Python 2.7 の場合:
   
        C:\python27\scripts\wfastcgi-enable
   
    Python 3.4 の場合:
   
        C:\python34\scripts\wfastcgi-enable
3. C:\inetpub\wwwroot\helloworld に web.config ファイルを作成します。 `scriptProcessor` 属性の値は、前の手順の出力と一致させる必要があります。 wfastcgi の設定の詳細については、「[pypi wfastcgi][wfastcgi]」を参照してください。
   
   Python 2.7 の場合:
   
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
   
   Python 3.4 の場合:
   
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
4. Django のプロジェクト フォルダーを参照するように、IIS の既定の Web サイトの場所を更新します。
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. ブラウザーに Web ページを読み込みます。

![Azure の Hello World ページが表示されているブラウザー ウィンドウ][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Azure 仮想マシンをシャットダウンする
このチュートリアルが終了したら、チュートリアル用に作成した Azure VM をシャットダウンするか削除することをお勧めします。 他のチュートリアルのためにリソースが解放され、Azure の使用料金がかからなくなります。

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi

