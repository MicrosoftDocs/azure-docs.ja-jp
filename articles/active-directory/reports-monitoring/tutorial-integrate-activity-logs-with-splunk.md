---
title: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Splunk にストリーミングする | Microsoft Docs
description: Azure Monitor (プレビュー) を使用して Azure Active Directory のログを Splunk と統合する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
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
ms.openlocfilehash: 94039655a6cbe7878fa1f61ce759485392dc7e98
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438854"
---
# <a name="integrate-azure-ad-logs-with-splunk-using-azure-monitor-preview"></a>Azure Monitor (プレビュー) を使用して Azure AD のログを Splunk と統合する

この記事では、Azure Monitor を使用して Azure Active Directory (Azure AD) のログを Splunk と統合する方法について説明します。 最初にログを Azure イベント ハブにルーティングした後、イベント ハブを Splunk と統合します。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。
* Azure AD のアクティビティ ログを含む Azure イベント ハブ。 [アクティビティ ログをイベント ハブにストリーミングする](quickstart-azure-monitor-stream-logs-to-event-hub.md)方法を確認してください。 
* Splunk 向けの Azure Monitor アドオン。 [Splunk のインスタンスをダウンロードして構成](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)します。

## <a name="tutorial"></a>チュートリアル 

1. Splunk インスタンスを開き、**[Data Summary]\(データの概要\)** を選択します。

    ![[Data Summary]\(データの概要\) ボタン](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. **[Sourcetypes]\(ソース タイプ\)** タブを選択し、**amal: aadal:audit** を選択します。

    ![[Data Summary]\(データの概要\) の [Sourcetypes]\(ソース タイプ\) タブ](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    次の図のような Azure AD アクティビティ ログが表示されます。

    ![アクティビティ ログ](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> アドオンを Splunk インスタンスにインストールできない場合 (たとえば、プロキシを使用している場合、Splunk Cloud で実行している場合など)、Splunk HTTP Event Collector にこれらのイベントを転送できます。 そのためには、イベント ハブの新しいメッセージによってトリガーされるこの [Azure 関数](https://github.com/Microsoft/AzureFunctionforSplunkVS)を使います。 
>

## <a name="next-steps"></a>次の手順

* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
