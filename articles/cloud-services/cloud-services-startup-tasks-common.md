---
title: Cloud Services 共通のスタートアップ タスク | Microsoft Docs
description: クラウド サービスの Web ロールや worker ロールで実行できる共通のスタートアップ タスクの例を示します。
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 2eb299ad841444a3100eac207b225d5377959f85
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358962"
---
# <a name="common-cloud-service-startup-tasks"></a>クラウド サービス共通のスタートアップ タスク
この記事では、クラウド サービスで実行できる共通のスタートアップ タスクの例を示します。 スタートアップ タスクを使用して、ロールを開始する前の操作を実行できます。 対象となる操作としては、コンポーネントのインストール、COM コンポーネントの登録、レジストリ キーの設定、実行時間の長いプロセスの開始などがあります。 

スタートアップ タスクの仕組み、特にスタートアップ タスクを定義するエントリを作成する方法については、 [こちらの記事](cloud-services-startup-tasks.md) をご覧ください。

> [!NOTE]
> スタートアップ タスクを使用できるのはクラウド サービス Web ロールと worker ロールのみであり、Virtual Machines には使用できません。
> 

## <a name="define-environment-variables-before-a-role-starts"></a>ロールを開始する前に環境変数を定義する
特定のタスク向けの環境変数を定義する必要がある場合は、[Task] 要素内の [Environment] 要素を使用します。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

デプロイメントに関する情報を参照するには、変数に [有効な Azure XPath 値](cloud-services-role-config-xpath.md) を指定することもできます。 `value` 属性を使用する代わりに、 [RoleInstanceValue] 子要素を定義します。

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>AppCmd.exe を使用して IIS スタートアップを構成する
[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) コマンド ライン ツールを使用して、Azure のスタートアップ時の IIS 設定を管理できます。 *AppCmd.exe* には Azure のスタートアップ タスクで使用する構成設定へのコマンド ライン アクセスが用意されています。 *AppCmd.exe*を使用すると、アプリケーションやサイトの Web サイト設定を追加、変更、または削除できます。

ただし、 *AppCmd.exe* をスタートアップ タスクとして使用するにはいくつか注意する点があります。

* スタートアップ タスクは、再起動の間に複数回実行されることがあります。 たとえば、ロールが再利用される場合です。
* *AppCmd.exe* アクションは、複数回実行されるとエラーが発生することがあります。 たとえば、*Web.config* にセクションを 2 回追加しようとするとエラーが発生する可能性があります。
* ゼロ以外の終了コードや **errorlevel**が返されると、スタートアップ タスクが失敗します。 たとえば、*AppCmd.exe* でエラーが発生した場合です。

*AppCmd.exe* を呼び出した後、**errorlevel** を確認することをお勧めします。これは、*AppCmd.exe* への呼び出しを *.cmd* ファイルでラップすると簡単に実行できます。 既知の **errorlevel** 応答が検出された場合は無視するか、その応答を返すことができます。

*AppCmd.exe* によって返される errorlevel は winerror.h ファイルに一覧表示されています。[MSDN](/windows/desktop/Debug/system-error-codes--0-499-) で確認することもできます。

### <a name="example-of-managing-the-error-level"></a>エラー レベルの管理の例
この例では *Web.config* に JSON 用の圧縮セクションと圧縮エントリを追加し、エラー処理とログ記録を示します。

ここに示す [ServiceDefinition.csdef] ファイルの関連セクションでは、[executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) 属性を `elevated` に設定し、*AppCmd.exe* に *Web.config* ファイルの設定を変更する十分なアクセス許可を与えます。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

*Startup.cmd* バッチ ファイルは *AppCmd.exe* を使用して *Web.config* ファイルに JSON 用の圧縮セクションと圧縮エントリを追加します。 予期される **errorlevel** 183 は、VERIFY.EXE コマンド ライン プログラムを使用してゼロに設定されます。 予期しない errorlevel は StartupErrorLog.txt に記録されます。

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>ファイアウォール規則を追加する
Azure では、実質的に 2 つのファイアウォールがあります。 最初のファイアウォールは、仮想マシンと外部世界の間の接続を制御します。 このファイアウォールは [ServiceDefinition.csdef] ファイルの [EndPoints] 要素によって制御されます。

2 つ目のファイアウォールは、仮想マシンとその仮想マシン内の処理との間の接続を制御します。 このファイアウォールは、`netsh advfirewall firewall` コマンド ライン ツールを使用して制御できます。

Azure はお使いのロール内で開始されるプロセス用のファイアウォール規則を作成します。 たとえば、サービスやプログラムを開始すると、Azure はそのサービスがインターネットと通信するのに必要なファイアウォール規則を自動的に作成します。 ただし、お使いのロール外のプロセスによって開始されるサービス (COM + サービスや Windows によってスケジュール設定されるタスクなど) を作成する場合は、そのサービスへのアクセスを許可するファイアウォール規則を手動で作成する必要があります。 これらのファイアウォール規則はスタートアップ タスクを使用して作成できます。

ファイアウォール規則を作成するスタートアップ タスクには、**elevated** の [executionContext][Task] が必要です。 次のスタートアップ タスクを [ServiceDefinition.csdef] ファイルに追加します。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

ファイアウォール規則を追加するには、お使いのスタートアップ バッチ ファイルに適切な `netsh advfirewall firewall` コマンドを使用する必要があります。 この例では、スタートアップ タスクに TCP ポート 80 に対するセキュリティと暗号化が必要です。

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>特定の IP アドレスをブロックする
IIS **web.config** ファイルを修正することで、特定の IP アドレスに対する Azure Web ロールのアクセスを制限できます。 **ApplicationHost.config** ファイルの **ipSecurity** セクションのロックを解除するコマンド ファイルも使用する必要があります。

**ApplicationHost.config** ファイルの **ipSecurity** セクションのロックを解除するには、ロールの開始時に実行されるコマンド ファイルを作成します。 お使いの Web ロールのルート レベルに **startup** という名前のフォルダーを作成し、このフォルダーに **startup.cmd** という名前のバッチ ファイルを作成します。 このファイルを Visual Studio プロジェクトに追加し、プロパティを **[常にコピーする]** に設定して、ファイルが常にパッケージに含まれるようにします。

次のスタートアップ タスクを [ServiceDefinition.csdef] ファイルに追加します。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

このコマンドを **startup.cmd** ファイルに追加します。

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

これにより、Web ロールが初期化されるたびに **startup.cmd** バッチ ファイルが実行され、必要な **ipSecurity** セクションのロックが解除されます。

最後に、お使いの Web ロールの [web.config](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) ファイルの **system.webServer セクション** を変更し、次の例に示すようにアクセス許可が付与された IP アドレスの一覧を追加します。

このサンプル構成では、定義した 2 つ以外のすべての IP についてサーバーへのアクセスを **許可** します。

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

このサンプル構成では、定義した 2 つ以外のすべての IP についてサーバーへのアクセスを **拒否** します。

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>PowerShell のスタートアップ タスクを作成する
Windows PowerShell のスクリプトは [ServiceDefinition.csdef] から直接呼び出すことはできませんが、スタートアップ バッチ ファイルから呼び出すことができます。

PowerShell では (既定では) 未署名のスクリプトは実行されません。 スクリプトに署名しない場合は、未署名のスクリプトを実行するように PowerShell を構成する必要があります。 未署名のスクリプトを実行するには、**ExecutionPolicy** を **Unrestricted** に設定する必要があります。 使用する **ExecutionPolicy** の設定は、Windows PowerShell のバージョンに基づきます。

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

PowerShell 2.0 または 1.0 を実行するゲスト OS を使用している場合は、バージョン 2 の実行を強制できます。バージョン 2 を使用できない場合はバージョン 1 を使用してください。

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>スタートアップ タスクからのファイルをローカル ストレージに作成する
スタートアップ タスクによって作成されたファイルをローカル ストレージ リソースに格納し、後でお使いのアプリケーションからアクセスできます。

ローカル ストレージ リソースを作成するには、[ServiceDefinition.csdef] ファイルに [LocalResources] セクションを追加した後で [LocalStorage] 子要素を追加します。 ローカル ストレージ リソースに一意の名前を付与し、お使いのスタートアップ タスク用に適切なサイズを設定します。

スタートアップ タスクにローカル ストレージ リソースを使用するには、ローカル ストレージ リソースの場所を参照する環境変数を作成する必要があります。 これにより、スタートアップ タスクとアプリケーションがローカル ストレージ リソースのファイルを読み書きできます。

**ServiceDefinition.csdef** ファイルの関連セクションを次に示します。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

例として、この **Startup.cmd** バッチ ファイルでは **PathToStartupStorage** 環境変数を使用して、**MyTest.txt** ファイルをローカル ストレージの場所に作成します。

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

ローカル ストレージ フォルダーには Azure SDK から [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) メソッドを使用してアクセスできます。

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>エミュレーターまたはクラウドで実行する
クラウドとコンピューティング エミュレーターで動作するスタートアップ タスクを区別することができます。 たとえば、エミュレーターで実行されている場合にのみ、SQL データの新しいコピーを使用するように設定できます。 または、エミュレーターで実行する場合は不要な、クラウドのパフォーマンスを最適化する操作を実行することもできます。

コンピューティング エミュレーターとクラウドで別の操作を実行するには、[ServiceDefinition.csdef] ファイルに環境変数を作成します。 その後、スタートアップ タスク内の値に対する環境変数をテストします。

環境変数を作成するには、[Variable]/[RoleInstanceValue] 要素を追加し、XPath 値 `/RoleEnvironment/Deployment/@emulated` を作成します。 **%ComputeEmulatorRunning%** 環境変数の値は、コンピューティング エミュレーターで実行される場合は `true`、クラウドで実行される場合は `false` になります。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

これで、タスクは、 **%ComputeEmulatorRunning%** 環境変数をチェックして、ロールがクラウドで実行されるかエミュレーターで実行されるかに基づいて異なるアクションを実行できます。 環境変数をチェックする .cmd シェル スクリプトはこちらです。

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>タスクが既に実行されていることを検出する
ロールが再起動されずに再利用され、スタートアップ タスクが再度実行される場合があります。 タスクが既にホスト VM で実行されたかどうかを示すフラグはありません。 一部のタスクは実行回数が考慮されず、複数回実行されることがあります。 ただし、タスクが複数回実行されることを回避する必要がある状況になることがあります。

タスクが既に実行されていることを検出する最も簡単な方法は、タスクが成功したときに **%TEMP%** フォルダーにファイルを作成し、タスクの開始時にそのファイルを確認する方法です。 次に示すサンプル cmd シェル スクリプトは、ユーザーの代わりにそれを実行します。

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>タスクのベスト プラクティス
ここでは、お使いの Web ロールや worker ロールのタスクを構成するときに従う必要があるいくつかのベスト プラクティスを紹介します。

### <a name="always-log-startup-activities"></a>常にスタートアップ アクティビティをログに記録する
Visual Studio にはバッチ ファイルを順番に実行するデバッガーが用意されていないため、バッチ ファイルの操作に関するデータをできる限り多く入手することをお勧めします。 バッチ ファイル (**stdout** と **stderr** の両方) の出力をログ記録すると、バッチ ファイルのデバッグと修正に役立つ重要な情報を入手できます。 **%TEMP%** 環境変数で指定されたディレクトリにある StartupLog.txt に **stdout** と **stderr** の両方をログに記録するには、ログに記録する特定の行の末尾にテキスト `>>  "%TEMP%\\StartupLog.txt" 2>&1` を追加します。 たとえば、 **%PathToApp1Install%** ディレクトリで setup.exe を実行するには、次のようにします。

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

xml を単純化するために、すべてのスタートアップ タスクをログと共に呼び出し、子タスクが常に同じ環境変数を共有するラッパー *cmd* ファイルを作成できます。

`>> "%TEMP%\StartupLog.txt" 2>&1` を各スタートアップ タスクの末尾で使用することをわずらわしいと感じるかもしれません。 ログを処理するラッパーを作成することで、タスクのログ記録をそのラッパーに任せることができます。 このラッパーは、実際に実行するバッチ ファイルを呼び出します。 ターゲットのバッチ ファイルからの出力は、*Startuplog.txt* ファイルにリダイレクトされます。

次の例は、スタートアップ バッチ ファイルからのすべての出力をリダイレクトする方法を示しています。 この例では、ServerDefinition.csdef ファイルが *logwrap.cmd* を呼び出すスタートアップ タスクを作成します。 *logwrap.cmd* は *Startup2.cmd* を呼び出して、すべての出力を **%TEMP%\\StartupLog.txt** にリダイレクトします。

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

**StartupLog.txt** ファイル内のサンプル出力:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **StartupLog.txt** ファイルは、*C:\Resources\temp\\{ロール識別子}\RoleTemp* フォルダーに配置されます。
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>スタートアップ タスクに適した executionContext を設定する
スタートアップ タスクに適切な特権を設定します。 ロールが通常の特権で実行されるときでも、スタートアップ タスクは管理者特権で実行する必要がある場合があります。

[executionContext][Task] 属性はスタートアップ タスクの特権レベルを設定します。 `executionContext="limited"` を使用すると、スタートアップ タスクにロールと同じ特権レベルが付与されます。 `executionContext="elevated"` を使用すると、スタートアップ タスクに管理者特権が付与されます。これにより、お使いのロールに管理者特権を与えることなく、スタートアップ タスクで管理者のタスクを実行できます。

管理者特権を必要とするスタートアップ タスクの例は、 **AppCmd.exe** を使用して IIS を構成するスタートアップ タスクです。 **AppCmd.exe** には `executionContext="elevated"` が必要です。

### <a name="use-the-appropriate-tasktype"></a>適切な taskType を使用する
[taskType][Task] 属性は、スタートアップ タスクを実行する方法を決定します。 **simple**、**background**、および **foreground** の 3 つの値があります。 background タスクと foreground タスクは非同期的に開始され、simple タスクは一度に 1 回のみ同期的に実行されます。

**simple** スタートアップ タスクでは、ServiceDefinition.csdef ファイルに表示される順序でタスクが実行されます。 **simple** タスクがゼロ以外の終了コードで終了すると、スタートアップ手続きは停止し、ロールは開始されません。

**background** スタートアップ タスクと **foreground** スタートアップ タスクの違いは、**foreground** タスクはその **foreground** タスクが終了するまでロールを実行し続けることです。 つまり、**foreground** タスクがハングまたはクラッシュすると、**foreground** タスクが強制的に終了されるまでロールは再利用されません。 このため、**foreground** タスクの機能が必要でない限り、非同期的なスタートアップ タスクは **background** タスクにすることをお勧めします。

### <a name="end-batch-files-with-exit-b-0"></a>EXIT/B 0 でバッチ ファイルを終了する
ロールが開始されるのは、各 simple スタートアップ タスクの **errorlevel** がゼロの場合のみです。 すべてのプログラムに **errorlevel** (終了コード) が正しく設定されているとは限らないため、すべてが正しく実行されるにはバッチ ファイルの末尾を `EXIT /B 0` にする必要があります。

ロールが開始されない一般的な原因は、スタートアップ バッチ ファイルの末尾に `EXIT /B 0` がないことです。

> [!NOTE]
> 入れ子になったバッチファイルで `/B` パラメーターを使用すると、ハングする場合があります。 別のバッチ ファイルが現在のバッチ ファイルを呼び出す場合 ([ログ ラッパーを使用する場合など](#always-log-startup-activities)) に、このハング問題が発生しないようにすることができます。 このような場合は、`/B` パラメーターを省略できます。
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>スタートアップ タスクを複数回実行する
すべてのロールの再利用で再起動が実行されるとは限りませんが、すべてのロールの再利用ですべてのスタートアップ タスクが実行されます。 これは、再起動の間にスタートアップ タスクを複数回問題なく実行できる必要があることを意味します。 これについては、[前のセクション](#detect-that-your-task-has-already-run)で説明しています。

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>ロールでアクセスする必要があるファイルをローカル ストレージに格納する
スタートアップ タスクの実行時にファイルをコピーまたは作成し、後からお使いのロールでアクセスするには、そのファイルをローカル ストレージに配置する必要があります。 [前のセクション](#create-files-in-local-storage-from-a-startup-task)を参照してください。

## <a name="next-steps"></a>次の手順
クラウドの [サービス モデルとパッケージ](cloud-services-model-and-package.md)

[タスク](cloud-services-startup-tasks.md) の動作について説明します。

クラウド サービス パッケージを[作成してデプロイ](cloud-services-how-to-create-deploy-portal.md)します。

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
