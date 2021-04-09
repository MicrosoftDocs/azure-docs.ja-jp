---
title: Azure Logic Apps から SendGrid に接続する
description: Azure Logic Apps を使用して SendGrid で電子メールの送信とメーリング リストの管理を行うタスクとワークフローを自動化する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87290440"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して SendGrid で電子メールの送信とメーリング リストの管理を行う

Azure Logic Apps と SendGrid コネクタを使用すると、電子メールを送信したり受信者リストを管理したりする、次のような自動化されたタスクとワークフローを作成できます。

* メールを送信する。
* 一覧に受信者を追加します。
* グローバル抑制を取得、追加、および管理します。

ロジック アプリで SendGrid アクションを使用して、これらのタスクを実行することができます。 他のアクションで SendGrid のアクションからの出力を使用することもできます。 

このコネクタではアクションのみが提供されます。ロジック アプリを開始するには、**Recurrence** トリガーなど、個別のトリガーを使用します。 たとえば、受信者を定期的にリストに追加する場合は、Office 365 Outlook コネクタまたは Outlook.com コネクタを使用して、受信者およびリストに関する電子メールを送信することができます。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [SendGrid アカウント](https://www.sendgrid.com/)と [SendGrid API キー](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   接続を作成して SendGrid アカウントにアクセスしてよいという承認が、API キーによってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SendGrid アカウントにアクセスするためのロジック アプリ。 SendGrid アクションを使用するには、**Recurrence** トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-sendgrid"></a>SendGrid に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 以下からパスを選択します。 

   * アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

     または

   * アクションを追加するステップの間で、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「sendgrid」と入力します。 アクションの一覧で、目的のアクションを選択します。

1. 接続の名前を指定します。 

1. SendGrid API キーを入力し、 **[作成]** を選択します。

1. 選択したアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/sendgrid/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
