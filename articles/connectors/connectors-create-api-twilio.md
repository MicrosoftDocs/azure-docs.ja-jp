---
title: Azure Logic Apps から Twilio に接続する
description: Azure Logic Apps を使用して、Twilio アカウントを通じてグローバル SMS、MMS、および IP メッセージを管理するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d144960972f5a1b45e88cc3a0ea015925cae3b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288146"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Azure Logic Apps で Twilio のメッセージを管理する

Azure Logic Apps と Twilio コネクタを使用すると、Twilio のメッセージを取得、送信、および一覧表示する、自動化されたタスクとワークフローを作成できます。メッセージには、グローバル SMS、MMS、および IP メッセージが含まれます。 これらのアクションを使用して、Twilio アカウントでタスクを実行することができます。 他のアクションに Twilio アクションからの出力を使用させることもできます。 たとえば、新しいメッセージが到着したときに、Slack コネクタでメッセージの内容を送信することができます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [Twilio](https://www.twilio.com/) から: 

  * Twilio アカウント ID と[認証トークン](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them) (Twilio ダッシュボードに表示されます)

    接続を作成して、ロジック アプリから Twilio アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。 
    Twilio の試用アカウントを使用している場合は、"*確認済み*" の電話番号にのみ SMS を送信できます。

  * SMS を送信できる確認済みの Twilio 電話番号

  * SMS を受信できる確認済みの Twilio 電話番号

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Twilio アカウントにアクセスするロジック アプリ。 Twilio アクションを使用するには、**Recurrence** トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-twilio"></a>Twilio に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 以下からパスを選択します。 

     * アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

       または

     * アクションを追加するステップの間で、ステップ間の矢印の上にポインターを移動します。 
     表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。
     
       検索ボックスに、フィルターとして「twilio」と入力します。 
       アクションの一覧で、目的のアクションを選択します。

1. 接続で必要な詳細を指定し、 **[作成]** を選択します。

   * 接続のために使用する名前
   * Twilio アカウント ID 
   * Twilio アクセス (認証) トークン

1. 選択したアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/twilio/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
