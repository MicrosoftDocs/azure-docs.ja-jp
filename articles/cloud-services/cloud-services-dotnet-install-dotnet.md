---
title: "クラウド サービスのロールに .NET をインストールする | Microsoft Docs"
description: "この記事では、クラウド サービスの Web ロールと worker ロールに .NET Framework を手動でインストールする方法について説明します。"
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0ac8ca0c5407925728ed0431294a3234b58d6e63
ms.openlocfilehash: 04506596ba21c3ebef7237eaad8c5d786ad672fe
ms.lasthandoff: 02/27/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>クラウド サービスのロールに .NET をインストールする
この記事では、クラウド サービスの Web ロールと worker ロールにゲスト OS に付属するバージョンとは異なるバージョンの .NET Framework をインストールする方法について説明します。 たとえば、次の手順を使用すると、.NET 4.6 のバージョンが付属しない Azure ゲスト OS ファミリ 4 に .NET 4.6.1 をインストールできます。 ゲスト OS のリリース版の最新情報については、[Azure ゲスト OS リリース ニュース](cloud-services-guestos-update-matrix.md)を参照してください。

>[!NOTE]
>ゲスト OS 5 には、.NET 4.6 が含まれています。

>[!IMPORTANT]
>Azure SDK 2.9 では、ゲスト OS 4 以下における.NET 4.6 のデプロイに制限があります。 修正プログラムは[こちら](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)で入手できます。

.NET を Web ロールや worker ロールにインストールするプロセスとして、クラウド プロジェクトの一部として .NET インストーラー パッケージを含め、ロールのスタートアップ タスクとしてインストーラーを起動します。  

## <a name="add-the-net-installer-to-your-project"></a>プロジェクトに .NET インストーラーを追加する
* インストールする .NET Framework の Web インストーラーをダウンロードします。
  * [.NET 4.6.1 Web インストーラー](http://go.microsoft.com/fwlink/?LinkId=671729)
* Web ロールの場合
  1. **ソリューション エクスプローラー**で、該当するクラウド サービス プロジェクトの **[ロール]** の下のロールを右クリックし、**[追加]、[新しいフォルダー]** の順に選択します。 *bin*
  2. **bin** フォルダーを右クリックし、**[追加]、[既存の項目]** の順に選択します。 .NET インストーラーを選択して bin フォルダーに追加します。
* worker ロールの場合
  1. ロールを右クリックし、**[追加]、[既存の項目]** の順に選択します。 .NET インストーラーを選択して、ロールに追加します。 

この方法でロールの Content フォルダーに追加したファイルは、クラウド サービス パッケージに自動的に追加され、仮想マシン上の一貫性のある場所にデプロイされます。 クラウド サービス内のすべての Web および worker ロールについて、すべてのロールがインストーラーのコピーを保持するように、このプロセスを繰り返します。

> [!NOTE]
> ご自分のアプリケーションが .NET 4.6 を対象としている場合でも、クラウド サービス ロールに .NET 4.6.1 をインストールする必要があります。 Azure ゲスト OS には、更新プログラム [3098779](https://support.microsoft.com/kb/3098779) および [3097997](https://support.microsoft.com/kb/3097997) が含まれています。 これらの更新プログラムがインストールされている状態で .NET 4.6 をインストールすると、.NET アプリケーションを実行したときに問題が発生する場合があります。そのため、.NET 4.6 ではなく、.NET 4.6.1 を直接インストールする必要があります。 詳細については、[KB 3118750](https://support.microsoft.com/kb/3118750) を参照してください。
> 
> 

![インストーラー ファイルを持つロール コンテンツ][1]

## <a name="define-startup-tasks-for-your-roles"></a>ロールのスタートアップ タスクを定義する
スタートアップ タスクでは、ロールが起動する前に操作を実行できます。 .NET Framework をスタートアップ タスクとしてインストールすると、任意のアプリケーション コードが実行される前に Framework がインストールされます。 スタートアップ タスクの詳細については、「 [Azure でスタートアップ タスクを実行する](cloud-services-startup-tasks.md)」を参照してください。 

1. すべてのロールについて、**WebRole** または **WorkerRole** ノードの下で次のコマンドを *ServiceDefinition.csdef* ファイルに追加します。
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    上の構成では、管理者特権でコンソール コマンド *install.cmd* を実行して .NET Framework をインストールできます。 この構成では、 *NETFXInstall*という名前の LocalStorage も作成されます。 スタートアップ スクリプトは、.NET framework インストーラーがダウンロードされ、このローカル ストレージ リソースからインストールされるように、このリソースを使用する一時フォルダーを設定します。 このリソースのサイズは少なくとも 1024 MB に設定して、フレームワークが適切にインストールされるようにする必要があります。 スタートアップ タスクの詳細については、「 [クラウド サービス共通のスタートアップ タスク](cloud-services-startup-tasks-common.md) 
2. **install.cmd** ファイルを作成します。次に、ロールを右クリックし、**[追加]、[既存の項目]** の順に選択して、このファイルをすべてのロールに追加します。 これで、すべてのロールに .NET インストーラー ファイルと、install.cmd ファイルが設定されました。
   
    ![すべてのファイルを持つロール コンテンツ][2]
   
   > [!NOTE]
   > メモ帳などの単純なテキスト エディターを使用してこのファイルを作成します。 Visual Studio を使用してテキスト ファイルを作成し、名前を '.cmd' に変更した場合、ファイルにまだ UTF-8 バイト オーダー マークが含まれていることがあるため、スクリプトの最初の行を実行するとエラーが発生します。 Visual Studio を使用してファイルを作成した場合は、実行時に無視されるよう REM (コメント) をファイルの最初の行に追加してください。 
   > 
   > 
3. 次のスクリプトを **install.cmd** ファイルに追加します。
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
   
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
   
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
   
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
   
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
   
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
   
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
   
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
   
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
   
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
   
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
   
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
   
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
    :exit
    EXIT /B 0
    ```
   
    インストール スクリプトにより、指定されたバージョンの .NET Framework が既にコンピューターにインストールされているかどうか、レジストリを照会することで確認されます。 対象の .NET バージョンがインストールされていない場合、.Net Web インストーラーが起動します。 問題がないかトラブルシューティングするために、スクリプトがすべてのアクティビティを *InstallLogs* ローカル ストレージに保存された *startuptasklog-(currentdatetime).txt* という名前のファイルに記録します。
   
   > [!NOTE]
   > 旧バージョンの情報を引き続き提供するために、スクリプトには .NET 4.5.2 または .NET 4.6 をインストールする方法がまだ示されています。 .NET 4.5.2 は Azure ゲスト OS で既に使用可能であるため、手動でインストールする必要はありません。 [KB 3118750](https://support.microsoft.com/kb/3118750)の問題があるため、.NET 4.6 ではなく、.NET 4.6.1 を直接インストールする必要があります。
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>診断でスタートアップ タスク ログが BLOB ストレージに転送されるように構成する
インストールの問題のトラブルシューティングを簡単にするために、スタートアップ スクリプトまたは .NET インストーラーが生成したログ ファイルを BLOB ストレージに転送するように Azure 診断を構成できます。 この方法では、リモート デスクトップをロールに移動するのではなく、ログ ファイルを BLOB ストレージから単にダウンロードしてログを表示できます。

診断を構成するには、 *diagnostics.wadcfgx* を開き、次のコマンドを **Directories** ノードに追加します。 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

これにより、Azure 診断が *NETFXInstall* リソースの *log* ディレクトリ内のすべてのファイルを *netfx-install* BLOB コンテナーの診断ストレージ アカウントに転送するように構成されます。

## <a name="deploying-your-service"></a>サービスのデプロイ
サービスをデプロイすると、スタートアップ タスクが実行され、またインストールされていない場合、.NET Framework がインストールされます。 Framework のインストール中、ロールはビジー状態になります。また、Framework のインストールで必要な場合、ロールが再起動されることがあります。 

## <a name="additional-resources"></a>その他のリソース
* [.NET Framework のインストール][Installing the .NET Framework]
* [方法 : インストールされている .NET Framework バージョンを確認する][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework のインストールのトラブルシューティング][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



