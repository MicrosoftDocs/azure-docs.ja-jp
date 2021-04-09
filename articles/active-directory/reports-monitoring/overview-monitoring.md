---
title: Azure Active Directory 監視とは | Microsoft Docs
description: Azure Active Directory 監視の概要について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577779"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Azure Active Directory 監視とは

Azure Active Directory (Azure AD) 監視を使用すると、Azure AD のアクティビティ ログを別のエンドポイントにルーティングできるようになります。 その後、それを長期的な使用のために保持したり、サードパーティのセキュリティ情報およびイベント管理 (SIEM) ツールと統合して環境の分析情報を取得したりすることができます。

現時点では、以下のものにログをルーティングすることができます。

- Azure ストレージ アカウント。
- Azure イベント ハブ。Splunk および Sumologic のインスタンスと統合することができます。
- Azure Log Analytics ワークスペース。このワークスペースで、データの分析や、特定のイベントのダッシュボードとアラートの作成を行うことができます。

**事前に必要なロール**:全体管理者

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD のレポートと監視のライセンスと前提条件

Azure AD サインイン ログにアクセスするには、Azure AD プレミアム ライセンスが必要になります。

機能とライセンスの詳細については、[Azure Active Directory 料金ガイド](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

Azure AD の監視とレポートをデプロイするには、Azure AD テナントのグローバル管理者またはセキュリティ管理者であるユーザーが必要になります。

ログ データの最終転送先によっては、次のいずれかが必要になります。

* ListKeys アクセス許可が付与された Azure ストレージ アカウント。 BLOB ストレージ アカウントではなく、一般的なストレージ アカウントを使用することをお勧めします。 ストレージの料金情報については、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)を参照してください。

* サードパーティ製の SIEM ソリューションと統合するための Azure Event Hubs の名前空間。

* Azure Monitor ログにログを送信する Azure Log Analytics ワークスペース。

## <a name="diagnostic-settings-configuration"></a>診断設定を構成する

Azure AD アクティビティ ログの監視設定を構成するには、まず、[Azure portal](https://portal.azure.com) にサインインし、次に **Azure Active Directory** を選択します。 ここからは、次の 2 つの方法で診断設定の構成のページにアクセスすることができます。

* **[監視]** セクションの **[診断設定]** を選択します。

    ![診断設定](./media/overview-monitoring/diagnostic-settings.png)
    
* **[監査ログ]** または **[サインイン]** を選択し、**[設定のエクスポート]** を選択します。 

    ![設定のエクスポート](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>ストレージ アカウントへのログのルーティング

ログを Azure ストレージ アカウントにルーティングすると、[保持ポリシー](reference-reports-data-retention.md)で規定されている既定の保持期間より長くログを保持できます。 データをストレージ アカウントにルーティングする方法については、[こちら](quickstart-azure-monitor-route-logs-to-storage-account.md)を参照してください。

## <a name="stream-logs-to-event-hub"></a>ログをイベント ハブにストリーミングする

ログを Azure イベント ハブにルーティングすることで、Sumologic や Splunk などのサードパーティの SIEM ツールと統合することができます。 この統合によって、Azure AD のアクティビティ ログ データと、SIEM によって管理されている他のデータを組み合わせ、より豊富な環境分析情報を提供することができます。 ログをイベント ハブにストリーム配信する方法については、[こちら](tutorial-azure-monitor-stream-logs-to-event-hub.md)を参照してください。

## <a name="send-logs-to-azure-monitor-logs"></a>Azure Monitor ログへのログの送信

[Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md)は、さまざまなソースからの監視データを統合し、アプリケーションとリソースの操作に関する分析情報を取得するためのクエリ言語と分析エンジンを提供するソリューションです。 Azure AD のアクティビティ ログを Azure Monitor ログに送信することで、収集したデータに対する迅速な取得、監視、およびアラートを行うことができます。 [Azure Monitor ログにデータを送信する](howto-integrate-activity-logs-with-log-analytics.md)方法を学習してください。

Azure AD のアクティビティ ログ用の既製のビューをインストールして、サインインと監査イベントを含む一般的なシナリオを監視することもできます。 [Azure AD のアクティビティ ログ用の Log Analytics ビューをインストールして使用する](howto-install-use-log-analytics-views.md)方法を学習してください。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor でのアクティビティ ログ](concept-activity-logs-azure-monitor.md)
* [ログをイベント ハブにストリーミングする](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Azure Monitor ログへのログの送信](howto-integrate-activity-logs-with-log-analytics.md)
