---
title: Wunderlist | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 Wunderlist では、ユーザーの作業を支援する todo リストとタスク マネージャーを提供します。  買い物メモを家族と共有したり、プロジェクトで作業したり、休暇の計画を立てたりするときも、Wunderlist を使えばタスクを簡単に確認、共有、完了できます。 Wunderlist は携帯電話、タブレット、コンピューター間で即時に同期するため、すべてのタスクにどこからでもアクセスできます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 084ac505003e926e9c4b75352ec96a77f8c3584a
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-wunderlist-connector"></a>Wunderlist コネクタの使用
Wunderlist では、ユーザーの作業を支援する todo リストとタスク マネージャーを提供します。  買い物メモを家族と共有したり、プロジェクトで作業したり、休暇の計画を立てたりするときも、Wunderlist を使えばタスクを簡単に確認、共有、完了できます。 Wunderlist は携帯電話、タブレット、コンピューター間で即時に同期するため、すべてのタスクにどこからでもアクセスできます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Wunderlist コネクタは、アクションとして使用できます。Wunderlist コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Wunderlist コネクタでは、次のアクションやトリガーを使用できます。

### <a name="wunderlist-actions"></a>Wunderlist のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |自分のアカウントに関連付けられたリストを取得します。 |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |リストを作成します。 |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |特定のリストからタスクを取得します。 |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |タスクを作成します。 |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |特定のリストまたは特定のタスクからサブタスクを取得します。 |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |特定のタスク内にサブタスクを作成します。 |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |特定のリストまたは特定のタスクのメモを取得します。 |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |特定のタスクにメモを追加します。 |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |特定のリストまたは特定のタスクのタスク コメントを取得します。 |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |特定のタスクにコメントを追加します。 |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |特定のリストまたは特定のタスクのアラームを取得します。 |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |アラームを設定します。 |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |特定のリストまたは特定のタスクのファイルを取得します。 |
| [GetList](connectors-create-api-wunderlist.md#getlist) |特定のリストを取得します。 |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |リストを削除します。 |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |特定のリストを更新します。 |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |特定のタスクを取得します。 |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |特定のタスクを更新します。 |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |特定のタスクを削除します。 |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |特定のサブタスクを取得します。 |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |特定のサブタスクを更新します。 |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |特定のサブタスクを削除します。 |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |特定のメモを取得します。 |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |特定のメモを更新します。 |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |特定のメモを削除します。 |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |特定のタスク コメントを取得します。 |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |特定のアラームを更新します。 |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |特定のアラームを削除します。 |

### <a name="wunderlist-triggers"></a>Wunderlist のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| タスクの期限日 |リスト内のタスクの期限が来たときに新しいフローをトリガーします |
| 新しいタスクの作成時 |新しいタスクがリストで作成されたときに新しいフローをトリガーします |
| アラームの発生時 |アラームが発生したときに新しいフローをトリガーします |

## <a name="create-a-connection-to-wunderlist"></a>Wunderlist への接続を作成する
Wunderlist を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |Wunderlist の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="reference-for-wunderlist"></a>Wunderlist のリファレンス
適用されるバージョン: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
タスクの期限日: リスト内のタスクの期限が来たときに新しいフローをトリガーします

```GET: /trigger/tasksdue```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |

## <a name="triggertasknew"></a>TriggerTaskNew
新しいタスクの作成時: 新しいタスクがリストで作成されたときに新しいフローをトリガーします

```GET: /trigger/tasksnew```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |

## <a name="triggerreminder"></a>TriggerReminder
アラームの発生時: アラームが発生したときに新しいフローをトリガーします

```GET: /trigger/reminders```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| task_id |integer |× |query |なし |タスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |

## <a name="retrievelists"></a>RetrieveLists
リストの取得: 自分のアカウントに関連付けられたリストを取得します。

```GET: /lists```

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createlist"></a>CreateList
リストの作成: リストを作成します。

```POST: /lists```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post | |○ |body |なし |作成する新しいリスト |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| default |操作に失敗しました。 |

## <a name="listtasks"></a>ListTasks
タスクの取得: 特定のリストからタスクを取得します。

```GET: /tasks```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| 完了 |boolean |× |query |なし |完了 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createtask"></a>CreateTask
タスクの作成: タスクを作成します

```POST: /tasks```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post | |○ |body |なし |作成する新しいタスク |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 201 |作成日時 |

## <a name="listsubtasks"></a>ListSubTasks
サブタスクの取得: 特定のリストまたは特定のタスクからサブタスクを取得します。

```GET: /subtasks```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| task_id |integer |× |query |なし |タスク ID |
| 完了 |boolean |× |query |なし |完了 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createsubtask"></a>CreateSubTask
サブタスクの作成: 特定のタスク内にサブタスクを作成します

```POST: /subtasks```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post | |○ |body |なし |作成する新しいサブタスク |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 201 |作成日時 |

## <a name="listnotes"></a>ListNotes
メモの取得: 特定のリストまたは特定のタスクのメモを取得します。

```GET: /notes```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| task_id |integer |× |query |なし |タスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createnote"></a>CreateNote
メモの作成: 特定のタスクにメモを追加します

```POST: /notes```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post | |○ |body |なし |作成する新しいメモ |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 201 |作成日時 |

## <a name="listcomments"></a>ListComments
タスク コメントの取得: 特定のリストまたは特定のタスクのタスク コメントを取得します。

```GET: /task_comments```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| task_id |integer |× |query |なし |タスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createcomment"></a>CreateComment
タスクのコメントの追加: 特定のタスクにコメントを追加します

```POST: /task_comments```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post | |○ |body |なし |作成する新しいタスク コメント |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 201 |作成日時 |

## <a name="retrievereminders"></a>RetrieveReminders
アラームの取得: 特定のリストまたは特定のタスクのアラームを取得します。

```GET: /reminders```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| task_id |integer |× |query |なし |タスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createreminder"></a>CreateReminder
アラームの設定: アラームを設定します。

```POST: /reminders```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post | |○ |body |なし |作成する新しいアラーム |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| default |操作に失敗しました。 |

## <a name="retrievefiles"></a>RetrieveFiles
ファイルの取得: 特定のリストまたは特定のタスクのファイルを取得します。

```GET: /files```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| task_id |integer |× |query |なし |タスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="getlist"></a>GetList
リストの取得: 特定のリストを取得します

```GET: /lists/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |リスト ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="deletelist"></a>DeleteList
リストの削除: リストを削除します

```DELETE: /lists/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |リスト ID |
| revision |integer |○ |query |なし |リビジョン |

#### <a name="response"></a>応答
| 名前 | Description |
| --- | --- |
| 204 |コンテンツなし |

## <a name="updatelist"></a>UpdateList
リストの更新: 特定のリストを更新します

```PATCH: /lists/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |リスト ID |
| post | |○ |body |なし |リストの詳細 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="gettask"></a>GetTask
タスクの取得: 特定のタスクを取得します

```GET: /tasks/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| id |integer |あり |path |なし |タスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="updatetask"></a>UpdateTask
タスクの更新: 特定のタスクを更新します

```PATCH: /tasks/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| id |integer |あり |path |なし |タスク ID |
| post | |○ |body |なし |タスクの詳細 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="deletetask"></a>DeleteTask
タスクの削除: 特定のタスクを削除します

```DELETE: /tasks/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |○ |query |なし |リスト ID |
| id |integer |あり |path |なし |タスク ID |
| revision |integer |○ |query |なし |リビジョン |

#### <a name="response"></a>応答
| 名前 | Description |
| --- | --- |
| 204 |コンテンツなし |

## <a name="getsubtask"></a>GetSubTask
サブタスクの取得: 特定のサブタスクを取得します

```GET: /subtasks/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |サブタスク ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="updatesubtask"></a>UpdateSubTask
サブタスクの更新: 特定のサブタスクを更新します

```PATCH: /subtasks/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |サブタスク ID |
| post | |○ |body |なし |サブタスクの詳細 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="deletesubtask"></a>DeleteSubTask
サブタスクの削除: 特定のサブタスクを削除します

```DELETE: /subtasks/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |サブタスク ID |
| revision |integer |○ |query |なし |リビジョン |

#### <a name="response"></a>応答
| 名前 | Description |
| --- | --- |
| 204 |コンテンツなし |

## <a name="getnote"></a>GetNote
メモの取得: 特定のメモを取得します

```GET: /notes/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |メモ ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="updatenote"></a>UpdateNote
メモの更新: 特定のメモを更新します

```PATCH: /notes/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |メモ ID |
| post | |○ |body |なし |メモの詳細 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="deletenote"></a>DeleteNote
メモの削除: 特定のメモを削除します

```DELETE: /notes/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |メモ ID |
| revision |integer |○ |query |なし |リビジョン |

#### <a name="response"></a>応答
| 名前 | Description |
| --- | --- |
| 204 |コンテンツなし |

## <a name="getcomment"></a>GetComment
タスク コメントの取得: 特定のタスク コメントを取得します

```GET: /task_comments/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |コメント ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="updatereminder"></a>UpdateReminder
アラームの更新: 特定のアラームを更新します

```PATCH: /reminders/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |アラーム ID |
| post | |○ |body |なし |アラームの詳細 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |

## <a name="deletereminder"></a>DeleteReminder
アラームの削除: 特定のアラームを削除します

```DELETE: /reminders/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |あり |path |なし |アラームの ID です。 |
| revision |integer |○ |query |なし |リビジョン |

#### <a name="response"></a>応答
| 名前 | Description |
| --- | --- |
| 204 |コンテンツなし |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="list"></a>一覧表示
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| created_at |string |なし |
| title |string |なし |
| list_type |string |なし |
| type |string |なし |
| revision |integer |なし |

### <a name="createdlist"></a>CreatedList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| created_at |string |なし |
| title |string |なし |
| revision |integer |なし |
| type |string |なし |

### <a name="task"></a>タスク
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| assignee_id |integer |なし |
| assigner_id |integer |なし |
| created_at |string |なし |
| created_by_id |integer |なし |
| due_date |string |なし |
| list_id |integer |なし |
| revision |integer |なし |
| starred |boolean |なし |
| title |string |なし |

### <a name="subtask"></a>サブタスク
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| task_id |integer |なし |
| created_at |string |なし |
| created_by_id |integer |なし |
| revision |string |なし |
| title |string |なし |

### <a name="note"></a>注
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| task_id |integer |なし |
| content |string |なし |
| created_at |string |なし |
| updated_at |string |なし |
| revision |integer |なし |

### <a name="comment"></a>コメント
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| task_id |integer |なし |
| revision |integer |なし |
| text |string |なし |
| type |string |なし |
| created_at |string |なし |

### <a name="reminder"></a>アラーム
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| date |string |なし |
| task_id |integer |なし |
| revision |integer |なし |
| type |string |なし |
| created_at |string |なし |
| updated_at |string |なし |

### <a name="createdreminder"></a>CreatedReminder
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| date |string |なし |
| task_id |integer |なし |
| revision |integer |なし |
| created_at |string |なし |
| updated_at |string |なし |

### <a name="file"></a>ファイル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| url |string |なし |
| task_id |integer |なし |
| list_id |integer |なし |
| user_id |integer |なし |
| file_name |string |なし |
| content_type |string |なし |
| file_size |integer |なし |
| local_created_at |string |なし |
| created_at |string |なし |
| updated_at |string |なし |
| type |string |なし |
| revision |integer |なし |

### <a name="newtask"></a>NewTask
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| list_id |integer |はい |
| title |string |はい |
| assignee_id |integer |なし |
| 完了 |boolean |なし |
| recurrence_type |string |なし |
| recurrence_count |integer |なし |
| due_date |string |なし |
| starred |boolean |なし |

### <a name="newlist"></a>NewList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| title |string |はい |

### <a name="newsubtask"></a>NewSubtask
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| list_id |integer |はい |
| task_id |integer |はい |
| title |string |はい |
| 完了 |boolean |なし |

### <a name="newnote"></a>NewNote
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| list_id |integer |はい |
| task_id |integer |はい |
| content |string |はい |

### <a name="newcomment"></a>NewComment
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| list_id |integer |はい |
| task_id |integer |はい |
| text |string |はい |

### <a name="newreminder"></a>NewReminder
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| list_id |integer |はい |
| task_id |integer |はい |
| date |string |はい |

### <a name="updatetask"></a>UpdateTask
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| revision |integer |なし |
| title |string |なし |
| assignee_id |integer |なし |
| 完了 |boolean |なし |
| recurrence_type |string |なし |
| recurrence_count |integer |なし |
| due_date |string |なし |
| starred |boolean |なし |

### <a name="updatelist"></a>UpdateList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| revision |integer |なし |
| title |string |なし |

### <a name="updatesubtask"></a>UpdateSubtask
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| revision |integer |なし |
| title |string |なし |
| 完了 |boolean |なし |

### <a name="updatenote"></a>UpdateNote
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| revision |integer |なし |
| content |string |なし |

### <a name="updatereminder"></a>UpdateReminder
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| date |string |なし |
| revision |integer |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)


