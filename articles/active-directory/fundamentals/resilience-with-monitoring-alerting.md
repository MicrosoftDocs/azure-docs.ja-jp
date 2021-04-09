---
title: Azure AD B2C を使用した監視と分析による回復性 | Microsoft Docs
description: Azure AD B2C を使用した監視と分析による回復性
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a834d4d30c40b618b1601a7f8901c68143ef4912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648519"
---
# <a name="resilience-through-monitoring-and-analytics"></a>監視と分析による回復性

監視することにより、アプリケーションとサービスの可用性とパフォーマンスは最大限に高められます。 インフラストラクチャとアプリケーションのテレメトリを収集、分析し、対応する包括的なソリューションが提供されます。 サービスやアプリケーションで問題が見つかると、アラートで事前に通知されます。 これにより、サービスのエンド ユーザーが問題に気付く前に問題を識別して対処できます。 [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) では、監査ログとサインイン ログの分析と検索を行い、カスタム ビューを作成することができます。

## <a name="monitor-and-get-notified-through-alerts"></a>監視を行いアラートを通じて通知を受ける

システムとインフラストラクチャを監視することは、サービス全体の正常性を確保するために不可欠です。 これは、新規ユーザーの到着、エンド ユーザーの認証率、変換などのビジネス メトリックを定義するところから始まります。 このような監視対象の指標を構成します。 プロモーションや休日のトラフィックに起因する急上昇が見込まれる場合は、そのイベントおよび対応するビジネス メトリックのベンチマーク向けに推定値を修正してください。 イベントの後に、以前のベンチマークに戻します。

同様に、障害やパフォーマンスの中断を検出するには、適切なベースラインを設定し、アラートを定義することが、新たに発生した問題に迅速に対応するために不可欠です。

![監視と分析のコンポーネントを示す図](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>監視とアラートを実装する方法

- **監視**:[Azure Monitor](../../active-directory-b2c/azure-monitor.md) を使用して、主要なサービス レベル目標 (SLO) に対して正常性を継続的に監視し、重大な変化が起きたときに通知を受けることができます。 まず、SLO を維持するために正常性を監視する必要があるビジネスの重要なコンポーネントである Azure AD B2C ポリシーまたはアプリケーションを特定します。 SLO に対応した主要指標を特定します。
たとえば、次のメトリックはどちらも急激に低下するとビジネスの損失につながるため、追跡の対象とします。

  - **要求の総数**: Azure AD B2C ポリシーに送信された要求の総数 "n"。

  - **成功率 (%)** : 成功した要求の数 / 要求の総数。

  Azure AD B2C ポリシー ベースのログ、[監査ログ](../../active-directory-b2c/analytics-with-application-insights.md)、およびサインイン ログが格納される [Application Insights](../../active-directory-b2c/analytics-with-application-insights.md) で、[主要指標](../../active-directory-b2c/view-audit-logs.md)にアクセスします。  

   - **視覚化**:Log Analytics のビルド ダッシュボードを使用して、主要指標を視覚的に監視します。

   - **現在の期間**:現在の期間 (今週など) における要求の総数と成功率 (%) の変化を示す一時的なグラフを作成します。

   - **前の期間**:参照目的で、前の期間 (先週など) における要求の総数と成功率 (%) の変化を示す一時的なグラフを作成します。

- **アラート**:Log Analytics を使用して、主要指標が急激に変化したときにトリガーされる [アラート](../../azure-monitor/alerts/alerts-log.md)を定義します。 このような変化は SLO に悪影響を与える可能性があります。 アラートには、電子メール、SMS、Webhook などのさまざまな形式の通知方法が使用されます。 まず、アラートがトリガーされるしきい値として機能する条件を定義します。 次に例を示します。
  - 要求の総数の急激な減少に対するアラート: 要求の総数が急激に減少したときにアラートをトリガーします。 たとえば、要求の総数が直前の期間と比べて 25% 減少した場合にアラートを生成します。  
  - 成功率 (%) の著しい低下に対するアラート: 選択したポリシーの成功率が著しく低下したときにアラートをトリガーします。
  - アラートを受信したら、[Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md)、[Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)、および Azure AD B2C 用 [VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)を使用して問題のトラブルシューティングを行います。 問題を解決し、更新したアプリケーションやポリシーをデプロイした後は、通常の範囲に戻るまで主要指標の監視が継続されます。

- **サービス アラート**: [Azure AD B2C のサービス レベル アラート](../../service-health/service-health-overview.md)を使用して、サービスの問題、計画メンテナンス、正常性に関するアドバイザリ、セキュリティ アドバイザリの通知を受けることができます。

- **レポート**: [Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) を使用して、ユーザー分析情報、技術的な課題、および成長機会の把握に役立つレポートを作成します。
  - **正常性ダッシュボード**: [Azure ダッシュボード機能を使用してカスタム ダッシュボード](../../azure-monitor/app/tutorial-app-dashboards.md)を作成します。これにより、Log Analytics クエリを使用してグラフを追加することができます。 たとえば、成功および失敗したサインインのパターン、失敗した理由、要求を行うために使用されたデバイスに関するテレメトリを確認します。
  - **Azure AD B2C 体験の破棄**:[ブック](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys)を使用して、破棄された Azure AD B2C 体験 (ユーザーがサインインまたはサインアップ体験を開始したまま完了していないケース) の一覧を追跡できます。 ここから、ポリシー ID や、ユーザーが体験を破棄するまでに実行したステップに関する詳細を確認できます。
  - **Azure AD B2C 監視ブック**:[監視ブック](https://github.com/azure-ad-b2c/siem) (Azure AD B2C ダッシュボード、多要素認証 (MFA) 操作、条件付きアクセス レポート、および correlationId によるログの検索を含む) を使用して、Azure AD B2C 環境の正常性に関する詳細な分析情報を得ることができます。
  
## <a name="next-steps"></a>次のステップ

- [Azure AD B2C 開発者向けの回復性に関するリソース](resilience-b2c.md)
  - [回復性があるエンドユーザー エクスペリエンス](resilient-end-user-experience.md)
  - [回復性がある外部プロセスとのインターフェイス](resilient-external-processes.md)
  - [開発者のベスト プラクティスで回復性を実現する](resilience-b2c-developer-best-practices.md)
- [認証インフラストラクチャの回復性を強化する](resilience-in-infrastructure.md)
- [アプリケーションで認証と認可の回復性を向上させる](resilience-app-development-overview.md)