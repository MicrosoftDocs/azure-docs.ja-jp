---
title: Azure Cloud Services でのスタートアップ タスクの実行 | Microsoft Docs
description: スタートアップ タスクを使用すると、アプリ用にクラウド サービス環境を準備できます。 スタートアップ タスクの動作のしくみおよびそれらを作成する方法について説明します
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: cea28aba4c57f69a030d05ac192f9578967cbc3f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359468"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>クラウド サービスのスタートアップ タスクを構成して実行する方法
ロールが開始する前に、スタートアップ タスクを使用して操作を実行できます。 対象となる操作としては、コンポーネントのインストール、COM コンポーネントの登録、レジストリ キーの設定、実行時間の長いプロセスの開始などがあります。

> [!NOTE]
> スタートアップ タスクを使用できるのはクラウド サービス Web ロールと worker ロールのみであり、Virtual Machines には使用できません。
> 
> 

## <a name="how-startup-tasks-work"></a>スタートアップ タスクの動作方法
スタートアップ タスクはロールが開始する前に実行されるアクションであり、[ServiceDefinition.csdef] ファイルにおいて [Startup] 要素の [Task] 要素を使用して定義されます。 多くの場合スタートアップ タスクはバッチ ファイルですが、コンソール アプリケーションまたは PowerShell スクリプトを開始するバッチ ファイルでもかまいません。

スタートアップ タスクに情報を渡すには環境変数を使用し、スタートアップ タスクから情報を受け取るにはローカル ストレージを使用します。 たとえば、環境変数を使用してインストールするプログラムへのパスを指定でき、ファイルをローカル ストレージに書き込んでおいて後からロールで読み取ることができます。

スタートアップ タスクでは、 **TEMP** 環境変数によって指定されているディレクトリに情報およびエラーのログを記録できます。 スタートアップ タスクがクラウドで実行されると、**TEMP** 環境変数は *C:\\Resources\\temp\\[GUID].[ロール名]\\RoleTemp* ディレクトリに解決されます。

再起動と再起動の間に、スタートアップ タスクを何回でも実行できます。 たとえば、スタートアップ タスクはロールのリサイクルのたびに実行され、ロールのリサイクルには再起動が含まれない場合があります。 複数回実行されても問題がないように、スタートアップ タスクを作成する必要があります。

スタートアップ プロセスが完了するには、スタートアップ タスクが **errorlevel** (終了コード) ゼロで終了する必要があります。 スタートアップ タスクがゼロ以外の **errorlevel**で終了すると、ロールは開始しません。

## <a name="role-startup-order"></a>ロールのスタートアップ順序
Azure でのロールのスタートアップ手順を次に示します。

1. インスタンスは **開始中** とマークされ、トラフィックを受け取りません。
2. **taskType** 属性に従って、すべてのスタートアップ タスクが実行されます。
   
   * **単純な** タスクは、一度に 1 つずつ同期的に実行されます。
   * **background** タスクと **foreground** タスクは、スタートアップ タスクと並列に非同期的に開始されます。  
     
     > [!WARNING]
     > IIS はスタートアップ プロセスのスタートアップ タスク ステージの間に完全に構成されない場合があるので、ロール固有のデータを使用できないことがあります。 ロール固有のデータが必要なスタートアップ タスクは、 [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))を使用する必要があります。
     > 
     > 
3. ロール ホスト プロセスが開始され、サイトが IIS に作成されます。
4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) メソッドが呼び出されます。
5. インスタンスは **準備完了** とマークされ、トラフィックがインスタンスにルーティングされるようになります。
6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) メソッドが呼び出されます。

## <a name="example-of-a-startup-task"></a>スタートアップ タスクの例
スタートアップ タスクは、[ServiceDefinition.csdef] ファイルの **Task** 要素で定義されています。 **commandLine** 属性では、スタートアップ バッチ ファイルまたはコンソール コマンドの名前とパラメーターを指定します。**executionContext** 属性では、スタートアップ タスクの特権レベルを指定します。**taskType** 属性では、タスクの実行方法を指定します。

この例では、環境変数 **MyVersionNumber** をスタートアップ タスク用に作成し、値を "**1.0.0.0**" に設定しています。

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

次の例では、 **Startup.cmd** バッチ ファイルは、TEMP 環境変数で指定されているディレクトリの StartupLog.txt ファイルに、"The current version is 1.0.0.0" という行を書き込みます。 `EXIT /B 0` の行は、 **errorlevel** が 0 でスタートアップ タスクが終了することを保証します。

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> スタートアップ バッチ ファイルが Azure のプロジェクトに適切にデプロイされるようにするには (Web ロールの場合は **approot\\bin**、worker ロールの場合は **approot**)、Visual Studio で、スタートアップ バッチ ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピーする]** に設定する必要があります。
> 
> 

## <a name="description-of-task-attributes"></a>タスクの属性の説明
次に、 **ServiceDefinition.csdef** ファイルの [ServiceDefinition.csdef] 要素の属性について説明します。

**commandLine** -スタートアップ タスクのコマンド ラインを指定します。

* スタートアップ タスクを開始するコマンドと、オプションのコマンド ライン パラメーターです。
* 通常、これは .cmd または .bat バッチ ファイルのファイル名です。
* タスクの位置は、デプロイメント用の AppRoot\\Bin フォルダーに対する相対パスです。 環境変数は、タスクのパスとファイルを決定するときに展開されません。 環境変数の展開が必要な場合は、スタートアップ タスクを呼び出す小さな .cmd スクリプトを作成できます。
* [PowerShell スクリプト](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)を開始するコンソール アプリケーションまたはバッチ ファイルを指定できます。

**executionContext** -スタートアップ タスクの特権レベルを指定します。 指定できる特権レベルは limited または elevated です。

* **limited**  
  スタートアップ タスクは、ロールと同じ特権で実行します。 [Runtime] 要素の **executionContext** 属性も **limited** である場合は、ユーザー特権が使用されます。
* **elevated**  
  スタートアップ タスクは、管理者特権で実行します。 これにより、ロール自体の特権レベルを上げることなく、プログラムのインストール、IIS の構成の変更、レジストリの変更、その他の管理者レベル タスクを実行できます。  

> [!NOTE]
> スタートアップ タスクの特権レベルは、ロール自体と同じでなくてもかまいません。
> 
> 

**taskType** -スタートアップ タスクを実行する方法を指定します。

* **単純な**  
  タスクは、 [ServiceDefinition.csdef] ファイルで指定されている順序で、一度に 1 つずつ、同期的に実行されます。 ある **simple** スタートアップ タスクが 0 の **errorlevel** で終了すると、次の **simple** スタートアップ タスクが実行されます。 それ以上実行する **simple** スタートアップ タスクがない場合は、ロール自体が開始されます。   
  
  > [!NOTE]
  > **simple** タスクが 0 以外の **errorlevel** で終了した場合は、インスタンスがブロックされます。 後続の **simple** スタートアップ タスクおよびロール自体は開始されません。
  > 
  > 
  
    バッチ ファイルを 0 の **errorlevel** で確実に終了させるには、バッチ ファイル プロセスの最後で `EXIT /B 0` コマンドを実行します。
* **background**  
  タスクは、ロールのスタートアップと並行して、非同期的に実行されます。
* **フォアグラウンド**  
  タスクは、ロールのスタートアップと並行して、非同期的に実行されます。 **foreground** タスクと **background** タスクの重要な違いは、**foreground** タスクではタスクが終了するまでロールがリサイクルまたはシャットダウンされなくなることです。 **background** タスクにはこのような制限はありません。

## <a name="environment-variables"></a>環境変数
環境変数は、スタートアップ タスクに情報を渡すための手段です。 たとえば、インストールするプログラムを含む BLOB へのパス、ロールで使用するポート番号、スタートアップ タスクの機能を制御する設定などを設定できます。

スタートアップ タスクの環境変数には、静的環境変数と、 [RoleEnvironment] クラスのメンバーに基づく環境変数の 2 種類があります。 どちらも [ServiceDefinition.csdef] ファイルの [Environment] セクションにあり、[Variable] 要素と **name** 属性を使用します。

静的環境変数は、 **Variable** 要素の [Variable] 属性を使用します。 上の例では、**MyVersionNumber** という名前の環境変数を作成し、静的な値 "**1.0.0.0**" を設定しています。 もう 1 つの例として、**StagingOrProduction** という名前の環境変数を作成し、手動で値 "**staging**" または "**production**" を設定して、**StagingOrProduction** 環境変数の値に基づいて異なるスタートアップ アクションを実行できます。

RoleEnvironment クラスに基づく環境変数では、 **Variable** 要素の [Variable] 属性は使用しません。 代わりに [RoleInstanceValue] 子要素と適切な **xPath** 属性値を使用して、[RoleEnvironment] クラスの特定のメンバーに基づいて環境変数を作成します。 さまざまな [RoleEnvironment] の値にアクセスするための **XPath** 属性の値については、[こちら](cloud-services-role-config-xpath.md)を参照してください。

たとえば、インスタンスがコンピューティング エミュレーターで実行しているときは "**true**"、クラウドで実行しているときは "**false**" になる環境変数を作成するには、次の [Variable] 要素と [RoleInstanceValue] 要素を使用します。

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>次の手順
Cloud Service で [一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md) を実行する方法を学習します。

[パッケージ化](cloud-services-model-and-package.md) します。  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
