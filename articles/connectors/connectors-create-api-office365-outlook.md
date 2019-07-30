---
title: Office 365 Outlook に接続する - Azure Logic Apps | Microsoft Docs
description: Office 365 REST API と Azure Logic Apps を使用して、メール、連絡先、予定表を管理します
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 52abf17e869216e65780129a7b48df79bd79f67a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105056"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Office 365 Outlook コネクタの使用
Office 365 Outlook コネクタを使用すると、Office 365 Outlook の操作が可能になります。 このコネクタを使用すると、連絡先と予定表アイテムの作成、編集、更新に加え、電子メールの取得、送信、返信を行うことができます。

Office 365 Outlook では、次のことができます。

* Office 365 の電子メールや予定表の機能を使用して、ワークフローを構築します。 
* 新しい電子メールを受信したときや予定表アイテムが更新されたときなどに、トリガーを使用してワークフローを開始します。
* アクションを使用して、電子メールの送信、新しい予定表イベントの作成などの操作を実行します。 たとえば、Salesforce 内に新しいオブジェクトがあるとき (トリガー) に Office 365 Outlook に電子メールを送信します (アクション)。 

この記事では、ロジック アプリ内で Office 365 Outlook コネクタを使用する方法を説明し、トリガーとアクションの一覧を示します。

> [!NOTE]
> 本記事は、一般公開された Logic Apps の一般公開 (GA) を対象としています。
> 
> 

Logic Apps の詳細については、「[Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="connect-to-office-365"></a>Office 365 への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成します。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、Office 365 Outlook に接続するには、最初に Office 365 "*接続*" が必要になります。 接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。 そのため、Office 365 Outlook の場合は、Office 365 アカウントの資格情報を入力して接続を作成します。

## <a name="create-the-connection"></a>接続の作成
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーは、指定された間隔と頻度でサービスを "ポーリング" します。 トリガーの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

1. ロジック アプリで「office 365」と入力して、トリガーの一覧を取得します。  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. **[Office 365 Outlook - When an upcoming event is starting soon (Office 365 Outlook - 予定しているイベントが間もなく開始されるとき)]** を選択します。 接続が既に存在する場合は、ドロップダウン リストから予定表を選択します。
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    サインインを求められたら、サインインの詳細を入力して接続を作成します。 この手順については、このトピックの「[接続の作成](connectors-create-api-office365-outlook.md#create-the-connection)」を参照してください。 
   
   > [!NOTE]
   > この例では、予定表のイベントが更新されたときにロジック アプリが実行されます。 このトリガーの結果を確認するには、自分にテキスト メッセージを送信する別のアクションを追加してください。 たとえば、15 分以内に予定表イベントが開始するときにテキスト メッセージを送信する Twilio "*メッセージの送信*" アクションを追加します。 
   > 
   > 
3. **[編集]** を選択し、 **[頻度]** と **[間隔]** の値を設定します。 たとえば、トリガーを使用して 15 分ごとにポーリングを実行するには、 **[頻度]** を **[分]** に設定し、 **[間隔]** を **15** に設定します。 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="use-an-action"></a>アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

1. プラス記号を選択します。 いくつかの選択肢が表示されます。 **[アクションの追加]** 、 **[条件の追加]** 、 **[その他]** です。
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. **[アクションの追加]** を選択します。
3. テキスト ボックスに「office 365」と入力して、使用可能なすべてのアクションの一覧を取得します。
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. この例では、 **[Office 365 Outlook - Create contact (Office 365 Outlook - 連絡先を作成する)]** を選択します。 接続が既に存在する場合は、 **[Folder ID (フォルダー ID)]** 、 **[名]** 、およびその他のプロパティを指定します。  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    接続情報の入力を求められたら、詳細を入力して接続を作成します。 これらのプロパティについては、このトピックの「[接続の作成](connectors-create-api-office365-outlook.md#create-the-connection)」を参照してください。 
   
   > [!NOTE]
   > この例では、Office 365 Outlook で新しい連絡先を作成します。 別のトリガーからの出力を使用して、連絡先を作成できます。 たとえば、SalesForce の "*When an object is created (オブジェクトが作成されたとき)* " トリガーを追加します。 次に、SalesForce のフィールドを使用して Office 365 で新しい連絡先を作成する Office 365 Outlook の "*Create contact (連絡先を作成する)* " アクションを追加します。 
   > 
   > 
5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/office365connector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次の手順
[ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。

