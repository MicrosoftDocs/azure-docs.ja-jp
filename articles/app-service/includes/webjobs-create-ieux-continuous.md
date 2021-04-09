---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743833"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> 継続的 Web ジョブを作成する

1. [Azure portal](https://portal.azure.com) にアクセスします。
1. **App Service** にアクセスします <abbr title="アプリ リソースは、Web アプリ、API アプリ、またはモバイル アプリである場合があります。">アプリ リソース</abbr>.
1. **[Web ジョブ]** を選択します。

   ![Web ジョブを選択する](../media/web-sites-create-web-jobs/select-webjobs.png)

1. **[Web ジョブ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](../media/web-sites-create-web-jobs/wjblade.png)

1. **[Web ジョブの追加]** 設定を、次の表に示されているように使用します。

   ![構成する必要がある [Web ジョブの追加] 設定を示すスクリーンショット。](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 設定      | 値の例   | 
   | ------------ | ----------------- | 
   | <abbr title="App Service アプリ内で一意の名前。 文字または数字で始まる必要があり、`-` と `_` 以外の特殊文字を使用することはできません。">名前</abbr> | myContinuousWebJob | 
   | <abbr title=" 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。">ファイルのアップロード</abbr> | ConsoleApp.zip |
   | <abbr title="種類には、継続的とトリガーがあります。">Type</abbr> | 継続的 | 
   | <abbr title="継続的 Web ジョブでのみ使用できます。 プログラムまたはスクリプトがすべてのインスタンスで実行されるか、1 つのインスタンスだけで実行されるかどうかを決定します。 複数のインスタンスで実行するオプションは、Free または Shared 価格レベルには適用されません。">スケール</abbr> | 複数のインスタンス | 

1. **[OK]** をクリックします。

    **[Web ジョブ]** ページに新しい Web ジョブが表示されます。

    ![Web ジョブの一覧](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. 継続的 Web ジョブを **開始または停止するには**、一覧の Web ジョブを右クリックし、 **[停止]** または **[開始]** をクリックします。

   ![継続的 Web ジョブを停止する](../media/web-sites-create-web-jobs/continuousstop.png)
