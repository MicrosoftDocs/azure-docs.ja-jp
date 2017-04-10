---
title: "Azure App Service の Web アプリの診断ログの有効化"
description: "診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログ記録された情報にアクセスする方法を説明します。"
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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 6a000e84f38e7e2b56f1d79d5c88f1704b6789a0
ms.lasthandoff: 03/21/2017


---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Azure App Service の Web アプリの診断ログの有効化
## <a name="overview"></a>概要
Azure では、組み込みの診断機能により、 [App Service Web アプリ](http://go.microsoft.com/fwlink/?LinkId=529714)のデバッグを容易に行うことができます。 この記事では、診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログに記録された情報にアクセスする方法について説明します。

この記事では、 [Azure ポータル](https://portal.azure.com)、Azure PowerShell、Azure コマンド ライン インターフェイス (Azure CLI) で診断ログを使用する方法を示します。 Visual Studio で診断ログを使用する方法の詳細については、「 [Visual Studio での Azure のトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)」を参照してください。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web サーバーの診断とアプリケーション診断
App Service Web Apps は、Web サーバーと Web アプリケーションの両方のログ情報を診断する機能を備えています。 これらは論理的に **Web サーバー診断**と**アプリケーション診断**に分けられます。

### <a name="web-server-diagnostics"></a>Web サーバー診断
次の種類のログを有効または無効にできます。

* **詳細なエラー ログ** - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。 このログには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。
* **失敗した要求トレース** - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。 このログが便利なのは、サイトのパフォーマンスを向上させたり、特定の HTTP エラーが返される理由を特定したりする場合です。
* **Web サーバーのログ記録** - [W3C 拡張ログ ファイル形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用した、HTTP トランザクションに関する情報。 このレポートが便利なのは、全体的なサイト メトリック、たとえば、サイトで処理された要求の数や、特定の IP アドレスからの要求の数を特定するときです。

### <a name="application-diagnostics"></a>アプリケーション診断
アプリケーション診断では、Web アプリケーションによって生成された情報を取り込むことができます。 ASP.NET アプリケーションは、 [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) クラスを使用して、情報をアプリケーション診断ログに記録できます。 For example:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

実行時にこれらのログを取得してトラブルシューティングに役立てることができます。 詳細については、 [Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関するページを参照してください。

App Service Web Apps は、Web アプリにコンテンツをパブリッシュしたときのデプロイ情報もログに記録します。 これは自動的に行われ、展開ログの構成設定はありません。 デプロイ ログでは、デプロイが失敗した理由を特定できます。 たとえば、カスタムのデプロイ スクリプトを使用している場合は、デプロイ ログを使用して、スクリプトでエラーが発生する理由を特定できることがあります。

## <a name="enablediag"></a>診断を有効にする方法
[Azure Portal](https://portal.azure.com) で診断を有効にするには、Web アプリのブレードに移動し、**[設定]、[診断ログ]** の順にクリックします。

<!-- todo:cleanup dogfood addresses in screenshot -->
![ログ パーツ](./media/web-sites-enable-diagnostic-log/logspart.png)

**アプリケーション診断**を有効にするときに、**レベル**も選択できます。 この設定を使用して、取得された情報を、**情報**、**警告**、または**エラー情報**にフィルター処理できます。 これを **詳細** に設定すると、アプリケーションにより生成されるすべての情報がログに記録されます。

> [!NOTE]
> web.config ファイルの変更とは異なり、アプリケーション診断の有効化や診断ログ レベルの変更によって、アプリケーションが実行されているアプリケーション ドメインがリサイクルされることはありません。
>
>

[クラシック ポータル](https://manage.windowsazure.com)の Web アプリの **[構成]** タブで、**Web サーバーのログ採取**用の**ストレージ**または**ファイル システム**を選択できます。 **[ストレージ]** を選択すると、ストレージ アカウントを選択でき、ログ書き込み先の BLOB コンテナーを指定できます。 **サイト診断** 用のその他のすべてのログはファイル システムにのみ書き込まれます。

[クラシック ポータル](https://manage.windowsazure.com) の Web アプリの **[構成]** タブには、アプリケーション診断用のその他の設定も含まれています。

* **ファイル システム** - アプリケーション診断情報が Web アプリのファイル システムに保存されます。 これらのファイルは、FTP によってアクセスするか、Azure PowerShell または Azure コマンド ライン ツールを使用して Zip アーカイブとしてダウンロードできます。
* **テーブル ストレージ** - 指定された Azure ストレージ アカウントおよびテーブル名にアプリケーション診断情報が保存されます。
* **BLOB ストレージ** - 指定された Azure ストレージ アカウントおよび BLOB コンテナーにアプリケーション診断情報が保存されます。
* **リテンション期間** - 既定では、**Blob Storage** からログが自動的に削除されることはありません。 ログを自動的に削除するには、 **[保有期間の設定]** を選択して、ログを保有する日数を入力します。

> [!NOTE]
> [ストレージ アカウントのアクセス キーを再生成する](../storage/storage-create-storage-account.md)場合は、該当するログ構成を更新後のキーを使用するように設定し直す必要があります。 これを行うには、次の手順を実行します。
>
> 1. **[構成]** タブで、該当するログ機能を **[オフ]** に設定します。 設定を保存します。
> 2. ストレージ アカウントの BLOB またはテーブルへのログを再び有効にします。 設定を保存します。
>
>

ファイル システム、テーブル ストレージ、BLOB ストレージへのログ記録は、任意に組み合わせて同時に有効にすることができます。また、それぞれ個別にログ レベルを設定できます。 たとえば、BLOB ストレージへのエラーと警告の長期間のログ記録、ファイル システムへの詳細レベルのログ記録を同時に有効にすることができます。

ログ書き込み先の&3; つの場所のいずれでも、ログ記録されたイベントについて同じ基本的な情報が得られますが、**テーブル ストレージ**と **Blob Storage** には、インスタンス ID、スレッド ID、より詳細なタイムスタンプ (目盛り形式) など、追加の情報がログ記録されます。**ファイル システム**には、このような情報はログ記録されません。

> [!NOTE]
> **テーブル ストレージ**または **Blob  Storage** に格納されている情報には、これらのストレージ システムを直接操作できるストレージ クライアントまたはアプリケーションからアクセスできます。 たとえば、Visual Studio 2013 のストレージ エクスプローラーを使用すると、テーブル ストレージまたは BLOB ストレージを操作できます。HDInsight を使用すると、BLOB ストレージに格納されているデータにアクセスできます。 [Azure SDK](/downloads/#) のいずれかを使用して、Azure Storage にアクセスするアプリケーションを記述することもできます。
>
> [!NOTE]
> 診断を有効にするには、Azure PowerShell から **Set-AzureWebsite** コマンドレットを使用する方法もあります。 Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、 [Azure PowerShell の使用方法](/develop/nodejs/how-to-guides/powershell-cmdlets/)に関するページを参照してください。
>
>

## <a name="download"></a> ログをダウンロードする方法
Web アプリケーション ファイル システムに保存された診断情報には、FTP を使用して直接アクセスできます。 さらに、Azure PowerShell または Azure コマンド ライン インターフェイスを使用して Zip アーカイブとしてダウンロードすることもできます。

ログが保存されるディレクトリ構造は次のとおりです。

* **アプリケーション ログ** : /LogFiles/Application/。 このフォルダーには、アプリケーション ログによって生成された情報を含む&1; つ以上のテキスト ファイルが格納されます。
* **失敗した要求トレース** : /LogFiles/W3SVC#########/。 このフォルダーには、1 つの XSL ファイルと&1; つ以上の XML ファイルが格納されます。 この XSL ファイルは、XML ファイルが Internet Explorer で表示されるときに、コンテンツの書式設定とフィルター処理を行う役割を果たすため、必ず XML ファイルと同じディレクトリにダウンロードしてください。
* **詳細なエラー ログ** : /LogFiles/DetailedErrors/。 このフォルダーには、発生した HTTP エラーに関する詳細な情報を記録した&1; つ以上の .htm ファイルが格納されます。
* **Web サーバー ログ** : /LogFiles/http/RawLogs。 このフォルダーには、 [W3C 拡張ログ ファイル形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用して形式が設定された&1; つ以上のテキスト ファイルが格納されます。
* **デプロイ ログ** : /LogFiles/Git。 このフォルダーには、Git デプロイのログだけでなく、Azure Web Apps が使用する内部デプロイ プロセスによって生成されたログも格納されます。

### <a name="ftp"></a>FTP
FTP を使用して診断情報にアクセスするには、 **クラシック ポータル** で Web アプリの [ダッシュボード](https://manage.windowsazure.com)にアクセスします。 **[概要]** セクションで、**[FTP 診断ログ]** リンクを使用し、FTP を使用してログ ファイルにアクセスします。 **[デプロイ/FTP ユーザー]** エントリには、FTP サイトへのアクセスに使用するユーザー名が一覧表示されます。

> [!NOTE]
> **[デプロイ/FTP ユーザー]** エントリが設定されていない場合や、このユーザーのパスワードを忘れた場合は、**ダッシュボード**の **[概要]** セクションで **[デプロイ資格情報のリセット]** リンクを使用すると、新しいユーザーとパスワードを作成できます。
>
>

### <a name="download-with-azure-powershell"></a>Azure PowerShell を使用してダウンロードする
ログ ファイルをダウンロードするには、Azure PowerShell の新しいインスタンスを開始し、次のコマンドを使用します。

    Save-AzureWebSiteLog -Name webappname

これにより、**-Name** パラメーターにより指定された Web アプリのログが、現在のディレクトリにある **logs.zip** というファイルに保存されます。

> [!NOTE]
> Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、 [Azure PowerShell の使用方法](/develop/nodejs/how-to-guides/powershell-cmdlets/)に関するページを参照してください。
>
>

### <a name="download-with-azure-command-line-interface"></a>Azure コマンド ライン インターフェイスを使用してダウンロードする
Azure コマンド ライン インターフェイスを使用してログ ファイルをダウンロードするには、新しいコマンド プロンプト、PowerShell、Bash、ターミナル セッションを開き、次のコマンドを入力します。

    azure site log download webappname

これにより、"webappname" という名前の Web アプリのログが、現在のディレクトリにある **diagnostics.zip** というファイルに保存されます。

> [!NOTE]
> Azure コマンド ライン インターフェイス (Azure CLI) をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、 [Azure CLI の使用方法](../cli-install-nodejs.md)に関するページを参照してください。
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Application Insights でログを表示する方法
Visual Studio Application Insights には、ログをフィルターおよび検索したり、要求やその他のイベントにログを関連付けたりするためのツールが用意されています。

1. Application Insights SDK を Visual Studio のプロジェクトに追加します。
   * ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。 Application Insights リソースの作成などの手順が示されます。 [詳細情報](../application-insights/app-insights-asp-net.md)
2. トレース リスナーのパッケージをプロジェクトに追加します。
   * プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。 ログを自動的に削除するには、 `Microsoft.ApplicationInsights.TraceListener` [詳細情報](../application-insights/app-insights-asp-net-trace-logs.md)
3. プロジェクトをアップロードして実行し、ログ データを生成します。
4. [Azure Portal](https://portal.azure.com/) で、新しい Application Insights リソースを参照し、**[検索]** を開きます。 ログ データが、要求、使用状況、およびその他の製品利用統計情報と共に表示されます。 一部の製品利用統計情報については、表示されるまで数分かかる場合があります。[更新] をクリックします。 [詳細情報](../application-insights/app-insights-diagnostic-search.md)

[Application Insights でのパフォーマンス追跡についての詳細情報](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> ログをストリーミングする方法
アプリケーションの開発中に、ログ情報をほぼリアルタイムで参照すると役立つことがよくあります。 これは、Azure PowerShell または Azure コマンド ライン インターフェイスを使用して開発環境にログ情報をストリーミングすることで実現できます。

> [!NOTE]
> 一部の種類のログ バッファーはログ ファイルに書き込まれるため、ストリーミング中に無効な順序エラーが発生する可能性があります。 たとえば、ユーザーがページにアクセスしたときに発生するアプリケーション ログ エントリは、ページ要求の該当する HTTP ログ エントリより前のストリームに表示されることがあります。
>
> [!NOTE]
> ログのストリーミングでは、**D:\\home\\LogFiles\\** フォルダーに格納されているテキスト ファイルに書き込まれた情報もストリーミングされます。
>
>

### <a name="streaming-with-azure-powershell"></a>Azure PowerShell を使用してストリーミングする
ログ情報をストリーミングするには、Azure PowerShell の新しいインスタンスを開始して、次のコマンドを使用します。

    Get-AzureWebSiteLog -Name webappname -Tail

**-Name** パラメーターにより指定された Web アプリに接続され、ログ イベントが Web アプリで発生したら、PowerShell ウィンドウへの情報のストリーミングが開始されます。 /LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、 **-Message** パラメーターを使用します。 For example:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

特定のログの種類 (HTTP など) をフィルター処理するには、 **-Path** パラメーターを使用します。 For example:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

使用可能なパスの一覧を表示するには、-ListPath パラメーターを使用します。

> [!NOTE]
> Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、 [Azure PowerShell の使用方法](/develop/nodejs/how-to-guides/powershell-cmdlets/)に関するページを参照してください。
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Azure コマンド ライン ツールを使用してストリーミングする
ログ情報をストリーミングするには、新しいコマンド プロンプト、PowerShell、Bash、またはターミナル セッションを開き、次のコマンドを入力します。

    azure site log tail webappname

"webappname" という名前の Web アプリに接続され、ログ イベントが Web アプリで発生したら、ウィンドウへの情報のストリーミングが開始されます。 /LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、 **-Filter** パラメーターを使用します。 For example:

    azure site log tail webappname --filter Error

特定のログの種類 (HTTP など) をフィルター処理するには、 **--Path** パラメーターを使用します。 For example:

    azure site log tail webappname --path http

> [!NOTE]
> Azure コマンド ライン インターフェイスをインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、 [Azure コマンド ライン インターフェイスの使用方法](../cli-install-nodejs.md)に関するページを参照してください。
>
>

## <a name="understandlogs"></a> 診断ログを読む方法
### <a name="application-diagnostics-logs"></a>アプリケーション診断ログ
アプリケーション診断では、ファイル システム、テーブル ストレージ、BLOB ストレージのうち、どれにログを保存するかに応じて、.NET アプリケーション向けの一定の形式で情報が保存されます。 格納される一連の基本的なデータは、3 種類のすべてのストレージ間で同じで、イベントが発生した日時、イベントを生成したプロセスの ID、イベントの種類 (情報、警告、エラー)、イベントのメッセージです。

**ファイル システム**

ファイル システムにログが記録される行またはストリーミングによって受信する行は、それぞれ以下の形式になります。

    {Date}  PID[{process id}] {event type/level} {message}

たとえば、エラー イベントは次のようになります。

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

ファイル システムにログ記録する場合は、使用できる&3; つのログ記録方法のうちで最も基本的な情報が提供され、時間、プロセス ID、イベント レベル、メッセージのみを確認できます。

**Table Storage**

テーブル ストレージにログを記録する場合は、追加のプロパティを使用して、テーブルに格納されているデータだけでなく、イベントに関するより詳細な情報も簡単に検索できます。 テーブルに格納される各エンティティ (行) に次のプロパティ (列) が使用されます。

| プロパティ名 | 値/形式 |
| --- | --- |
| PartitionKey |yyyyMMddHH の形式によるイベントの日時 |
| RowKey |このエンティティを一意に識別する GUID 値 |
| Timestamp |イベントが発生した日時 |
| EventTickCount |イベントが発生した目盛り形式 (高精度) の日時 |
| ApplicationName |Web アプリケーション名 |
| レベル |イベント レベル (例: エラー、警告、情報) |
| EventId |このイベントのイベント ID<p><p>何も指定しない場合は既定で 0 |
| InstanceId |イベントが発生した Web アプリケーションのインスタンス |
| Pid |プロセス ID |
| Tid |イベントを生成したスレッドの ID |
| メッセージ |イベントの詳細メッセージ |

**BLOB ストレージ**

BLOB ストレージにログを記録するときには、値をコンマで区切った (CSV) 形式で格納されます。 テーブル ストレージと同様、追加のフィールドがログに記録されて、イベントについてより詳細な情報が提供されます。 CSV 内の各行に次のプロパティが使用されます。

| プロパティ名 | 値/形式 |
| --- | --- |
| 日付 |イベントが発生した日時 |
| レベル |イベント レベル (例: エラー、警告、情報) |
| ApplicationName |Web アプリケーション名 |
| InstanceId |イベントが発生した Web アプリのインスタンス |
| EventTickCount |イベントが発生した目盛り形式 (高精度) の日時 |
| EventId |このイベントのイベント ID<p><p>何も指定しない場合は既定で 0 |
| Pid |プロセス ID |
| Tid |イベントを生成したスレッドの ID |
| メッセージ |イベントの詳細メッセージ |

BLOB に格納されるデータは次のようになります。

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> この例に示しているように、ログの最初の行は列ヘッダーになります。
>
>

### <a name="failed-request-traces"></a>失敗した要求トレース
失敗した要求トレースは **fr######.xml** という名前の XML ファイルに保存されます。 ログに記録された情報を見やすくするには、**freb.xsl** という名前の XSL スタイルシートを XML ファイルと同じディレクトリに配置します。 Internet Explorer でいずれかの XML ファイルを開くと、トレース情報が XSL スタイルシートを使用して書式設定されて表示されます。 たとえば、次のように表示されます。

![失敗した要求をブラウザーで表示したところ](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>詳細なエラー ログ
詳細なエラー ログは、発生した HTTP エラーに関する詳細な情報を提供する HTML ドキュメントです。 単なる HTML ドキュメントであるため、Web ブラウザーを使用して表示できます。

### <a name="web-server-logs"></a>Web サーバー ログ
Web サーバー ログは [W3C 拡張ログ形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)(.aspx) で書式設定されます。 この情報は、テキスト エディターを使用して表示したり、 [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619)などのユーティリティで解析したりできます。

> [!NOTE]
> Azure Web Apps によって生成されるログでは、**s-computername**、**s-ip**、または **cs-version** のフィールドはサポートされていません。
>
>

## <a name="nextsteps"></a> 次のステップ
* [Web Apps を監視する方法](/manage/services/web-sites/how-to-monitor-websites/)
* [Visual Studio での Azure の Web Apps のトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)
* [HDInsight での Web アプリ ログの分析](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
>
>

## <a name="whats-changed"></a>変更内容
* Web サイトから App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更ガイドについては、「 [Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

