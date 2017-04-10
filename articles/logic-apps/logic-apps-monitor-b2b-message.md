---
title: "B2B トランザクションのメッセージを監視する - Azure Logic Apps | Microsoft Docs"
description: "統合アカウントで Logic Apps を使用して、プロセス間およびアプリケーション間の B2B 通信時にメッセージを監視および追跡します。"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>AS2、X12 および EDIFACT メッセージのログ記録を開始または有効にして、成功、失敗、メッセージのプロパティを監視します。

B2B 通信では、実行中の&2; つのビジネス プロセスまたはアプリケーション間でメッセージ交換が行われます。 リレーションシップは、ビジネス プロセスの間の契約を定義するものです。 通信が確立されたら、メッセージの監視を設定して、通信が予想どおりに機能していることを確認できます。 高度な詳細情報とデバッグのために、統合アカウントで診断を設定できます。

メッセージ追跡は、AS2、X12、EDIFACT の各 B2B プロトコルで使用できます。 

## <a name="prerequisites"></a>前提条件

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 統合アカウント。[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)を作成できます。
* ロジック アプリ。[ロジック アプリ](logic-apps-create-a-logic-app.md)を作成し、[ログ記録を有効にする](logic-apps-monitor-your-logic-apps.md)ことができます。

## <a name="enable-logging-for-an-integration-account"></a>統合アカウントのログ記録の有効化

統合アカウントのログ記録は、**Azure Portal** または **Monitor** を使用して有効にすることができます。

### <a name="enable-logging-with-azure-portal"></a>Azure ポータルを使用したログの有効化

1. 統合アカウントを選択し、**[診断ログ]** を選択します。

    ![統合アカウントを選択する](media/logic-apps-monitor-b2b-message/pic5.png)

2. **サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で **[統合アカウント]** を選択します。 **[リソース]** で使用する統合アカウントを選択します。 **[診断を有効にする]** をクリックして、選択した統合アカウントで診断を有効にします。

    ![統合アカウントで診断を設定する](media/logic-apps-monitor-b2b-message/pic2.png)

3. 状態として **[有効]** を選択します。

    ![診断の状態を有効にする](media/logic-apps-monitor-b2b-message/pic3.png)

4. **[Send to Log Analytics (Log Analytics に送信)]** を選択し、データを出力するように Log Analytics を構成します。

    ![診断データをログに送信する](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>Monitor を使用したログの有効化

0. **[Monitor]** を選択し、**[診断ログ]** を選択します。

    ![[Monitor] を選択し、[診断ログ] を選択する](media/logic-apps-monitor-b2b-message/pic1.png)

0. **サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で **[統合アカウント]** を選択します。 **[リソース]** で使用する統合アカウントを選択します。 **[診断を有効にする]** をクリックして、選択した統合アカウントで診断を有効にします。

    ![統合アカウントで診断を設定する](media/logic-apps-monitor-b2b-message/pic2.png)

0. 状態として **[有効]** を選択します。

    ![診断の状態を有効にする](media/logic-apps-monitor-b2b-message/pic3.png) 

0. **[Send to Log Analytics (Log Analytics に送信)]** を選択し、データを出力するように **Log Analytics** を構成します。

    ![診断データをログに送信する](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>ソリューションの拡張

**Log Analytics** の他に、統合アカウントと [Logic Apps](./logic-apps-monitor-your-logic-apps.md) を構成して、イベント ハブやストレージ アカウントにも出力できます。

![Azure 診断の設定](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

こうしたテレメトリをイベント ハブやストレージから他のサービス ([Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)、[Power BI](https://powerbi.com) など) に渡すことで、統合ワークフローをリアルタイムに監視することができます。

## <a name="supported-tracking-schema"></a>サポートされている追跡スキーマ

次の種類の追跡スキーマがサポートされています。カスタム以外はすべて固定スキーマです。

* [カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)
* [AS2 の追跡スキーマ](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>次のステップ

[OMS ポータルでの B2B メッセージの追跡](logic-apps-track-b2b-messages-omsportal.md "B2B メッセージの追跡")

[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")


