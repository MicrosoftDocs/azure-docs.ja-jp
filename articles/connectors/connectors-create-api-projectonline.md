---
title: ProjectOnline | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。 Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 77e106d9170fc49d66d322bd9c92bf0e14869259
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-projectonline-connector"></a>ProjectOnline コネクタの使用
Project Online は、プロジェクト ポートフォリオ管理 (PPM) や日常業務を行うための Microsoft の柔軟なオンライン ソリューションです。 Office 365 経由で配信される Project Online により、強力なプロジェクト管理機能が直ちに実現します。ほとんどすべての場所やデバイスから、プロジェクトとプロジェクト ポートフォリオ投資の計画、優先順位付け、管理を実行できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。 
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
ProjectOnline コネクタは、アクションとして使用できます。ProjectOnline コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。 

 ProjectOnline コネクタでは、次のアクションやトリガーを使用できます。

### <a name="projectonline-actions"></a>ProjectOnline のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |プロジェクト オンライン サイトでプロジェクトを一覧表示します |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |プロジェクト オンライン サイトで新しいプロジェクトを作成します |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |プロジェクトで新しいタスクを作成します |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |プロジェクト オンライン サイトでエンタープライズ リソースを作成します |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |プロジェクトで発行されたタスクを一覧表示します |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |サイト内のプロジェクトをチェック アウトします |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |サイト内の既存のプロジェクトをチェックインおよび発行します |

### <a name="projectonline-triggers"></a>ProjectOnline のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| 新しいプロジェクトの作成時 |新しいプロジェクトが作成されるたびにフローをトリガーします |
| 新しいリソースの作成時 |新しいリソースが作成されたときにフローをトリガーします |
| 新しいタスクの作成時 |新しいタスクが作成されたときにフローをトリガーします |

## <a name="create-a-connection-to-projectonline"></a>ProjectOnline への接続を作成する
ProjectOnline を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |ProjectOnline の資格情報を提供します |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="reference-for-projectonline"></a>ProjectOnline のリファレンス
適用されるバージョン: 1.0

## <a name="onnewproject"></a>OnNewProject
新しいプロジェクトの作成時: 新しいプロジェクトが作成されるたびにフローをトリガーします 

```GET: /trigger/_api/ProjectData/Projects``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="onnewresource"></a>OnNewResource
新しいリソースの作成時: 新しいリソースが作成されたときに新しいフローをトリガーします 

```GET: /trigger/_api/ProjectData/Resources``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="onnewtask"></a>OnNewTask
新しいタスクの作成時: 新しいタスクが作成されたときにフローをトリガーします 

```GET: /trigger/_api/ProjectData/Tasks``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="listprojects"></a>ListProjects
プロジェクトの一覧表示: プロジェクト オンライン サイトでプロジェクトを一覧表示します 

```GET: /_api/ProjectServer/Projects``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createproject"></a>CreateProject
プロジェクトの新規作成: プロジェクト オンライン サイトで新しいプロジェクトを作成します 

```POST: /_api/ProjectServer/Projects``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |
| proj | |○ |body |なし |作成する新しいプロジェクト |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |ForbIDden |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createtask"></a>CreateTask
新しいタスクの作成: プロジェクトで新しいタスクを作成します 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |あり |path |なし |タスクを追加するプロジェクトの一意の ID |
| タスク | |○ |body |なし |プロジェクトに追加する新しいタスク |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="createresource"></a>CreateResource
新しいリソースの作成: プロジェクト オンライン サイトでエンタープライズ リソースを作成します 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |
| resource | |○ |body |なし |プロジェクトに追加する新しいエンタープライズ リソース |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="listtasks"></a>ListTasks
タスクの一覧表示: プロジェクトで発行されたタスクを一覧表示します 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |あり |path |なし |タスクを取得するプロジェクトの一意の ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="checkoutproject"></a>CheckoutProject
プロジェクトのチェック アウト: サイト内のプロジェクトをチェック アウトします 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |あり |path |なし |タスクを追加するプロジェクトの一意の ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="publishproject"></a>PublishProject
プロジェクトのチェックインと発行: サイト内の既存のプロジェクトをチェックインおよび発行します 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |○ |query |なし |プロジェクト サイトのルート サイト URL (例: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |あり |path |なし |チェックインするプロジェクトの一意の ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="triggerproject"></a>TriggerProject
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ProjectStartDate |string |なし |
| ProjectFinishDate |string |なし |
| ProjectCreatedDate |string |なし |
| ProjectId |string |なし |
| ProjectModifiedDate |string |なし |
| ProjectType |integer |なし |
| ProjectName |string |なし |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="triggerresource"></a>TriggerResource
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ResourceId |string |なし |
| ResourceBaseCalendar |string |なし |
| ResourceBookingType |integer |なし |
| ResourceCanLevel |boolean |なし |
| ResourceCostPerUse |number |なし |
| ResourceCreatedDate |string |なし |
| ResourceEarliestAvailableFrom |string |なし |
| ResourceEmail |string |なし |
| ResourceInitials |string |なし |
| ResourceIsActive |boolean |なし |
| ResourceIsGeneric |boolean |なし |
| ResourceLatestAvailableTo |string |なし |
| ResourceModifiedDate |string |なし |
| ResourceName |string |なし |
| ResourceStatsuName |string |なし |
| ResourceType |integer |なし |
| TypeDescription |string |なし |
| TypeName |string |なし |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="triggertask"></a>TriggerTask
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ProjectId |string |なし |
| TaskId |string |なし |
| ProjectName |string |なし |
| TaskName |string |なし |
| TaskCreatedDate |string |なし |
| TaskModifieddate |string |なし |
| TaskStartDate |string |なし |
| TaskFinishDate |string |なし |
| TaskPriority |integer |なし |
| TaskIsActive |boolean |なし |

### <a name="newproject"></a>NewProject
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |はい |
| Description |string |なし |
| 開始 |string |なし |

### <a name="newreource"></a>NewReource
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |はい |
| IsBudget |boolean |なし |
| IsGeneric |boolean |なし |
| IsInactive |boolean |なし |

### <a name="project"></a>Project
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ApprovedStart |string |なし |
| ApprovedEnd |string |なし |
| CheckedOutDate |string |なし |
| CheckOutDescription |string |なし |
| CheckOutId |string |なし |
| CreatedDate |string |なし |
| ID |string |なし |
| IsCheckedOut |boolean |なし |
| LastPublishedDate |string |なし |
| LastSavedDate |string |なし |
| OptimizerDecision |integer |なし |
| PlannerDecision |integer |なし |
| ProjectType |integer |なし |
| 名前 |string |なし |
| WinprojVersion |string |なし |

### <a name="projectswrapper"></a>ProjectsWrapper
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="newtask"></a>NewTask
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| parameters |未定義 |はい |

### <a name="taskparameters"></a>TaskParameters
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |はい |
| メモ |string |なし |
| 開始 |string |なし |
| 時間 |string |なし |

### <a name="enterpriseresource"></a>EnterpriseResource
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| CanLevel |boolean |なし |
| コード |string |なし |
| CostAccrual |integer |なし |
| CostCenter |string |なし |
| 作成日時 |string |なし |
| DefaultBookingType |integer |なし |
| 電子メール |string |なし |
| ExternalId |string |なし |
| グループ |string |なし |
| HireDate |string |なし |
| ID |string |なし |
| Initials |string |なし |
| IsActive |boolean |なし |
| IsBudget |boolean |なし |
| IsCheckedOut |boolean |なし |
| IsGeneric |boolean |なし |
| IsTeam |boolean |なし |
| MaterialLabel |string |なし |
| 修正済み |string |なし |
| 名前 |string |なし |
| Phonetics |string |なし |
| ResourceType |integer |なし |
| TerminationDate |string |なし |

### <a name="taskswrapper"></a>TasksWrapper
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="task"></a>タスク
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 作成日時 |string |なし |
| 修正済み |string |なし |
| 開始 |string |なし |
| 完了 |string |なし |
| 名前 |string |なし |
| ID |string |なし |
| 優先順位 |integer |なし |
| PercentComplete |integer |なし |
| メモ |string |なし |
| 連絡先 |string |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)


