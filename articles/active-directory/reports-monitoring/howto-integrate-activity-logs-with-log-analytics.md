---
title: Azure Active Directory のログを Azure Monitor ログにストリーミングする | Microsoft Docs
description: Azure Active Directory のログを Azure Monitor ログと統合する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f328ed44252f7fb314552d6d05df9806f59d972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591121"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Azure AD ログを Azure Monitor ログと統合する

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure Monitor ログを使用すると、データのクエリを行って、特定のイベントを検索し、傾向を分析して、さまざまなデータ ソース間の相関を実行できます。 Azure Monitor ログに Azure AD アクティビティ ログを統合すると、次のようなタスクを実行できます。

 * Azure Security Center によって公開されたセキュリティ ログに対して Azure AD のサインイン ログを比較します

 * Azure Application Insights からのアプリケーション パフォーマンス データを相関させることによって、アプリケーションのサインイン ページでのパフォーマンス ボトルネックのトラブルシューティングを行います。  

Ignite セッションの次のビデオでは、実用的なユーザー シナリオで Azure AD ログに対して Azure Monitor ログを使用する利点が実演されています。

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

この記事では、Azure Active Directory (Azure AD) のログを Azure Monitor と統合する方法について説明します。

## <a name="supported-reports"></a>サポートされるレポート

監査アクティビティ ログとサインイン アクティビティ ログを Azure Monitor ログにルーティングして、さらに詳しく分析できます。 

* **[監査ログ]** :テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](concept-audit-logs.md)で把握できます。
* **サインイン ログ**:監査ログによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](concept-sign-ins.md)で判断することができます。
* **プロビジョニング ログ**:[プロビジョニング ログ](../app-provisioning/application-provisioning-log-analytics.md)を利用することで、サードパーティ製アプリケーションで作成、更新、削除されたユーザーを監視できます。 

> [!NOTE]
> 現時点では、B2C 関連の監査およびサインインのアクティビティ ログはサポートされません。
>

## <a name="prerequisites"></a>前提条件 

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure AD テナント。
* Azure AD テナントの "*グローバル管理者*" または "*セキュリティ管理者*" であるユーザー。
* Azure サブスクリプションの Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](../../azure-monitor/logs/quick-create-workspace.md)を確認してください。

## <a name="licensing-requirements"></a>ライセンスの要件

この機能を使用するには、Azure AD Premium P1 または P2 ライセンスが必要です。 要件に対する適切なライセンスを確認するには、「[Free、Basic、および Premium エディションの一般公開されている機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

## <a name="send-logs-to-azure-monitor"></a>ログを Azure Monitor に送信する

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]**  >  **[診断設定]**  ->  **[Add diagnostic setting]\(診断設定の追加\)** を選択します。 **[監査ログ]** または **[サインイン]** ページから **[エクスポート設定]** を選択して、診断設定の構成ページに移動することもできます。  
    
3. **[診断設定]** メニューで **[Send to Log Analytics workspace]\(Log Analytics ワークスペースに送信\)** チェック ボックスをオンにして、 **[構成]** を選択します。

4. ログ送信先の Log Analytics ワークスペースを選択するか、表示されたダイアログ ボックスで新しいワークスペースを作成します。  

5. 次のいずれかまたは両方を実行します。
    * 監査ログを Log Analytics ワークスペースに送信するには、 **[AuditLogs]** チェックボックスをオンにします。 
    * サインイン ログを Log Analytics ワークスペースに送信するには、 **[SignInLogs]** チェックボックスをオンにします。

6. **[保存]** を選択して設定を保存します。

    ![診断設定](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. 約 15 分後に、イベントが Log Analytics ワークスペースにストリーミングされていることを確認します。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログ を使用して Azure AD アクティビティ ログを分析する](howto-analyze-activity-logs-log-analytics.md)
* [Azure Active Directory ログ分析用のビューのインストールと使用](howto-install-use-log-analytics-views.md)