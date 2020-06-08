---
title: WebJobs でバックグラウンド タスクを実行する
description: Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する方法を説明します。 さまざまなスクリプト形式から選択し、CRON 式を使用して実行します。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 028551f04b2e44e9456e2f7343159ad9b52fd25f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085146"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する

この記事では、実行可能ファイルまたはスクリプトをアップロードする WebJobs を [Azure ポータル](https://portal.azure.com)を使用してデプロイする方法について説明します。 Visual Studio を使用した WebJobs の開発とデプロイ方法については、[Visual Studio を使用した WebJobs のデプロイ](webjobs-dotnet-deploy-vs.md)に関する記事を参照してください。

## <a name="overview"></a>概要
WebJobs は、Web アプリ、API アプリ、またはモバイル アプリと同じインスタンスでプログラムやスクリプトを実行できる [Azure App Service](index.yml) の機能です。 WebJobs の使用に追加コストはかかりません。

> [!IMPORTANT]
> WebJobs は App Service on Linux ではまだサポートされていません。

Azure WebJobs SDK と WebJobs を使用して、多くのプログラミング タスクを単純化できます。 詳細については、「 [Azure WebJobs SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。

Azure Functions は、プログラムとスクリプトを実行する別の方法を提供します。 WebJobs と Functions の比較については、「[Flow、Logic Apps、Functions、WebJobs の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)」を参照してください。

## <a name="webjob-types"></a>WebJobs の種類

次の表で、*継続的* WebJobs と*トリガーされる* WebJobs の違いを説明します。


|継続的  |トリガー  |
|---------|---------|
| WebJobs が作成されるとすぐに開始します。 ジョブが終了しないようにするため、プログラムまたはスクリプトは、その動作を無限ループ内で実行します。 ジョブが終了した場合でも、再開することができます。 | 手動またはスケジュールに従ってトリガーされたときにのみ開始します。 |
| Web アプリが実行されているすべてのインスタンスで実行されます。 必要に応じて、WebJobs を 1 つのインスタンスに制限できます。 |Azure が負荷分散用に選択した 1 つのインスタンス上で実行されます。|
| リモート デバッグをサポートします。 | リモート デバッグをサポートしません。|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>スクリプトまたはプログラムでサポートされるファイルの種類

次のファイルの種類がサポートされます。

* .cmd、.bat、.exe (Windows コマンドを使用)
* .ps1 (PowerShell を使用)
* .sh (Bash を使用)
* .php (PHP を使用)
* .py (Python を使用)
* .js (Node.js を使用)
* .jar (Java を使用)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> 継続的 WebJobs を作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

2. **[WebJobs ]** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[WebJobs ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[WebJobs の追加]** 設定を、次の表に示されているように使用します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 設定      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **名前** | myContinuousWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。 |
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | 継続的 | [WebJobs の種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **スケール** | 複数のインスタンス | 継続的 WebJobs でのみ使用できます。 プログラムまたはスクリプトがすべてのインスタンスで実行されるか、1 つのインスタンスだけで実行されるかどうかを決定します。 複数のインスタンスで実行するオプションは、Free または Shared [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)には適用されません。 | 

4. **[OK]** をクリックします。

   **[WebJobs]** ページに新しい WebJobs が表示されます。

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. 継続的 WebJobs を開始または停止するには、一覧の WebJobs を右クリックし、 **[停止]** または **[開始]** をクリックします。

    ![継続的 Web ジョブを停止する](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>手動でトリガーされる WebJobs を作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

2. **[WebJobs ]** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[WebJobs ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[WebJobs の追加]** 設定を、次の表に示されているように使用します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | 設定      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **名前** | myTriggeredWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。|
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | トリガー | [WebJobs の種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **トリガー** | マニュアル | |

4. **[OK]** をクリックします。

   **[WebJobs]** ページに新しい WebJobs が表示されます。

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. WebJobs を実行するには、一覧でその名前を右クリックし、 **[実行]** をクリックします。
   
    ![Web ジョブの実行](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>スケジュールされた WebJobs を作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

2. **[WebJobs ]** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[WebJobs ]** ページで、 **[追加]** を選択します。

   ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[WebJobs の追加]** 設定を、次の表に示されているように使用します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | 設定      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **名前** | myScheduledWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。 |
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | トリガー | [WebJobs の種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **トリガー** | スケジュール | スケジュールを確実に動作させるために、Always On 機能を有効にします。 Always On 機能は、Basic、Standard、および Premium の価格レベルでのみ利用できます。|
   | **CRON 式** | 0 0/20 * * * * | [CRON 式](#ncrontab-expressions)については、次のセクションで説明します。 |

4. **[OK]** をクリックします。

   **[WebJobs]** ページに新しい WebJobs が表示されます。

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB 式

[NCRONTAB 式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)は、ポータルで入力するか、`settings.job` ファイルを WebJobs の *.zip* ファイルのルートに含めることができます。例を以下に示します。

```json
{
    "schedule": "0 */15 * * * *"
}
```

詳細については、「[Scheduling a triggered WebJob (トリガーされる WebJobs のスケジュール設定)](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)」を参照してください。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>ジョブ履歴を表示する

1. 履歴を表示する WebJobs を選択し、 **[ログ]** ボタンを選択します。
   
   ![[ログ] ボタン](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. **[WebJob Details]\(WebJobs の詳細\)** ページで、詳細を確認する実行時間を選択します。
   
   ![Web ジョブの詳細](./media/web-sites-create-web-jobs/webjobdetails.png)

3. **[WebJob Run Details]\(WebJobs の実行の詳細\)** ページで、 **[出力の切り替え]** を選択すると、ログの内容のテキストが表示されます。
   
    ![Web ジョブ実行の詳細](./media/web-sites-create-web-jobs/webjobrundetails.png)

   出力されるテキストを別のブラウザー ウィンドウに表示するには、 **[ダウンロード]** を選択します。 テキスト自体をダウンロードするには、 **[ダウンロード]** を右クリックし、ブラウザーのオプションを使用してファイルの内容を保存します。
   
5. WebJobs の一覧に移動するには、ページの上部にある **[WebJobs]** 階層リンクを選択します。

    ![[Web ジョブ] 階層リンク](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![履歴ダッシュボードに表示された Web ジョブの一覧](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> 次のステップ

Azure WebJobs SDK と WebJobs を使用して、多くのプログラミング タスクを単純化できます。 詳細については、「 [Azure WebJobs SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。
