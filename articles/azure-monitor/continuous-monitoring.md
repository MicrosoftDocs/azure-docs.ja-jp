---
title: Azure Monitor を使用した継続的監視 | Microsoft Docs
description: Azure Monitor を使用してワークフロー全体にわたって継続的監視を有効にする手順について詳しく説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 2fe9d7e4da8950cee2dcb2fbcfb26355ace7285d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044019"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Azure Monitor を使用した継続的監視

継続的監視とは、お客様の DevOps と IT 運用のライフサイクルを構成する各フェーズを横断的に監視するために必要なプロセスとテクノロジをいいます。 これは、お客様のアプリケーションとインフラストラクチャの正常性、パフォーマンス、信頼性を開発から運用まで継続的に確保するうえで役に立ちます。 継続的監視は、お客様がソフトウェアの開発と提供をより高速かつ確実に行ってお客様のユーザーに継続的価値を提供するのに役立つ、継続的インテグレーションと継続的デプロイ (CI/CD) の概念に基づいています。

[Azure Monitor](overview.md) は、クラウドおよびオンプレミスのアプリケーションとインフラストラクチャの全体についてフルスタックの観測性を備えた Azure 内の統合監視ソリューションです。 開発とテストの間は [Visual Studio や Visual Studio Code](https://visualstudio.microsoft.com/) とシームレスに連携し、デプロイおよび運用の間は、リリース管理と作業項目管理のために [Azure DevOps](/azure/devops/user-guide/index) と統合されます。 さらに、お客様の既存の IT プロセスにおける問題とインシデントを追跡するのに役立つ任意の ITSM ツールや SIEM ツールとも統合されます。

この記事では、Azure Monitor を使用してお客様のワークフロー全体にわたって継続的監視を有効にする手順について、詳しく説明します。 ここには、さまざまな機能の実装の詳細が記載された他のドキュメントへのリンクが含まれています。


## <a name="enable-monitoring-for-all-your-applications"></a>すべてのアプリケーションについて監視を有効にする
お客様の環境全体に対する観測性を確保するためには、お客様のすべての Web アプリケーションとサービスに対して監視を有効にする必要があります。 そうすることで、すべてのコンポーネントについてエンドツーエンドのトランザクションと接続を容易に視覚化できます。

- [Azure DevOps Projects](../devops-project/overview.md) により、お客様の既存のコードと Git リポジトリが簡単に使用できるようになり、サンプル アプリケーションのいずれかを選択して Azure への継続的インテグレーション (CI) と継続的デリバリー (CD) のパイプラインを作成することができます。
- [お客様の DevOps リリース パイプラインに継続的監視](./app/continuous-monitoring.md)を取り入れると、監視データに基づいてお客様のデプロイをゲート管理したりロールバックしたりできます。
- [Status Monitor](./app/monitor-performance-live-website-now.md) では、Azure Application Insights を使用して Windows 上のライブ .NET アプリをインストルメント化できます。お客様のコードに変更を加えたり、再デプロイしたりする必要はありません。
- お客様のアプリケーションのコードにアクセスできる場合は、[.NET](./app/asp-net.md) 向け、[.NET Core](./app/asp-net-core.md) 向け、[Java](./app/java-get-started.md) 向け、[Node.js](./app/nodejs-quick-start.md) 向け、または[その他のプログラミング言語](./app/platforms.md)向けの Azure Monitor Application Insights SDK をインストールして、[Application Insights](./app/app-insights-overview.md) を使用した完全な監視を有効にします。 これにより、お客様のアプリケーションとビジネスに適したカスタム イベント、メトリック、ページ ビューを指定できます。



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>インフラストラクチャ全体について監視を有効にする
アプリケーションの信頼性が、その基になるインフラストラクチャの信頼性を超えることはありません。 お客様のインフラストラクチャ全体に対して監視を有効にすることは、完全な観測性を実現するのに役立つほか、何らかの障害が発生した際に考え得る根本的原因を検出しやすくするのに役立ちます。 Azure Monitor は、リソース (VM、コンテナー、ストレージ、ネットワークなど) を含むお客様のハイブリッド インフラストラクチャ全体の正常性とパフォーマンスの追跡を支援します。

- お客様のほとんどの Azure リソースから[プラットフォーム メトリック、アクティビティ ログ、診断ログ](agents/data-sources.md)が構成なしで自動的に得られます。
- [VM insights](vm/vminsights-overview.md) を使用して、VM に対してより高度な監視を有効にします。
-  [コンテナー用 Azure Monitor](containers/container-insights-overview.md) を使用して、AKS クラスターに対してより高度な監視を有効にします。
- お客様の環境内のさまざまなアプリケーションおよびサービス用に[監視ソリューション](./monitor-reference.md)を追加します。


[Infrastructure as Code](/azure/devops/learn/what-is-infrastructure-as-code) は、DevOps チームがソース コードに使用するのと同じバージョン管理を使用し、記述的モデルでインフラストラクチャを管理するものです。 これにより、お客様の環境の信頼性とスケーラビリティが高まり、お客様のアプリケーションの管理に使用されるものと同様のプロセスを利用できるようになります。

-  多数のリソースに対して監視を有効にし、アラートを構成するには、[Resource Manager テンプレート](./logs/resource-manager-workspace.md)を使用します。
- お客様のリソースに対してさまざまなルールを強制するには、[Azure Policy](../governance/policy/overview.md) を使用します。 これにより、それらのリソースがお客様の会社の標準やサービス レベル アグリーメントに準拠している状態が確保されます。 


##  <a name="combine-resources-in-azure-resource-groups"></a>Azure リソース グループでリソースをまとめる
今日、Azure 上の標準的なアプリケーションには、さまざまなリソース (VM やアプリ サービスのほか、Cloud Services、AKS クラスター、Service Fabric でホストされるマイクロサービスなど) が含まれています。 これらのアプリケーションでは、Event Hubs、Storage、SQL、Service Bus などの依存関係がよく利用されます。

- Azure リソース グループでリソースをまとめることで、お客様のさまざまなアプリケーションを構成するすべてのリソースについて完全な可視性を得ることができます。 [リソース グループ用 Azure Monitor](./insights/resource-group-insights.md) を使用すると、フルスタックのアプリケーション全体の正常性とパフォーマンスを簡単な方法で追跡できます。また、各コンポーネントをドリルダウンして調査やデバッグを行うことができます。

## <a name="ensure-quality-through-continuous-deployment"></a>継続的デプロイによって品質を確保する
継続的インテグレーションと継続的デプロイでは、自動化されたテストの結果に基づいて、お客様のアプリケーションに対するコードの変更を自動的に統合してデプロイできます。 これにより、デプロイ プロセスが効率化されるほか、運用への移行前にあらゆる変更の品質が確保されます。


- [Azure Pipelines](/azure/devops/pipelines) を使用して継続的デプロイを実装し、お客様の CI/CD テストに基づいて、コードのコミットから運用までお客様のプロセス全体を自動化します。
- [品質ゲート](/azure/devops/pipelines/release/approvals/gates)を使用して、お客様のデプロイの前後に監視を統合します。 これにより、お客様のアプリケーションが開発から運用に移行する際に正常性とパフォーマンスの主要指標 (KPI) を確実に満たし、インフラストラクチャ環境の違いやスケールの違いがお客様の KPI に悪い影響を及ぼさないようにできます。
- お客様のさまざまなデプロイ環境 (開発、テスト、カナリア、運用) の間で[個別の監視インスタンスを維持](./app/separate-resources.md)します。そうすることで、関連するアプリケーションおよびインフラストラクチャ全体で、収集されたデータの適合性が得られます。 環境をまたいでデータを相互に関連付ける必要がある場合は、[メトリックス エクスプローラーのマルチリソース グラフ](./essentials/metrics-charts.md)を使用するか、[Azure Monitor のクロスリソース クエリ](logs/cross-workspace-query.md)を使用することができます。


## <a name="create-actionable-alerts-with-actions"></a>アクションにつながる実用的なアラートを作成する
監視の重要な側面の 1 つは、現在の問題と予測される問題を管理者に対してプロアクティブに通知することです。 

- 予測可能な障害状態を特定するために、ログとメトリックに基づいて [Azure Monitor のアラートを](./alerts/alerts-overview.md)作成します。 すべてのアラートがアクションにつながるものになることを目標にして、それらが実際に重大な状態を表すようにしてください。また、誤検知が少なくなるようにしてください。 お客様独自の静的なしきい値を定義するのではなく、[動的しきい値](alerts/alerts-dynamic-thresholds.md)を使用して、メトリック データに対するベースラインを自動的に計算します。 
- お客様の管理者への通知に最も効果的な手段が使用されるよう、アラートに関するアクションを定義します。 [通知のアクション](alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)としては、SMS、電子メール、プッシュ通知、音声通話が利用できます。
- より高度なアクションを使用し、[Webhook](alerts/activity-log-alerts-webhook.md) を通じて[お客様の ITSM ツール](alerts/itsmc-overview.md)や他のアラート管理システムに接続します。
- さらに、Webhook を使用してアラートから起動できる [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) や [Azure Automation Runbook](../automation/automation-webhooks.md) を使って、アラートで特定された状態を修復します。 
- [自動スケーリング](./autoscale/tutorial-autoscale-performance-schedule.md)を使用し、収集されたメトリックに基づいてお客様のコンピューティング リソースを動的に増減させます。

## <a name="prepare-dashboards-and-workbooks"></a>ダッシュボードとブックを準備する
お客様の開発担当者と運用担当者が同じテレメトリとツールにアクセスできるようにすると、彼らがお客様の環境全体のパターンを確認して、お客様の平均検出時間 (MTTD) と平均復元時間 (MTTR) を最小限に抑えることができるようになります。

- お客様の組織内のさまざまな役割のために、一般的なメトリックとログに基づく[カスタム ダッシュボード](./app/tutorial-app-dashboards.md)を準備します。 すべての Azure リソースからのデータをダッシュボードでまとめることができます。
- 開発担当者と運用担当者の間で知識を共有できるように[ブック](./visualize/workbooks-overview.md)を準備します。 これらは、メトリック グラフやログ クエリが含まれた動的レポートとして準備される場合があります。また、基本的な問題に対処するカスタマー サポートや運用担当者を支援するために開発者によって作成されたトラブルシューティング ガイドとして準備される場合もあります。

## <a name="continuously-optimize"></a>継続的に最適化する
 監視は、Build-Measure-Learn (構築-測定-学習) という一般的な方針に含まれている基本的要素の 1 つです。これによると、お客様の KPI とユーザー行動のメトリックを継続的に追跡し、計画の繰り返しを通じてそれらを最適化することを目指すことが推奨されます。 Azure Monitor は、お客様のビジネスに関連するメトリックとログを収集したり、必要に応じて新しいデータ ポイントを次回のデプロイに追加したりするのに役に立ちます。

- Application Insights のツールを使用して、[エンドユーザーの行動とエンゲージメントを追跡](./app/tutorial-users.md)します。
- [影響分析](./app/usage-impact.md)を使用して、重要な KPI を高めるためにどの分野に注力すべきかの優先度付けを行います。


## <a name="next-steps"></a>次のステップ

- [Azure Monitor](overview.md) のさまざまなコンポーネントについて確認する。
- お客様のリリース パイプラインに[継続的監視を追加](./app/continuous-monitoring.md)する。