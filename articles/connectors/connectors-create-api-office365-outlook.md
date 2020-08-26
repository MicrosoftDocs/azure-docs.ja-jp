---
title: Office 365 Outlook に接続する
description: Azure Logic Apps を使用して、Office 365 Outlook のメール、連絡先、カレンダーを管理するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 07/27/2020
tags: connectors
ms.openlocfilehash: e0e152b3c0e10c34bc6213ddf867b79c58d1d767
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832979"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して、Office 365 Outlook のメール、連絡先、カレンダーを管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Office 365 Outlook コネクタ](/connectors/office365connector/)を使用すると、ロジック アプリを構築することで Office 365 を管理する自動化されたタスクとワークフローを作成できます。 たとえば、次のタスクを自動化します。

* メールの取得、送信、返信を行う。 
* カレンダーで会議をスケジュールする。
* 連絡先を追加および編集する。 

任意のトリガーを使用してワークフローを開始できます。たとえば、新しいメールが届いたとき、カレンダーの項目が更新されたとき、または Salesforce などの別のサービスでイベントが発生したときなどです。 トリガー イベントに応答するアクションを使用できます。たとえば、メールを送信したり、新しいカレンダー イベントを作成したりできます。 

> [!NOTE]
> @outlook.com または @hotmail.com アカウントのタスクを自動化するには、[Outlook.com コネクタ](../connectors/connectors-create-api-outlook.md)を使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。 

* [Office 365 アカウント](https://www.office.com/)

* Office 365 Outlook アカウントにアクセスするロジック アプリ。 Office 365 Outlook トリガーを使用してワークフローを開始するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。 Office 365 Outlook アクションをワークフローに追加するには、ロジック アプリにトリガーが既に存在している必要があります。

## <a name="add-a-trigger"></a>トリガーの追加

[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)は、ロジック アプリのワークフローを開始するイベントです。 この例のロジック アプリでは、指定された間隔と頻度に基づいて、メール アカウントの更新されたカレンダー イベントを確認する "ポーリング" トリガーを使用します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで空のロジック アプリを開きます。

1. 検索ボックスに、フィルターとして「`office 365 outlook`」と入力します。 この例では、 **[予定しているイベントがすぐに開始されるとき]** を選択します。
   
   ![ロジック アプリを開始するトリガーの選択](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. サインインを求められた場合は、ロジック アプリがアカウントに接続できるように、Office 365 の資格情報を入力します。 それ以外の場合、接続が既に存在する場合は、トリガーのプロパティに関する情報を指定します。

   > [!NOTE]
   > サインイン資格情報を変更した場合でも、接続は取り消されるまで期限切れとはなりません。 詳細については、「[Azure Active Directory における構成可能なトークンの有効期間](../active-directory/develop/active-directory-configurable-token-lifetimes.md)」を参照してください。

   この例では、トリガーによってチェックされるカレンダーを選択します。次に例を示します。

   ![トリガーのプロパティを構成する](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. トリガーに、**[頻度]** と **[間隔]** の値を設定します。 **タイム ゾーン**など、使用可能なその他のトリガーのプロパティを追加するには、**[新しいパラメーターの追加]** リストからそれらプロパティを選択します。

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

1. サインインを求められた場合は、ロジック アプリがアカウントに接続できるように、Office 365 の資格情報を入力します。 それ以外の場合、接続が既に存在する場合は、アクションのプロパティに関する情報を指定します。

   > [!NOTE]
   > サインイン資格情報を変更した場合でも、接続は取り消されるまで期限切れとはなりません。 詳細については、「[Azure Active Directory における構成可能なトークンの有効期間](../active-directory/develop/active-directory-configurable-token-lifetimes.md)」を参照してください。

   この例では、アクションによって新しい連絡先が作成される連絡先のフォルダーを選択します。次に例を示します。

   ![アクションのプロパティを構成する](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   使用可能なその他のアクションのプロパティを追加するには、**[新しいパラメーターの追加]** リストからそれらプロパティを選択します。

1. デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/office365/)を参照してください。 

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。