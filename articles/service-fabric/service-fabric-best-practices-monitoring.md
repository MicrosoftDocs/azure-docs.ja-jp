---
title: Azure Service Fabric の監視に関するベスト プラクティス
description: Azure Service Fabric を使用したクラスターとアプリケーションの監視のためのベスト プラクティスと設計上の考慮事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a7b1c1b3fc3196557b862c488ee01af8b8e1f04f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529252"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Azure Service Fabric での監視と診断のベスト プラクティス

[監視と診断](./service-fabric-diagnostics-overview.md)は、あらゆるクラウド環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 たとえば、アプリケーションの使われ方、Service Fabric プラットフォームによって実行されたアクション、パフォーマンス カウンターでのリソースの使用状況、クラスターの全体的な正常性を追跡できます。 この情報を使って、問題の診断と修正を行い、再発を防ぐことができます。

## <a name="application-monitoring"></a>アプリケーションの監視

アプリケーションの監視は、アプリケーションの機能やコンポーネントがどのように使用されているかを追跡します。 アプリケーションを監視して、ユーザーに影響が及ぶ問題を把握します。 ビジネス ロジックはアプリケーションごとに固有なので、アプリケーションの監視は、アプリケーションとそのサービスを開発するユーザーに責任があります。 Azure のアプリケーション監視ツールである [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md) を使用して、アプリケーションの監視を設定することをお勧めします。

## <a name="cluster-monitoring"></a>クラスターの監視

Service Fabric の目標の 1 つは、ハードウェア障害に対する回復性をアプリケーションに持たせることです。 この目標を実現するには、プラットフォームのシステム サービスの機能を利用します。この機能は、インフラストラクチャの問題を特定し、ワークロードをクラスター内の別のノードにすばやくフェールオーバーします。 ただし、システム サービス自体に問題がある場合はどうなるでしょうか。 または、ワークロードのデプロイまたは移動でサービス配置のルールに違反したらどうなるでしょうか。 Service Fabric には、このような問題や他の問題に対応する診断機能があるため、Service Fabric プラットフォームがアプリケーション、サービス、コンテナー、およびノードとどのようにやり取りしているかについて確実に把握することができます。

Windows クラスターの場合は、[診断エージェント](./service-fabric-diagnostics-event-aggregation-wad.md)と [Azure Monitor ログ](./service-fabric-diagnostics-oms-setup.md)を使用してクラスターの監視を設定することをお勧めします。

Linux クラスターの場合、Azure Monitor ログは Azure プラットフォームとインフラストラクチャの監視にも推奨されるツールです。 Linux プラットフォームの診断には、「[Syslog 内の Service Fabric Linux クラスター イベント](./service-fabric-diagnostics-oms-syslog.md)」で説明されているように異なる構成が必要です。

## <a name="infrastructure-monitoring"></a>インフラストラクチャ監視

クラスター レベルのイベントを監視するには、[Azure Monitor ログ](./service-fabric-diagnostics-oms-agent.md)が推奨されます。 前のリンクで説明されているように、ワークスペースを使用して Log Analytics エージェントを構成すると、パフォーマンス メトリック (CPU 使用率など)、.NET パフォーマンス カウンター (プロセス レベルの CPU 使用率など)、Service Fabric パフォーマンス カウンター (信頼できるサービスからの例外数など)、コンテナー メトリック (CPU 使用率など) を収集できるようになります。  Azure Monitor ログで利用するには、コンテナー ログを stdout または stderr に書き込む必要があります。

## <a name="watchdogs"></a>ウォッチドッグ

一般的に、ウォッチドッグとは、複数のサービスにわたって正常性と負荷を監視し、エンドポイントに ping を送信し、クラスター内の予期しない正常性イベントを報告する個別のサービスを指します。 これは、1 つのサービスのパフォーマンスのみに基づくと検出されない可能性があるエラーを防ぐために役立ちます。 またウォッチドッグは、ユーザー操作を必要としない修復アクションを実行するコードをホストする場所としても適しています。たとえば、特定の時間間隔でストレージ内のログ ファイル クリーンアップを行うなどです。 サンプルのウォッチドッグ サービス実装については、「[Syslog 内の Service Fabric Linux クラスター イベント](https://github.com/Azure-Samples/service-fabric-watchdog-service)」を参照してください。

## <a name="next-steps"></a>次のステップ

* アプリケーションのインストルメント化の概要については、[アプリケーション レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-app.md)に関するページを参照してください。
* アプリケーションの Application Insights を設定する手順をについては、「[Service Fabric での ASP.NET Core アプリケーションの監視と診断](service-fabric-tutorial-monitoring-aspnet.md)」をご覧ください。
* Service Fabric が提供するプラットフォームおよびイベント監視の詳細については、[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)に関するページを参照してください。
* Service Fabric との Azure Monitor ログを構成する: [クラスターに Azure Monitor ログを設定する](service-fabric-diagnostics-oms-setup.md)
* コンテナーの監視用に Azure Monitor ログを設定する方法については、「[Azure Service Fabric での Windows コンテナーの監視と診断](service-fabric-tutorial-monitoring-wincontainers.md)」を参照してください。
* Service Fabric での診断の問題と解決策の例については、[一般的なシナリオの診断](service-fabric-diagnostics-common-scenarios.md)に関する記事を参照してください。
* Azure リソースの一般的な監視の推奨事項については、「[ベスト プラクティス - 監視と診断](/azure/architecture/best-practices/monitoring)」を参照してください。
