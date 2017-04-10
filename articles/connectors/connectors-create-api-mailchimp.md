---
title: MailChimp | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 MailChimp は、電子メール マーケティング活動を管理および自動化するための企業向け SaaS サービスです。このサービスにより、マーケティング電子メールの送信、自動メッセージ、対象を絞ったキャンペーンなどを行うことができます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>MailChimp コネクタの使用
MailChimp は、電子メール マーケティング活動を管理および自動化するための企業向け SaaS サービスです。このサービスにより、マーケティング電子メールの送信、自動メッセージ、対象を絞ったキャンペーンなどを行うことができます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
MailChimp コネクタは、アクションとして使用できます。MailChimp コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。

 MailChimp コネクタでは、次のアクションやトリガーを使用できます。

### <a name="mailchimp-actions"></a>MailChimp のアクション
実行できるアクションは以下のとおりです。

| アクション | Description |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |キャンペーンの種類、受信者の一覧、およびキャンペーンの設定 (subject line、title、from_name、および reply_to) に基づいて新しいキャンペーンを作成します |
| [newlist](connectors-create-api-mailchimp.md#newlist) |MailChimp アカウントで新しい一覧を作成します |
| [addmember](connectors-create-api-mailchimp.md#addmember) |一覧のメンバーを追加または更新します |
| [removemember](connectors-create-api-mailchimp.md#removemember) |一覧からメンバーを削除します |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |一覧の特定メンバーの情報を更新します |

### <a name="mailchimp-triggers"></a>MailChimp のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| 一覧へのメンバーの追加時 |新しいメンバーが一覧に追加されたときにワークフローをトリガーします |
| 新しい一覧の作成時 |新しい一覧が作成されたときにワークフローをトリガーします |

## <a name="create-a-connection-to-mailchimp"></a>MailChimp への接続の作成
MailChimp を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |MailChimp の資格情報を提供します |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="reference-for-mailchimp"></a>MailChimp のリファレンス
適用されるバージョン: 1.0

## <a name="newcampaign"></a>newcampaign
新しいキャンペーン: キャンペーンの種類、受信者の一覧、およびキャンペーンの設定 (subject line、title、from_name、および reply_to) に基づいて新しいキャンペーンを作成します

```POST: /campaigns```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |○ |body |なし |新しいキャンペーンの要求パラメーターと共に本文で送信する Json オブジェクト |

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

## <a name="newlist"></a>newlist
新しい一覧: MailChimp アカウントで新しい一覧を作成します

```POST: /lists```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |○ |body |なし |新しいキャンペーンの要求パラメーターと共に本文で送信する Json オブジェクト |

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

## <a name="addmember"></a>addmember
一覧へのメンバーの追加: 一覧のメンバーを追加または更新します

```POST: /lists/{list_id}/members```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |あり |path |なし |一覧の一意の ID |
| newMemberInList | |○ |body |なし |新しいメンバー情報と共に本文で送信する Json オブジェクト |

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

## <a name="removemember"></a>removemember
一覧からのメンバーの削除: 一覧からメンバーを削除します。

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |あり |path |なし |一覧の一意の ID |
| member_email |string |あり |path |なし |削除するメンバーの電子メール アドレス |

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

## <a name="updatemember"></a>updatemember
メンバー情報の更新: 一覧の特定メンバーの情報を更新します

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |あり |path |なし |一覧の一意の ID |
| member_email |string |あり |path |なし |更新するメンバーの一意の電子メール アドレス |
| updateMemberInListRequest | |○ |body |なし |更新されたメンバー情報と共に本文で送信する Json オブジェクト |

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

## <a name="onmembersubscribed"></a>OnMemberSubscribed
一覧へのメンバーの追加時: 新しいメンバーが一覧に追加されたときにワークフローをトリガーします

```GET: /trigger/lists/{list_id}/members```

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |あり |path |なし |一覧の一意の ID |

#### <a name="response"></a>応答
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

## <a name="oncreatelist"></a>OnCreateList
新しい一覧の作成時: 新しい一覧が作成されたときにワークフローをトリガーします

```GET: /trigger/lists```

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
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

## <a name="object-definitions"></a>オブジェクト定義
### <a name="newcampaignrequest"></a>NewCampaignRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| type |string |はい |
| recipients |未定義 |はい |
| 設定 |未定義 |はい |
| variate_settings |未定義 |なし |
| tracking |未定義 |なし |
| rss_opts |未定義 |なし |
| social_card |未定義 |なし |

### <a name="recipient"></a>Recipient
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| list_id |string |はい |
| segment_opts |未定義 |なし |

### <a name="settings"></a>Settings
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| subject_line |string |はい |
| title |string |なし |
| from_name |string |はい |
| reply_to |string |はい |
| use_conversation |boolean |なし |
| to_name |string |なし |
| folder_id |integer |なし |
| authenticate |boolean |なし |
| auto_footer |boolean |なし |
| inline_css |boolean |なし |
| auto_tweet |boolean |なし |
| auto_fb_post |array |なし |
| fb_comments |boolean |なし |

### <a name="variatesettings"></a>Variate_Settings
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| winner_criteria |string |なし |
| wait_time |integer |なし |
| test_size |integer |なし |
| subject_lines |array |なし |
| send_times |array |なし |
| from_names |array |なし |
| reply_to_addresses |array |なし |

### <a name="tracking"></a>追跡
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| opens |boolean |なし |
| html_clicks |boolean |なし |
| text_clicks |boolean |なし |
| goal_tracking |boolean |なし |
| ecomm360 |boolean |なし |
| google_analytics |string |なし |
| clicktale |string |なし |
| salesforce |未定義 |なし |
| highrise |未定義 |なし |
| capsule |未定義 |なし |

### <a name="rssopts"></a>RSS_Opts
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| feed_url |string |なし |
| frequency |string |なし |
| constrain_rss_img |string |なし |
| schedule |未定義 |なし |

### <a name="socialcard"></a>Social_Card
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| image_url |string |なし |
| description |string |なし |
| title |string |なし |

### <a name="segmentopts"></a>Segment_Opts
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| saved_segment_id |integer |なし |
| match |string |なし |

### <a name="salesforce"></a>Salesforce
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| campaign |boolean |なし |
| notes |boolean |なし |

### <a name="highrise"></a>Highrise
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| campaign |boolean |なし |
| notes |boolean |なし |

### <a name="capsule"></a>Capsule
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| notes |boolean |なし |

### <a name="schedule"></a>スケジュール
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| hour |integer |なし |
| daily_send |未定義 |なし |
| weekly_send_day |string |なし |
| monthly_send_date |number |なし |

### <a name="dailysend"></a>Daily_Send
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| sunday |boolean |なし |
| monday |boolean |なし |
| tuesday |boolean |なし |
| wednesday |boolean |なし |
| thursday |boolean |なし |
| friday |boolean |なし |
| saturday |boolean |なし |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |なし |
| type |string |なし |
| create_time |string |なし |
| archive_url |string |なし |
| status |string |なし |
| emails_sent |integer |なし |
| send_time |string |なし |
| content_type |string |なし |
| recipient |array |なし |
| 設定 |未定義 |なし |
| variate_settings |未定義 |なし |
| tracking |未定義 |なし |
| rss_opts |未定義 |なし |
| ab_split_opts |未定義 |なし |
| social_card |未定義 |なし |
| report_summary |未定義 |なし |
| delivery_status |未定義 |なし |
| _links |array |なし |

### <a name="absplitopts"></a>AB_Split_Opts
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| split_test |string |なし |
| pick_winner |string |なし |
| wait_units |string |なし |
| wait_time |integer |なし |
| split_size |integer |なし |
| from_name_a |string |なし |
| from_name_b |string |なし |
| reply_email_a |string |なし |
| reply_email_b |string |なし |
| subject_a |string |なし |
| subject_b |string |なし |
| send_time_a |string |なし |
| send_time_b |string |なし |
| send_time_winner |string |なし |

### <a name="reportsummary"></a>Report_Summary
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| opens |integer |なし |
| unique_opens |integer |なし |
| open_rate |number |なし |
| clicks |integer |なし |
| subscriber_clicks |number |なし |
| click_rate |number |なし |

### <a name="deliverystatus"></a>Delivery_Status
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| enabled |boolean |なし |
| can_cancel |boolean |なし |
| status |string |なし |
| emails_sent |integer |なし |
| emails_canceled |integer |なし |

### <a name="link"></a>リンク
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| rel |string |なし |
| href |string |なし |
| 静的メソッド |string |なし |
| targetSchema |string |なし |
| schema |string |なし |

### <a name="newlistrequest"></a>NewListRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |はい |
| contact |未定義 |はい |
| permission_reminder |string |はい |
| use_archive_bar |boolean |なし |
| campaign_defaults |未定義 |はい |
| notify_on_subscribe |string |なし |
| notify_on_unsubscribe |string |なし |
| email_type_option |boolean |はい |
| visibility |string |なし |

### <a name="contact"></a>連絡先
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| company |string |はい |
| address1 |string |はい |
| address2 |string |なし |
| city |string |はい |
| state |string |はい |
| zip |string |はい |
| country |string |はい |
| phone |string |はい |

### <a name="campaigndefaults"></a>Campaign_Defaults
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| from_name |string |はい |
| from_email |string |はい |
| subject |string |なし |
| 言語 |string |はい |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |はい |
| name |string |はい |
| contact |未定義 |はい |
| permission_reminder |string |はい |
| use_archive_bar |boolean |なし |
| campaign_defaults |未定義 |はい |
| notify_on_subscribe |string |なし |
| notify_on_unsubscribe |string |なし |
| date_created |string |なし |
| list_rating |integer |なし |
| email_type_option |boolean |はい |
| subscribe_url_short |string |なし |
| subscribe_url_long |string |なし |
| beamer_address |string |なし |
| visibility |string |なし |
| modules |array |なし |
| stats |未定義 |なし |
| _links |array |なし |

### <a name="stats"></a>統計
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| member_count |integer |なし |
| unsubscribe_count |integer |なし |
| cleaned_count |integer |なし |
| member_count_since_send |integer |なし |
| unsubscribe_count_since_send |integer |なし |
| cleaned_count_since_send |integer |なし |
| campaign_count |integer |なし |
| campaign_last_sent |integer |なし |
| merge_field_count |integer |なし |
| avg_sub_rate |number |なし |
| avg_unsub_rate |number |なし |
| target_sub_rate |number |なし |
| open_rate |number |なし |
| click_rate |number |なし |
| last_sub_date |string |なし |
| last_unsub_date |string |なし |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| lists |array |なし |
| total_items |integer |なし |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| email_type |string |なし |
| status |string |はい |
| merge_fields |未定義 |なし |
| interests |string |なし |
| 言語 |string |なし |
| vip |boolean |なし |
| location |未定義 |なし |
| email_address |string |はい |

### <a name="firstandlastname"></a>FirstAndLastName
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| FNAME |string |なし |
| LNAME |string |なし |

### <a name="location"></a>場所
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| latitude |number |なし |
| longitude |number |なし |

### <a name="memberresponsemodel"></a>MemberResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |なし |
| email_address |string |なし |
| unique_email_id |string |なし |
| email_type |string |なし |
| status |string |なし |
| merge_fields |未定義 |なし |
| interests |string |なし |
| stats |未定義 |なし |
| ip_signup |string |なし |
| timestamp_signup |string |なし |
| ip_opt |string |なし |
| timestamp_opt |string |なし |
| member_rating |integer |なし |
| last_changed |string |なし |
| 言語 |string |なし |
| vip |boolean |なし |
| email_client |string |なし |
| location |未定義 |なし |
| last_note |未定義 |なし |
| list_id |string |なし |
| _links |array |なし |

### <a name="lastnote"></a>Last_Note
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| note_id |integer |なし |
| created_at |string |なし |
| created_by |string |なし |
| note |string |なし |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| members |array |なし |
| list_id |string |なし |
| total_items |integer |なし |

### <a name="object"></a>オブジェクト
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| email_address |string |なし |
| email_type |string |なし |
| status |string |はい |
| merge_fields |未定義 |なし |
| interests |string |なし |
| 言語 |string |なし |
| vip |boolean |なし |
| location |未定義 |なし |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| members |array |なし |
| list_id |string |なし |
| total_items |integer |なし |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |はい |
| email_address |string |はい |
| unique_email_id |string |なし |
| email_type |string |なし |
| status |string |なし |
| merge_fields |未定義 |はい |
| interests |string |なし |
| stats |未定義 |なし |
| ip_signup |string |なし |
| timestamp_signup |string |なし |
| ip_opt |string |なし |
| timestamp_opt |string |なし |
| member_rating |integer |なし |
| last_changed |string |なし |
| 言語 |string |なし |
| vip |boolean |なし |
| email_client |string |なし |
| location |未定義 |なし |
| last_note |未定義 |なし |
| list_id |string |なし |
| _links |array |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


