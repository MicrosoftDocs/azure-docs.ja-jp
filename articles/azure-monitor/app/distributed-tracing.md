---
title: Azure Application Insights における分散トレース | Microsoft Docs
description: OpenCensus プロジェクトでローカル フォワーダーとパートナーシップを使用した分散トレースに対する Microsoft のサポートに関する情報を提供します
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bc04748f2a5b8caa8f589140dd46f0650b7b390
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102977"
---
# <a name="what-is-distributed-tracing"></a>分散トレースとは

最新のクラウドと[マイクロサービス](https://azure.com/microservices) アーキテクチャの出現により、シンプルで個別にデプロイできるサービスが生み出され、可用性とスループットを高めながらコストを削減できるようになってきました。 しかし、これらの動きによって、個別のサービスで全体を理解するのがより簡単になった一方、システム全体の判断やデバッグがより難しくなっています。

モノリシック アーキテクチャでは、呼び出し履歴でデバッグを行っていました。 呼び出し履歴は、実行のフロー (メソッド A がメソッド B を呼び出し、メソッド B がメソッド C を呼び出す) と、これらの各呼び出しについての詳細とパラメーターを示すことができるすばらしいツールです。 これは、モノリシックまたは 1 つのプロセス上で実行されている複数のサービスに最適ですが、呼び出しがローカル履歴の参照だけでなく、プロセス境界を超える場合はどのようにしてデバッグするのでしょうか。 

ここで分散トレースの出番です。  

分散トレースは、スローされたシンプルなパフォーマンス プロファイラーを加えた、最新のクラウドとマイクロサービス アーキテクチャ向けの呼び出し履歴に相当するものです。 Azure Monitor では、分散トレース データを使用するために 2 つのエクスペリエンスを提供します。 1 つ目は[トランザクション診断](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics)ビューで、時間ディメンションが追加された呼び出し履歴に似ています。 トランザクション診断ビューでは、1 つのトランザクション/要求に表示が提供され、要求ごとに基づいて信頼性の問題とパフォーマンスのボトルネックの根本原因を見つけるのに役立ちます。

Azure Monitor では、[アプリケーション マップ](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) ビューも提供されており、システムでどのようにやりとりするかのトポロジ ビュー、および平均パフォーマンスとエラー率の内容を示すために、多くのトランザクションを集計します。 

## <a name="how-to-enable-distributed-tracing"></a>分散トレースを有効にする方法

アプリケーション内のサービス全体で分散トレースを有効にすることは、各サービスにサービスが実装された言語に基づいて適切な SDK やライブラリを追加するのと同じくらいシンプルです。

## <a name="enabling-via-application-insights-sdks"></a>Application Insights SDK を使用して有効にする

.NET、.NET Core、Java、Node.js、および JavaScript 向けの Application Insights SDK では、すべて分散トレースがネイティブでサポートされます。 各 Application Insights SDK をインストールおよび構成する手順については、以下から入手できます。

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.JS](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

適切な Application Insights SDK がインストールおよび構成されると、トレース情報は、SDK 依存関係の自動コレクターによって一般的なフレームワーク、ライブラリ、テクノロジが自動収集されます。 サポートされるテクノロジの完全なリストは、「[依存関係の自動収集](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)」で利用できます。

 さらに、任意のテクノロジは、[TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) 上の [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) への呼び出しによって手動で追跡できます。

## <a name="enable-via-opencensus"></a>OpenCensus を使用して有効にする

Application Insights SDK に加えて、Application Insights では [OpenCensus](https://opencensus.io/) からの分散トレースもサポートされます。 OpenCensus はオープン ソースで、ベンダーに捕らわれない単一の配布のライブラリであり、サービスにメトリック コレクションと分散トレースを提供します。 また、オープン ソース コミュニティを有効にして、Redis、Memcached、または MongoDB などの一般的なテクノロジで分散トレースを有効にすることもできます。 [Microsoft では、その他のいくつかの監視やクラウド パートナーを使って OpenCensus で共同作業を行うことができます](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)。

OpenCensus を使用して分散トレース機能をアプリケーションに追加するには、まず [Application Insights のローカル フォワーダーをインストールして構成します](./../../azure-monitor/app/opencensus-local-forwarder.md)。 ここから、ローカル フォワーダーを使用して分散トレース データをルーティングするように、OpenCensus を構成します。 [Python](./../../azure-monitor/app/opencensus-python.md) と [Go](./../../azure-monitor/app/opencensus-go.md) の両方がサポートされます。

OpenCensus Web サイトには、[Python](https://opencensus.io/api/python/trace/usage.html) と [Go](https://godoc.org/go.opencensus.io) 向けの API リファレンス ドキュメント、および OpenCensus を使用するためのさまざまなガイドがあります。 

## <a name="next-steps"></a>次の手順

* [OpenCensus Python の使用ガイド](https://opencensus.io/api/python/trace/usage.html)
* [アプリケーション マップ](./../../azure-monitor/app/app-map.md)
* [エンドツーエンドのパフォーマンスの監視](./../../azure-monitor/learn/tutorial-performance.md)
