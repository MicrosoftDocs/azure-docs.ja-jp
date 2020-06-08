---
title: VS を使用して WebJobs を開発してデプロイする
description: Visual Studio で Azure WebJobs を開発し、Azure App Service にデプロイする方法について、スケジュールされたタスクの作成を含めて説明します。
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d73c858bdbfee7c5b7c7e31b62b1f601b7b6838a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866437"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Visual Studio を使用して WebJobs を開発してデプロイする - Azure App Service

この記事では、Visual Studio を使用して、コンソール アプリケーション プロジェクトを [Azure WebJobs](https://go.microsoft.com/fwlink/?LinkId=390226)として [App Service](overview.md) の Web アプリにデプロイする方法について説明します。 [Azure ポータル](https://portal.azure.com)を使用して WebJobs をデプロイする方法については、「[WebJobs でバックグラウンド タスクを実行する](webjobs-create.md)」を参照してください。

複数の WebJobs を単一の Web アプリに発行することができます。 Web アプリの各 WebJobs に一意の名前が付いていることを確認してください。

バージョン 3.x の [Azure WebJobs SDK](webjobs-sdk-how-to.md) では .NET Core アプリと .NET Framework アプリのどちらかとして実行される WebJobs を開発できますが、バージョン 2.x では .NET Framework のみがサポートされます。 WebJobs プロジェクトをデプロイする方法は、.NET Core プロジェクトと .NET Framework プロジェクトでは異なります。

## <a name="webjobs-as-net-core-console-apps"></a>.NET Core コンソール アプリとしての WebJobs

バージョン 3.x の WebJobs を使用する場合は、.NET Core コンソール アプリとして WebJobs を作成して発行できます。 .NET Core コンソール アプリケーションを作成して、WebJobs として Azure に発行する詳細な手順については、「[イベント ドリブンのバックグラウンド処理で Azure WebJobs SDK の使用を開始する](webjobs-sdk-get-started.md)」を参照してください。

> [!NOTE]
> .NET Core WebJobs を Web プロジェクトにリンクすることはできません。 Web アプリで WebJobs をデプロイする必要がある場合は、[.NET Framework コンソール アプリとして WebJobs を作成する](#webjobs-as-net-framework-console-apps)必要があります。  

### <a name="deploy-to-azure-app-service"></a>Azure App Service にデプロイする

Visual Studio から App Service への .NET Core WebJobs の発行では、ASP.NET Core アプリの発行と同じツールを使用します。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJobs の種類

既定では、.NET Core コンソール プロジェクトから発行された WebJobs は、トリガー時またはオンデマンドでのみ実行されます。 プロジェクトを更新し、[スケジュールに従って実行する](#scheduled-execution)ことも、継続的に実行することもできます。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>スケジュールされた実行

.NET Core コンソール アプリケーションを Azure に発行するときに、新しい*settings.job* ファイルがプロジェクトに追加されます。 WebJobs の実行スケジュールを設定する場合に、このファイルを使用します。 詳細については、「[トリガーされた WebJobs のスケジュール設定](#scheduling-a-triggered-webjob)」を参照してください。

#### <a name="continuous-execution"></a>継続的な実行

Visual Studio を使用して、Azure で Always On が有効な場合に継続的に実行するように WebJobs を変更することができます。

1. [Azure にプロジェクトを発行](#deploy-to-azure-app-service)していない場合は、発行します。

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行]** タブで、 **[設定]** を選択します。 

1. **[プロファイル設定]** ダイアログの **[WebJobs の種類]** で **[継続的]** を選んでから、 **[保存]** を選択します。

    ![Web ジョブの発行の設定ダイアログ](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. **[発行]** を選択し、設定が更新された WebJobs を再発行します。

## <a name="webjobs-as-net-framework-console-apps"></a>.NET Framework コンソール アプリとしての WebJobs  

Visual Studio で WebJobs が有効な .NET Framework Console Application プロジェクトがデプロイされるとき、Web アプリの適切なフォルダーにランタイム ファイルがコピーされます (継続的な WebJobs の場合は *App_Data/jobs/continuous*、スケジュールされたまたはオンデマンドの WebJobs の場合は *App_Data/jobs/triggered*)。

WebJobs 対応のプロジェクトでは、次の項目が追加されています。

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet パッケージ。
* デプロイメントとスケジューラの設定を含む [webjob-publish-settings.json](#publishsettings) ファイル。 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

これらの項目を既存のコンソール アプリケーション プロジェクトに追加することもできれば、テンプレートを使用して新しい WebJobs 対応のコンソール アプリケーション プロジェクトを作成することもできます。 

プロジェクト自体を WebJobs としてデプロイできます。また、Web プロジェクトをデプロイしたときに常に自動的にデプロイされるように、プロジェクトを Web プロジェクトにリンクすることもできます。 プロジェクトをリンクする場合、Visual Studio で、Web プロジェクト内の [webjobs-list.json](#webjobslist) ファイルに WebJobs 対応のプロジェクトの名前が含められます。

![Diagram showing WebJob project linking to web project](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>前提条件

Visual Studio 2015 を使用している場合は、[Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/) をインストールします。

Visual Studio 2017 を使用している場合は、[Azure 開発ワークロード](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)をインストールします。

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>既存のコンソール アプリケーション プロジェクトに対する WebJobs のデプロイを有効にする

2 つのオプションがあります。

* [Web プロジェクトを使用した自動デプロイメントを有効にする](#convertlink)。

  Web プロジェクトをデプロイしたときに自動的に WebJobs としてデプロイされるように、既存のコンソール アプリケーション プロジェクトを構成します。 WebJobs を、関連する Web アプリケーションが実行する Web アプリケーションと同じアプリケーションで実行する場合に、このオプションを使用します。

* [Web プロジェクトなしでデプロイメントを有効にする](#convertnolink)。

  Web プロジェクトへリンクせず、既存のコンソール アプリケーション プロジェクトがそれ自身で WebJobs としてデプロイされるように構成します。 Web アプリケーションで Web アプリケーションを実行せずに、WebJobs 自身によって Web アプリケーション内で実行されようにする場合に、このオプションを使用します。 このオプションは、Web アプリケーション リソースとは関係なく WebJobs リソースを拡張する必要がある場合などに使用できます。

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Web プロジェクトを使用した WebJobs の自動デプロイメントを有効にする

1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、 **[追加]**  >  **[既存のプロジェクトを Azure WebJobs として]** をクリックします。
   
    ![[既存のプロジェクトを Azure Web ジョブとして]](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [[Azure WebJobs の追加]](#configure) ダイアログ ボックスが表示されます。
2. **[プロジェクト名]** ボックスの一覧で、コンソール アプリケーション プロジェクトを選択し、WebJobs として追加します。
   
    ![Selecting project in Add Azure WebJob dialog](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. [[Azure WebJobs の追加]](#configure) ダイアログで必要な設定を完了し、 **[OK]** をクリックします。 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Web プロジェクトなしで WebJobs デプロイメントを有効にする
1. **ソリューション エクスプローラー**でコンソール アプリケーション プロジェクトを右クリックし、 **[Azure WebJob として発行する]** をクリックします。 
   
    ![[Azure Web ジョブとして発行]](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [[Azure WebJobs の追加]](#configure) ダイアログ ボックスが表示され、 **[プロジェクト名]** ボックスに選択されたプロジェクトが示されます。
2. [[Azure WebJobs の追加]](#configure) ダイアログ ボックスで必要な設定を完了し、 **[OK]** をクリックします。
   
   **Web の発行** ウィザードが表示されます。  すぐに発行しない場合は、ウィザードを閉じます。 [プロジェクトをデプロイする](#deploy)ときのために、入力した設定値は保存されます。

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>新しい WebJobs 対応のプロジェクトを作成する
新しい WebJobs 対応のプロジェクトを作成するために、 [前のセクション](#convert)で説明したように、コンソール アプリケーション プロジェクト テンプレートを使用して、WebJobs デプロイメントを有効にすることができます。 または、次のように WebJobs の新しいプロジェクト テンプレートを使用できます。

* [独立した WebJobs 用に WebJobs の新しいプロジェクト テンプレートを使用する](#createnolink)
  
    プロジェクトを作成し、Web プロジェクトへはリンクせずに、それ自身で WebJobs としてデプロイされるように構成します。 Web アプリケーションで Web アプリケーションを実行せずに、WebJobs 自身によって Web アプリケーション内で実行されようにする場合に、このオプションを使用します。 このオプションは、Web アプリケーション リソースとは関係なく WebJobs  リソースを拡張する必要がある場合などに使用できます。
* [Web プロジェクトにリンクされた WebJobs 用に WebJobs の新しいプロジェクト テンプレートを使用する](#createlink)
  
    同じソリューション内の Web プロジェクトがデプロイされたときに WebJobs として自動的にデプロイされるように構成して、プロジェクトを作成します。 WebJobs を、関連する Web アプリケーションが実行する Web アプリケーションと同じアプリケーションで実行する場合に、このオプションを使用します。

> [!NOTE]
> WebJobs の new-project テンプレートは、NuGet パッケージを自動的にインストールし、 *WebJobs SDK* 用にコードを [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)に含めます。 WebJobs SDK を使用しない場合は、*Program.cs* 内の `host.RunAndBlock` ステートメントを削除または変更します。
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 独立した WebJobs 用に WebJobs の新しいプロジェクト テンプレートを使用する
1. **[ファイル]**  >  **[新しいプロジェクト]** の順にクリックし、 **[新しいプロジェクト]** ダイアログ ボックスで **[クラウド]**  >  **[Azure WebJob (.NET Framework)]** の順にクリックします。
   
    ![New Project dialog showing WebJob template](./media/webjobs-dotnet-deploy-vs/np.png)
2. 前に示された指示に従い、 [コンソール アプリケーション プロジェクトを独立した WebJobs プロジェクトにします](#convertnolink)。

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Web プロジェクトにリンクされた WebJobs 用に WebJobs の新しいプロジェクト テンプレートを使用する
1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、 **[追加]**  >  **[新しい Azure WebJobs プロジェクト]** をクリックします。
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [[Azure WebJobs の追加]](#configure) ダイアログ ボックスが表示されます。
2. [[Azure WebJobs の追加]](#configure) ダイアログ ボックスで必要な設定を完了し、 **[OK]** をクリックします。

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>[Azure WebJobs の追加] ダイアログ ボックス
**[Azure WebJob の追加]** ダイアログを使用して、WebJobs の WebJobs 名と実行モード設定を入力できます。 

![Add Azure WebJob dialog](./media/webjobs-dotnet-deploy-vs/aaw2.png)

このダイアログのフィールドは、Azure ポータルの **[WebJobs の追加]** ダイアログのフィールドに対応しています。 詳細については、「 [WebJobs でのバックグラウンド タスクの実行](webjobs-create.md)」を参照してください。

> [!NOTE]
> * コマンド ライン デプロイメントについては、「 [Enabling Command-line or Continuous Delivery of Azure WebJobs (Azure WebJobs のコマンド ラインによる配信または継続的配信を有効にする)](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)」を参照してください。
> * WebJobs をデプロイした後、その WebJobs の種類を変更して再デプロイすることに決めた場合は、*webjobs-publish-settings.json* ファイルを削除する必要があります。 これにより発行オプションが再度表示され、WebJobs の種類を変更できます。
> * WebJobs をデプロイし、後から実行モードを継続的から継続的以外、またはその逆に変更した場合、Visual Studio は再デプロイ時に Azure 内に新しい WebJobs を作成します。 その他のスケジュール設定を変更し、実行モードを同じままにするか、または [スケジュール済み] と [オンデマンド] の間で切り替えた場合は、Visual Studio は新しいジョブを作成するのではなく、既存のジョブを更新します。
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
WebJobs のデプロイメント用にコンソール アプリケーションを構成すると、Visual Studio によって [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet パッケージがインストールされ、WebJobs プロジェクトの *Properties* フォルダーにある *webjob-publish-settings.json* ファイルにスケジュール情報が格納されます。 次にこのファイルの例を示します。

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。 ファイル スキーマは [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) に格納され、そこで表示できます。  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
WebJobs 対応のプロジェクトを Web プロジェクトにリンクすると、Visual Studio は WebJobs プロジェクトの名前を Web プロジェクトの *Properties* フォルダー内の *webjobs-list.json* ファイルに格納します。 次の例に示すように、一覧には複数の WebJobs プロジェクトが含まれる場合があります。

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。 ファイル スキーマは [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) に格納され、そこで表示できます。

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>WebJobs プロジェクトをデプロイする
Web プロジェクトにリンクされた WebJobs プロジェクトは、Web プロジェクトと共に自動的にデプロイされます。 Web プロジェクトのデプロイメントについては、左のナビゲーションにある **[How-to guides]\(ハウツー ガイド\)** の  >  **[Deploy app]\(アプリをデプロイする\)** を参照してください。

WebJobs プロジェクトがそれ自身でデプロイされるように指定するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[Azure WebJob として発行する]** をクリックします。 

![[Azure Web ジョブとして発行]](./media/webjobs-dotnet-deploy-vs/paw.png)

独立した WebJobs の場合は、Web プロジェクトで使用されたウィザードと同じ **Web の発行** ウィザードが表示されますが、変更可能な設定値の数は少なくなります。

## <a name="scheduling-a-triggered-webjob"></a>トリガーされた WebJobs のスケジュール設定

WebJobs では *settings.job* ファイルを使用して、WebJobs が実行されるタイミングを決定します。 WebJobs の実行スケジュールを設定する場合に、このファイルを使用します。 次の例では、午前 9 時から午後 5 時まで 1 時間ごとに実行されます。

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

このファイルは、`wwwroot\app_data\jobs\triggered\{job name}` または `wwwroot\app_data\jobs\continuous\{job name}` など、WebJobs のスクリプトと共に、WebJobs フォルダーのルートに配置する必要があります。 WebJobs を Visual Studio からデプロイする場合は、`settings.job` ファイルのプロパティを **[新しい場合はコピーする]** に設定してください。 

[Azure portal から WebJobs を作成する](webjobs-create.md)場合は、自動的に settings.job ファイルが作成されます。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON 式

WebJobs では、Azure Functions でのタイマー トリガーと同じ CRON 式がスケジュール設定で使用されます。 CRON サポートの詳細については、[タイマー トリガーのリファレンス記事](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)を参照してください。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>settings.job 参照

WebJobs では、次の設定がサポートされています。

| **設定** | **Type**  | **説明** |
| ----------- | --------- | --------------- |
| `is_in_place` | All | 最初に一時フォルダーにコピーすることなく、特定の時点でジョブを実行できます。 詳細については、「[WebJobs working directory](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)」 (WebJobs 作業ディレクトリ) を参照してください。 |
| `is_singleton` | 継続的 | WebJobs をスケールアウトした時にのみ、単一のインスタンスに対して実行します。詳細については、「[Set a continuous job as singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)」 (シングルトンとしての継続的なジョブの設定) を参照してください。 |
| `schedule` | トリガー | CRON ベースのスケジュールに従って、WebJobs を実行します。 詳細については、[タイマー トリガーのリファレンス記事](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)を参照してください。 |
| `stopping_wait_time`| All | シャットダウン動作の制御を許可します。 詳細については、「[Graceful shutdown](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)」 (正常なシャットダウン) を参照してください。 |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [WebJobs SDK の詳細を確認してください](webjobs-sdk-how-to.md)
