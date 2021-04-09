---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109392"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>手動でトリガーされる Web ジョブを作成する

1. [Azure portal](https://portal.azure.com) にアクセスします。
1. **App Service** にアクセスします <abbr title="アプリ リソースは、Web アプリ、API アプリ、またはモバイル アプリである場合があります。">アプリ リソース</abbr>.
1. **[Web ジョブ]** を選択します。

    ![Web ジョブを選択する](../media/web-sites-create-web-jobs/select-webjobs.png)

2. **[Web ジョブ]** ページで、 **[追加]** を選択します。

   ![[Web ジョブ] ページ](../media/web-sites-create-web-jobs/wjblade.png)

3. **[Web ジョブの追加]** 設定を、次の表に示されているように使用します。

    ![手動でトリガーされる Web ジョブを作成するために指定する必要がある設定を示すスクリーンショット。](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | 設定      | 値の例   | 
    | ------------ | ----------------- | 
   | <abbr title="App Service アプリ内で一意の名前。 文字または数字で始まる必要があり、`-` と `_` 以外の特殊文字を使用することはできません。">名前</abbr> | myTriggeredWebJob | 
    | <abbr title="実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。">ファイルのアップロード</abbr> | ConsoleApp.zip |
    | <abbr title="種類には、継続的とトリガーがあります。">Type</abbr> | トリガー | 
    | <abbr title="種類には、スケジュールと手動があります">トリガー</a> | マニュアル | |

4. **[OK]** をクリックします。 

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。

   ![Web ジョブの一覧](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **手動 Web ジョブを実行するには**、一覧でその名前を右クリックし、 **[実行]** をクリックします。
   
    ![Web ジョブの実行](../media/web-sites-create-web-jobs/runondemand.png)

