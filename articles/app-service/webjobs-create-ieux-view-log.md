---
title: Web ジョブのログ履歴を表示する
description: 失敗したジョブと成功したジョブのログ履歴を表示します。
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743840"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Azure portal で Web ジョブの履歴を表示する

失敗したジョブと成功したジョブのログ履歴を表示します。

1. 履歴を表示する Web ジョブを選択し、 **[ログ]** ボタンを選択します。

    ![[ログ] ボタン](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. **[WebJob Details]\(Web ジョブの詳細\)** ページで、詳細を確認する実行時間を選択します。

    ![Web ジョブの詳細](./media/web-sites-create-web-jobs/webjobdetails.png)

1. **[WebJob Run Details]\(Web ジョブの実行の詳細\)** ページで、 **[出力の切り替え]** を選択すると、ログの内容のテキストが表示されます。

    ![Web ジョブ実行の詳細](./media/web-sites-create-web-jobs/webjobrundetails.png)

    出力されるテキストを別のブラウザー ウィンドウに表示するには、 **[ダウンロード]** を選択します。 テキスト自体をダウンロードするには、 **[ダウンロード]** を右クリックし、ブラウザーのオプションを使用してファイルの内容を保存します。

1. Web ジョブの一覧に移動するには、ページの上部にある **[Web ジョブ]** 階層リンクを選択します。

    ![[Web ジョブ] 階層リンク](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![履歴ダッシュボードに表示された Web ジョブの一覧](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>次のステップ

* [Web ジョブ SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) を使用して多くのプログラミング タスクを単純化する

* [Visual Studio を使用した Web ジョブの開発とデプロイ](webjobs-dotnet-deploy-vs.md)について学習する