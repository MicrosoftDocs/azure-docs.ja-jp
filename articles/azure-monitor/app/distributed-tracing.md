---
title: Azure Application Insights における分散トレース | Microsoft Docs
description: OpenCensus プロジェクトでパートナーシップを使用した分散トレースに対する Microsoft のサポートに関する情報を提供します
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579515"
---
# <a name="what-is-distributed-tracing"></a>分散トレースとは

最新のクラウドと[マイクロサービス](https://azure.com/microservices) アーキテクチャの出現により、シンプルで個別にデプロイできるサービスが生み出され、可用性とスループットを高めながらコストを削減できるようになってきました。 しかし、これらの動きによって、個別のサービスで全体を理解するのがより簡単になった一方、システム全体の判断やデバッグがより難しくなっています。

モノリシック アーキテクチャでは、呼び出し履歴でデバッグを行っていました。 呼び出し履歴は、実行のフロー (メソッド A がメソッド B を呼び出し、メソッド B がメソッド C を呼び出す) と、これらの各呼び出しについての詳細とパラメーターを示すことができるすばらしいツールです。 これは、モノリシックまたは 1 つのプロセス上で実行されている複数のサービスに最適ですが、呼び出しがローカル履歴の参照だけでなく、プロセス境界を超える場合はどのようにしてデバッグするのでしょうか。 

ここで分散トレースの出番です。  

分散トレースは、スローされたシンプルなパフォーマンス プロファイラーを加えた、最新のクラウドとマイクロサービス アーキテクチャ向けの呼び出し履歴に相当するものです。 Azure Monitor では、分散トレース データを使用するために 2 つのエクスペリエンスを提供します。 1 つ目は[トランザクション診断](./transaction-diagnostics.md)ビューで、時間ディメンションが追加された呼び出し履歴に似ています。 トランザクション診断ビューでは、1 つのトランザクション/要求に表示が提供され、要求ごとに基づいて信頼性の問題とパフォーマンスのボトルネックの根本原因を見つけるのに役立ちます。

Azure Monitor では、[アプリケーション マップ](./app-map.md) ビューも提供されており、システムでどのようにやりとりするかのトポロジ ビュー、および平均パフォーマンスとエラー率の内容を示すために、多くのトランザクションを集計します。 

## <a name="how-to-enable-distributed-tracing"></a>分散トレースを有効にする方法

アプリケーション内の各サービスにまたがって分散トレースを有効にすることは、各サービスが実装されたときの言語に基づいて、それらのサービスに適切なエージェント、SDK、またはライブラリを追加することと同じくらい簡単です。

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>自動インストルメンテーションまたは SDK を使用した Application Insights 経由の有効化

.NET、.NET Core、Java、Node.js、JavaScript 用の Application Insights エージェントや SDK はすべて、分散トレースをネイティブにサポートしています。 各 Application Insights SDK をインストールおよび構成する手順については、以下から入手できます。

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

適切な Application Insights SDK がインストールおよび構成されると、トレース情報は、SDK 依存関係の自動コレクターによって一般的なフレームワーク、ライブラリ、テクノロジが自動収集されます。 サポートされるテクノロジの完全なリストは、「[依存関係の自動収集](./auto-collect-dependencies.md)」で利用できます。

 さらに、任意のテクノロジは、[TelemetryClient](./api-custom-events-metrics.md) 上の [TrackDependency](./api-custom-events-metrics.md) への呼び出しによって手動で追跡できます。

## <a name="enable-via-opencensus"></a>OpenCensus を使用して有効にする

Application Insights SDK に加えて、Application Insights では [OpenCensus](https://opencensus.io/) からの分散トレースもサポートされます。 OpenCensus はオープン ソースで、ベンダーに捕らわれない単一の配布のライブラリであり、サービスにメトリック コレクションと分散トレースを提供します。 また、オープン ソース コミュニティを有効にして、Redis、Memcached、または MongoDB などの一般的なテクノロジで分散トレースを有効にすることもできます。 [Microsoft では、その他のいくつかの監視やクラウド パートナーを使って OpenCensus で共同作業を行うことができます](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)。

[Python](opencensus-python.md) 

OpenCensus Web サイトには、[Python](https://opencensus.io/api/python/trace/usage.html) と [Go](https://godoc.org/go.opencensus.io) 向けの API リファレンス ドキュメント、および OpenCensus を使用するためのさまざまなガイドがあります。 

## <a name="next-steps"></a>次のステップ

* [OpenCensus Python の使用ガイド](https://opencensus.io/api/python/trace/usage.html)
* [アプリケーション マップ](./app-map.md)
* [エンドツーエンドのパフォーマンスの監視](../app/tutorial-performance.md)

