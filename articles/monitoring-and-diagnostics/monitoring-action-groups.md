---
title: "Azure Portal でのアクション グループの作成および管理 | Microsoft Docs"
description: "Azure Portal でアクション グループを作成および管理する方法について説明します。"
author: anirudhcavale
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
ms.date: 05/15/2017
ms.author: ancav
ms.openlocfilehash: 7347be8520e643cd166851d3f525a9a0726b40c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal でのアクション グループの作成および管理
## <a name="overview"></a>概要 ##
この記事では、Azure Portal でアクション グループを作成および管理する方法について説明します。

アクション グループを使用して、アクションのリストを構成できます。 これらのグループはアクティビティ ログ アラートを定義するときに活用できます。 これらのグループは、定義する各アクティビティ ログ アラートで再利用することができ、アクティビティ ログ アラートがトリガーされるときに必ず特定のアクション グループが呼び出されます。

アクション グループには、最大 10 個のアクションの種類を設定できます。 各アクションは次のプロパティで構成されます。

* **名前**: アクション グループ内の一意識別子。  
* **アクションの種類**: SMS の送信、メールの送信、webhook の呼び出し、または ITSM ツールへのデータの送信。
* **詳細**: 対応する電話番号、メール アドレス、webhook の URI、または ITSM 接続の詳細。

Azure Resource Manager テンプレートを使用したアクション グループの構成に関する詳細については、「[アクション グループの Resource Manager テンプレート](monitoring-create-action-group-with-resource-manager-template.md)」を参照してください。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal を使用したアクション グループの作成 ##
1. [ポータル](https://portal.azure.com)で、**[モニター]** を選択します。 **[モニター]** ブレードでは、すべての監視設定とデータが 1 つのビューにまとめられています。

    ![[モニター] サービス](./media/monitoring-action-groups/home-monitor.png)
2. **[アクティビティ ログ]** セクションで、**[アクション グループ]** を選択します。

    ![[アクション グループ] タブ](./media/monitoring-action-groups/action-groups-blade.png)
3. **[アクション グループの追加]** を選択し、フィールドに入力します。

    ![[アクション グループの追加] コマンド](./media/monitoring-action-groups/add-action-group.png)
4. **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

      ![[アクション グループの追加] ダイアログ ボックス](./media/monitoring-action-groups/action-group-define.png)

5. **[サブスクリプション]** ボックスには、現在のサブスクリプションが自動入力されます。 このサブスクリプションにアクション グループが保存されます。

6. アクション グループが保存される **[リソース グループ]** を選択します。

7. 次に、各アクションを指定して、アクションの一覧を定義します。

    a. **[名前]**: このアクションの一意識別子を入力します。

    b. **[アクションの種類]**: SMS、メール、webhook、または ITSM を選びます。

    c. **[詳細]**: アクションの種類に基づいて、電話番号、メール アドレス、webhook の URI、または ITSM 接続の詳細を入力します。 ITSM アクションの場合は、さらに ITSM ツールで必要な **[作業項目]** および他のフィールドを指定します。 

> [!NOTE]
> ITSM アクションには ITSM 接続が必要です。 [ITSM 接続](../log-analytics/log-analytics-itsmc-overview.md)の作成方法を確認してください。 現在、ITSM アクションはアクティビティ ログ アラートに対してのみ機能します。 他のアラートの種類では動作しません。
>
>

8. **[OK]** を選択して、アクション グループを作成します。

## <a name="manage-your-action-groups"></a>アクション グループの管理 ##
アクション グループを作成すると、**[モニター]** ブレードの **[アクション グループ]** セクションに表示されます。 次の操作を行うために管理するアクション グループを選択します。

* アクションの追加、編集、または削除。
* アクション グループの削除。

## <a name="next-steps"></a>次のステップ ##
* 詳細については、「[SMS アラート動作](monitoring-sms-alert-behavior.md)」を参照してください。  
* [アクティビティ ログ アラートに対する webhook スキーマについて理解](monitoring-activity-log-alerts-webhook.md)します。  
* [ITSM コネクタ](../log-analytics/log-analytics-itsmc-overview.md)について学習します。
* アラートの[レート制限](monitoring-alerts-rate-limiting.md)について学習します。 
* [アクティビティ ログ アラートの概要](monitoring-overview-alerts.md)を把握し、アラートを受信する方法について学習します。  
* [サービスの正常性通知が投稿されるたびにアラートを設定](monitoring-activity-log-alerts-on-service-notifications.md)する方法について学習します。
