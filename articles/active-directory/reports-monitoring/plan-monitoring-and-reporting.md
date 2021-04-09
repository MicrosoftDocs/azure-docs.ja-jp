---
title: レポートと監視のデプロイを計画する - Azure AD
description: レポートと監視の実装を計画および実行する方法について説明します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 683367251c220abe36660d61463bce9e5a0c52f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577760"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory のレポートと監視のデプロイを計画する

Azure Active Directory (Azure AD) のレポートおよび監視ソリューションは、法令、セキュリティ、運用の各要件と、既存の環境およびプロセスによって異なります。 この記事では、さまざまな設計オプションを紹介し、正しいデプロイ戦略のガイドを示します。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD のレポートと監視のベネフィット

Azure AD のレポートは、サインイン イベント、監査イベント、ディレクトリへの変更など、環境内の Azure AD アクティビティの包括的なビューとログを提供します。

レポートから得たデータによって次のことが可能となります。

* アプリとサービスがどのように利用されているかを明らかにする。

* 自分が管理している環境の正常性に影響する潜在的リスクを検出する。

* ユーザーの作業を妨げている問題をトラブルシューティングする。

* Azure AD ディレクトリに対する変更の監査イベントを確認して、分析情報を得る。

> [!IMPORTANT]
> Azure AD の監視を使用すると、Azure AD レポートによって生成されたログを別のターゲット システムにルーティングすることができます。 その後、それを長期的な使用のために保持したり、サードパーティのセキュリティ情報およびイベント管理 (SIEM) ツールと統合して環境の分析情報を取得したりすることができます。

Azure AD の監視では、次の場所にログをルーティングできます。

* アーカイブ用の Azure ストレージ アカウント。
* (以前は Azure Log Analytics ワークスペースと呼ばれていた) Azure Monitor ログ。ここでは、データの分析や、特定のイベントのダッシュボードとアラートの作成を行うことができます。
* Splunk、Sumologic、QRadar などの既存の SIEM ツールと統合できる Azure イベント ハブ。

> [!NOTE]
最近になって、Log Analytics の代わりに Azure Monitor ログという用語が使われるようになりました。 ログ データは引き続き Log Analytics ワークスペースに格納され、同じ Log Analytics サービスによって収集されて分析されます。 [Azure Monitor のログ](../../azure-monitor/data-platform.md)の役割をより適切に反映させるために、用語を更新しています。 詳しくは、[Azure Monitor の用語の変更](../../azure-monitor/terminology.md)に関するページをご覧ください。

[レポート保持ポリシーの詳細を確認します](./reference-reports-data-retention.md)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD のレポートと監視のライセンスと前提条件

Azure AD サインイン ログにアクセスするには、Azure AD プレミアム ライセンスが必要になります。

機能とライセンスの詳細については、[Azure Active Directory 料金ガイド](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

Azure AD の監視とレポートをデプロイするには、Azure AD テナントのグローバル管理者またはセキュリティ管理者であるユーザーが必要になります。

ログ データの最終転送先によっては、次のいずれかが必要になります。

* ListKeys アクセス許可が付与された Azure ストレージ アカウント。 BLOB ストレージ アカウントではなく、一般的なストレージ アカウントを使用することをお勧めします。 ストレージの料金情報については、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=storage)を参照してください。

* サードパーティ製の SIEM ソリューションと統合するための Azure Event Hubs の名前空間。

* Azure Monitor ログにログを送信する Azure Log Analytics ワークスペース。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure のレポートと監視のデプロイ プロジェクトを計画する

このプロジェクトでは、レポートを使用および監視する対象ユーザーを定義し、Azure AD の監視アーキテクチャを定義します。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md)します。 また、利害関係者およびそのプロジェクトでの入力と説明責任を文書化することで、プロジェクトでの利害関係者の役割をよく理解させます。

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。

### <a name="document-your-current-infrastructure-and-policies"></a>現在のインフラストラクチャとポリシーを文書化する

現在のインフラストラクチャとポリシーによって、レポートと監視の設計が促進されます。 次のことがわかっていることを確認します。

* 使用している SIEM ツールがある場合、それは何か。

* Azure インフラストラクチャ (既存のストレージ アカウントや、使用している監視など)。

* 組織のログ保持ポリシー (必要とされ、該当するコンプライアンス フレームワークなど)。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Azure AD のレポートと監視のデプロイを計画する

レポートと監視は、ビジネスの要件を満たし、使用パターンの分析情報を入手し、組織のセキュリティ体制を強化するために使用されます。

### <a name="business-use-cases"></a>ビジネス ユース ケース

* ビジネスのニーズを満たすためにソリューションに必要
* ビジネスのニーズを満たすために、存在することが望ましい
* 適用なし

|領域 |説明 |
|-|-|
|保持| **30 日を超えるログ保有期間**。 ‎法的または業務上の要件により、Azure AD の監査ログとサインイン ログを 30 日よりも長く保存する必要があります。 |
|Analytics| **ログが検索可能である必要がある**。 ‎保存されたログを分析ツールを使用して検索できる必要があります。 |
| Operational Insights| **さまざまなチームのための分析情報**。 さまざまなユーザーにアクセス権を付与し、アプリケーションの使用状況、サインイン エラー、セルフサービスの使用状況、傾向など、運用に関する分析情報を入手できるようにする必要があります。 |
| セキュリティ分析情報| **さまざまなチームのための分析情報**。 さまざまなユーザーにアクセス権を付与し、アプリケーションの使用状況、サインイン エラー、セルフサービスの使用状況、傾向など、運用に関する分析情報を入手できるようにする必要があります。 |
| SIEM システムでの統合      | **SIEM の統合**。 ‎Azure AD のサインイン ログと監査ログを既存の SIEM システムに統合およびストリーム配信する必要があります。 |

### <a name="choose-a-monitoring-solution-architecture"></a>監視ソリューションのアーキテクチャを選択する

Azure AD の監視では、Azure AD のアクティビティ ログを、ビジネスのニーズに最も合ったシステムにルーティングできます。 その後は、それらのログを保持して長期的なレポートや分析に利用し、環境の分析情報を入手したり、SIEM ツールと統合したりできます。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>意思決定フロー チャート![後続のセクションの説明内容を示す画像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>ログをストレージ アカウントにアーカイブする

ログを Azure ストレージ アカウントにルーティングすると、[保持ポリシー](./reference-reports-data-retention.md)で規定されている既定の保持期間より長くログを保持できます。 ログをアーカイブする必要があるが SIEM システムと統合する必要はなく、継続的なクエリや分析も不要な場合は、この方法を使用します。 この場合もオンデマンド検索は実行できます。

データをストレージ アカウントにルーティングする方法については、[こちら](./quickstart-azure-monitor-route-logs-to-storage-account.md)を参照してください。

#### <a name="send-logs-to-azure-monitor-logs"></a>Azure Monitor ログへのログの送信

[Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md)は、さまざまなソースからの監視データを統合します。 アプリケーションの操作とリソースの使用に関する分析情報が得られるクエリ言語と分析エンジンも提供されます。 Azure AD のアクティビティ ログを Azure Monitor ログに送信することで、収集したデータに対する迅速な取得、監視、およびアラートを行うことができます。 データの直接の送信先となる既存の SIEM ソリューションは存在しないが、クエリや分析が必要な場合は、この方法を使用します。 データが Azure Monitor ログに記録されたら、そのデータをイベント ハブに送信し、必要に応じてそこから SIEM に送信することができます。

[Azure Monitor ログにデータを送信する](./howto-integrate-activity-logs-with-log-analytics.md)方法を学習してください。

Azure AD のアクティビティ ログ用の既製のビューをインストールして、サインインと監査イベントを含む一般的なシナリオを監視することもできます。

[Azure AD のアクティビティ ログ用の Log Analytics ビューをインストールして使用する](./howto-install-use-log-analytics-views.md)方法を学習してください。

#### <a name="stream-logs-to-your-azure-event-hub"></a>Azure イベント ハブにログをストリーム配信する

Azure イベント ハブにログをルーティングすると、サードパーティ製の SIEM ツールとの統合が可能になります。 この統合によって、Azure AD のアクティビティ ログ データと、SIEM によって管理されている他のデータを組み合わせ、より豊富な環境分析情報を提供することができます。 

ログをイベント ハブにストリーム配信する方法については、[こちら](./tutorial-azure-monitor-stream-logs-to-event-hub.md)を参照してください。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Azure AD のレポートと監視の運用とセキュリティを計画する

利害関係者は Azure AD ログにアクセスして運用の分析情報を入手する必要があります。 該当する可能性が高いユーザーは、セキュリティ チームのメンバー、内部または外部の監査人、ID とアクセスの管理運用チームなどです。

Azure AD のロールを使用すると、自分のロールに応じて、Azure AD のレポートを構成および表示する権限を委任することができます。 Azure AD のレポートを閲覧するアクセス許可が必要な組織内のユーザーと、そのようなユーザーにとって適切なロールを特定します。 

次のロールが Azure AD のレポートを閲覧できます。

* 全体管理者

* セキュリティ管理者

* セキュリティ閲覧者

* レポート閲覧者

[Azure AD 管理者ロール](../roles/permissions-reference.md)の詳細を確認します。

*アカウント侵害* のリスクを軽減するために、最小限の特権の概念を常に適用してください。 組織をさらにセキュリティで保護するために、[Privileged Identity Management](../privileged-identity-management/pim-configure.md) の実装を検討します。

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD のレポートと監視をデプロイする

このセクションでは、以上の設計ガイダンスに従って下された決定に応じた、さまざまなデプロイ オプションに関するドキュメントを紹介します。

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD ログを使用およびアーカイブする

[Azure portal でアクティビティ レポートを見つける](./howto-find-activity-reports.md)

[Azure AD のログを Azure Storage アカウントにアーカイブする](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>監視と分析を実装する

[ログを Azure Monitor に送信する](./howto-integrate-activity-logs-with-log-analytics.md)

[Azure Active Directory ログ分析用のビューのインストールと使用](./howto-install-use-log-analytics-views.md)

[Azure Monitor ログ を使用して Azure AD アクティビティ ログを分析する](./howto-analyze-activity-logs-log-analytics.md)

* [Azure Monitor で監査ログのスキーマを解釈する](./reference-azure-monitor-audit-log-schema.md)

* [Azure Monitor でサインイン ログのスキーマを解釈する](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Azure AD のログを Azure イベント ハブにストリーム配信する](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Azure Monitor を使用して Azure AD のログを Splunk と統合する](./howto-integrate-activity-logs-with-splunk.md)

* [Azure Monitor を使用して Azure AD のログを SumoLogic と統合する](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>次のステップ

[Privileged Identity Management](../privileged-identity-management/pim-configure.md) の実装を検討する 

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) の実装を検討する