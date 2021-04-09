---
title: Azure Cloud Services (クラシック) のロールに .NET をインストールする | Microsoft Docs
description: この記事では、クラウド サービスの Web ロールと worker ロールに .NET Framework を手動でインストールする方法について説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99061853"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>Azure Cloud Services (クラシック) のロールに .NET をインストールする

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

この記事では、Azure ゲスト OS に付属するバージョンとは異なるバージョンの .NET Framework をインストールする方法について説明します。 ゲスト OS にインストールした .NET を使用して、クラウド サービスの Web ロールおよび worker ロールを構成できます。

たとえば、.NET Framework 4.6 のどのリリースも付属していないゲスト OS ファミリ 4 に .NET Framework 4.6.2 をインストールすることができます (ゲスト OS ファミリ 5 には .NET Framework 4.6 が付属しています)。Azure ゲスト OS のリリース版の最新情報については、[Azure ゲスト OS リリースに関するニュース](cloud-services-guestos-update-matrix.md)を参照してください。 

>[!IMPORTANT]
>Azure SDK 2.9 には、ゲスト OS ファミリ 4 以前への .NET Framework 4.6 のデプロイに制限があります。 この制限に対する修正は、[Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) サイトで公開されています。

Web ロールと worker ロールに .NET をインストールするには、.NET Web インストーラーをクラウド サービス プロジェクトに含めます。 このインストーラーをロールのスタートアップ タスクの一部として起動します。 

## <a name="add-the-net-installer-to-your-project"></a>プロジェクトに .NET インストーラーを追加する
.NET Framework の Web インストーラーをダウンロードするには、次のうち、インストールするバージョンを選択します。

* [.NET Framework 4.8 の Web インストーラー](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [.NET Framework 4.7.2 の Web インストーラー](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework 4.6.2 の Web インストーラー](https://www.microsoft.com/download/details.aspx?id=53345)

*Web* ロールのインストールを追加するには、次の操作を実行します。
  1. **ソリューション エクスプローラー** で、該当するクラウド サービス プロジェクトの **[ロール]** の下の *Web* ロールを右クリックし、**[追加]** > **[新しいフォルダー]** の順に選択します。 **bin** という名前のフォルダーを作成します。
  2. bin フォルダーを右クリックし、 **[追加]**  >  **[既存の項目]** の順に選択します。 .NET インストーラーを選択して bin フォルダーに追加します。
  
*worker* ロールのインストールを追加するには、次の操作を実行します。
* *worker* ロールを右クリックし、**[追加]** > **[既存の項目]** の順に選択します。 .NET インストーラーを選択して、ロールに追加します。 

この方法でロールの Content フォルダーに追加したファイルは、クラウド サービス パッケージに自動的に追加されます。 その後、ファイルは、仮想マシン上の一貫性のある場所にデプロイされます。 クラウド サービス内の各 Web ロールおよび worker ロールについて、すべてのロールがインストーラーのコピーを保持するように、このプロセスを繰り返します。

> [!NOTE]
> ご自分のアプリケーションが .NET Framework 4.6 を対象としている場合でも、クラウド サービス ロールに .NET Framework 4.6.2 をインストールする必要があります。 ゲスト OS には、サポート技術情報の[更新プログラム 3098779](https://support.microsoft.com/kb/3098779) および[更新プログラム 3097997](https://support.microsoft.com/kb/3097997) が含まれています。 サポート技術情報の更新プログラムの上に .NET Framework 4.6 がインストールされると、.NET アプリケーションを実行したときに、問題が発生する可能性があります。 この問題を回避するために、.NET Framework 4.6 ではなく、バージョン 4.6.2 をインストールしてください。 詳しくは、[サポート技術情報の記事 3118750](https://support.microsoft.com/kb/3118750) および [4340191](https://support.microsoft.com/kb/4340191) をご覧ください。
> 
> 

![インストーラー ファイルが含まれたロール コンテンツ][1]

## <a name="define-startup-tasks-for-your-roles"></a>ロールのスタートアップ タスクを定義する
ロールが開始する前に、スタートアップ タスクを使用して操作を実行できます。 .NET Framework をスタートアップ タスクの一部としてインストールすると、アプリケーション コードが実行される前に Framework がインストールされます。 スタートアップ タスクの詳細については、[Azure におけるスタートアップの実行](cloud-services-startup-tasks.md)に関するページを参照してください。 

1. すべてのロールについて、**WebRole** ノードまたは **WorkerRole** ノードにある ServiceDefinition.csdef ファイルに、次の内容を追加します。
   
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
   
    上記の構成では、管理者特権でコンソール コマンド `install.cmd` を実行して .NET Framework をインストールしています。 この構成では、**NETFXInstall** という名前の **LocalStorage** 要素も作成されます。 このスタートアップ スクリプトにより、このローカル ストレージ リソースを使用するための一時フォルダーが設定されます。 
    
    > [!IMPORTANT]
    > フレームワークが適切にインストールされるようにするために、このリソースのサイズは少なくとも 1,024 MB に設定します。
    
    スタートアップ タスクの詳細については、「[クラウド サービス共通のスタートアップ タスク](cloud-services-startup-tasks-common.md)」を参照してください。

2. **install.cmd** という名前のファイルを作成し、このファイルに次のインストール スクリプトを追加します。

   このスクリプトにより、指定されたバージョンの .NET Framework が既にマシンにインストールされているかどうかがレジストリに照会することで確認されます。 対象の .NET Framework バージョンがインストールされていない場合、.NET Framework Web インストーラーが開かれます。 問題のトラブルシューティングを実行するために、スクリプトがすべてのアクティビティを **InstallLogs** ローカル ストレージに保存された startuptasklog-(その時点の日時).txt ファイルに記録します。
   
   > [!IMPORTANT]
   > install.cmd ファイルは、Windows のメモ帳などの基本的なテキスト エディターを使用して作成してください。 Visual Studio を使用してテキスト ファイルを作成し、拡張子を .cmd に変更すると、ファイルに UTF-8 バイト オーダー マークが含まれる可能性があります。 このマークが含まれていると、スクリプトの最初の行を実行するときにエラーが発生します。 このエラーを避けるために、スクリプトの最初の行を REM ステートメントにしてください。このステートメントにより、バイト オーダーの処理をスキップできます。 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
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
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
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
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. このトピックで前述したように、**ソリューション エクスプローラー** で **[追加]**  >  **[既存の項目]** の順に選択して、各ロールに install.cmd ファイルを追加します。 

    この手順が完了したら、すべてのロールに .NET インストーラー ファイルと install.cmd ファイルが設定されます。

   ![すべてのファイルが含まれたロール コンテンツ][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>診断でスタートアップ ログが BLOB ストレージに転送されるように構成する
インストールの問題に関するトラブルシューティングを簡単にするために、スタートアップ スクリプトまたは .NET インストーラーが生成したログ ファイルを Azure Blob Storage に転送するように Microsoft Azure Diagnostics を構成できます。 この方法では、リモート デスクトップをロールに移動するのではなく、ログ ファイルを BLOB ストレージからダウンロードしてログを表示できます。


診断を構成するには、diagnostics.wadcfgx ファイルを開き、**Directories** ノードに次の内容を追加します。 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

この XML により、診断が **NETFXInstall** リソースの log ディレクトリ内のファイルを **netfx-install** BLOB コンテナーの診断ストレージ アカウントに転送するように構成されます。

## <a name="deploy-your-cloud-service"></a>クラウド サービスをデプロイする
クラウド サービスをデプロイすると、.NET Framework がインストールされていない場合は、スタートアップ タスクにより .NET Framework がインストールされます。 Framework のインストール中、ロールは "*ビジー*" 状態になります。 また、Framework のインストールで再起動が必要な場合、サービスのロールも再起動されることがあります。 

## <a name="additional-resources"></a>その他のリソース
* [.NET Framework のインストール][Installing the .NET Framework]
* [インストールされている .NET Framework バージョンを確認する][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework のインストールのトラブルシューティング][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



