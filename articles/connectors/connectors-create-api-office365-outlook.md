---
title: "ロジック アプリに Office 365 Outlook コネクタを追加する | Microsoft Docs"
description: "Office 365 コネクタを含むロジック アプリを作成して Office 365 を操作できるようにします。 たとえば、連絡先や予定表項目の作成、編集、更新を行うことができます。"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 92c0892128655141f29380890c31451e62ca8853
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Office 365 Outlook コネクタの使用
Office 365 Outlook コネクタを使用すると、Office 365 Outlook の操作が可能になります。 このコネクタを使用すると、連絡先と予定表アイテムの作成、編集、更新に加え、電子メールの取得、送信、返信を行うことができます。

Office 365 Outlook では、次のことができます。

* Office 365 の電子メールや予定表の機能を使用して、ワークフローを構築します。 
* 新しい電子メールを受信したときや予定表アイテムが更新されたときなどに、トリガーを使用してワークフローを開始します。
* アクションを使用して、電子メールの送信、新しい予定表イベントの作成などの操作を実行します。 たとえば、Salesforce 内に新しいオブジェクトがあるとき (トリガー) に Office 365 Outlook に電子メールを送信します (アクション)。 

このトピックでは、ロジック アプリ内で Office 365 Outlook コネクタを使用する方法を説明し、トリガーとアクションの一覧を示します。

> [!NOTE]
> 本記事は、一般公開された Logic Apps の一般公開 (GA) を対象としています。
> 
> 

Logic Apps の詳細については、「[Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」と[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

## <a name="connect-to-office-365"></a>Office 365 への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成します。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、Office 365 Outlook に接続するには、最初に Office 365 "*接続*" が必要になります。 接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。 そのため、Office 365 Outlook の場合は、Office 365 アカウントの資格情報を入力して接続を作成します。

## <a name="create-the-connection"></a>接続の作成
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーは、指定された間隔と頻度でサービスを "ポーリング" します。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

1. ロジック アプリで「office 365」と入力して、トリガーの一覧を取得します。  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. **[Office 365 Outlook - When an upcoming event is starting soon (Office 365 Outlook - 予定しているイベントが間もなく開始されるとき)]** を選択します。 接続が既に存在する場合は、ドロップダウン リストから予定表を選択します。
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    サインインを求められたら、サインインの詳細を入力して接続を作成します。 この手順については、このトピックの「[接続の作成](connectors-create-api-office365-outlook.md#create-the-connection)」を参照してください。 
   
   > [!NOTE]
   > この例では、予定表のイベントが更新されたときにロジック アプリが実行されます。 このトリガーの結果を確認するには、自分にテキスト メッセージを送信する別のアクションを追加してください。 たとえば、15 分以内に予定表イベントが開始するときにテキスト メッセージを送信する Twilio "*メッセージの送信*" アクションを追加します。 
   > 
   > 
3. **[編集]** を選択し、**[頻度]** と **[間隔]** の値を設定します。 たとえば、トリガーを使用して 15 分ごとにポーリングを実行するには、**[頻度]** を **[分]** に設定し、**[間隔]** を **15** に設定します。 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="use-an-action"></a>アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

1. プラス記号を選択します。 **[アクションの追加]**、**[条件の追加]**、**[More (その他)]** のいずれかのオプションという複数の選択肢があります。
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. **[アクションの追加]**を選択します。
3. テキスト ボックスに「office 365」と入力して、使用可能なすべてのアクションの一覧を取得します。
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. この例では、**[Office 365 Outlook - Create contact (Office 365 Outlook - 連絡先を作成する)]** を選択します。 接続が既に存在する場合は、**[Folder ID (フォルダー ID)]**、**[名]**、およびその他のプロパティを指定します。  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    接続情報の入力を求められたら、詳細を入力して接続を作成します。 これらのプロパティについては、このトピックの「[接続の作成](connectors-create-api-office365-outlook.md#create-the-connection)」を参照してください。 
   
   > [!NOTE]
   > この例では、Office 365 Outlook で新しい連絡先を作成します。 別のトリガーからの出力を使用して、連絡先を作成できます。 たとえば、SalesForce の "*When an object is created (オブジェクトが作成されたとき)*" トリガーを追加します。 次に、SalesForce のフィールドを使用して Office 365 で新しい連絡先を作成する Office 365 Outlook の "*Create contact (連絡先を作成する)*" アクションを追加します。 
   > 
   > 
5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="office-365-triggers"></a>Office 365 トリガー
| トリガー | Description |
| --- | --- |
| [[Office&365; Outlook - When an upcoming event is starting soon (Office&365; Outlook - 予定しているイベントが間もなく開始されるとき)]](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon) |この操作では、予定表イベントが間もなく開始されるときにフローをトリガーします。 |
| [[When a new email arrives (新しい電子メールが届いたとき)]](connectors-create-api-office365-outlook.md#when-a-new-email-arrives) |この操作では、新しい電子メールが届いたときにフローをトリガーします。 |
| [[When a new event is created (新しいイベントが作成されたとき)]](connectors-create-api-office365-outlook.md#when-a-new-event-is-created) |この操作では、予定表に新しいイベントが作成されたときにフローをトリガーします。 |
| [[When an event is modified (イベントが変更されたとき)]](connectors-create-api-office365-outlook.md#when-an-event-is-modified) |この操作では、予定表のイベントが変更されたときにフローをトリガーします。 |

## <a name="office-365-actions"></a>Office 365 アクション
| [操作] | Description |
| --- | --- |
| [電子メールを取得する](connectors-create-api-office365-outlook.md#get-emails) |この操作では、フォルダーから電子メールを取得します。 |
| [電子メールを送信する](connectors-create-api-office365-outlook.md#send-an-email) |この操作では、電子メール メッセージを送信します。 |
| [電子メールを削除する](connectors-create-api-office365-outlook.md#delete-email) |この操作では、ID に基づいて電子メールを削除します。 |
| [既読としてマークする](connectors-create-api-office365-outlook.md#mark-as-read) |この操作では、電子メールを既読としてマークします。 |
| [電子メールに返信する](connectors-create-api-office365-outlook.md#reply-to-email) |この操作では、電子メールに返信します。 |
| [添付ファイルを取得する](connectors-create-api-office365-outlook.md#get-attachment) |この操作では、ID に基づいて電子メールの添付ファイルを取得します。 |
| [オプションを指定して電子メールを送信する](connectors-create-api-office365-outlook.md#send-email-with-options) |この操作では、複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます。 |
| [承認の電子メールを送信する](connectors-create-api-office365-outlook.md#send-approval-email) |この操作では、承認の電子メールを送信し、受信者からの返信を待ちます。 |
| [予定表を取得する](connectors-create-api-office365-outlook.md#get-calendars) |この操作では、使用できる予定表を一覧表示します。 |
| [イベントを取得する](connectors-create-api-office365-outlook.md#get-events) |この操作では、予定表からイベントを取得します。 |
| [イベントを作成する](connectors-create-api-office365-outlook.md#create-event) |この操作では、予定表に新しいイベントを作成します。 |
| [イベントを取得する](connectors-create-api-office365-outlook.md#get-event) |この操作では、予定表から特定のイベントを取得します。 |
| [インベントリを削除する](connectors-create-api-office365-outlook.md#delete-event) |この操作では、予定表のイベントを削除します。 |
| [イベントを更新する](connectors-create-api-office365-outlook.md#update-event) |この操作では、予定表のイベントを更新します。 |
| [連絡先フォルダーを取得する](connectors-create-api-office365-outlook.md#get-contact-folders) |この操作では、使用できる連絡先フォルダーを一覧表示します。 |
| [連絡先を取得する](connectors-create-api-office365-outlook.md#get-contacts) |この操作では、連絡先フォルダーから連絡先を取得します。 |
| [連絡先を作成する](connectors-create-api-office365-outlook.md#create-contact) |この操作では、連絡先フォルダーに連絡先を作成します。 |
| [連絡先を取得する](connectors-create-api-office365-outlook.md#get-contact) |この操作では、連絡先フォルダーから特定の連絡先を取得します。 |
| [連絡先を削除する](connectors-create-api-office365-outlook.md#delete-contact) |この操作では、連絡先フォルダーから連絡先を削除します。 |
| [連絡先を更新する](connectors-create-api-office365-outlook.md#update-contact) |この操作では、連絡先フォルダーの連絡先を更新します。 |

### <a name="trigger-and-action-details"></a>トリガーとアクションの詳細
このセクションでは、必須または任意の入力プロパティ、コネクタに関連付けられた対応する出力など、各トリガーとアクションに関する具体的な詳細について説明します。

#### <a name="when-an-upcoming-event-is-starting-soon"></a>[Office&365; Outlook - When an upcoming event is starting soon (Office&365; Outlook - 予定しているイベントが間もなく開始されるとき)]
この操作では、予定表イベントが間もなく開始されるときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表の一意識別子 |
| lookAheadTimeInMinutes |Look ahead time (開始までの時間) |間もなく開始されるイベントまでの時間 (分) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarItemsList: 予定表項目の一覧

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| 値 |array |予定表アイテムの一覧 |

#### <a name="get-emails"></a>電子メールを取得する
この操作では、フォルダーから電子メールを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| folderPath |フォルダー パス |電子メールを取得するフォルダーのパス (既定値: 'Inbox') |
| top |Top (上位) |取得する電子メールの件数 (既定値: 10) |
| fetchOnlyUnread |Fetch Only Unread Messages (未読メッセージのみを取得する) |未読の電子メールのみを取得しますか？ |
| includeAttachments |Include Attachments (添付ファイルを含める) |True に設定すると、電子メールと共に添付ファイルも取得されます |
| searchQuery |検索クエリ |電子メールをフィルターする検索クエリ |
| skip |Skip |スキップする電子メールの件数 (既定値: 0) |
| skipToken |Skip Token (skip トークン) |新しいページを取得するスキップ トークン |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
ReceiveMessage: 電子メール メッセージを受信します

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ファイル |string |ファイル |
| To |string |To |
| [件名] |string |[件名] |
| body |string |body |
| 重要度 |string |重要度 |
| HasAttachment |boolean |添付ファイルを含む |
| ID |string |メッセージ ID |
| IsRead |boolean |開封済み |
| DateTimeReceived |string |受信した日時 |
| [添付ファイル] |array |[添付ファイル] |
| Cc |string |someone@contoso.com のようにセミコロンで区切って電子メール アドレスを指定 |
| [Bcc] |string |someone@contoso.com のようにセミコロンで区切って電子メール アドレスを指定 |
| IsHtml |boolean |HTML メッセージ |

#### <a name="send-an-email"></a>電子メールを送信する
この操作では、電子メール メッセージを送信します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| emailMessage* |電子メール |電子メール |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="delete-email"></a>電子メールを削除する
この操作では、ID に基づいて電子メールを削除します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| messageId* |メッセージ ID |削除する電子メールの ID |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="mark-as-read"></a>既読としてマークする
この操作では、電子メールを既読としてマークします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| messageId* |メッセージ ID |既読とマークする電子メールの ID |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="reply-to-email"></a>[Reply to email (電子メールに返信する)]
この操作では、電子メールに返信します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| messageId* |メッセージ ID |返信する電子メールの ID |
| comment* |コメント |応答のコメント |
| replyAll |全員へ返信 |すべての受信者に返信する |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="get-attachment"></a>添付ファイルを取得する
この操作では、ID に基づいて電子メールの添付ファイルを取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| messageId* |メッセージ ID |電子メールの ID |
| attachmentId* |Attachment Id (添付ファイルの ID) |ダウンロードする添付ファイルの ID |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="when-a-new-email-arrives"></a>[When a new email arrives (新しい電子メールが届いたとき)]
この操作では、新しい電子メールが届いたときにフローをトリガーします。

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| folderPath |フォルダー パス |取得する電子メール フォルダー (既定値: 受信トレイ)。 |
| to |To |受信者の電子メール アドレス |
| ファイル |ファイル |差出人アドレス |
| importance |重要度 |電子メールの重要度 (High、Normal、Low) (既定値: Normal) |
| fetchOnlyWithAttachment |Has Attachments (添付ファイルあり) |ファイルが添付された電子メールのみを取得します |
| includeAttachments |Include Attachments (添付ファイルを含める) |添付ファイルを含めます |
| subjectFilter |Subject Filter (件名フィルター) |サブジェクト内で検索する文字列 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
TriggerBatchResponse[ReceiveMessage]

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="send-email-with-options"></a>オプションを指定して電子メールを送信する
この操作では、複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| optionsEmailSubscription* |オプションの電子メールのサブスクリプション要求 |オプションの電子メールのサブスクリプション要求 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
SubscriptionResponse: 承認の電子メール サブスクリプションのモデル

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| id |string |サブスクリプションの ID |
| resource |string |サブスクリプション要求のリソース |
| notificationType |string |通知の種類 |
| notificationUrl |string |通知 URL |

#### <a name="send-approval-email"></a>承認の電子メールを送信します
この操作では、承認の電子メールを送信し、受信者からの返信を待ちます。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| approvalEmailSubscription* |承認の電子メールのサブスクリプション要求 |承認の電子メールのサブスクリプション要求 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
SubscriptionResponse: 承認の電子メール サブスクリプションのモデル

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| id |string |サブスクリプションの ID |
| resource |string |サブスクリプション要求のリソース |
| notificationType |string |通知の種類 |
| notificationUrl |string |通知 URL |

#### <a name="get-calendars"></a>予定表を取得する
この操作では、使用できる予定表を一覧表示します。 

この呼び出しには、パラメーターはありません。

##### <a name="output-details"></a>出力の詳細
TablesList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="get-events"></a>Get events
この操作では、予定表からイベントを取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarEventList: 予定表アイテムの一覧

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| 値 |array |予定表アイテムの一覧 |

#### <a name="create-event"></a>イベントを作成する
この操作では、予定表に新しいイベントを作成します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| item* |項目 |作成するイベント |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarEvent: コネクタに固有の予定表イベント モデル クラス。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ID |string |イベントの一意の識別子。 |
| 出席者 |array |イベントの出席者の一覧。 |
| body |未定義 |イベントに関連付けられているメッセージの本文。 |
| BodyPreview |string |イベントに関連付けられているメッセージのプレビュー。 |
| カテゴリ |array |イベントに関連付けられているカテゴリ。 |
| ChangeKey |string |イベント オブジェクトのバージョンを識別します。 イベントが変更されるたびに、ChangeKey も変更されます。 |
| DateTimeCreated |string |イベントが作成された日時。 |
| DateTimeLastModified |string |イベントが最後に変更された日時。 |
| End |string |イベントの終了時刻。 |
| EndTimeZone |string |会議の終了時刻のタイム ゾーンを指定します。 この値は、Windows で定義されている値である必要があります (例: "太平洋標準時")。 |
| HasAttachments |boolean |イベントに添付ファイルがある場合は true に設定されます。 |
| [重要度] |string |イベントの重要度: Low、Normal、または High。 |
| IsAllDay |boolean |イベントが全日のイベントの場合は true に設定されます。 |
| IsCancelled |boolean |イベントが取り消された場合は true に設定されます。 |
| IsOrganizer |boolean |メッセージの送信者が開催者でもある場合は true に設定されます。 |
| 場所 |未定義 |イベントの場所。 |
| Organizer |未定義 |イベントの開催者。 |
| 繰り返し |未定義 |イベントの繰り返しパターン。 |
| アラーム |integer |イベントの開始を何分前から通知するか。 |
| ResponseRequested |boolean |イベントが受理または拒否されたときに送信者が応答を希望している場合は、true に設定されます。 |
| ResponseStatus |未定義 |イベント メッセージへの応答で送信された応答の種類を示します。 |
| SeriesMasterId |string |"Series Master (系列マスター)" イベントの種類の一意の識別子。 |
| ShowAs |string |空きまたはビジー状態として表示されます。 |
| 開始 |string |イベントの開始時刻。 |
| StartTimeZone |string |会議の開始時刻のタイム ゾーンを指定します。 この値は、Windows で定義されている値である必要があります (例: "太平洋標準時")。 |
| [件名] |string |イベントの件名。 |
| 型 |string |イベントの種類: "単一インスタンス"、"この予定だけ"、"例外"、または "Series Master (系列マスター)"。 |
| WebLink |string |イベントに関連付けられているメッセージのプレビュー。 |

#### <a name="get-event"></a>イベントを取得する
この操作では、予定表から特定のイベントを取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| id* |Item id (項目 ID) |イベントを選択します |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarEvent: コネクタに固有の予定表イベント モデル クラス。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ID |string |イベントの一意の識別子。 |
| 出席者 |array |イベントの出席者の一覧。 |
| body |未定義 |イベントに関連付けられているメッセージの本文。 |
| BodyPreview |string |イベントに関連付けられているメッセージのプレビュー。 |
| カテゴリ |array |イベントに関連付けられているカテゴリ。 |
| ChangeKey |string |イベント オブジェクトのバージョンを識別します。 イベントが変更されるたびに、ChangeKey も変更されます。 |
| DateTimeCreated |string |イベントが作成された日時。 |
| DateTimeLastModified |string |イベントが最後に変更された日時。 |
| End |string |イベントの終了時刻。 |
| EndTimeZone |string |会議の終了時刻のタイム ゾーンを指定します。 この値は、Windows で定義されている値である必要があります (例: "太平洋標準時")。 |
| HasAttachments |boolean |イベントに添付ファイルがある場合は true に設定されます。 |
| [重要度] |string |イベントの重要度: Low、Normal、または High。 |
| IsAllDay |boolean |イベントが全日のイベントの場合は true に設定されます。 |
| IsCancelled |boolean |イベントが取り消された場合は true に設定されます。 |
| IsOrganizer |boolean |メッセージの送信者が開催者でもある場合は true に設定されます。 |
| 場所 |未定義 |イベントの場所。 |
| Organizer |未定義 |イベントの開催者。 |
| 繰り返し |未定義 |イベントの繰り返しパターン。 |
| アラーム |integer |イベントの開始を何分前から通知するか。 |
| ResponseRequested |boolean |イベントが受理または拒否されたときに送信者が応答を希望している場合は、true に設定されます。 |
| ResponseStatus |未定義 |イベント メッセージへの応答で送信された応答の種類を示します。 |
| SeriesMasterId |string |"Series Master (系列マスター)" イベントの種類の一意の識別子。 |
| ShowAs |string |空きまたはビジー状態として表示されます。 |
| 開始 |string |イベントの開始時刻。 |
| StartTimeZone |string |会議の開始時刻のタイム ゾーンを指定します。 この値は、Windows で定義されている値である必要があります (例: "太平洋標準時")。 |
| [件名] |string |イベントの件名。 |
| 型 |string |イベントの種類: "単一インスタンス"、"この予定だけ"、"例外"、または "Series Master (系列マスター)"。 |
| WebLink |string |イベントに関連付けられているメッセージのプレビュー。 |

#### <a name="delete-event"></a>インベントリを削除する
この操作では、予定表のイベントを削除します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| id* |ID |イベントを選択します |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="update-event"></a>イベントを更新する
この操作では、予定表のイベントを更新します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| id* |ID |イベントを選択します |
| item* |項目 |更新するイベント |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarEvent: コネクタに固有の予定表イベント モデル クラス。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ID |string |イベントの一意の識別子。 |
| 出席者 |array |イベントの出席者の一覧。 |
| body |未定義 |イベントに関連付けられているメッセージの本文。 |
| BodyPreview |string |イベントに関連付けられているメッセージのプレビュー。 |
| カテゴリ |array |イベントに関連付けられているカテゴリ。 |
| ChangeKey |string |イベント オブジェクトのバージョンを識別します。 イベントが変更されるたびに、ChangeKey も変更されます。 |
| DateTimeCreated |string |イベントが作成された日時。 |
| DateTimeLastModified |string |イベントが最後に変更された日時。 |
| End |string |イベントの終了時刻。 |
| EndTimeZone |string |会議の終了時刻のタイム ゾーンを指定します。 この値は、Windows で定義されている値である必要があります (例: "太平洋標準時")。 |
| HasAttachments |boolean |イベントに添付ファイルがある場合は true に設定されます。 |
| [重要度] |string |イベントの重要度: Low、Normal、または High。 |
| IsAllDay |boolean |イベントが全日のイベントの場合は true に設定されます。 |
| IsCancelled |boolean |イベントが取り消された場合は true に設定されます。 |
| IsOrganizer |boolean |メッセージの送信者が開催者でもある場合は true に設定されます。 |
| 場所 |未定義 |イベントの場所。 |
| Organizer |未定義 |イベントの開催者。 |
| 繰り返し |未定義 |イベントの繰り返しパターン。 |
| アラーム |integer |イベントの開始を何分前から通知するか。 |
| ResponseRequested |boolean |イベントが受理または拒否されたときに送信者が応答を希望している場合は、true に設定されます。 |
| ResponseStatus |未定義 |イベント メッセージへの応答で送信された応答の種類を示します。 |
| SeriesMasterId |string |"Series Master (系列マスター)" イベントの種類の一意の識別子。 |
| ShowAs |string |空きまたはビジー状態として表示されます。 |
| 開始 |string |イベントの開始時刻。 |
| StartTimeZone |string |会議の開始時刻のタイム ゾーンを指定します。 この値は、Windows で定義されている値である必要があります (例: "太平洋標準時")。 |
| [件名] |string |イベントの件名。 |
| 型 |string |イベントの種類: "単一インスタンス"、"この予定だけ"、"例外"、または "Series Master (系列マスター)"。 |
| WebLink |string |イベントに関連付けられているメッセージのプレビュー。 |

#### <a name="when-a-new-event-is-created"></a>[When a new event is created (新しいイベントが作成されたとき)]
この操作では、予定表に新しいイベントが作成されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarItemsList: 予定表項目の一覧

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| 値 |array |予定表アイテムの一覧 |

#### <a name="when-an-event-is-modified"></a>[When an event is modified (イベントが変更されたとき)]
この操作では、予定表のイベントが変更されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Calendar id (予定表 ID) |予定表を選択します |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
CalendarItemsList: 予定表項目の一覧

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| 値 |array |予定表アイテムの一覧 |

#### <a name="get-contact-folders"></a>連絡先フォルダーを取得する
この操作では、使用できる連絡先フォルダーを一覧表示します。 

この呼び出しには、パラメーターはありません。

##### <a name="output-details"></a>出力の詳細
TablesList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="get-contacts"></a>連絡先を取得する
この操作では、連絡先フォルダーから連絡先を取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Folder id (フォルダー ID) |取得する連絡先フォルダーの一意識別子 |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
ContactList: 連絡先の一覧

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| 値 |array |連絡先の一覧 |

#### <a name="create-contact"></a>連絡先を作成する
この操作では、連絡先フォルダーに連絡先を作成します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Folder id (フォルダー ID) |連絡先フォルダーを選択します |
| item* |項目 |作成する連絡先 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
連絡先: 連絡先

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ID |string |連絡先の一意の識別子。 |
| ParentFolderId |string |連絡先の親フォルダーの ID。 |
| Birthday |string |連絡先の誕生日。 |
| FileAs |string |連絡先をファイリングするときに使用する名前。 |
| displayName |string |連絡先の表示名。 |
| GivenName |string |連絡先の名。 |
| Initials |string |連絡先の頭文字。 |
| MiddleName |string |連絡先のミドル ネーム。 |
| NickName |string |連絡先のニックネーム。 |
| Surname |string |連絡先の姓。 |
| タイトル |string |連絡先の肩書き。 |
| Generation |string |連絡先の世代。 |
| EmailAddresses |array |連絡先の電子メール アドレス。 |
| ImAddresses |array |連絡先のインスタント メッセージング (IM) アドレス。 |
| JobTitle |string |連絡先の役職。 |
| CompanyName |string |連絡先の会社の名前。 |
| 部署 |string |連絡先の所属部門。 |
| OfficeLocation |string |連絡先のオフィスの場所。 |
| Profession |string |連絡先の職業。 |
| BusinessHomePage |string |連絡先の会社のホーム ページ。 |
| AssistantName |string |連絡先のアシスタントの名前。 |
| Manager |string |連絡先の上司の名前。 |
| HomePhones |array |連絡先の自宅電話番号。 |
| BusinessPhones |array |連絡先の勤務先電話番号。 |
| MobilePhone1 |string |連絡先の携帯電話番号。 |
| HomeAddress |未定義 |連絡先の自宅の住所。 |
| BusinessAddress |未定義 |連絡先の勤務先の住所。 |
| OtherAddress |未定義 |連絡先の他の住所。 |
| YomiCompanyName |string |連絡先の会社名の日本語読み。 |
| YomiGivenName |string |連絡先の名の日本語読み。 |
| YomiSurname |string |連絡先の姓の日本語読み。 |
| カテゴリ |array |連絡先に関連付けられているカテゴリ。 |
| ChangeKey |string |イベント オブジェクトのバージョンを識別します。 |
| DateTimeCreated |string |連絡先が作成された日時。 |
| DateTimeLastModified |string |連絡先が変更された日時。 |

#### <a name="get-contact"></a>連絡先を取得する
この操作では、連絡先フォルダーから特定の連絡先を取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Folder id (フォルダー ID) |連絡先フォルダーを選択します |
| id* |Item id (項目 ID) |取得する連絡先の一意識別子 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
連絡先: 連絡先

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ID |string |連絡先の一意の識別子。 |
| ParentFolderId |string |連絡先の親フォルダーの ID。 |
| Birthday |string |連絡先の誕生日。 |
| FileAs |string |連絡先をファイリングするときに使用する名前。 |
| displayName |string |連絡先の表示名。 |
| GivenName |string |連絡先の名。 |
| Initials |string |連絡先の頭文字。 |
| MiddleName |string |連絡先のミドル ネーム。 |
| NickName |string |連絡先のニックネーム。 |
| Surname |string |連絡先の姓。 |
| タイトル |string |連絡先の肩書き。 |
| Generation |string |連絡先の世代。 |
| EmailAddresses |array |連絡先の電子メール アドレス。 |
| ImAddresses |array |連絡先のインスタント メッセージング (IM) アドレス。 |
| JobTitle |string |連絡先の役職。 |
| CompanyName |string |連絡先の会社の名前。 |
| 部署 |string |連絡先の所属部門。 |
| OfficeLocation |string |連絡先のオフィスの場所。 |
| Profession |string |連絡先の職業。 |
| BusinessHomePage |string |連絡先の会社のホーム ページ。 |
| AssistantName |string |連絡先のアシスタントの名前。 |
| Manager |string |連絡先の上司の名前。 |
| HomePhones |array |連絡先の自宅電話番号。 |
| BusinessPhones |array |連絡先の勤務先電話番号。 |
| MobilePhone1 |string |連絡先の携帯電話番号。 |
| HomeAddress |未定義 |連絡先の自宅の住所。 |
| BusinessAddress |未定義 |連絡先の勤務先の住所。 |
| OtherAddress |未定義 |連絡先の他の住所。 |
| YomiCompanyName |string |連絡先の会社名の日本語読み。 |
| YomiGivenName |string |連絡先の名の日本語読み。 |
| YomiSurname |string |連絡先の姓の日本語読み。 |
| カテゴリ |array |連絡先に関連付けられているカテゴリ。 |
| ChangeKey |string |イベント オブジェクトのバージョンを識別します。 |
| DateTimeCreated |string |連絡先が作成された日時。 |
| DateTimeLastModified |string |連絡先が変更された日時。 |

#### <a name="delete-contact"></a>連絡先を削除する
この操作では、連絡先フォルダーから連絡先を削除します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Folder id (フォルダー ID) |連絡先フォルダーを選択します |
| id* |ID |削除する連絡先の一意識別子 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="update-contact"></a>連絡先を更新する
この操作では、連絡先フォルダーの連絡先を更新します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |Folder id (フォルダー ID) |連絡先フォルダーを選択します |
| id* |ID |更新する連絡先の一意識別子 |
| item* |項目 |更新する連絡先項目 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
連絡先: 連絡先

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| ID |string |連絡先の一意の識別子。 |
| ParentFolderId |string |連絡先の親フォルダーの ID。 |
| Birthday |string |連絡先の誕生日。 |
| FileAs |string |連絡先をファイリングするときに使用する名前。 |
| displayName |string |連絡先の表示名。 |
| GivenName |string |連絡先の名。 |
| Initials |string |連絡先の頭文字。 |
| MiddleName |string |連絡先のミドル ネーム。 |
| NickName |string |連絡先のニックネーム。 |
| Surname |string |連絡先の姓。 |
| タイトル |string |連絡先の肩書き。 |
| Generation |string |連絡先の世代。 |
| EmailAddresses |array |連絡先の電子メール アドレス。 |
| ImAddresses |array |連絡先のインスタント メッセージング (IM) アドレス。 |
| JobTitle |string |連絡先の役職。 |
| CompanyName |string |連絡先の会社の名前。 |
| 部署 |string |連絡先の所属部門。 |
| OfficeLocation |string |連絡先のオフィスの場所。 |
| Profession |string |連絡先の職業。 |
| BusinessHomePage |string |連絡先の会社のホーム ページ。 |
| AssistantName |string |連絡先のアシスタントの名前。 |
| Manager |string |連絡先の上司の名前。 |
| HomePhones |array |連絡先の自宅電話番号。 |
| BusinessPhones |array |連絡先の勤務先電話番号。 |
| MobilePhone1 |string |連絡先の携帯電話番号。 |
| HomeAddress |未定義 |連絡先の自宅の住所。 |
| BusinessAddress |未定義 |連絡先の勤務先の住所。 |
| OtherAddress |未定義 |連絡先の他の住所。 |
| YomiCompanyName |string |連絡先の会社名の日本語読み。 |
| YomiGivenName |string |連絡先の名の日本語読み。 |
| YomiSurname |string |連絡先の姓の日本語読み。 |
| カテゴリ |array |連絡先に関連付けられているカテゴリ。 |
| ChangeKey |string |イベント オブジェクトのバージョンを識別します。 |
| DateTimeCreated |string |連絡先が作成された日時。 |
| DateTimeLastModified |string |連絡先が変更された日時。 |

## <a name="http-responses"></a>HTTP 応答
上記のアクションとトリガーは、次の HTTP 状態コードを&1; つ以上返す場合があります。 

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。


