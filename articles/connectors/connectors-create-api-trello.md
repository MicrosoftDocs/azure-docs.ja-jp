---
title: Azure Logic Apps から Trello に接続する | Microsoft Docs
description: Azure Logic Apps を使用して、Trello プロジェクトのリスト、ボード、およびカードの監視と管理を行うタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ae77488df6ce58d8cab61933f50d24edec35b1ab
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050733"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Azure Logic Apps を使用して Trello の監視と管理を行う

Azure Logic Apps と Trello コネクタを使用して、Trello のリスト、カード、ボード、チーム メンバーなどを監視および管理する、次のような自動化されたタスクとワークフローを作成することができます。

* 新しいカードがボードおよびリストに追加されるのを監視します。 
* ボード、カード、およびリストを作成、取得、および管理します。
* カードにコメントおよびメンバーを追加します。
* ボード、ボードのラベル、ボード上のカード、カードのコメント、カードのメンバー、チームのメンバー、および自分がメンバーであるチームを一覧表示します。 
* チームを取得します。

Trello アカウントから応答を取得し、その出力を他のアクションが使用できるようにするトリガーを使用できます。 Trello アカウントでタスクを実行するアクションを使用できます。 他のアクションに Trello アクションからの出力を使用させることもできます。 たとえば、新しいカードがボードまたはリストに追加されたときに、Slack コネクタでメッセージを送信することができます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Trello アカウントとユーザー資格情報

  接続を作成して Trello アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Trello アカウントにアクセスするロジック アプリ。 Trello トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 Trello アクションを使用するには、**Recurrence** トリガーなどのトリガーでロジック アプリを開始します。

## <a name="connect-to-trello"></a>Trello に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリの場合、検索ボックスに、フィルターとして「trello」と入力します。 トリガーの一覧で、目的のトリガーを選択します。 

   または

   既存のロジック アプリの場合、アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 
   検索ボックスに、フィルターとして「trello」と入力します。 
   アクションの一覧で、目的のアクションを選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. Trello へのサインインを求められたら、ロジック アプリのアクセスを承認し、サインインします。

1. 選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/trello/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。