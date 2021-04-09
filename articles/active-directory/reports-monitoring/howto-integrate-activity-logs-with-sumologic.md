---
title: Azure Monitor を使用してログを SumoLogic にストリーミングする | Microsoft Docs
description: Azure Monitor を使用して Azure Active Directory のログを SumoLogic と統合する方法について説明します。
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
ms.openlocfilehash: 51e1f45c787c319c32358e7f310108131647d60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335835"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Azure Monitor を使用して Azure Active Directory のログを SumoLogic と統合する方法

この記事では、Azure Monitor を使用して Azure Active Directory (Azure AD) のログを SumoLogic と統合する方法について説明します。 最初にログを Azure イベント ハブにルーティングした後、イベント ハブを SumoLogic と統合します。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。
* Azure AD のアクティビティ ログを含む Azure イベント ハブ。 [アクティビティ ログをイベント ハブにストリーミングする](./tutorial-azure-monitor-stream-logs-to-event-hub.md)方法を確認してください。 
* SumoLogic でのシングル サインオンが有効なサブスクリプション。

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Azure AD のログを SumoLogic と統合する手順 

1. まず、[Azure AD のログを Azure Event Hubs にストリーム配信](./tutorial-azure-monitor-stream-logs-to-event-hub.md)します。
2. [Azure Active Directory のログを収集](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)するための構成を SumoLogic インスタンスに対して行います。
3. 対象環境のリアルタイム分析を行うことができる事前構成済みのダッシュボードを使用するために、[Azure AD SumoLogic アプリをインストール](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)します。

   ![ダッシュボード](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)