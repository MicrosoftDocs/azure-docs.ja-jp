---
title: Visual Studio を使用して Web ジョブの開発とデプロイを行う
description: Visual Studio で Azure Web ジョブを開発し、Azure App Service にデプロイする方法について、スケジュールされたタスクの作成を含めて説明します。
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89226031"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Visual Studio を使用して Web ジョブの開発とデプロイを行う

この記事では、Visual Studio を使用して、コンソール アプリ プロジェクトを [Azure Web ジョブ](https://go.microsoft.com/fwlink/?LinkId=390226)として [Azure App Service](overview.md) の Web アプリにデプロイする方法について説明します。 [Azure portal](https://portal.azure.com) を使用して Web ジョブをデプロイする方法については、「[Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する](webjobs-create.md)」を参照してください。

Web ジョブを開発するときには、[.NET Core アプリ](#webjobs-as-net-core-console-apps)または [.NET Framework アプリ](#webjobs-as-net-framework-console-apps)のどちらとして実行するかを選択できます。 バージョン 3.x の [Azure WebJobs SDK](webjobs-sdk-how-to.md) では .NET Core アプリと .NET Framework アプリのどちらかとして実行される WebJobs を開発できますが、バージョン 2.x では .NET Framework のみがサポートされます。 Web ジョブ プロジェクトをデプロイする方法は、.NET Core プロジェクトの場合と .NET Framework プロジェクトの場合では異なります。

1 つの Web アプリ内で各 Web ジョブが一意の名前を持つ場合は、複数の Web ジョブを 1 つの Web アプリに発行できます。

## <a name="webjobs-as-net-core-console-apps"></a>.NET Core コンソール アプリとしての WebJobs

バージョン 3.x の Azure WebJobs SDK を使用する場合、.NET Core コンソール アプリとしての Webジョブの作成と発行が可能です。 .NET Core コンソール アプリを作成し、Web ジョブとして Azure に発行する詳細な手順については、「[イベント ドリブンのバックグラウンド処理で Azure WebJobs SDK の使用を開始する](webjobs-sdk-get-started.md)」を参照してください。

> [!NOTE]
> .NET Core Web ジョブを Web プロジェクトにリンクすることはできません。 Web アプリと共に Web ジョブをデプロイする必要がある場合は、[.NET Framework コンソール アプリとして Web ジョブを作成します](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>Azure App Service にデプロイする

Visual Studio から Azure App Service への .NET Core Web ジョブの発行では、ASP.NET Core アプリの発行と同じツールを使用します。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>.NET Framework コンソール アプリとしての WebJobs  

Visual Studio を使用して、Web ジョブ対応の .NET Framework コンソール アプリ プロジェクトをデプロイする場合、Web アプリの適切なフォルダーにランタイム ファイルがコピーされます (継続的な Web ジョブの場合は *App_Data/jobs/continuous*、スケジュールされた Web ジョブやオンデマンド Web ジョブの場合は *App_Data/jobs/triggered*)。

Web ジョブ対応のプロジェクトには、Visual Studio によって以下の項目が追加されます。

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet パッケージ。
* デプロイメントとスケジューラの設定を含む [webjob-publish-settings.json](#publishsettings) ファイル。 

![Web ジョブとしてのデプロイを可能にするためにコンソール アプリに何が追加されるかを示す図](./media/webjobs-dotnet-deploy-vs/convert.png)

これらの項目を既存のコンソール アプリ プロジェクトに追加することも、テンプレートを使用して新しい Web ジョブ対応のコンソール アプリ プロジェクトを作成することもできます。 

プロジェクトは、それ自体を Web ジョブとしてデプロイするか、Web プロジェクトをデプロイするときには常に自動的にデプロイされるように Web プロジェクトにリンクします。 プロジェクトをリンクする場合、Visual Studio で、Web プロジェクト内の [webjobs-list.json](#webjobslist) ファイルに Web ジョブ対応のプロジェクトの名前が含められます。

![Diagram showing WebJob project linking to web project](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>前提条件

[Azure 開発ワークロード](/visualstudio/install/install-visual-studio#step-4---choose-workloads)と共に Visual Studio 2017 または Visual Studio 2019 をインストールします。

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> 既存のコンソール アプリ プロジェクトに対する Web ジョブのデプロイを有効にする

2 つのオプションがあります。

* [Web プロジェクトを使用した自動デプロイメントを有効にする](#convertlink)。

  Web プロジェクトをデプロイしたときに自動的に Web ジョブとしてデプロイされるように、既存のコンソール アプリ プロジェクトを構成します。 Web ジョブを、関連する Web アプリケーションが実行する Web アプリケーションと同じアプリケーションで実行する場合に、このオプションを使用します。

* [Web プロジェクトなしでデプロイメントを有効にする](#convertnolink)。

  既存のコンソール アプリ プロジェクトを、Web プロジェクトにリンクせず、それ自体が Web ジョブとしてデプロイされるように構成します。 Web アプリケーションで Web アプリケーションを実行せずに、Web ジョブ自身によって Web アプリケーション内で実行されようにする場合に、このオプションを使用します。 Web アプリケーションのリソースから独立して Web ジョブのリソースをスケーリングする場合は、そうすることをお勧めします。

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Web プロジェクトを使用した Web ジョブの自動デプロイメントを有効にする

1. **ソリューション エクスプローラー** で Web プロジェクトを右クリックし、 **[追加]**  >  **[既存のプロジェクトを Azure Web ジョブとして]** を選択します。
   
    ![[既存のプロジェクトを Azure Web ジョブとして]](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスが表示されます。
2. **[プロジェクト名]** ドロップダウン リストでコンソール アプリ プロジェクトを選択し、Web ジョブとして追加します。
   
    ![[Azure Web ジョブの追加] ダイアログ ボックスでのプロジェクトの選択](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスでの指定を完了し、 **[OK]** を選択します。 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Web プロジェクトなしで Web ジョブ デプロイメントを有効にする
1. **ソリューション エクスプローラー** でコンソール アプリ プロジェクトを右クリックし、 **[Azure WebJob として発行]** を選択します。 
   
    ![[Azure Web ジョブとして発行]](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスが表示され、 **[プロジェクト名]** ボックスに選択されたプロジェクトが示されます。
2. [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスでの指定を完了し、 **[OK]** を選択します。
   
   **Web の発行** ウィザードが表示されます。 すぐに発行しない場合は、ウィザードを閉じます。 [プロジェクトをデプロイする](#deploy)ときのために、入力した設定値は保存されます。

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>新しい Web ジョブ対応のプロジェクトを作成する
新しい Web ジョブ対応のプロジェクトを作成するには、[前のセクション](#convert)で説明したように、コンソール アプリ プロジェクト テンプレートを使用して、Web ジョブのデプロイを有効にします。 または、次のように Web ジョブの新しいプロジェクト テンプレートを使用できます。

* [独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する](#createnolink)
  
    プロジェクトを作成し、Web プロジェクトへはリンクせずに、それ自身で Web ジョブとしてデプロイされるように構成します。 Web アプリケーションで Web アプリケーションを実行せずに、Web ジョブ自身によって Web アプリケーション内で実行されようにする場合に、このオプションを使用します。 Web アプリケーションのリソースから独立して Web ジョブのリソースをスケーリングする場合は、そうすることをお勧めします。
* [Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する](#createlink)
  
    同じソリューション内の Web プロジェクトをデプロイするときに Web ジョブとして自動的にデプロイするように構成して、プロジェクトを作成します。 Web ジョブを、関連する Web アプリケーションが実行する Web アプリケーションと同じアプリケーションで実行する場合に、このオプションを使用します。

> [!NOTE]
> WebJobs の new-project テンプレートは、NuGet パッケージを自動的にインストールし、 *WebJobs SDK* 用にコードを [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)に含めます。 WebJobs SDK を使用しない場合は、*Program.cs* 内の `host.RunAndBlock` ステートメントを削除または変更します。
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する
1. **[File]**  >  **[New]**  >  **[Project]** の順に選択します。 **[新しいプロジェクトの作成]** ダイアログ ボックスで、C# 用の **Azure Web ジョブ (.NET Framework)** を検索して選択します。
   
2. 以前の指示に従って、[コンソール アプリ プロジェクトを独立した Web ジョブ プロジェクトにします](#convertnolink)。

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する
1. **ソリューション エクスプローラー** で Web プロジェクトを右クリックし、 **[追加]**  >  **[新しい Azure Web ジョブ プロジェクト]** を選択します。
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスが表示されます。
2. [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスでの指定を完了し、 **[OK]** を選択します。


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.json ファイル
Web ジョブのデプロイ用にコンソール アプリを構成すると、Visual Studio によって [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet パッケージがインストールされ、Web ジョブ プロジェクトの *Properties* フォルダーにある *webjob-publish-settings.json* ファイルにスケジュール情報が格納されます。 次にこのファイルの例を示します。

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。 ファイル スキーマは [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) に格納され、そこで表示できます。  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.json ファイル
Web ジョブ対応のプロジェクトを Web プロジェクトにリンクすると、Visual Studio は Web ジョブ プロジェクトの名前を Web プロジェクトの *Properties* フォルダー内の *webjobs-list.json* ファイルに格納します。 次の例に示すように、一覧には複数の WebJobs プロジェクトが含まれる場合があります。

```json
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
```

このファイルは、Visual Studio で IntelliSense を使用して、直接編集することができます。 ファイル スキーマは [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) に格納されています。

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Web ジョブ プロジェクトをデプロイする
Web プロジェクトにリンクした Web ジョブ プロジェクトは、その Web プロジェクトと共に自動的にデプロイされます。 Web プロジェクトのデプロイについては、左側のナビゲーションの **[ハウツー ガイド]**  >  **[アプリのデプロイ]** を参照してください。

Web ジョブ プロジェクトをそれ自体でデプロイするには、**ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[Azure Web ジョブとして発行]** をクリックします。 

![[Azure Web ジョブとして発行]](./media/webjobs-dotnet-deploy-vs/paw.png)

独立した Web ジョブの場合は、Web プロジェクトで使用されたウィザードと同じ **Web の発行** ウィザードが表示されますが、変更可能な設定値の数は少なくなります。

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>[Azure Web ジョブの追加] ダイアログ ボックス
**[Azure Web ジョブの追加]** ダイアログでは、Web ジョブの Web ジョブ名と実行モード設定を入力できます。 

![[Azure Web ジョブの追加] ダイアログ ボックス](./media/webjobs-dotnet-deploy-vs/aaw2.png)

このダイアログ ボックスのフィールドの一部は、Azure portal の **[Web ジョブの追加]** ダイアログ ボックスのフィールドに対応しています。 詳細については、「[Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する](webjobs-create.md)」を参照してください。

Web ジョブのデプロイ情報:

* コマンド ライン デプロイメントについては、「 [Enabling Command-line or Continuous Delivery of Azure WebJobs (Azure Web ジョブのコマンド ラインによる配信または継続的配信を有効にする)](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)」を参照してください。

* Web ジョブをデプロイし、その後、Web ジョブの種類を変更して再デプロイすることに決めた場合は、*webjobs-publish-settings.json* ファイルを削除します。 そうすることで、Visual Studio に発行オプションが再表示されるので、Web ジョブの種類を変更できます。

* Web ジョブをデプロイし、後から実行モードを継続的から継続的以外、またはその逆に変更した場合、Visual Studio は再デプロイ時に Azure 内に新しい Web ジョブを作成します。 その他のスケジュール設定を変更し、実行モードを同じままにするか、[スケジュール済み] と [オンデマンド] の間で切り替えた場合、Visual Studio では、新しいジョブは作成されずに既存のジョブが更新されます。

## <a name="webjob-types"></a>Web ジョブの種類

Web ジョブの種類は、*トリガー* または *継続的* です。

- トリガー (既定値):トリガー Web ジョブは、バインディング イベントまたは[スケジュール](#scheduling-a-triggered-webjob)に基づいて開始されるか、手動でトリガーされたときに開始されます (オンデマンド)。 Web アプリが実行されている 1 つのインスタンスで実行されます。

- 継続的:[継続的](#continuous-execution) Web ジョブは、作成されるとすぐに開始されます。 既定では、すべての Web アプリのスケーリングされたインスタンスで実行されますが、*settings.job* を使用して 1 つのインスタンスとして実行するように構成できます。

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>トリガーされた Web ジョブのスケジュール設定

コンソール アプリを Azure に発行すると、Visual Studio によって Web ジョブの種類が既定で **[トリガー]** に設定され、プロジェクトに新しい *settings.job* ファイルが追加されます。 Web ジョブの種類がトリガーの場合は、このファイルを使用して Web ジョブの実行スケジュールを設定できます。

*settings.job* ファイルを使用して、Web ジョブの実行スケジュールを設定します。 次の例では、午前 9 時から午後 5 時まで 1 時間ごとに実行されます。

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

このファイルは、Web ジョブのスクリプトと一緒に、`wwwroot\app_data\jobs\triggered\{job name}` や `wwwroot\app_data\jobs\continuous\{job name}` などの Web ジョブ フォルダーのルートに置かれます。 Web ジョブを Visual Studio からデプロイする場合は、Visual Studio で、*settings.job* ファイルのプロパティを **[新しい場合はコピーする]** に設定します。

[Azure portal から Web ジョブを作成する](webjobs-create.md)場合は、自動的に *settings.job* ファイルが作成されます。

#### <a name="cron-expressions"></a>CRON 式

WebJobs では、Azure Functions でのタイマー トリガーと同じ CRON 式がスケジュール設定で使用されます。 CRON サポートの詳細については、「[Azure Functions のタイマー トリガー](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)」を参照してください。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>settings.job 参照

WebJobs では、次の設定がサポートされています。

| **設定** | **Type**  | **説明** |
| ----------- | --------- | --------------- |
| `is_in_place` | All | 最初に一時フォルダーにコピーすることなく、そのままの場所で Web ジョブを実行できます。 詳細については、「[Web ジョブの作業ディレクトリ](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)」を参照してください。 |
| `is_singleton` | 継続的 | スケールアウトされるときは、単一インスタンス上でのみ Web ジョブが実行されます。詳細については、「[継続的ジョブをシングルトンとして設定する](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)」を参照してください。 |
| `schedule` | トリガー | CRON ベースのスケジュールに従って、Web ジョブを実行します。 詳細については、「[NCRONTAB 式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)」を参照してください。 |
| `stopping_wait_time`| All | シャットダウン動作の制御を許可します。 詳細については、[グレースフル シャットダウン](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)に関する記事を参照してください。 |

### <a name="continuous-execution"></a>継続的な実行

Azure で **Always On** を有効にする場合は、Visual Studio を使用して、継続的に実行するように Web ジョブを変更できます。

1. [Azure にプロジェクトを発行](#deploy-to-azure-app-service)していない場合は、発行します。

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行]** タブで、 **[編集]** を選択します。 

1. **[プロファイル設定]** ダイアログ ボックスの **[Web ジョブの種類]** で **[継続的]** を選択してから、 **[保存]** を選択します。

    ![Web ジョブの発行設定のダイアログ ボックス](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. **[発行]** タブで **[発行]** を選択し、設定が更新された Web ジョブを再発行します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [WebJobs SDK の詳細を確認してください](webjobs-sdk-how-to.md)