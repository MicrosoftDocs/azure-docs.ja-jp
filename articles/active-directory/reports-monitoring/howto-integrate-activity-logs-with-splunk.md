---
title: Azure Monitor を使用して Splunk を統合する | Microsoft Docs
description: Azure Monitor を使用して Azure Active Directory のログを Splunk と統合する方法について説明します。
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
ms.date: 03/10/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6a597d4fd58646f56e271cb6027fb46db1e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634228"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>方法:Azure Monitor を使用して Azure Active Directory のログを Splunk と統合する

この記事では、Azure Monitor を使用して Azure Active Directory (Azure AD) のログを Splunk と統合する方法について説明します。 最初にログを Azure イベント ハブにルーティングした後、イベント ハブを Splunk と統合します。

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。

- Azure AD のアクティビティ ログを含む Azure イベント ハブ。 [アクティビティ ログをイベント ハブにストリーミングする](./tutorial-azure-monitor-stream-logs-to-event-hub.md)方法を確認してください。 

-  [Microsoft Cloud Services 用の Splunk アドオン](https://splunkbase.splunk.com/app/3110/#/details)。 

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory ログの統合 

1. Splunk インスタンスを開き、 **[Data Summary]\(データの概要\)** を選択します。

    ![[Data Summary]\(データの概要\) ボタン](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. **[Sourcetypes]\(ソース タイプ\)** タブを選択し、**amal: aadal:audit** を選択します。

    ![[Data Summary]\(データの概要\) の [Sourcetypes]\(ソース タイプ\) タブ](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    次の図のような Azure AD アクティビティ ログが表示されます。

    ![アクティビティ ログ](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> アドオンを Splunk インスタンスにインストールできない場合 (たとえば、プロキシを使用している場合、Splunk Cloud で実行している場合など)、Splunk HTTP Event Collector にこれらのイベントを転送できます。 そのためには、イベント ハブの新しいメッセージによってトリガーされるこの [Azure 関数](https://github.com/Microsoft/AzureFunctionforSplunkVS)を使います。 
>

## <a name="next-steps"></a>次のステップ

* [Azure Monitor で監査ログのスキーマを解釈する](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor でサインイン ログのスキーマを解釈する](reference-azure-monitor-sign-ins-log-schema.md)
* [よく寄せられる質問と既知の問題](concept-activity-logs-azure-monitor.md#frequently-asked-questions)