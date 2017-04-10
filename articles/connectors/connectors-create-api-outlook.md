---
title: Outlook.com | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。 メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 409e1a104fa73d911ea508cbff311cb48fc20f9f


---
# <a name="get-started-with-the-outlookcom-connector"></a>Outlook.com コネクタの使用
Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。 メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
>
>

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Outlook.com コネクタは、アクションとして使用できます。Outlook.com コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Outlook.com コネクタでは、次のアクションやトリガーを使用できます。

### <a name="outlookcom-actions"></a>Outlook.com のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |フォルダーから電子メールを取得します |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |電子メールを送信します |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |ID を指定して電子メールを削除します |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |電子メールを既読としてマークします |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |電子メールに返信します |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |ID を指定して電子メールの添付ファイルを取得します |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |承認の電子メールを送信し、受信者からの返信を待ちます |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |予定表を取得します |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |予定表から項目を取得します |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |新しいイベントを作成します |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |予定表から特定の項目を取得します |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |予定表項目を削除します |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |予定表項目の一部を更新します |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |連絡先フォルダーを取得します |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |連絡先フォルダーから連絡先を取得します |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |新しい連絡先を作成します |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |連絡先フォルダーから特定の連絡先を取得します |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |連絡先を削除します |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |連絡先の一部を更新します |

### <a name="outlookcom-triggers"></a>Outlook.com のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| イベントが間もなく開始されるとき |予定表イベントが間もなく開始されるときにフローをトリガーします |
| 新しい電子メールの着信時 |新しい電子メールが着信したときにフローをトリガーします |
| 新しい項目の作成時 |新しい予定表項目が作成されたときにトリガーされます |
| 項目の更新時 |予定表項目が変更されたときにトリガーされます |

## <a name="create-a-connection-to-outlookcom"></a>Outlook.com への接続を作成する
Outlook.com を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |Outlook.com の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。  
>
>

## <a name="reference-for-outlookcom"></a>Outlook.com のリファレンス
適用されるバージョン: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
イベントが間もなく開始されるとき: 予定表イベントが間もなく開始されるときにフローをトリガーします

```GET: /Events/OnUpcomingEvents```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |○ |query |なし |予定表の一意識別子 |
| lookAheadTimeInMinutes |integer |× |query |15 |間もなく開始されるイベントまでの時間 (分) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="getemails"></a>GetEmails
電子メールを取得する: フォルダーから電子メールを取得します

```GET: /Mail```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |× |query |Inbox |電子メールを取得するフォルダーのパス (既定値: 'Inbox') |
| top |integer |× |query |10 |取得する電子メールの件数 (既定値: 10) |
| fetchOnlyUnread |ブール値 |× |query |true |未読の電子メールのみを取得しますか？ |
| includeAttachments |ブール値 |× |query |false |True に設定すると、電子メールと共に添付ファイルも取得されます |
| searchQuery |string |× |query |なし |電子メールをフィルターする検索クエリ |
| skip |integer |× |query |0 |スキップする電子メールの件数 (既定値: 0) |
| skipToken |string |× |query |なし |新しいページを取得するスキップ トークン |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="sendemail"></a>SendEmail
電子メールを送信する: 電子メールを送信します

```POST: /Mail```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |○ |body |なし |電子メール |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="deleteemail"></a>DeleteEmail
電子メールを削除する: ID を指定して電子メールを削除します

```DELETE: /Mail/{messageId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |あり |path |なし |削除する電子メールの ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="markasread"></a>MarkAsRead
既読としてマークする: 電子メールを既読としてマークします

```POST: /Mail/MarkAsRead/{messageId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |あり |path |なし |既読とマークする電子メールの ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="replyto"></a>ReplyTo
電子メールに返信する: 電子メールに返信します

```POST: /Mail/ReplyTo/{messageId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |あり |path |なし |返信する電子メールの ID |
| comment |string |○ |query |なし |応答のコメント |
| replyAll |ブール値 |× |query |false |すべての受信者に返信する |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="getattachment"></a>GetAttachment
添付ファイルを取得する: ID を指定して電子メールの添付ファイルを取得します

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| messageId |string |あり |path |なし |電子メールの ID |
| attachmentId |string |あり |path |なし |ダウンロードする添付ファイルの ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="onnewemail"></a>OnNewEmail
新しい電子メールの着信時: 新しい電子メールが着信したときにフローをトリガーします

```GET: /Mail/OnNewEmail```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |× |query |Inbox |取得する電子メール フォルダー (既定値: 受信トレイ)。 |
| to |string |× |query |なし |受信者の電子メール アドレス |
| from |string |× |query |なし |差出人アドレス |
| importance |string |× |query |Normal |電子メールの重要度 (High、Normal、Low) (既定値: Normal) |
| fetchOnlyWithAttachment |ブール値 |× |query |false |ファイルが添付された電子メールのみを取得します |
| includeAttachments |ブール値 |× |query |false |添付ファイルを含めます |
| subjectFilter |string |× |query |なし |サブジェクト内で検索する文字列 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |承認済み |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
オプションを指定して電子メールを送信する: 複数のオプションを指定して電子メールを送信し、受信者からオプションのいずれかを含む返信が送られるまで待ちます

```POST: /mailwithoptions/$subscriptions```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |○ |body |なし |オプションの電子メールのサブスクリプション要求 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 201 |サブスクリプションの作成 |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="sendapprovalmail"></a>SendApprovalMail
承認の電子メールを送信する: 承認の電子メールを送信し、受信者からの返信を待ちます

```POST: /approvalmail/$subscriptions```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |○ |body |なし |承認の電子メールのサブスクリプション要求 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 201 |サブスクリプションの作成 |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="calendargettables"></a>CalendarGetTables
予定表を取得する: 予定表を取得します

```GET: /datasets/calendars/tables```

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendargetitems"></a>CalendarGetItems
イベントを取得する: 予定表から項目を取得します

```GET: /datasets/calendars/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |取得する予定表の一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendarpostitem"></a>CalendarPostItem
イベントを作成する: 新しいイベントを作成します

```POST: /datasets/calendars/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |予定表の一意識別子 |
| item | |あり |body |なし |作成する予定表項目 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendargetitem"></a>CalendarGetItem
イベントを取得する: 予定表から特定の項目を取得します

```GET: /datasets/calendars/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |予定表の一意識別子 |
| id |string |あり |path |なし |取得する予定表項目の一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
イベントを削除する: 予定表から項目を削除します

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |予定表の一意識別子 |
| id |string |あり |path |なし |削除する予定表項目の一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendarpatchitem"></a>CalendarPatchItem
イベントを更新する: 予定表項目の一部を更新します

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |予定表の一意識別子 |
| id |string |あり |path |なし |更新する予定表項目の一意識別子 |
| item | |あり |body |なし |更新する予定表項目 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
新しい項目に対して: 新しい予定表項目が作成されたときにトリガーされます

```GET: /datasets/calendars/tables/{table}/onnewitems```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |予定表の一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
更新された項目に対して: 予定表項目が変更されたときにトリガーされます

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |予定表の一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="contactgettables"></a>ContactGetTables
連絡先フォルダーを取得する: 連絡先フォルダーを取得します

```GET: /datasets/contacts/tables```

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="contactgetitems"></a>ContactGetItems
連絡先を取得する: 連絡先フォルダーから連絡先を取得します

```GET: /datasets/contacts/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |取得する連絡先フォルダーの一意識別子 |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="contactpostitem"></a>ContactPostItem
連絡先を作成する: 新しい連絡先を作成します

```POST: /datasets/contacts/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |連絡先フォルダーの一意識別子 |
| item | |あり |body |なし |作成する連絡先 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="contactgetitem"></a>ContactGetItem
連絡先を取得する: 連絡先フォルダーから特定の連絡先を取得します

```GET: /datasets/contacts/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |連絡先フォルダーの一意識別子 |
| id |string |あり |path |なし |取得する連絡先の一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="contactdeleteitem"></a>ContactDeleteItem
連絡先を削除する: 連絡先を削除します

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |連絡先フォルダーの一意識別子 |
| id |string |あり |path |なし |削除する連絡先の一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="contactpatchitem"></a>ContactPatchItem
連絡先を更新する: 連絡先の一部を更新します

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テーブル |string |あり |path |なし |連絡先フォルダーの一意識別子 |
| id |string |あり |path |なし |更新する連絡先の一意識別子 |
| item | |あり |body |なし |更新する連絡先項目 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="object"></a>オブジェクト
### <a name="sendmessage"></a>SendMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [添付ファイル] |array |なし |
| From |string |なし |
| Cc |string |なし |
| [Bcc] |string |なし |
| [件名] |string |はい |
| 本文 |string |はい |
| [重要度] |string |なし |
| IsHtml |boolean |なし |
| To |string |はい |

### <a name="sendattachment"></a>SendAttachment
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| @odata.type |string |なし |
| 名前 |string |はい |
| ContentBytes |string |はい |

### <a name="receivemessage"></a>ReceiveMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |なし |
| IsRead |boolean |なし |
| HasAttachment |boolean |なし |
| DateTimeReceived |string |なし |
| [添付ファイル] |array |なし |
| From |string |なし |
| Cc |string |なし |
| [Bcc] |string |なし |
| [件名] |string |はい |
| 本文 |string |はい |
| [重要度] |string |なし |
| IsHtml |boolean |なし |
| To |string |はい |

### <a name="receiveattachment"></a>ReceiveAttachment
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |はい |
| ContentType |string |はい |
| @odata.type |string |なし |
| 名前 |string |はい |
| ContentBytes |string |はい |

### <a name="digestmessage"></a>DigestMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [件名] |string |はい |
| 本文 |string |なし |
| [重要度] |string |なし |
| Digest |array |はい |
| [添付ファイル] |array |なし |
| To |string |はい |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| tabular |未定義 |なし |
| BLOB |未定義 |なし |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source セクション |string |なし |
| displayName |string |なし |
| urlEncoding |string |なし |
| tableDisplayName |string |なし |
| tablePluralName |string |なし |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source セクション |string |なし |
| displayName |string |なし |
| urlEncoding |string |なし |

### <a name="tablemetadata"></a>TableMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |なし |
| title |string |なし |
| x-ms-permission |string |なし |
| x-ms-capabilities |未定義 |なし |
| schema |未定義 |なし |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| sortRestrictions |未定義 |なし |
| filterRestrictions |未定義 |なし |
| filterFunctions |array |なし |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| sortable |boolean |なし |
| unsortableProperties |array |なし |
| ascendingOnlyProperties |array |なし |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| filterable |boolean |なし |
| nonFilterableProperties |array |なし |
| requiredProperties |array |なし |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| NotificationUrl |string |なし |
| メッセージ |未定義 |なし |

### <a name="messagewithoptions"></a>MessageWithOptions
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [件名] |string |はい |
| オプション |string |はい |
| 本文 |string |なし |
| [重要度] |string |なし |
| [添付ファイル] |array |なし |
| To |string |はい |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |なし |
| resource |string |なし |
| notificationType |string |なし |
| notificationUrl |string |なし |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| NotificationUrl |string |なし |
| メッセージ |未定義 |なし |

### <a name="approvalmessage"></a>ApprovalMessage
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| [件名] |string |はい |
| オプション |string |はい |
| 本文 |string |なし |
| [重要度] |string |なし |
| [添付ファイル] |array |なし |
| To |string |はい |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| SelectedOption |string |なし |

### <a name="tableslist"></a>TablesList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="table"></a>テーブル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |なし |
| DisplayName |string |なし |

### <a name="item"></a>項目
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |なし |

### <a name="calendaritemslist"></a>CalendarItemsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="calendaritem"></a>CalendarItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |なし |

### <a name="contactitemslist"></a>ContactItemsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="contactitem"></a>ContactItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |なし |

### <a name="datasetslist"></a>DataSetsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="dataset"></a>DataSet
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |なし |
| DisplayName |string |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)



<!--HONumber=Jan17_HO3-->


