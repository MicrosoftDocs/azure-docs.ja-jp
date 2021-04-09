---
title: Office 365 Outlook と統合する
description: Azure Logic Apps を使用して、Office 365 Outlook のメール、連絡先、カレンダーを管理するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 87a9c8a38ede7008d658068329c8c72e7e3c4117
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095454"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して、Office 365 Outlook のメール、連絡先、カレンダーを管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Office 365 Outlook コネクタ](/connectors/office365connector/)を使用すると、ロジック アプリを構築することで職場または学校アカウントを管理する自動化されたタスクとワークフローを作成できます。 たとえば、次のタスクを自動化することができます。

* メールの取得、送信、返信を行う。
* カレンダーで会議をスケジュールする。
* 連絡先を追加および編集する。

任意のトリガーを使用してワークフローを開始できます。たとえば、新しいメールが届いたとき、カレンダーの項目が更新されたとき、または Salesforce などの別のサービスでイベントが発生したときなどです。 トリガー イベントに応答するアクションを使用できます。たとえば、メールを送信したり、新しいカレンダー イベントを作成したりできます。

## <a name="prerequisites"></a>前提条件

* [職場または学校アカウント](https://www.office.com/)を使用してサインインする Outlook アカウント。 @outlook.com または @hotmail.com アカウントを持っている場合は、代わりに [Outlook.com コネクタ](../connectors/connectors-create-api-outlook.md)を使用します。 別のユーザー アカウント (サービス アカウントなど) を使用して Outlook に接続する方法については、[他のアカウントを使用して接続する](#connect-using-other-accounts)方法に関するページをご覧ください。

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* Outlook アカウントにアクセスするロジック アプリ。 Office 365 Outlook トリガーを使用してワークフローを開始するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。 Office 365 Outlook アクションをワークフローに追加するには、ロジック アプリにトリガーが既に存在している必要があります。

## <a name="add-a-trigger"></a>トリガーの追加

[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)は、ロジック アプリのワークフローを開始するイベントです。 この例のロジック アプリでは、指定された間隔と頻度に基づいて、メール アカウントの更新されたカレンダー イベントを確認する "ポーリング" トリガーを使用します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで空のロジック アプリを開きます。

1. 検索ボックスに、フィルターとして「`office 365 outlook`」と入力します。 この例では、 **[予定しているイベントがすぐに開始されるとき]** を選択します。
   
   ![ロジック アプリを開始するトリガーの選択](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Outlook アカウントへのアクティブな接続がない場合は、サインインして、その接続を作成するように求めるプロンプトが表示されます。 別のユーザー アカウント (サービス アカウントなど) を使用して Outlook に接続する方法については、[他のアカウントを使用して接続する](#connect-using-other-accounts)方法に関するページをご覧ください。 それ以外の場合は、トリガーのプロパティに関する情報を指定します。

   > [!NOTE]
   > サインイン資格情報を変更した場合でも、接続は取り消されるまで期限切れとはなりません。 詳細については、「[Azure Active Directory における構成可能なトークンの有効期間](../active-directory/develop/active-directory-configurable-token-lifetimes.md)」を参照してください。

   この例では、トリガーによってチェックされるカレンダーを選択します。次に例を示します。

   ![トリガーのプロパティを構成する](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. トリガーに、**[頻度]** と **[間隔]** の値を設定します。 **タイム ゾーン** など、使用可能なその他のトリガーのプロパティを追加するには、**[新しいパラメーターの追加]** リストからそれらプロパティを選択します。

   たとえば、トリガーを使用して 15 分ごとにカレンダーをチェックするには、**[頻度]** を **[分]** に設定し、**[間隔]** を `15` に設定します。 

   ![トリガーの頻度と間隔を設定する](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. デザイナーのツール バーで、 **[保存]** を選択します。

次に、トリガーが起動した後に実行するアクションを追加します。 たとえば、Twilio の **[メッセージを送信]** アクションを追加できます。これは 15 分以内にカレンダー イベントが開始されたときにテキストを送信します。

## <a name="add-an-action"></a>アクションを追加する

[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、ロジック アプリのワークフローによって実行される操作です。 この例のロジック アプリでは、Office 365 Outlook に新しい連絡先が作成されます。 別のトリガーまたはアクションからの出力を使用して、連絡先を作成できます。 たとえば、ロジック アプリで Dynamics 365 トリガー **[レコードが作成されたとき]** を使用します。 Office 365 Outlook の **[連絡先の作成]** アクションを追加し、SalesForce トリガーからの出力を使用して新しい連絡先を追加できます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ワークフローの最後のステップとしてアクションを追加するには、**[新しいステップ]** を選択します。 

   ステップの間にアクションを追加するには、該当するステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「`office 365 outlook`」と入力します。 この例では **[連絡先の作成]** を選択します。

   ![ロジック アプリで実行するアクションの選択](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Outlook アカウントへのアクティブな接続がない場合は、サインインして、その接続を作成するように求めるプロンプトが表示されます。 別のユーザー アカウント (サービス アカウントなど) を使用して Outlook に接続する方法については、[他のアカウントを使用して接続する](#connect-using-other-accounts)方法に関するページをご覧ください。 それ以外の場合は、アクションのプロパティに関する情報を指定します。

   > [!NOTE]
   > サインイン資格情報を変更した場合でも、接続は取り消されるまで期限切れとはなりません。 詳細については、「[Azure Active Directory における構成可能なトークンの有効期間](../active-directory/develop/active-directory-configurable-token-lifetimes.md)」を参照してください。

   この例では、アクションによって新しい連絡先が作成される連絡先のフォルダーを選択します。次に例を示します。

   ![アクションのプロパティを構成する](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   使用可能なその他のアクションのプロパティを追加するには、**[新しいパラメーターの追加]** リストからそれらプロパティを選択します。

1. デザイナーのツール バーで、 **[保存]** を選択します。

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>他のアカウントを使用して接続する

Azure に現在サインインしているものとは別のアカウントを使用して Outlook に接続しようとすると、[シングル サインオン (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) のエラーが発生する可能性があります。 この問題は、あるアカウントを使用して Azure portal にサインインしたが、別のアカウントを使用して接続を作成した場合に発生します。 デザイナーでは、Azure portal にサインインしているアカウントを使用することを前提としています。 この問題を解決するには、次のオプションがあります。

* ロジック アプリのリソース グループで、**共同作成者** ロールを使用してもう一方のアカウントを設定します。

  1. ロジック アプリのリソース グループ メニューで、 **[アクセス制御 (IAM)]** を選択します。 **共同作成者** ロールを使用して、他のアカウントを設定します。 
  
     詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

  1. このロールを設定した後、共同作成者のアクセス許可を持つアカウントを使用して Azure portal にサインインします。 これで、このアカウントを使用して Outlook への接続を作成できるようになりました。

* 職場または学校アカウントに "メールボックス所有者として送信する" アクセス許可が付与されるように、他のアカウントを設定します。

   管理者アクセス許可を持っている場合は、サービス アカウントのメールボックスで、 **[メールボックス所有者として送信する]** または **[代理として送信する]** アクセス許可を使用して職場または学校アカウントを設定します。 詳細については、「[別のユーザーにメールボックス アクセス許可を付与する - 管理者向けヘルプ](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user)」をご覧ください。 その後、職場または学校アカウントを使用して接続を作成できます。 ここで、送信者を指定できるトリガーまたはアクションで、サービス アカウントのメール アドレスを使用できます。

   たとえば、 **[電子メールの送信]** アクションには、オプションの **[送信者 (メールボックス所有者として送信する)]** パラメーターがあります。このパラメーターは、アクションに追加したり、サービス アカウントのメール アドレスを送信者として使用したりできます。 このパラメーターを追加するには、次の手順に従います。

   1. **[電子メールの送信]** アクションで、 **[パラメーターの追加]** リストを開き、 **[送信者 (メールボックス所有者として送信する)]** パラメーターを選択します。

   1. アクションでパラメーターが表示されたら、サービス アカウントのメール アドレスを入力します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/office365/)を参照してください。 

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
