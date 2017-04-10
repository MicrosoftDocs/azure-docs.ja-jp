---
title: "Web ジョブでバックグラウンド タスクを実行する"
description: "Web アプリでバックグラウンド タスクを実行する方法について説明します。"
services: app-service
documentationcenter: 
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 5d0d46447c3e0a3a1047e2bbedd44bbd46dd7f1b
ms.lasthandoff: 03/01/2017


---
# <a name="run-background-tasks-with-webjobs"></a>Web ジョブでバックグラウンド タスクを実行する
## <a name="overview"></a>概要
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリの Web ジョブでプログラムまたはスクリプトを実行するときには、オンデマンドで実行、連続的に実行、スケジュールに従って実行の&3; とおりの方法があります。 Web ジョブの使用に追加コストはかかりません。

この記事では、 [Azure ポータル](https://portal.azure.com)を使用して Web ジョブをデプロイする方法について説明します。 Visual Studio または継続的な配信プロセスを使用したデプロイ方法については、 [Azure WebJobs の Web Apps へのデプロイ方法](websites-dotnet-deploy-webjobs.md)に関するページをご覧ください。

Azure の Web ジョブ SDK は多くの Web ジョブのプログラミング タスクを簡略化しています。 詳細については、「 [Azure Web ジョブ SDK とは](websites-dotnet-webjobs-sdk.md)」をご覧ください。

 Azure Functions では、サーバーなしの環境や App Service アプリからプログラムやスクリプトを実行するための別の方法が提供されています。 詳細については、「 [Azure Functions の概要](../azure-functions/functions-overview.md)」をご覧ください。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="acceptablefiles"></a>スクリプトやプログラムで許容可能なファイルの種類
次のファイルの種類を指定できます。

* .cmd、.bat、.exe (windows cmd を使用)
* .ps1 (powershell を使用)
* .sh (bash を使用)
* .php (php を使用)
* .py (python を使用)
* .js (node を使用)
* .jar (java を使用)

## <a name="CreateOnDemand"></a>ポータルでのオンデマンドの Web ジョブの作成
1. [Azure Portal](https://portal.azure.com) の **[Web アプリ]** ブレードで、**[すべての設定] > [Web ジョブ]** をクリックし、**[Web ジョブ]** ブレードを表示します。
   
    ![[Web ジョブ] ブレード](./media/web-sites-create-web-jobs/wjblade.png)
2. **[追加]**をクリックします。 **[Web ジョブの追加]** ダイアログが表示されます。
   
    ![[Web ジョブの追加] ブレード](./media/web-sites-create-web-jobs/addwjblade.png)
3. **[名前]** ボックスに、Web ジョブの名前を入力します。 名前は、先頭を文字または数字にする必要があります。"-" と "_" 以外の特殊文字を使用することはできません。
4. **[実行方法]** ボックスの一覧の **[オンデマンドで実行]** をクリックします。
5. **[ファイル アップロード]** ボックスで、フォルダー アイコンをクリックし、スクリプトが含まれている zip ファイルを参照します。 この zip ファイルには、実行可能ファイル (.exe .cmd .bat .sh .php .py .js) のほか、プログラムまたはスクリプトの実行に必要な補助ファイルもすべて含まれている必要があります。
6. **[作成]** をクリックし、Web アプリにスクリプトをアップロードします。 
   
    Web ジョブに指定した名前が、 **[Web ジョブ]** ブレードの一覧に表示されます。
7. Web ジョブを実行するには、一覧でその名前を右クリックし、 **[実行]**をクリックします。
   
    ![Web ジョブの実行](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateContinuous"></a>連続的に実行する Web ジョブの作成
1. 連続的に実行する Web ジョブを作成するには、一度だけ実行する Web ジョブを作成する場合と同じ手順に従います。ただし、**[実行方法]** ボックスの一覧では **[連続的]** を選択します。
2. 連続的な Web ジョブを開始または停止するには、一覧で Web ジョブを右クリックし、**[開始]** または **[停止]** をクリックします。

> [!NOTE]
> Web アプリが複数のインスタンスで実行される場合、連続的に実行する Web ジョブはすべてのインスタンス上で実行されます。 オンデマンドで実行する Web ジョブおよびスケジュールに従って実行するタスクは、負荷分散目的で Microsoft Azure によって選択された単一のインスタンス上で実行されます。
> 
> 連続的に実行する Web ジョブをすべてのインスタンス上で確実に実行するには、Web アプリで [常時接続]* 構成設定を有効にします。そうしないと、SCM ホスト サイトが長時間アイドル状態になったときに Web ジョブが実行を停止する可能性があります。
> 
> 

## <a name="CreateScheduledCRON"></a>CRON 式を使用してスケジュール済みの WebJob を作成する
この方法は、Basic、Standard、または Premium モードで実行されている Web Apps に使用できます。この方法を使用するには、アプリで **Always On** 設定が有効になっている必要があります。

オンデマンドの WebJob をスケジュール済みの WebJob に変換するには、単に WebJob zip ファイルの root に `settings.job` ファイルを追加します。 この JSON ファイルには、次の例のように `schedule` プロパティを [CRON 式](https://en.wikipedia.org/wiki/Cron)と共に含める必要があります。

CRON 式は 6 つのフィールド: `{second} {minute} {hour} {day} {month} {day of the week}`から成ります。

たとえば、15 分ごとに WebJob をトリガーする場合、 `settings.job` は次のようになります。

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

その他の CRON スケジュールの例:

* 1 時間ごと (分の値が 0 のとき): `0 0 * * * *` 
* 午前 9 時から午後 5 時まで 1 時間ごと: `0 0 9-17 * * *` 
* 毎日午前 9 時 30 分: `0 30 9 * * *`
* 平日の毎日午前 9 時 30 分: `0 30 9 * * 1-5`

**注**: WebJobを Visual Studio からデプロイする場合は、必ず `settings.job` ファイルのプロパティを [新しい場合はコピーする] に設定してください。

## <a name="CreateScheduled"></a>Azure Scheduler を使用してスケジュール済みの WebJob を作成する
次の代替の方法では、Azure Scheduler を使用します。 この場合、WebJob によってスケジュールは直接認識されません。 代わりに、スケジュールに基づいて WebJob をトリガーするように Azure Scheduler を構成します。 

Azure Portal には、スケジュールされた Web ジョブを作成する機能がまだありません。ただし、この機能が追加されるまで、[クラシック ポータル](http://manage.windowsazure.com)を使用して、これを行うことができます。

1. [クラシック ポータル](http://manage.windowsazure.com) で、[Web ジョブ] ページに移動し、 **[追加]**をクリックします。
2. **[実行方法]** ボックスの一覧の **[スケジュールに従って実行]** をクリックします。
   
    ![スケジュールに従って実行する新しいジョブ][NewScheduledJob]
3. **[Scheduler のリージョン]** ボックスの一覧で、ジョブに応じたリージョンを選択し、ダイアログ ボックスの右下にある右矢印をクリックして、次の画面に進みます。
4. **[ジョブの作成]** ダイアログ ボックスの **[繰り返し]** ボックスで、繰り返しの種類として **[一度だけのジョブ]** または **[定期的なジョブ]** を選択します。
   
    ![繰り返しのスケジュール設定][SchdRecurrence]
5. また、**[開始]** ボックスの一覧で、開始時刻として **[現在]** または **[指定時]** を選択します。
   
    ![開始時刻の指定][SchdStart]
6. 特定の日時に開始するには、 **[開始日時]**の各ボックスで、開始時刻の値を選択します。
   
    ![指定時の開始を指定][SchdStartOn]
7. 定期的なジョブを選択した場合は、**[繰り返しの間隔]** で実行頻度を指定し、**[終了日]** で終了日時を指定します。
   
    ![繰り返しのスケジュール設定][SchdRecurEvery]
8. **[週]** を選択した場合は **[特定のスケジュールに従って]** チェック ボックスをオンにし、ジョブを実行する曜日 (複数も可能) を指定することができます。
   
    ![曜日の指定][SchdWeeksOnParticular]
9. **[月]** を選択して **[特定のスケジュールに従って]** チェック ボックスをオンにした場合は、**[日]** をクリックして、1 か月のうちジョブを実行する日 (複数も可能) を指定することができます。 
   
    ![1 か月のうち特定の日を指定][SchdMonthsOnPartDays]
10. **[平日]**を選択した場合は、1 か月のうちジョブを実行する曜日 (複数も可能) を指定することができます。
    
     ![1 か月のうち特定の曜日を指定][SchdMonthsOnPartWeekDays]
11. 最後に、**[発生回数]** を併せて使用すると、1 か月のうち指定した曜日にジョブを実行する週 (1 週目、2 週目、3 週目など) を選択することができます。
    
    ![1 か月のうち特定の週の特定の曜日を指定][SchdMonthsOnPartWeekDaysOccurences]
12. ジョブ (複数可) を作成したら、それらの名前が状態、スケジュールの種類、その他の情報と共に [Web ジョブ] タブに表示されます。 最近実行された 30 件の Web ジョブに関する履歴情報が管理されます。
    
    ![ジョブの一覧][WebJobsListWithSeveralJobs]

### <a name="Scheduler"></a>スケジュールされたジョブと Azure Scheduler
スケジュールされたジョブは、 [クラシック ポータル](http://manage.windowsazure.com)の Azure Scheduler ページでさらに構成できます。

1. [Web ジョブ] ページで、ジョブの **[スケジュール]** リンクをクリックして Azure スケジューラ ポータル ページに移動します。 
   
   ![Azure Scheduler へのリンク][LinkToScheduler]
2. Scheduler ページで、ジョブをクリックします。
   
    ![Scheduler ポータル ページ上のジョブ][SchedulerPortal]
3. **[ジョブの操作]** ページが開き、ここでジョブをさらに構成できます。 
   
    ![Scheduler の [ジョブの操作] ページ][JobActionPageInScheduler]

## <a name="ViewJobHistory"></a>ジョブ履歴の表示
1. Web ジョブ SDK で作成したジョブを含め、ジョブの実行履歴を表示するには、[Web ジョブ] ブレードの **[ログ]** 列で対応するリンクをクリックします  (必要に応じて、クリップボード アイコンを使用して、ログ ファイル ページの URL をクリップボードにコピーできます)。
   
    ![ログのリンク](./media/web-sites-create-web-jobs/wjbladelogslink.png)
2. リンクをクリックすると、Web ジョブの詳細ページが開きます。 このページには、実行されたコマンド、そのコマンドを最後に実行した時間、その実行が成功したか失敗したかが表示されます。 **[最近実行されたジョブ]**で、さらに詳細を表示する時間をクリックします。
   
    ![Web ジョブ詳細][WebJobDetails]
3. **[Web ジョブ実行の詳細]** ページが表示されます。 ログの内容のテキストを表示するには、 **[出力切り替え]** をクリックします。 ログ出力がテキスト形式で表示されます。 
   
    ![Web ジョブ実行の詳細][WebJobRunDetails]
4. 出力されたテキストを別のブラウザー ウィンドウで確認するには、 **[ダウンロード]** リンクをクリックします。 テキスト自体をダウンロードするには、リンクを右クリックしてから、ブラウザーのオプションを使用してファイルの内容を保存します。
   
    ![ログ出力のダウンロード][DownloadLogOutput]
5. ページの上部にある **[Web ジョブ]** リンクを使用すると、履歴ダッシュボードに Web ジョブの一覧を簡単に表示できます。
   
    ![Web ジョブの一覧へのリンク][WebJobsLinkToDashboardList]
   
    ![履歴ダッシュボードに表示された Web ジョブの一覧][WebJobsListInJobsDashboard]
   
    これらのリンクのいずれかをクリックすると、選択したジョブの Web ジョブ詳細ページに移動します。

## <a name="WHPNotes"></a>メモ
* scm (デプロイ) サイトへの要求がなく Azure で Web アプリのポータルが開かれていない場合、[無料] モードの Web アプリは 20 分後にタイムアウトすることがあります。 実際のサイトへの要求があっても、この状態はリセットされません。
* 連続するジョブのコードは、無限ループで実行されるように記述する必要があります。
* 連続するジョブが連続的に実行されるのは、アプリが稼働状態になっているときのみです。
* [基本] モードと [標準] モードには [常時接続] 機能が用意されており、この機能を有効にすると、Web アプリがアイドル状態にならなくなります。
* デバッグできるのは、連続的に実行する Web ジョブだけです。 スケジュールされた Web ジョブとオンデマンドの Web ジョブのデバッグはサポートされていません。

## <a name="NextSteps"></a>次のステップ
詳細については、[Azure WebJobs の推奨リソース][WebJobsRecommendedResources]に関するページをご覧ください。

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png


