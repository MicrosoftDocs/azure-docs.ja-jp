---
title: OMS ポータルの Azure への移行 | Microsoft Docs
description: OMS ポータルは、すべての機能が Azure portal に移行されるのに伴い、非推奨となります。 この記事では、今回の切り替えの詳細について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bwren
ms.openlocfilehash: 80c4fa5fad574f1d6efe476df90a8396fa5cb4f3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205632"
---
# <a name="oms-portal-moving-to-azure"></a>OMS ポータルの Azure への移行

> [!NOTE]
> この記事は、別段の定めがある場合を除き、Azure のパブリック クラウドとガバメント クラウドの両方に適用されます。

**Azure パブリック クラウド向けの OMS ポータルは正式に廃止されました。Azure US Government クラウド向けの OMS ポータルは、2019 年 5 月 15 日に正式に廃止されました。** Azure portal への移行は喜ばしいイベントであり、切り替えは簡単に済むと見込んでいます。 しかし、変化には困難が付きものであり、中断を伴う可能性も考えられます。 この記事の残りの部分では、この切り替えの主なシナリオとロードマップについて説明します。

Azure portal は、すべての Azure サービスのハブであり、リソースをピン留めできるダッシュボード、リソースを見つけるためのインテリジェント検索、リソース管理のためのタグ付けなど、豊富な機能を備えた管理エクスペリエンスを提供しています。 監視と管理のワークフローを統合、合理化するために、Microsoft では OMS ポータルの機能を Azure portal に追加する作業を進めていました。 今回、すべての OMS ポータルの機能が Azure portal に組み込まれました。 実際に、Traffic Manager などの一部の新機能は、Azure Portal でのみ使用可能となっています。 OMS ポータルで実行していた作業をすべて Azure portal で実行できるだけでなく、新機能も使用できます。 まだ Azure portal を使用していない場合は、すぐに使い始めることをお勧めします。

## <a name="what-is-changing"></a>何が変わるのですか? 
OMS ポータルが非推奨となるのに伴い、次の変更が発表されています。 これらの各変更については、以降のセクションで詳しく説明します。

- 新しい[ワークスペースの作成は、Azure portal でのみ](#new-workspaces)行うことができます。
- [Alert Management ソリューションは、新しいアラート管理のエクスペリエンスに置き換えられました](#changes-to-alerts)。
- [ユーザー アクセス管理](#user-access-and-role-migration)が、Azure のロールベースのアクセス制御を使用して、Azure portal 内で行われるようになりました。
- [Application Insights Connector は不要になりました](#application-insights-connector-and-solution)。これは、クロスワークスペース クエリを通じて同じ機能を実現できるためです。
- [OMS モバイル アプリ](#oms-mobile-app)は廃止予定です。 
- [NSG ソリューションは、Traffic Analytics ソリューションを通じて入手できる拡張機能に置き換えられます](#azure-network-security-group-analytics)。
- System Center Operations Manager から Log Analytics への新しい接続では、[更新された管理パック](#system-center-operations-manager)が必要です。
- [Update Management](../../automation/automation-update-management.md) への移行については、「[OMS の更新プログラムの展開を Azure に移行する](../../automation/migrate-oms-update-deployments.md)」を参照してください。


## <a name="what-should-i-do-now"></a>現時点で何をすべきか
移行を実行しなくてもほとんどの機能は引き続き使用できますが、次のタスクは実行する必要があります。

- [ユーザーのアクセス許可を Azure portal に移行](#user-access-and-role-migration)する必要があります。
- Update Management ソリューションの移行について詳しくは、「[OMS の更新プログラムの展開を Azure に移行する](../../automation/migrate-oms-update-deployments.md)」をご覧ください。

Azure portal への切り替え方法については、「[OMS ポータルから Azure portal への切り替えに関する Log Analytics ユーザーの一般的な質問](oms-portal-faq.md)」を参照してください。 

## <a name="user-access-and-role-migration"></a>ユーザー アクセスとロールの移行
Azure portal のアクセス管理は、OMS ポータルのアクセス管理よりも機能が豊富で強力です。 Log Analytics でのアクセス管理の詳細については、「[ワークスペースを管理する](manage-access.md#manage-accounts-and-users)」を参照してください。

> [!NOTE]
> この記事の旧版では、アクセス許可は自動的に OMS ポータルから Azure portal に変換されると記載されていました。 この自動変換は現在では予定されていないため、手動で変換を実行する必要があります。

自分が Azure portal で既に適切なアクセス権を持っている場合は、何も変更する必要はありません。 適切なアクセス権がない場合は、管理者にアクセス許可を割り当ててもらう必要があります。

- OMS ポータルに ReadOnly User アクセス許可はあるが、Azure portal にアクセス許可がない。 
- OMS ポータルに Contributor アクセス許可はあるが、Azure portal には Reader アクセス許可しかない。
 
いずれの場合も、次の表の適切なロールを管理者が手動で割り当てる必要があります。 リソース グループまたはサブスクリプション レベルでこのロールを割り当てることをお勧めします。  これら両方のケースについて、より規範的なガイダンスを間もなく提供する予定です。

| OMS ポータルのアクセス許可 | Azure のロール |
|:---|:---|
| ReadOnly | Log Analytics 閲覧者 |
| Contributor | Log Analytics 共同作成者 |
| 管理者 | Owner | 
 

## <a name="new-workspaces"></a>新しいワークスペース
OMS ポータルを使用して新しいワークスペースを作成することはできなくなりました。 Azure Portal で新しいワークスペースを作成するには、「[Azure Portal で Log Analytics ワークスペースを作成する](../learn/quick-create-workspace.md)」のガイダンスに従ってください。

## <a name="changes-to-alerts"></a>アラートの変更内容

### <a name="alert-extension"></a>アラートの拡張機能  

アラートは [Azure portal に拡張されました](alerts-extend.md)。既存のアラートは引き続き OMS ポータルに表示されますが、Azure portal でのみこれらを管理できます。 Log Analytics Alert REST API または Log Analytics のアラート リソース テンプレートを使用してプログラムでアラートにアクセスする場合は、API 呼び出し、Azure Resource Manager テンプレート、PowerShell コマンドではアクションではなくアクション グループを使用する必要があります。

### <a name="alert-management-solution"></a>アラート管理ソリューション
以前のお知らせからの変更点として、[アラート管理ソリューション](alert-management-solution.md)は Azure portal で引き続き使用でき、完全にサポートされます。 Azure Marketplace からソリューションを引き続きインストールできます。

アラート管理ソリューションは引き続き使用できますが、Azure でのすべてのアラートを視覚化して管理するには [Azure Monitor の統合アラート インターフェイス](alerts-overview.md)を使用することをお勧めします。 この新しいエクスペリエンスでは、Log Analytics からのログ アラートをはじめ、Azure 内の複数のソースからのアラートがネイティブに集約されます。 Azure Monitor の統合アラート インターフェイスを使用する場合、アラート管理ソリューションは、System Center Operation Manager から Azure へのアラートの統合を有効にする場合のみ必要になります。 Azure Monitor の統合アラート インターフェイスでは、アラートの分布の表示、関連するアラートをスマート グループで自動的にグループ化する機能の活用、複数のサブスクリプションにわたるアラートの表示が可能なほか、さまざまなフィルターを適用することもできます。 アラート管理に関する今後の高度な機能は、主にこの新しいエクスペリエンスから利用できます。 

アラート管理ソリューションで収集されたデータ (アラートの種類を含むレコード) は、このソリューションがワークスペースにインストールされているかぎり、引き続き Log Analytics に保存されます。 

## <a name="oms-mobile-app"></a>OMS モバイル アプリ
OMS モバイル アプリは、OMS ポータルと共に非推奨となります。 OMS モバイル アプリの代わりに、モバイル デバイスのブラウザーから直接 Azure portal にアクセスすることで、IT インフラストラクチャ、ダッシュ ボード、保存されたクエリに関する情報にアクセスできます。 アラートを取得するには、[Azure のアクション グループ](action-groups.md)を構成して、SMS または音声通話の形式で通知が届くようにする必要があります。

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector と Application Insights ソリューション
[Application Insights Connector](app-insights-connector.md) では、Application Insights のデータを Log Analytics のワークスペースに含めることができます。 このデータの重複は、インフラストラクチャとアプリケーションのデータ全体を視覚化するために必要でした。 Application Insights のデータ保有期間サポートが 2019 年 3 月まで延長されたこと、[リソース間のクエリ](../log-query/cross-workspace-query.md)を実行する機能が利用できること、[複数の Azure Monitor Application Insights リソースを表示できること](../log-query/unify-app-resource-data.md)から、Application Insights リソースからのデータを複製して Log Analytics に送信する必要はありません。 さらに、Connector はアプリケーション プロパティのサブセットを Log Analytics に送信する一方、リソース間のクエリによって高い柔軟性が提供されます。  

そのため、Application Insights Connector は 2019 年 3 月 30 日の OMS ポータルの廃止と一緒に非推奨になり、Azure Marketplace から削除されました。 既存の接続は 2019 年 6 月 30日まで引き続き機能します。 OMS ポータルの廃止により、ポータルから既存の接続を構成および削除する方法がなくなります。 これは 2019 年 1 月に利用できる予定の REST API を使用してサポートされます。通知は [Azure の更新情報](https://azure.microsoft.com/updates/)に掲載されます。 

## <a name="azure-network-security-group-analytics"></a>Azure ネットワーク セキュリティ グループ分析
[Azure Network Security Group Analytics ソリューション](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor)は、最近発表された [Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) に置き換えられます。Traffic Analytics では、クラウド ネットワークにおけるユーザーとアプリケーションのアクティビティを視覚化できます。 Traffic Analytics は、組織のネットワーク アクティビティの監査、アプリケーションとデータのセキュリティ保護、ワークロードのパフォーマンスの最適化、コンプライアンスの維持に役立ちます。 

このソリューションでは、NSG フロー ログを分析し、次の分析情報を提供します。

- Azure とインターネット、パブリック クラウド リージョン、VNET、およびサブネットの間のネットワークのトラフィック フロー。
- ネットワーク上のアプリケーションとプロトコル。スニファーや専用のフロー収集アプライアンスは不要です。
- トップ トーカー、チャット数が多いアプリケーション、クラウド内の VM の会話、トラフィックのホット スポット。
- VNET 間のトラフィックの送信元と送信先、重要なビジネス サービスとアプリケーションの間の内部関係。
- 悪意のあるトラフィック、インターネットに対して開かれているポート、インターネットにアクセスしようとしているアプリケーションや VM などに対するセキュリティ。
- 容量使用率。過剰なプロビジョニングまたは過小使用の問題を回避するのに役立ちます。

診断の設定を引き続き使用して、NSG ログを Log Analytics に送信できるので、保存された既存の検索、アラート、ダッシュボードを引き続き使用できます。 このソリューションを既にインストール済みのお客様は、別途通知があるまで、引き続きお使いいただけます。 9 月 5 日以降、Network Security Group Analytics ソリューションはマーケットプレースから削除され、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)としてコミュニティを通じて入手できるようになります。

## <a name="system-center-operations-manager"></a>System Center Operations Manager
[Operations Manager 管理グループを Log Analytics 接続している](om-agents.md)場合は、変更なしで引き続き動作します。 ただし、新しい接続の場合は、[Operations Management Suite を構成するための Microsoft System Center Operations Manager Management Pack](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)に記載されているガイダンスに従う必要があります。

## <a name="next-steps"></a>次の手順
- OMS ポータルから Azure portal への移行に関するガイダンスについては、「[Common questions for transition from OMS portal to Azure portal for Log Analytics users (OMS ポータルから Azure portal への切り替えに関する Log Analytics ユーザーの一般的な質問)](oms-portal-faq.md)」を参照してください。
