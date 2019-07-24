---
title: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを SumoLogic にストリーミングする | Microsoft Docs
description: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを SumoLogic と統合する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bed1b96052117534f319bbf9570618e6481032f
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437210"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor-preview"></a>Azure Monitor (プレビュー) を使用して Azure Active Directory のログを SumoLogic と統合する方法

この記事では、Azure Monitor を使用して Azure Active Directory (Azure AD) のログを SumoLogic と統合する方法について説明します。 最初にログを Azure イベント ハブにルーティングした後、イベント ハブを SumoLogic と統合します。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。
* Azure AD のアクティビティ ログを含む Azure イベント ハブ。 [アクティビティ ログをイベント ハブにストリーミングする](quickstart-azure-monitor-stream-logs-to-event-hub.md)方法を確認してください。 
* SumoLogic でのシングル サインオンが有効なサブスクリプション。

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Azure AD のログを SumoLogic と統合する手順 

1. まず、[Azure AD のログを Azure Event Hubs にストリーム配信](quickstart-azure-monitor-stream-logs-to-event-hub.md)します。
2. [Azure Active Directory のログを収集](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)するための構成を SumoLogic インスタンスに対して行います。
3. 対象環境のリアルタイム分析を行うことができる事前構成済みのダッシュボードを使用するために、[Azure AD SumoLogic アプリをインストール](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)します。

   ![ダッシュボード](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>次の手順

* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
