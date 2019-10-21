---
title: Azure Scheduler でスケジュールされたジョブを作成する - Azure portal | Microsoft Docs
description: Azure portal で Azure Scheduler を使用して初めての自動化されたジョブを作成、スケジュール、実行する方法を説明します
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 48e03f41fa0a39642330288fc05ef709179b4c62
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300983"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Azure Scheduler で初めてのジョブを作成してスケジュールする - Azure portal

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。

このチュートリアルでは、ジョブを簡単に作成してスケジュールし、そのジョブを監視および管理する方法を示します。 

Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

## <a name="create-job"></a>ジョブを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。  

1. Azure のメイン メニューで **[リソースの作成]** を選択します。 検索ボックスに「scheduler」と入力します。 結果の一覧から **Scheduler** を選択し、 **[作成]** を選択します。

   ![Scheduler リソースを作成する](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   URL `https://www.microsoft.com/` に GET 要求を送信するジョブを作成します。 

1. **[スケジューラ ジョブ]** で次の情報を入力します。

   | プロパティ | 値の例 | 説明 |
   |----------|---------------|-------------| 
   | **Name** | getMicrosoft | ジョブの名前です | 
   | **ジョブ コレクション** | <*job-collection-name*> | ジョブ コレクションを作成するか、既存のコレクションを選択します。 | 
   | **サブスクリプション** | <*Azure サブスクリプション名*> | Azure サブスクリプションの名前 | 
   |||| 

1. **[アクションの設定 - 構成]** を選択し、次の情報を指定して、終わったら **[OK]** を選択します。

   | プロパティ | 値の例 | 説明 |
   |----------|---------------|-------------| 
   | **アクション** | **Http** | 実行するアクションの種類です | 
   | **メソッド** | **Get** | 呼び出すメソッドです | 
   | **URL** | **https://www.microsoft.com** | 送信先の URL です | 
   |||| 
   
   ![ジョブを定義する](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. **[スケジュール - 構成]** を選択し、スケジュールを定義して、終わったら **[OK]** を選択します。

   一度だけのジョブも作成できますが、この例では繰り返すスケジュールを設定します。

   | プロパティ | 値の例 | 説明 |
   |----------|---------------|-------------| 
   | **定期的なアイテム** | **繰り返し** | 一度だけのジョブまたは定期的なジョブです | 
   | **開始時刻** | <*今日の日時*> | ジョブの開始日時です | 
   | **繰り返し間隔** | **1 時間** | 繰り返しの間隔と頻度です | 
   | **終了** | 今日の日付から 2 日後が **[終了期限]** | ジョブの終了日時です | 
   | **UTC オフセット** | **UTC +08:00** | 協定世界時 (UTC) と現在の場所の時刻との差異です | 
   |||| 

   ![スケジュールを定義する](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. 準備ができたら、 **[作成]** をクリックします。

   作成したジョブが Azure によってデプロイされて、Azure ダッシュボードに表示されます。 

1. デプロイが成功した通知が表示されたら、 **[ダッシュボードにピン留め]** を選択します。 表示されない場合は、Azure ツール バーの **[通知]** アイコン (ベル) を選択して、 **[ダッシュボードにピン留め]** を選択します。

## <a name="monitor-and-manage-jobs"></a>ジョブを監視および管理する

ジョブを確認、監視、管理するには、Azure ダッシュボードで目的のジョブを選択します。 **[設定]** では、ジョブの確認と管理を行うことができます。

![ジョブの設定](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

これらの領域の詳細については、領域を選択してください。

* [**プロパティ**](#properties)
* [**アクションの設定**](#action-settings)
* [**スケジュール**](#schedule)
* [**履歴**](#history)
* [**ユーザー**](#users)

<a name="properties"></a>

### <a name="properties"></a>properties

ジョブの管理メタデータを記述する読み取り専用のプロパティを表示するには、 **[プロパティ]** を選択します。

![ジョブのプロパティを表示する](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>[アクションの設定]

ジョブの詳細な設定を変更するには、 **[アクションの設定]** を選択します。 

![アクションの設定を確認する](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| アクションの種類 | 説明 | 
|-------------|-------------| 
| すべての種類 | **[再試行ポリシー]** と **[エラー アクション]** の設定を変更できます。 | 
| HTTP および HTTPS | **[メソッド]** を許可されている任意のメソッドに変更できます。 ヘッダーおよび基本的な認証情報を追加、削除、変更することもできます。 | 
| ストレージ キュー| ストレージ アカウント、キュー名、SAS トークン、本文を変更することができます。 | 
| Service Bus | 名前空間、トピックまたはキューのパス、認証設定、トランスポートの種類、メッセージのプロパティ、メッセージの本文を変更することができます。 | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>スケジュール

ジョブ ウィザードを使用してスケジュールを設定する場合は、開始日時、繰り返しスケジュール、繰り返しジョブの終了日時など、そのスケジュールを変更できます。
さらに[複雑なスケジュールと高度な繰り返し](scheduler-advanced-complexity.md)を作成することもできます。

ビューまたはジョブのスケジュールを変更するには、 **[スケジュール]** を選択します。

![ジョブのスケジュールを表示する](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>履歴

選択したジョブのすべての実行に関するメトリックを表示するには、 **[履歴]** を選択します。 これらのメトリックでは、状態、再試行回数、出現回数、開始日時、終了日時など、ジョブの正常性に関するリアルタイム値が提供されます。

![ジョブの履歴とメトリックを表示する](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

実行ごとの完全な応答など、各実行の履歴の詳細を表示するには、 **[履歴]** で各実行を選択します。 

![ジョブ履歴の詳細を表示する](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>ユーザー

Azure のロールベースのアクセス制御 (RBAC) を使用して、細かなレベルで各ユーザーの Azure Scheduler へのアクセスを管理できます。 ロールに基づくアクセスを設定する方法については、[RBAC を使用したアクセスの管理](../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

* [概念、用語、エンティティ階層](scheduler-concepts-terms.md)について学習する
* [複雑なスケジュールと高度な繰り返しを作成する](scheduler-advanced-complexity.md)
* [Scheduler に対する高可用性と信頼性](scheduler-high-availability-reliability.md)について学習する
* [制限、クォータ、既定値、エラー コード](scheduler-limits-defaults-errors.md)について学習する
