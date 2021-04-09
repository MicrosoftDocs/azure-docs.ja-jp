---
title: PHP 用の Azure Web ロールと worker ロールの作成
description: Azure クラウド サービスで PHP Web ロールおよび worker ロールを作成し、PHP ランタイムを構成するためのガイド。
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95519848"
---
# <a name="create-php-web-and-worker-roles"></a>PHP Web ロールと worker ロールの作成

## <a name="overview"></a>概要

このガイドでは、Windows 開発環境で PHP Web ロールまたは worker ロールを作成し、使用可能な "ビルトイン" バージョンから特定バージョンの PHP を選択して、PHP 構成を変更し、拡張機能を有効にして、最後に Azure にデプロイする方法を示します。 また、指定した PHP ランタイムを (カスタムの構成および拡張機能と共に) 使用できるように Web ロールまたは worker ロールを構成する方法についても説明します。

Azure にはアプリケーションを実行するためのコンピューティング モデルとして、Azure App Service、Azure Virtual Machines、および Azure Cloud Services の 3 種類があります。 これら 3 つのモデルはすべて、PHP をサポートしています。 Web ロールと worker ロールを含む Cloud Services は、 *サービスとしてのプラットフォーム (PaaS)* を提供します。 クラウド サービス内で、Web ロールはフロントエンド Web アプリケーションをホストする専用のインターネット インフォメーション サービス (IIS) Web サーバーを提供します。 worker ロールは、ユーザーの操作や入力とは関係なく、非同期タスク、長時間かかるタスク、または常駐タスクを実行できます。

これらのオプションの詳細については、[Azure が提供するコンピューティング ホスティング オプション](cloud-services/cloud-services-choose-me.md)に関するページを参照してください。

## <a name="download-the-azure-sdk-for-php"></a>Azure SDK for PHP をダウンロードする

[Azure SDK for PHP](https://github.com/Azure/azure-sdk-for-php) は、いくつかのコンポーネントで構成されています。 この記事では、そのうち Azure PowerShell と Azure エミュレーターの 2 つを使用します。 これら 2 つのコンポーネントは、Microsoft Web Platform Installer を使用してインストールできます。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。

## <a name="create-a-cloud-services-project"></a>Cloud Services プロジェクトを作成する

PHP Web ロールまたは worker ロールを作成するための最初のステップは、Azure サービス プロジェクトを作成することです。 Azure サービス プロジェクトは、Web ロールおよび worker ロールの論理コンテナーとして機能し、プロジェクトの[サービス定義ファイル (.csdef)] と[サービス構成ファイル (.cscfg)] を格納します。

新しい Azure サービス プロジェクトを作成するには、Azure PowerShell を管理者として実行し、次のコマンドを実行します。

```powershell
PS C:\>New-AzureServiceProject myProject
```

このコマンドを実行すると、Web ロールおよび worker ロールを追加できる新しいディレクトリ (`myProject`) が作成されます。

## <a name="add-php-web-or-worker-roles"></a>PHP Web ロールまたは worker ロールを追加する

PHP Web ロールをプロジェクトに追加するには、プロジェクトのルート ディレクトリで次のコマンドを実行します。

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

worker ロールについては、次のコマンドを使用します。

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> `roleName` パラメーターは省略可能です。 省略した場合、ロール名は自動的に生成されます。 最初に作成された Web ロールは `WebRole1`、2 番目は `WebRole2` などとなります。 最初に作成された worker ロールは `WorkerRole1`、2 番目は `WorkerRole2` などとなります。
>
>

## <a name="use-your-own-php-runtime"></a>独自の PHP ランタイムを使用する

必要に応じて、ビルトインの PHP ランタイムを選択して前の手順のように構成する代わりに、独自の PHP ランタイムを使用することができます。 たとえば、開発環境で使用するものと同じ PHP ランタイムを Web ロールまたは worker ロールで使用することができます。 これにより、運用環境でもアプリケーションの動作が変わらないことが容易に確認できます。

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>独自の PHP ランタイムが使用されるように Web ロールを構成する

指定した PHP ランタイムが使用されるように Web ロールを構成するには、次の手順に従います。

1. 前のトピックの説明に従って、Azure Service プロジェクトを追加し、PHP Web ロールを追加します。
2. Web ロールのルート ディレクトリにある `bin` フォルダー内に `php` フォルダーを作成し、この `php` フォルダーに PHP ランタイム (すべてのバイナリ、構成ファイル、サブフォルダーなど) を追加します。
3. (省略可能) PHP ランタイムに [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers] が使用されている場合は、Web ロールを構成して、プロビジョニング時に [SQL Server Native Client 2012][sql native client] がインストールされるように指定する必要があります。 これを行うには、Web ロールのルート ディレクトリにある `bin` フォルダーに、 [sqlncli.msi x64 installer] を追加します。 次のステップで説明するスタートアップ スクリプトでは、ロールのプロビジョニング時にインストーラーが自動的に実行されます。 PHP ランタイムに Microsoft Drivers for PHP for SQL Server が使用されていない場合は、次のステップで示すスクリプトから、この行を削除できます。

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. `.php` ページに対する要求を処理する PHP ランタイムが使用されるように[インターネット インフォメーション サービス (IIS)][iis.net] を構成するスタートアップ タスクを定義します。 これを行うには、`setup_web.cmd` ファイル (Web ロールのルート ディレクトリにある `bin` フォルダー内) をテキスト エディターで開き、内容を次のスクリプトに置き換えます。

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. アプリケーション ファイルを Web ロールのルート ディレクトリに追加します。 これは Web サーバーのルート ディレクトリになります。
6. 後述の「[アプリケーションの発行](#publish-your-application)」の説明に従って、アプリケーションを発行します。

> [!NOTE]
> 独自の PHP ランタイムを使用するための前述の手順を実行した後で、`download.ps1` スクリプト (Web ロールのルート ディレクトリの `bin` フォルダーにある) は削除することができます。
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>独自の PHP ランタイムが使用されるように worker ロールを構成する

指定した PHP ランタイムが使用されるように worker ロールを構成するには、次の手順に従います。

1. 前のトピックの説明に従って、Azure Service プロジェクトを追加し、PHP worker ロールを追加します。
2. worker ロールのルート ディレクトリに `php` フォルダーを作成し、この `php` フォルダーに PHP ランタイム (すべてのバイナリ、構成ファイル、サブフォルダーなど) を追加します。
3. (省略可能) PHP ランタイムに [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers] が使用されている場合は、worker ロールを構成して、プロビジョニング時に [SQL Server Native Client 2012][sql native client] がインストールされるように指定する必要があります。 これを行うには、worker ロールのルート ディレクトリに、 [ フォルダーに、] を追加します。 次のステップで説明するスタートアップ スクリプトでは、ロールのプロビジョニング時にインストーラーが自動的に実行されます。 PHP ランタイムに Microsoft Drivers for PHP for SQL Server が使用されていない場合は、次のステップで示すスクリプトから、この行を削除できます。

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. ロールのプロビジョニング時に worker ロールの PATH 環境変数に `php.exe` 実行可能ファイルを追加するスタートアップ タスクを定義します。 これを行うには、 `setup_worker.cmd` ファイル (worker ロールのルート ディレクトリ内) をテキスト エディターで開き、内容を次のスクリプトに置き換えます。

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. アプリケーション ファイルを worker ロールのルート ディレクトリに追加します。
6. 後述の「[アプリケーションの発行](#publish-your-application)」の説明に従って、アプリケーションを発行します。

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>コンピューティング エミュレーターとストレージ エミュレーターでアプリケーションを実行する

Azure エミュレーターは、Azure アプリケーションをクラウドにデプロイする前にテストできるローカル環境を提供します。 ただし、エミュレーターと Azure 環境では、いくつか相違点があります。 詳細については、「[開発とテストのための Azure のストレージ エミュレーター使用](storage/common/storage-use-emulator.md)」を参照してください。

コンピューティング エミュレーターを使用する場合は、ローカルで PHP をインストールしておく必要があることに注意してください。 コンピューティング エミュレーターでは、アプリケーションを実行するためにローカルの PHP インストールが使用されます。

エミュレーターでプロジェクトを実行するには、プロジェクトのルート ディレクトリで次のコマンドを実行します。

```powershell
PS C:\MyProject> Start-AzureEmulator
```

出力は以下のようになります。

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Web ブラウザーを開き、出力に示されているローカル アドレス (上の出力例では`http://127.0.0.1:81` ) に移動すると、アプリケーションがエミュレーターで実行されていることを確認できます。

エミュレーターを停止するには、次のコマンドを実行します。

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>アプリケーションの発行

アプリケーションを発行するには、まず、 [Import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) コマンドレットを使用して発行設定をインポートする必要があります。 次に [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) コマンドレットを使用して、アプリケーションを発行できます。 サインインの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/)」を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、 [PHP デベロッパー センター](https://azure.microsoft.com/develop/php/)を参照してください。

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[サービス定義ファイル (.csdef)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[サービス構成ファイル (.cscfg)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[ フォルダーに、]: https://go.microsoft.com/fwlink/?LinkID=239648