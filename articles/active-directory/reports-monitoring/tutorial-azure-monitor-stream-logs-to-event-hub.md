---
title: チュートリアル - ログを Azure イベント ハブにストリーム配信する | Microsoft Docs
description: Azure Active Directory のログをイベント ハブにプッシュするよう Azure Diagnostics を設定する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0443dcb2bf3bd58f2474c507c9f9594fb6d8a7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469186"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>チュートリアル:Azure Active Directory ログを Azure イベント ハブにストリーム配信する

このチュートリアルでは、Azure Active Directory (Azure AD) のログを Azure イベント ハブにストリーム配信するよう Azure Monitor の診断設定をセットアップする方法について説明します。 このメカニズムを使用して、Splunk や QRadar といったサードパーティのセキュリティ情報イベント管理 (SIEM) ツールにログを統合します。

## <a name="prerequisites"></a>前提条件 

この機能を使用するには、次が必要です。

* Azure サブスクリプション。 Azure サブスクリプションを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure AD テナント。
* Azure AD テナントの "*グローバル管理者*" または "*セキュリティ管理者*" であるユーザー。
* Azure サブスクリプション内の Event Hubs 名前空間とイベント ハブ。 [イベント ハブの作成](../../event-hubs/event-hubs-create.md)方法に関するページを参照してください。

## <a name="stream-logs-to-an-event-hub"></a>イベント ハブにログをストリーム配信する

1. [Azure portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory]**  >  **[監視]**  >  **[監査ログ]** の順に選択します。 

3. **[エクスポート設定]** を選択します。  
    
4. **[診断設定]** ウィンドウで、次のいずれかの操作を実行します。
    * 既存の設定を変更するには、 **[設定の編集]** を選択します。
    * 新しい設定を追加するには、 **[診断の設定の追加]** を選択します。  
      最大で 3 つの設定を作成できます。

      ![設定のエクスポート](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. **[Stream to an event hub]\(イベント ハブにストリーム\)** チェックボックスをオンにし、 **[Event Hub/Configure]\(イベント ハブ/構成\)** を選択します。

6. ログのルーティング先となる Azure サブスクリプションと Event Hubs 名前空間を選択します。  
    サブスクリプションと Event Hubs 名前空間は、両方ともログのストリーム元である Azure AD テナントと関連付けられている必要があります。 Event Hubs 名前空間内にログの送信先となるイベント ハブを指定することもできます。 イベント ハブを指定しない場合、イベント ハブは名前空間内に既定の名前 **insights-logs-audit** で作成されます。

7. **[OK]** を選択してイベント ハブの構成を終了します。

8. 次のいずれかまたは両方を実行します。
    * 監査ログをイベント ハブに送信するには、 **[AuditLogs]** チェック ボックスをオンにします。 
    * サインイン ログをイベント ハブに送信するには、 **[SignInLogs]** チェック ボックスをオンにします。

9. **[保存]** を選択して設定を保存します。

    ![診断設定](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. 約 15 分後にイベントがイベント ハブに表示されることを確認します。 これを行うには、ポータルからイベント ハブに移動し、**受信メッセージ** の数がゼロより大きい値になっていることを確認します。 

    ![監査ログ](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>イベント ハブからデータにアクセスする

イベント ハブにデータが表示されたら、次の 2 つの方法でデータにアクセスして読み取ることができます。

* **サポートされている SIEM ツールを構成する**。 ほとんどのツールは、イベント ハブからデータを読み取るために、イベント ハブ接続文字列と、Azure サブスクリプションへの特定のアクセス許可が必要です。 以下に示したのは、Azure Monitor との統合に対応したサードパーティ ツールの例です。
    
    * **ArcSight**: Azure AD のログと Splunk の統合の詳細については、「[Azure Monitor を使用して Azure Active Directory のログを ArcSight と統合する](howto-integrate-activity-logs-with-arcsight.md)」を参照してください。
    
    * **Splunk**: Azure AD のログと Splunk の統合の詳細については、[Azure Monitor を使用した Azure AD のログと Splunk の統合](./howto-integrate-activity-logs-with-splunk.md)に関するページを参照してください。
    
    * **IBM QRadar**: DSM および Azure Event Hub Protocol は、[IBM サポート](https://www.ibm.com/support)からダウンロードすることができます。 Azure との統合について詳しくは、[IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) のサイトをご覧ください。
    
    * **Sumo Logic**: イベント ハブのデータを使用するように Sumo Logic を設定するには、「[Install the Azure AD app and view the dashboards (Azure AD アプリをインストールし、ダッシュボードを表示する)](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)」を参照してください。 

* **カスタム ツールを設定する**。 現在お使いの SIEM がまだ Azure Monitor 診断でサポートされていない場合は、Event Hubs API を使用してカスタム ツールを設定できます。 詳しくは、[イベント ハブからメッセージ受信を開始する](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)方法に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ

* [Azure Monitor を使用して Azure Active Directory のログを ArcSight と統合する](howto-integrate-activity-logs-with-arcsight.md)
* [Azure Monitor を使用して Azure AD のログを Splunk と統合する](./howto-integrate-activity-logs-with-splunk.md)
* [Azure Monitor を使用して Azure AD のログを SumoLogic と統合する](howto-integrate-activity-logs-with-sumologic.md)
* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
