---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743837"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>スケジュールされた Web ジョブを作成する


1. [Azure portal](https://portal.azure.com) にアクセスします。
1. **App Service** にアクセスします <abbr title="アプリ リソースは、Web アプリ、API アプリ、またはモバイル アプリである場合があります。">アプリ リソース</abbr>.
1. **[Web ジョブ]** を選択します。

   ![Web ジョブを選択する](../media/web-sites-create-web-jobs/select-webjobs.png)

1. **[Web ジョブ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](../media/web-sites-create-web-jobs/wjblade.png)

1. **[Web ジョブの追加]** 設定を、次の表に示されているように使用します。

    ![[Web ジョブの追加] ページ](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | 設定      | 値の例   |
    | ------------ | ----------------- | 
    | <abbr title="App Service アプリ内で一意の名前。 文字または数字で始まる必要があり、`-` と `_` 以外の特殊文字を使用することはできません。">名前</a> | myScheduledWebJob |  |
    | <abbr title="実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。">ファイルのアップロード</abbr> | ConsoleApp.zip |
    | <abbr title="種類には、継続的とトリガーがあります。">Type</abbr> | トリガー |
    | <abbr title="スケジュールを確実に動作させるために、Always On 機能を有効にします。 Always On 機能は、Basic、Standard、および Premium の価格レベルでのみ利用できます。">トリガー</a> | スケジュール済み |
    | CRON 式</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>CRON 式の詳細について学習する</summary>
     <a name="#ncrontab-expressions"></a>
    
     [NCRONTAB 式](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions)は、ポータルで入力するか、`settings.job` ファイルを Web ジョブの *.zip* ファイルのルートに含めることができます。例を以下に示します。
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     詳細については、「[Scheduling a triggered WebJob (トリガーされる Web ジョブのスケジュール設定)](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)」を参照してください。
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. **[OK]** をクリックします。

    **[Web ジョブ]** ページに新しい Web ジョブが表示されます。
    
    ![Web ジョブの一覧](../media/web-sites-create-web-jobs/listallwebjobs.png)
