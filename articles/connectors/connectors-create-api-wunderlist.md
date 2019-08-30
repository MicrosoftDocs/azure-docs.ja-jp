---
title: Azure Logic Apps から Wunderlist に接続する | Microsoft Docs
description: Azure Logic Apps を使用して、Wunderlist アカウントの一覧、タスク、リマインダーなどの監視と管理を行うタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: d57ab4a7b655a7d49a7120d358dccc627099e5fd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050662"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Wunderlist の監視と管理を行う

Azure Logic Apps と Wunderlist コネクターを使用して、Wunderlist アカウントの ToDo リスト、タスク、リマインダーなどの監視と管理を行う自動化されたタスクとワークフローを作成できます。他にも、次のようなアクションを実行できます。

* 新しいタスクの作成日時、タスクの期限、またはリマインダーの通知を監視します。
* リスト、メモ、タスク、サブタスクなどの作成と管理を行います。
* リマインダーを設定します。
* リスト、タスク、サブタスク、リマインダー、ファイル、メモ、コメントなどを取得します。

[Wunderlist](https://www.wunderlist.com/) は、プロジェクト、ToDo リスト、タスクを計画、管理、完了するのに役立つサービスであり、デバイスも場所も問わずに使用できるサービスです。 Wunderlist アカウントから応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 Wunderlist アカウントでタスクを実行するアクションを使用できます。 他のアクションに Wunderlist アクションからの出力を使用させることもできます。 たとえば、新しいタスクの期限が来たときに、Slack コネクタを使用してメッセージを投稿できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Wunderlist アカウントとユーザー資格情報。

   接続を作成してお使いの Wunderlist アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Yammer アカウントにアクセスするロジック アプリ。 Wunderlist トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 Wunderlist アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-wunderlist"></a>Wunderlist に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. パスを選択します。 

   * 空のロジック アプリの場合: 検索ボックスに、フィルターとして「Wunderlist」と入力します。 
   トリガーの一覧で、目的のトリガーを選択します。 

     または

   * 既存のロジック アプリの場合: 
   
     * アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

       または

     * アクションを追加するステップの間で、ステップ間の矢印の上にポインターを移動します。 
     表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]**  を選択します。
     
       検索ボックスに、フィルターとして「Wunderlist」と入力します。 
       アクションの一覧で、目的のアクションを選択します。

1. Wunderlist へのサインインを求められたら、今すぐサインインして、アクセスを許可するようにします。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/wunderlist/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。