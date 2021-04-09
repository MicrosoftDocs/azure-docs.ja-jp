---
title: Outlook.com に接続する
description: Azure Logic Apps を使用して、Outlook.com のメール、カレンダー、および連絡先を管理するタスクとワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75707188"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Outlook.com のメール、カレンダー、および連絡先を管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Outlook.com コネクタ](/connectors/outlook/)を使用すると、ロジック アプリをビルドすることで、@outlook.com または @hotmail.com アカウントを管理する自動化されたタスクとワークフローを作成できます。 たとえば、次のタスクを自動化します。

* メールの取得、送信、返信を行う。
* カレンダーで会議をスケジュールする。
* 連絡先を追加および編集する。

任意のトリガー (たとえば、新しいメールが届いたとき、カレンダーの項目が更新されたとき、別のサービスでイベントが発生したとき) を使用して、ワークフローを開始できます。 トリガー イベントに応答するアクションを使用できます。たとえば、メールを送信したり、新しいカレンダー イベントを作成したりできます。

> [!NOTE]
> @fabrikam.onmicrosoft.com などの Microsoft の職場アカウントのタスクを自動化するには、[Office 365 Outlook コネクタ](../connectors/connectors-create-api-office365-outlook.md)を使用してください。

## <a name="prerequisites"></a>前提条件

* [Outlook.com アカウント](https://outlook.live.com/owa/)

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Outlook.com アカウントにアクセスするロジック アプリ。 Outlook.com トリガーを使用してワークフローを開始するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。 Outlook.com アクションをワークフローに追加するには、ロジック アプリにトリガーが既に存在している必要があります。

## <a name="add-a-trigger"></a>トリガーの追加

[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)は、ロジック アプリのワークフローを開始するイベントです。 この例のロジック アプリでは、指定された間隔と頻度に基づいて、メール アカウントの新しいメールを確認する "ポーリング" トリガーを使用します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで空のロジック アプリを開きます。

1. 検索ボックスに、フィルターとして「outlook.com」と入力します。 この例では、**[新しいメールが届いたとき]** を選択します。

1. サインインを求められた場合は、Outlook.com の資格情報を入力して、お使いのアカウントにロジック アプリが接続できるようにします。 それ以外の場合、接続が既に存在する場合は、トリガーのプロパティに関する情報を指定します。

1. トリガーに、**[頻度]** と **[間隔]** の値を設定します。

   たとえば、トリガーを使用して 15 分ごとにポーリングを実行するには、**[頻度]** を **[分]** に設定し、**[間隔]** を **[15]** に設定します。

1. デザイナー ツールバーで、**[保存]** を選択します。これにより、ロジック アプリが保存されます。

トリガーに応答するために、別のアクションを追加します。 たとえば、Twilio の **[メッセージを送信する]** アクションを追加できます。これにより、メールが到着したときにテキストが送信されます。

## <a name="add-an-action"></a>アクションを追加する

[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、ロジック アプリのワークフローによって実行される操作です。 この例のロジック アプリでは、Outlook.com アカウントからメールが送信されます。 別のトリガーからの出力を使用して、アクションにデータを提供できます。 たとえば、ロジック アプリで、SalesForce の **[オブジェクトの作成時]** トリガーを使用するとします。 Outlook.com の **[メールを送信する]** アクションを追加し、SalesForce トリガーからの出力をメール内で使用できます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ワークフローの最後のステップとしてアクションを追加するには、**[新しいステップ]** を選択します。 

   ステップの間にアクションを追加するには、該当するステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「outlook.com」と入力します。 この例では、**[メールを送信する]** を選択します。 

1. サインインを求められた場合は、Outlook.com の資格情報を入力して、お使いのアカウントにロジック アプリが接続できるようにします。 それ以外の場合、接続が既に存在する場合は、アクションのプロパティに関する情報を指定します。

1. デザイナー ツールバーで、**[保存]** を選択します。これにより、ロジック アプリが保存されます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/outlook/)を参照してください。 

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
