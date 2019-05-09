---
title: アプリの診断ログの有効化 - Azure App Service
description: 診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログ記録された情報にアクセスする方法を説明します。
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894920"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service でのアプリの診断ログの有効化
## <a name="overview"></a>概要
Azure では、組み込みの診断機能により、 [App Service アプリ](https://go.microsoft.com/fwlink/?LinkId=529714)のデバッグを容易に行うことができます。 この記事では、診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログに記録された情報にアクセスする方法について説明します。

この記事では、[Azure portal](https://portal.azure.com) と Azure CLI を使用して診断ログを操作します。 Visual Studio で診断ログを使用する方法の詳細については、「 [Visual Studio での Azure のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)」を参照してください。

## <a name="whatisdiag"></a>Web サーバーの診断とアプリケーション診断
App Service は、Web サーバーと Web アプリケーションの両方のログ情報を診断する機能を備えています。 これらは論理的に **Web サーバー診断**と**アプリケーション診断**に分けられます。

### <a name="web-server-diagnostics"></a>Web サーバー診断
次の種類のログを有効または無効にできます。

* **詳細なエラー ログ記録** - HTTP 状態コードが 400 以上になる任意の要求の詳細情報。 これには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。 1 つの HTML ファイルが、アプリのファイル システム内の各エラーに対して生成され、最大 50 個のエラー (ファイル) が保持されます。 HTML ファイルの数が 50 を超えた場合、古い順に 26 ファイルが自動的に削除されます。
* **失敗した要求トレース** - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。 これは、サイトのパフォーマンスを向上させたり、特定の HTTP エラーを分離したりする場合に役立ちます。 アプリのファイル システム内に、各エラーに対して 1 つのフォルダーが生成されます。 ファイルの保持ポリシーは、上述した詳細なエラー ログ記録と同じです。
* **Web サーバーのログ記録** - [W3C 拡張ログ ファイル形式](/windows/desktop/Http/w3c-logging)を使用した、HTTP トランザクションに関する情報。 これは、処理された要求の数や特定の IP アドレスからの要求の数などの、全体的なサイト メトリックを調べるときに役立ちます。

### <a name="application-diagnostics"></a>アプリケーション診断
アプリケーション診断では、Web アプリケーションによって生成された情報を取り込むことができます。 ASP.NET アプリケーションは、 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) クラスを使用して、情報をアプリケーション診断ログに記録できます。 例: 

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

実行時にこれらのログを取得してトラブルシューティングに役立てることができます。 詳細については、 [Visual Studio での Azure App Service のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)に関するページを参照してください。

App Service では、アプリにコンテンツを発行したときのデプロイ情報もログに記録されます。 これは自動的に行われ、デプロイ ログの構成設定はありません。 デプロイ ログでは、デプロイが失敗した理由を特定できます。 たとえば、カスタムのデプロイ スクリプトを使用している場合は、デプロイ ログを使用して、スクリプトでエラーが発生する理由を特定できることがあります。

## <a name="enablediag"></a>診断を有効にする方法
[Azure portal](https://portal.azure.com) で診断を有効にするには、アプリのページに移動し、**[設定]、[診断ログ]** の順にクリックします。

<!-- todo:cleanup dogfood addresses in screenshot -->
![ログ パーツ](./media/web-sites-enable-diagnostic-log/logspart.png)

**アプリケーション診断**を有効にするときに、**レベル**も選択できます。 次の表に、各レベルに含まれるログのカテゴリを示します。

| Level| 含まれるログのカテゴリ |
|-|-|
|**Disabled** | なし |
|**Error** | エラー、クリティカル |
|**警告** | 警告、エラー、クリティカル|
|**情報** | 情報、警告、エラー、クリティカル|
|**詳細** | トレース、デバッグ、情報、警告、エラー、クリティカル (すべてのカテゴリ) |
|-|-|

**アプリケーション ログ**の場合、デバッグ目的で、一時的にファイル システム オプションを有効にできます。 このオプションは、12 時間で自動的にオフにされます。 BLOB ストレージ オプションを有効にして、ログを書き込む BLOB コンテナーを選ぶこともできます。

> [!NOTE]
> 現在、Blob Storage には .NET アプリケーションのログのみ書き込むことができます。 Java、PHP、Node.js、Python のアプリケーション ログは、(外部ストレージにログを書き込むようにコードを変更することなく) ファイル システム上にのみ保存できます。
>
>

**Web サーバー ログ**の場合、**[ストレージ]** または **[ファイル システム]** を選択できます。 **[ストレージ]** を選択すると、ストレージ アカウントを選択でき、ログ書き込み先の BLOB コンテナーを指定できます。 

ログをファイル システムに保存した場合、これらのファイルは、FTP によってアクセスするか、Azure CLI を使用して Zip アーカイブとしてダウンロードできます。

既定で、ログは自動的に削除されません (**アプリケーション ログ (ファイルシステム)** を除く)。 ログを自動的に削除するには、**[リテンション期間 (日)]** フィールドを設定します。

> [!NOTE]
> [ストレージ アカウントのアクセス キーを再生成する](../storage/common/storage-create-storage-account.md)場合は、該当するログ構成を更新後のキーを使用するように設定し直す必要があります。 これを行うには、次の手順を実行します。
>
> 1. **[構成]** タブで、該当するログ機能を **[オフ]** に設定します。 設定を保存します。
> 2. ストレージ アカウントの BLOB へのログを再び有効にします。 設定を保存します。
>
>

ファイル システムまたは BLOB ストレージへのログ記録は、任意に組み合わせて同時に有効にすることができます。また、それぞれ個別にログ レベルを設定できます。 たとえば、BLOB ストレージへのエラーと警告の長期間のログ記録、ファイル システムへの詳細レベルのログ記録を同時に有効にすることができます。

どちらのログ書き込み先でもログ記録されたイベントについて同じ基本的な情報が得られますが、**BLOB ストレージ**には、インスタンス ID、スレッド ID、より詳細なタイムスタンプ (目盛り形式) など、追加の情報がログ記録されます。**ファイル システム**には、このような情報はログ記録されません。

> [!NOTE]
> **BLOB ストレージ**に格納されている情報には、これらのストレージ システムを直接操作できるストレージ クライアントまたはアプリケーションからアクセスできます。 たとえば、Visual Studio 2013 のストレージ エクスプローラーを使用すると、BLOB ストレージを操作できます。HDInsight を使用すると、BLOB ストレージに格納されているデータにアクセスできます。 [Azure SDK](https://azure.microsoft.com/downloads/) のいずれかを使用して、Azure Storage にアクセスするアプリケーションを記述することもできます。
>

## <a name="download"></a> 方法:ログのダウンロード
アプリケーション ファイル システムに保存された診断情報には、FTP を使用して直接アクセスできます。 Azure CLI を使用して Zip アーカイブとしてダウンロードすることもできます。

ログが保存されるディレクトリ構造は次のとおりです。

* **アプリケーション ログ** : /LogFiles/Application/。 このフォルダーには、アプリケーション ログによって生成された情報を含む 1 つ以上のテキスト ファイルが格納されます。
* **失敗した要求トレース** : /LogFiles/W3SVC#########/。 このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。 この XSL ファイルは、XML ファイルが Internet Explorer で表示されるときに、コンテンツの書式設定とフィルター処理を行う役割を果たすため、必ず XML ファイルと同じディレクトリにダウンロードしてください。
* **詳細なエラー ログ** : /LogFiles/DetailedErrors/。 このフォルダーには、発生した HTTP エラーに関する詳細な情報を記録した 1 つ以上の .htm ファイルが格納されます。
* **Web サーバー ログ** : /LogFiles/http/RawLogs。 このフォルダーには、 [W3C 拡張ログ ファイル形式](/windows/desktop/Http/w3c-logging)を使用して形式が設定された 1 つ以上のテキスト ファイルが格納されます。
* **デプロイ ログ** : /LogFiles/Git。 このフォルダーには、Git デプロイのログだけでなく、Azure App Service が使用する内部デプロイ プロセスによって生成されたログも格納されます。 デプロイ ログは D:\home\site\deployments にもあります。

### <a name="ftp"></a>FTP

アプリの FTP サーバーへの FTP 接続を開くには、「[FTP/S を使用した Azure App Service へのアプリのデプロイ](deploy-ftp.md)」を参照してください。

アプリの FTP/S サーバーに接続したら、ログ ファイルが格納されている **LogFiles** フォルダーを開きます。

### <a name="download-with-azure-cli"></a>Azure CLI を使用してダウンロードする
Azure コマンド ライン インターフェイスを使用してログ ファイルをダウンロードするには、新しいコマンド プロンプト、PowerShell、Bash、ターミナル セッションを開き、次のコマンドを入力します。

    az webapp log download --resource-group resourcegroupname --name appname

このコマンドで、"appname" という名前のアプリのログが、現在のディレクトリにある **webapp_logs.zip** というファイルに保存されます。

> [!NOTE]
> Azure CLI をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、[Azure CLI の使用方法](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)に関するページをご覧ください。
>
>

## <a name="how-to-view-logs-in-application-insights"></a>方法:Application Insights のログの表示
Visual Studio Application Insights には、ログをフィルターおよび検索したり、要求やその他のイベントにログを関連付けたりするためのツールが用意されています。

1. Application Insights SDK を Visual Studio のプロジェクトに追加します。
   * ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。 Application Insights リソースの作成などの手順が示されます。 [詳細情報](../azure-monitor/app/asp-net.md)
2. トレース リスナーのパッケージをプロジェクトに追加します。
   * プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。 ログを自動的に削除するには、 `Microsoft.ApplicationInsights.TraceListener` [詳細情報](../azure-monitor/app/asp-net-trace-logs.md)
3. プロジェクトをアップロードして実行し、ログ データを生成します。
4. [Azure Portal](https://portal.azure.com/) で、新しい Application Insights リソースを参照し、**[検索]** を開きます。 ログ データが、要求、使用状況、およびその他の製品利用統計情報と共に表示されます。 一部の製品利用統計情報については、表示されるまで数分かかる場合があります。[更新] をクリックします。 [詳細情報](../azure-monitor/app/diagnostic-search.md)

[Application Insights でのパフォーマンス追跡についての詳細情報](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> 方法:ログのストリーミング
アプリケーションの開発中に、ログ情報をほぼリアルタイムで参照すると役立つことがよくあります。 Azure CLI を使用して、開発環境にログ情報をストリーミングすることができます。

> [!NOTE]
> 一部の種類のログ バッファーはログ ファイルに書き込まれるため、ストリーミング中に無効な順序エラーが発生する可能性があります。 たとえば、ユーザーがページにアクセスしたときに発生するアプリケーション ログ エントリは、ページ要求の該当する HTTP ログ エントリより前のストリームに表示されることがあります。
>
> [!NOTE]
> ログのストリーミングでは、**D:\\home\\LogFiles\\** フォルダーに格納されているテキスト ファイルに書き込まれた情報もストリーミングされます。
>
>

### <a name="streaming-with-azure-cli"></a>Azure CLI を使用してストリーミングする
ログ情報をストリーミングするには、新しいコマンド プロンプト、PowerShell、Bash、またはターミナル セッションを開き、次のコマンドを入力します。

    az webapp log tail --name appname --resource-group myResourceGroup

このコマンドで、"appname" という名前のアプリに接続され、ログ イベントがアプリで発生したら、ウィンドウへの情報のストリーミングが開始されます。 /LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、 **-Filter** パラメーターを使用します。 例: 

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

特定のログの種類 (HTTP など) をフィルター処理するには、 **--Path** パラメーターを使用します。 例: 

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Azure CLI をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、[Azure CLI の使用方法](../cli-install-nodejs.md)に関するページをご覧ください。
>
>

## <a name="understandlogs"></a> 方法:診断ログの解釈
### <a name="application-diagnostics-logs"></a>アプリケーション診断ログ
アプリケーション診断では、ファイル システムと BLOB ストレージのどちらにログを保存するかに応じて、.NET アプリケーション向けの一定の形式で情報が保存されます。 

格納される一連の基本的なデータは、両方の種類のストレージ間で同じで、イベントが発生した日時、イベントを生成したプロセスの ID、イベントの種類 (情報、警告、エラー)、イベントのメッセージです。 問題のトラブルシューティングに直ちにアクセスする必要がある場合は、ログ ストレージにファイル システムを使用するとログ ファイルがほぼ瞬時に更新されるため便利です。 BLOB ストレージは、ファイルがキャッシュされ、スケジュールに従ってストレージ コンテナーにフラッシュされるため、アーカイブの目的に使用されます。

**ファイル システム**

ファイル システムにログが記録される行またはストリーミングによって受信する行は、それぞれ以下の形式になります。

    {Date}  PID[{process ID}] {event type/level} {message}

たとえば、エラー イベントは次の例のように表示されます。

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

ファイル システムにログ記録する場合は、使用できる 3 つのログ記録方法のうちで最も基本的な情報が提供され、時間、プロセス ID、イベント レベル、メッセージのみを確認できます。

**BLOB ストレージ**

BLOB ストレージにログを記録するときには、値をコンマで区切った (CSV) 形式で格納されます。 追加のフィールドがログに記録されて、イベントについてより詳細な情報が提供されます。 CSV 内の各行に次のプロパティが使用されます。

| プロパティ名 | 値/形式 |
| --- | --- |
| Date |イベントが発生した日時 |
| Level |イベント レベル (例: エラー、警告、情報) |
| ApplicationName |アプリの名前 |
| InstanceId |イベントが発生したアプリのインスタンス |
| EventTickCount |イベントが発生した目盛り形式 (高精度) の日時 |
| EventId |このイベントのイベント ID<p><p>何も指定しない場合は既定で 0 |
| Pid |プロセス ID |
| Tid |イベントを生成したスレッドの ID |
| Message |イベントの詳細メッセージ |

BLOB に格納されるデータは次の例のようになります。

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> ASP.NET Core の場合、ログ記録は [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダーを使用して実現されます。このプロバイダーは、BLOB コンテナーに追加のログ ファイルを置きます。 詳しくは、[Azure 内での ASP.NET Core のログ記録](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure)に関するページをご覧ください。
>
>

### <a name="failed-request-traces"></a>失敗した要求トレース
失敗した要求トレースは **fr######.xml** という名前の XML ファイルに保存されます。 ログに記録された情報を見やすくするには、**freb.xsl** という名前の XSL スタイルシートを XML ファイルと同じディレクトリに配置します。 Internet Explorer でいずれかの XML ファイルを開くと、次の例のように、トレース情報が XSL スタイルシートを使用して書式設定されて表示されます。

![失敗した要求をブラウザーで表示したところ](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> 失敗した要求の書式設定済みのトレースを表示する簡単な方法として、ポータル内のアプリのページに移動します。 左側のメニューから、**[問題の診断と解決]** を選択し、**[Failed Request Tracing Logs]\(失敗した要求トレースのログ\)** を検索し、アイコンをクリックして目的のトレースを参照して表示します。
>

### <a name="detailed-error-logs"></a>詳細なエラー ログ
詳細なエラー ログは、発生した HTTP エラーに関する詳細な情報を提供する HTML ドキュメントです。 単なる HTML ドキュメントであるため、Web ブラウザーを使用して表示できます。

### <a name="web-server-logs"></a>Web サーバー ログ
Web サーバー ログは [W3C 拡張ログ形式](/windows/desktop/Http/w3c-logging)(.aspx) で書式設定されます。 この情報は、テキスト エディターを使用して表示したり、 [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619)などのユーティリティで解析したりできます。

> [!NOTE]
> Azure App Service によって生成されるログでは、**s-computername**、**s-ip**、または **cs-version** のフィールドはサポートされていません。
>
>

## <a name="nextsteps"></a> 次のステップ
* [Azure App Service を監視する方法](web-sites-monitor.md)
* [Visual Studio での Azure App Service のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)
* [HDInsight でのアプリ ログの分析](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
