---
title: Azure Logic Apps から SMTP に接続する
description: Azure Logic Apps を使用して、SMTP (簡易メール転送プロトコル) アカウントで電子メールを送信するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283947"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Azure Logic Apps を使用して SMTP アカウントから電子メールを送信する

Azure Logic Apps と簡易メール転送プロトコル (SMTP) コネクタを使用して、SMTP アカウントから電子メールを送信する自動化されたタスクとワークフローを作成できます。 他のアクションに SMTP アクションからの出力を使用させることもできます。 たとえば、SMTP が電子メールを送信した後、Slack コネクタを使用して、Slack で チームに通知できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* SMTP アカウントとユーザー資格情報。

  接続を作成してお使いの SMTP アカウントにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SMTP アカウントにアクセスするロジック アプリ。 SMTP アクションを使用するには、トリガーでロジック アプリを起動します。たとえば Salesforce アカウントがある場合は Salesforce トリガーを使用できます。

  たとえば、**レコードが作成されたとき** という Salesforce トリガーを使用して、ロジック アプリを開始できます。 
  このトリガーは、Salesforce で潜在顧客などの新しいレコードが作成されるたびに起動されます。 
  このトリガーの後で、SMTP の **電子メールの送信** アクションを使用できます。 これにより、新しいレコードが作成されたときに、ロジック アプリは、SMTP アカウントから新しいレコードに関する電子メールを送信します。

## <a name="connect-to-smtp"></a>SMTP への接続

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. SMTP アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「smtp」と入力します。 アクションの一覧で、目的のアクションを選択します。

1. メッセージが表示されたら、次の接続情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **Connection Name** | はい | SMTP サーバーへの接続の名前 | 
   | **SMTP サーバー アドレス** | はい | SMTP サーバーのアドレス | 
   | **ユーザー名** | はい | SMTP アカウントのユーザー名 | 
   | **パスワード** | はい | SMTP アカウントのパスワード | 
   | **SMTP サーバー ポート** | いいえ | 使用する SMTP サーバー上の特定のポート | 
   | **SSL を有効にしますか?** | いいえ | TLS または SSL での暗号化を有効または無効にする | 
   |||| 

1. 選択したアクションで必要な詳細を指定します。 

1. ロジック アプリを保存するか、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/smtpconnector/)を参照してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
