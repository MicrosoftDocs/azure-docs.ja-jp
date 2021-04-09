---
title: Azure Monitor ログを使用してアクティビティ ログを分析する | Microsoft Docs
description: Azure Monitor ログを使用して Azure Active Directory アクティビティ ログを分析する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: 905261058c2de0afae18cbc5572c64962bef8834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580015"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure Monitor ログを使用して Azure AD アクティビティ ログを分析する

[Azure AD アクティビティ ログを Azure Monitor ログと統合](howto-integrate-activity-logs-with-log-analytics.md)した後、Azure Monitor ログの機能を使用して、環境に対する洞察を得ることができます。 [Azure AD アクティビティ ログ用の Log Analytics ビュー](howto-install-use-log-analytics-views.md)をインストールして、環境での監査およびサインイン イベントに関する事前構築済みレポートへのアクセス権を取得することもできます。

この記事では、Log Analytics ワークスペースでの Azure AD アクティビティ ログを分析する方法について説明します。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件 

理解するには、次の内容が必要です。

* Azure サブスクリプションの Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](../../azure-monitor/logs/quick-create-workspace.md)を確認してください。
* 最初に、[Azure AD アクティビティ ログを Log Analytics ワークスペースにルーティングする](howto-integrate-activity-logs-with-log-analytics.md)手順を完了する。
*  Log Analytics ワークスペースへの[アクセス](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)
* Azure Active Directory の次のロール (Azure Active Directory ポータルで Log Analytics にアクセスする場合)
    - セキュリティ管理者
    - セキュリティ閲覧者
    - レポート閲覧者
    - 全体管理者
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Log Analytics ワークスペースに移動する

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]** を選択し、 **[監視]** セクションから **[ログ]** を選択し、Log Analytics ワークスペースを開きます。 ワークスペースは既定のクエリで開きます。

    ![既定のクエリ](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Azure AD アクティビティ ログのスキーマを表示する

ログは、ワークスペース内の **AuditLogs** テーブルと **SigninLogs** テーブルにプッシュされます。 これらのテーブルのスキーマを表示するには:

1. 以前のセクションの既定のクエリ ビューから、 **[スキーマ]** を選択し、ワークスペースを展開します。 

2. **[ログ管理]** セクションを展開し、続いて **[AuditLogs]** または **[SignInLogs]** のどちらかを展開してログ スキーマを表示します。
    ![監査ログ](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![サインイン ログ](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Azure AD アクティビティ ログのクエリを実行する

ワークスペースにログが用意されたので、これでこれらに対してクエリを実行できます。 たとえば、過去 1 週間に使用された最上位のアプリケーションを取得するには、既定のクエリを次のものに置き換えて、 **[実行]** を選択します

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

過去 1 週間にわたる最上位の監査イベントを取得するには、次のクエリを使用します。

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Azure AD アクティビティ ログ データに関するアラート

クエリでアラートを設定することもできます。 たとえば、10 個を超えるアプリケーションが過去 1 週間に使用された場合にアラートを構成するには:

1. ワークスペースから、 **[Set alert]** (アラートの設定) を選択して **[ルールの作成]** ページを開きます。

    ![警告の設定](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. アラートに作成された既定の **アラートの条件** を選択し、既定のメトリックの **[しきい値]** を 10 に更新します。

    ![アラートの条件](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. アラートの名前と説明を入力して、重大度を選択します。 この例では、これを **[情報]** に設定できます。

4. 信号が発生したときにアラートする **[アクション グループ]** を選択します。 電子メールまたはテキスト メッセージを使用してチームに通知することも、Webhook、Azure Functions、または Logic Apps を使用してアクションを自動化することもできます。 [Azure portal でのアラート グループの作成および管理](../../azure-monitor/alerts/action-groups.md)の詳細を理解します。

5. アラートを構成し終えたら、 **[アラートの作成]** を選択して有効にします。 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>Azure AD アクティビティ ログの事前構築済みブックを使用する

ブックには、監査、サインイン、プロビジョニングの各イベントに関する一般的なシナリオに関連した複数のレポートが表示されます。 前のセクションで説明されている手順を使用して、レポートに表示されるデータのいずれかについてアラートすることもできます。

* **プロビジョニング分析**:この [ブック](../app-provisioning/application-provisioning-log-analytics.md)には、プロビジョニングされた新しいユーザーとプロビジョニング エラーの数、更新されたユーザーと更新エラーの数、プロビジョニング解除されたユーザーと該当するエラーの数など、プロビジョニング アクティビティの監査に関連するレポートが表示されます。    
* **Sign-ins Events\(サインイン イベント\)** :このブックには、経時的にサインイン数を追跡する概要ビューだけでなく、アプリケーション、ユーザー、デバイスごとのサインインなど、サインイン アクティビティの監視に関連する最も重要なレポートが表示されます。
* **分析情報への条件付きアクセス**:条件付きアクセスに関する分析情報とレポートの [ブック](../conditional-access/howto-conditional-access-insights-reporting.md)を使用すると、自分の組織内での一定期間にわたる条件付きアクセス ポリシーの影響を把握できます。 

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログでクエリの使用を開始する](../../azure-monitor/logs/get-started-queries.md)
* [Create and manage alert groups in the Azure portal](../../azure-monitor/alerts/action-groups.md)
* [Azure Active Directory ログ分析用のビューのインストールと使用](howto-install-use-log-analytics-views.md)