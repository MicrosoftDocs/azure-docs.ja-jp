---
title: "サービス通知でアクティビティ ログ アラートを受け取る | Microsoft Docs"
description: "Azure サービスが発生したときに、SMS、電子メール、または Webhook で通知を受け取ります。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: bf6a98fd7e7e11764bef174f9efd0635fa7efe9a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="create-activity-log-alerts-on-service-notifications"></a>サービス通知のアクティビティ ログ アラートを作成する
## <a name="overview"></a>概要
この記事では、Azure Portal を使用してサービスの正常性通知を行うアクティビティ ログ アラートを設定する方法について説明します。  

Azure でサービス正常性通知を Azure サブスクリプションに送信するときに、アラートを受け取ることができます。 次の情報に基づくアラートを構成できます。

- サービス正常性通知のクラス (インシデント、メンテナンス、情報など)。
- 影響を受けたサービス。
- 影響を受けたリージョン。
- 通知の状態 (アクティブおよび解決済み)。
- 通知レベル (詳細、警告、エラー)。

次の方法でアラートを送信するユーザーを構成することもできます。

- 既存のアクション グループを選択します。
- 新しいアクション グループを作成します (将来のアラートで使用できます)。

アクション グループの詳細については、[アクション グループの作成および管理](monitoring-action-groups.md)に関するページを参照してください。

Azure Resource Manager テンプレートを使用したサービス正常性通知アラートの構成方法の詳細については、[Resource Manager テンプレート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)に関するページを参照してください。

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Azure Portal を使用して新しいアクション グループのサービス正常性通知に関するアラートを作成する
1. [ポータル](https://portal.azure.com)で、**[モニター]** を選択します。

    !["モニター" サービス](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2. **[アクティビティ ログ]** セクションで、**[アラート]** を選択します。

    ![[アラート] タブ](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

3. **[アクティビティ ログ アラートの追加]** を選択し、各フィールドに入力します。

    ![[アクティビティ ログ アラートの追加] コマンド](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

4. **[アクティビティ ログ アラート名]** ボックスに名前を入力し、**[説明]** を入力します。

    ![[アクティビティ ログ アラートの追加] ダイアログ ボックス](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

5. **[サブスクリプション]** ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションは、アクティビティ ログ アラートの保存に使用されます。 アラート リソースはこのサブスクリプションにデプロイされ、アクティビティ ログのイベントを監視します。

6. アラート リソースを作成する **[リソース グループ]** を選択します。 これは、アラートによって監視されているリソース グループではありません。 そうではなく、アラート リソースが配置されているリソース グループです。

7. **[イベント カテゴリ]** ボックスで、**[サービスの正常性]** を選択します。 必要に応じて、受信するサービス正常性通知の **[サービス]**、**[リージョン]**、**[種類]**、**[状態]**、および **[レベル]** を選択します。

8. **[通知手段]** で、**[新規]** アクション グループ ボタンを選択します。 **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、このアラートが発生したときに送信される通知で参照されます。

9. 受信者についての次の情報を入力して、受信者の一覧を定義します。

    a. **名前**: 受信者の名前、エイリアス、または識別子を入力します。

    b. **アクションの種類**: SMS、電子メール、または webhook を選択します。

    c. **詳細:** 選択したアクションの種類に基づいて、電話番号、電子メール アドレス、または webhook の URI を入力します。

10. **[OK]** を選択してアラートを作成します。

数分以内にアラートがアクティブになり、作成時に指定した条件に基づいてトリガーが開始されます。

アクティビティ ログ アラートの webhook スキーマの詳細については、「[Azure アクティビティ ログ アラートのための webhook](monitoring-activity-log-alerts-webhook.md)」を参照してください。

>[!NOTE]
>上記の手順で定義されたアクション グループは、今後すべてのアラート定義で既存のアクション グループとして再利用できます。
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Azure Portal を使用して既存のアクション グループのサービス正常性通知に関するアラートを作成する

1. 前のセクションの手順 1. から 7. に従って、サービス正常性通知を作成します。 

2. **[通知手段]** で、**[既存]** アクション グループ ボタンを選択します。 適切なアクション グループを選択します。

3. **[OK]** を選択してアラートを作成します。

数分以内にアラートがアクティブになり、作成時に指定した条件に基づいてトリガーが開始されます。

## <a name="manage-your-alerts"></a>アラートの管理

アラートを作成すると、**[モニター]** ブレードの **[アラート]** セクションにアラートが表示されます。 管理するアラートを選択します。

* 編集する。
* 削除する。
* 無効または有効にしてそのアラートの通知受信を一時的に停止または再開する。

## <a name="next-steps"></a>次のステップ
- [サービス正常性の通知](monitoring-service-notifications.md)について学習します。
- [通知のレート制限](monitoring-alerts-rate-limiting.md)について学習します。
- [アクティビティ ログ アラート webhook スキーマ](monitoring-activity-log-alerts-webhook.md)を確認します。
- [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを受信する方法について学習します。 
- [アクション グループ](monitoring-action-groups.md)について学習します。

