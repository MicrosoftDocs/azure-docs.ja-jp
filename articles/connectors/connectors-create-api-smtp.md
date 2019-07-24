---
title: Azure Logic Apps から SMTP に接続する | Microsoft Docs
description: Azure Logic Apps を使用して、SMTP (簡易メール転送プロトコル) アカウントで電子メールを送信するタスクとワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230507"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Azure Logic Apps を使用して SMTP アカウントから電子メールを送信する

Azure Logic Apps と簡易メール転送プロトコル (SMTP) コネクタを使用して、SMTP アカウントから電子メールを送信する自動化されたタスクとワークフローを作成できます。 他のアクションに SMTP アクションからの出力を使用させることもできます。 たとえば、SMTP が電子メールを送信した後、Slack コネクタを使用して、Slack で チームに通知できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* SMTP アカウントとユーザー資格情報。

  接続を作成してお使いの SMTP アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SMTP アカウントにアクセスするロジック アプリ。 SMTP アクションを使用するには、トリガーでロジック アプリを起動します。たとえば Salesforce アカウントがある場合は Salesforce トリガーを使用できます。

  たとえば、**レコードが作成されたとき**という Salesforce トリガーを使用して、ロジック アプリを開始できます。 
  このトリガーは、Salesforce で潜在顧客などの新しいレコードが作成されるたびに起動されます。 
  このトリガーの後で、SMTP の**電子メールの送信**アクションを使用できます。 これにより、新しいレコードが作成されたときに、ロジック アプリは、SMTP アカウントから新しいレコードに関する電子メールを送信します。

## <a name="connect-to-smtp"></a>SMTP への接続

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. SMTP アクションを追加する最後のステップの下で、**[新しいステップ]** を選択します。 

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]**  を選択します。

1. 検索ボックスに、フィルターとして「smtp」と入力します。 アクションの一覧で、目的のアクションを選択します。

1. メッセージが表示されたら、次の接続情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **Connection Name** | はい | SMTP サーバーへの接続の名前 | 
   | **SMTP サーバー アドレス** | はい | SMTP サーバーのアドレス | 
   | **ユーザー名** | はい | SMTP アカウントのユーザー名 | 
   | **パスワード** | はい | SMTP アカウントのパスワード | 
   | **SMTP サーバー ポート** | いいえ  | 使用する SMTP サーバー上の特定のポート | 
   | **SSL を有効にしますか?** | いいえ  | SSL 暗号化を有効または無効にします。 | 
   |||| 

1. 選択したアクションで必要な詳細を指定します。 

1. ロジック アプリを保存するか、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/smtpconnector/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。