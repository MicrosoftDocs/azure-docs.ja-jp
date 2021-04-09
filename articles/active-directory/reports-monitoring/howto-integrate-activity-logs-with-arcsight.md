---
title: Azure Monitor を使用してログを ArcSight と統合する | Microsoft Docs
description: Azure Monitor を使用して Azure Active Directory のログを ArcSight と統合する方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230519"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Azure Monitor を使用して Azure Active Directory のログを ArcSight と統合する

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) は、プラットフォーム内のセキュリティ上の脅威を検出して対処するために役立つセキュリティ情報イベント管理 (SIEM) ソリューションです。 Azure Monitor で Azure AD 用の ArcSight コネクタを使用して、Azure Active Directory (Azure AD) ログを ArcSight にルーティングできるようになりました。 この機能により、ArcSight を使用してテナントのセキュリティ侵害を監視することができます。  

この記事では、Azure Monitor を使用して Azure AD ログを ArcSight にルーティングする方法について説明します。 

## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。
* Azure AD のアクティビティ ログを含む Azure イベント ハブ。 [アクティビティ ログをイベント ハブにストリーミングする](./tutorial-azure-monitor-stream-logs-to-event-hub.md)方法を確認してください。 
* ArcSight Syslog NG Daemon SmartConnector (SmartConnector) または ArcSight Load Balancer の構成済みインスタンス。 イベントが ArcSight Load Balancer に送信されると、その結果、Load Balancer によってイベントは SmartConnector に送信されます。

[ArcSight SmartConnector for Azure Monitor Event Hub の構成ガイド](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)をダウンロードして開いてください。 このガイドには、ArcSight SmartConnector for Azure Monitor のインストールと構成に必要な手順が記載されています。 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Azure AD ログと ArcSight の統合

1. まず構成ガイドの「**Prerequisites**」(前提条件) セクションの手順を完了します。 このセクションには、次の手順が記載されています。
    * Azure でユーザー アクセス許可を設定し、コネクタのデプロイと構成を行うことができる **所有者** ロールを持つユーザーを用意します。
    * Syslog NG Daemon SmartConnector がインストールされているサーバー上のポートを開き、Azure からアクセスできるようにします。 
    * デプロイによって Windows PowerShell スクリプトが実行されるため、コネクタをデプロイするマシン上で PowerShell がスクリプトを実行できるようにする必要があります。

2. 構成ガイドの「**Deploying the Connector**」(コネクタのデプロイ) セクションの手順に従ってコネクタをデプロイします。 このセクションでは、コネクタのダウンロードと抽出、アプリケーションのプロパティの構成、および抽出されたフォルダーからのデプロイ スクリプトの実行方法について手順を追って説明されています。 

3. 「**Verifying the Deployment in Azure**」(Azure でのデプロイの検証) の手順に従って、コネクタが設定され、正しく機能していることを確認します。 次の点を確認します。
    * Azure サブスクリプションで必要な Azure 関数が作成されている。
    * Azure AD ログが正しい送信先にストリーミングされている。 
    * デプロイのアプリケーション設定が、Azure Function Apps の [アプリケーション設定] に保持されている。 
    * ArcSight コネクタ用の Azure AD アプリケーションと、CEF 形式のマップ ファイルを含むストレージ アカウントを使用して、ArcSight 用の新しいリソース グループが Azure 内に作成されます。

4. 最後に、構成ガイドの「**Post-Deployment Configurations**」(デプロイ後の構成) に記載されているデプロイ後の手順を完了します。 このセクションには、タイムアウト期間後に関数アプリがアイドルにならないように App Service プランを使用している場合に追加の構成を実行する方法、イベント ハブのリソース ログのストリーミングを構成する方法、および新しく作成したストレージ アカウントと関連付けられるように SysLog NG Daemon SmartConnector キーストア証明書を更新する方法が説明されています。

5. この構成ガイドには、Azure 上でコネクタのプロパティをカスタマイズする方法、およびコネクタをアップグレードおよびアンインストールする方法についても説明されています。 [Azure の従量課金プラン](https://azure.microsoft.com/pricing/details/functions)へのアップグレードや ArcSight Load Balancer の構成 (イベントの負荷が Syslog NG Daemon SmartConnector で処理できる量を超えている場合) など、パフォーマンスの改善に関するセクションもあります。

## <a name="next-steps"></a>次のステップ

[ArcSight SmartConnector for Azure Monitor Event Hub の構成ガイド](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)