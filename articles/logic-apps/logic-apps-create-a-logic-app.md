---
title: "初めての Azure ロジック アプリでのワークフローの作成 | Microsoft Docs"
description: "初めてのロジック アプリでアプリと SaaS サービスの接続を開始します"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>SaaS サービスを接続する新しいロジック アプリを作成します。
このトピックでは、初めての方に向けて [Azure Logic Apps](logic-apps-what-are-logic-apps.md)の使い方を簡単に説明します。 電子メール アドレス宛てに興味深いツイートを送信できる簡単なワークフローを紹介します。

このシナリオを使用するには、以下が必要です。

* Azure サブスクリプション
* Twitter アカウント
* Outlook.com または Office 365 Outlook アカウント

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>ツイートを電子メールで送信する新しいロジック アプリを作成する

1. [Azure ポータル](https://portal.azure.com)にサインインします。 

2. 左側のメニューから **[新規]** > **[Enterprise Integration]** > **[Logic App]** の順に選択します。

    または、**[新規]** を選択し、検索ボックスに「`logic app`」と入力して Enter キーを押します。 **[Logic App]** > **[作成]** の順に選択します。

3. ロジック アプリの名前を入力し、該当する Azure サブスクリプションを選択して、Azure リソース グループを作成または選択し、場所を選択して **[作成]** を選択します。

    **[ダッシュボードにピン留めする]** を選択した場合、ロジック アプリがデプロイ後に自動的に開きます。

4. ロジック アプリを初めて開いたときは、どのテンプレートから開始するかを選択できます。
ここでは、 **[Blank Logic App (空のロジック アプリ)]** をクリックして最初から作成します。 

5. 最初に作成する必要があるアイテムは、トリガーです。 これはロジック アプリを起動するイベントです。 検索ボックスで「**twitter**」を検索し、**[新しいツイートが投稿されたら]** を選択します。 Twitter アカウントのユーザー名とパスワードでサインインします。

6. ロジック アプリをトリガーするための検索語句を入力します。

   ![Twitter の検索](media/logic-apps-create-a-logic-app/twittersearch.png)

    **[頻度]** と **[間隔]** によってロジック アプリで新しいツイートを確認する頻度が決まります (その期間中のすべてのツイートが返されます)。

7. **[新しいステップ]** を選択して、**[アクションの追加]** または **[条件の追加]** を選択します。

    **[アクションの追加]** を選択すると、[利用可能なコネクタ](../connectors/apis-list.md)から検索してアクションを選択することができます。 

8. 指定した電子メール アドレスに Outlook アカウントから電子メールを送信するには、検索ボックスで「**outlook**」を検索し、**[電子メールを送信する]** を選択します。

   ![アクション](media/logic-apps-create-a-logic-app/actions.png)

9. 次は、電子メールに使用するパラメーターを入力する必要があります。

   ![パラメーター](media/logic-apps-create-a-logic-app/parameters.png)

10. 最後に **[保存]** を選択すると、ロジック アプリがライブ状態となります。

## <a name="manage-your-logic-app-after-creation"></a>作成後にロジック アプリを管理する

ここまでの手順で、ロジック アプリが実行できるようになりました。 このロジック アプリは、入力した検索語句を含むツイートを定期的に確認します。 検索語句に一致するツイートが検出されると、電子メールを送信します。 ここでは、最後にアプリを無効にしたり、動作内容を確認したりする方法を見ていきます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. 左側のメニューで **[その他のサービス]** をクリックします。 **[Enterprise Integration]** の **[Logic Apps]** を選択します。 自分のロジック アプリを選択します。

    *    アプリの状態、実行履歴、一般情報を表示するには、ロジック アプリのメニューの **[概要]** を選択します。 必要なデータが見つからない場合は、コマンド バーで **[最新の情報に更新]** を選択します。

    *    アプリを編集するには、ロジック アプリのメニューから **[ロジック アプリ デザイナー]** を選択します。

    *    アプリを一時的にオフにするには、ロジック アプリのメニューから **[概要]** を選択します。 コマンド バーで **[無効]** を選択します。

    *    アプリを削除するには、ロジック アプリのメニューから **[概要]** を選択します。 
    コマンド バーで **[削除]** を選択します。 ロジック アプリの名前を入力し、**[削除]** を選択します。

クラウド上で実行できる簡単なロジック アプリを 5 分もしないうちに作成できました。 Logic Apps の機能の使用について詳しくは、「 [ロジック アプリの機能を使用する]」をご覧ください。 ロジック アプリの定義自体の詳細については、「 [ロジック アプリの定義を作成する](../logic-apps/logic-apps-author-definitions.md)」を参照してください。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[ロジック アプリの機能を使用する]: logic-apps-create-a-logic-app.md
