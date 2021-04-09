---
title: Azure Service Fabric のアプリケーション レベルの監視
description: Azure Service Fabric クラスターの監視と診断に使用するアプリケーションおよびサービス レベルのイベントとログについて説明します。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a1df5d033701195f4fe5f6b7174f3883b84393ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791275"
---
# <a name="application-logging"></a>アプリケーションのログ記録

コードのインストルメント化は、ユーザーに関する分析情報を得る方法であるだけでなく、アプリケーションに異常があるかどうかを把握し、必要な修正を診断できる唯一の方法でもあります。 運用環境のサービスへのデバッガーの接続は技術的には可能ですが、一般的な方法ではありません。 そのため、詳細なインストルメンテーション データを入手することが重要です。

コードを自動的にインストルメント化する製品もあります。 これらのソリューションでうまくいくこともありますが、ほとんどの場合、ビジネス ロジックに固有の手動インストルメント化が必要になります。 最終的には、アプリケーションのフォレンジックなデバッグを実行できるだけの十分な情報が必要です。 任意のログ記録フレームワークで、Service Fabric アプリケーションをインストルメント化することができます。 このドキュメントでは、コードをインストルメント化するためのさまざまな方法と使用する方法の選択について説明します。 

これらの提案の使用方法の例については、「[Service Fabric アプリケーションにログ記録を追加する](service-fabric-how-to-diagnostics-log.md)」をご覧ください。

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights は、そのままで Service Fabric と強固に統合されています。 ユーザーは、AI Service Fabric NuGet パッケージを追加したり、作成および収集された、Azure Portal で表示可能なデータとログを受信したりできます。 また、アプリケーションを診断およびデバッグするためと、どのサービスとアプリケーションのどの部分が最も使用されているかを追跡するために、独自の利用統計情報を追加することが推奨されます。 SDK の [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) クラスには、アプリケーションの利用統計情報を追跡するためのさまざまな方法が用意されています。 [.NET アプリケーションの監視および診断](service-fabric-tutorial-monitoring-aspnet.md)に関するチュートリアルで、Application Insights をインストルメント化してアプリケーションに追加する方法の例を参照してください。

## <a name="eventsource"></a>EventSource

Visual Studio でテンプレートから Service Fabric ソリューションを作成すると、**EventSource** 派生クラス (**ServiceEventSource** または **ActorEventSource**) が生成されます。 アプリケーションまたはサービスのイベントを追加できるテンプレートが作成されます。 **EventSource** の名前は一意である **必要があり**、"MyCompany-&lt;solution&gt;-&lt;project&gt;" という既定のテンプレート文字列から名前を変更する必要があります。 同じ名前を使用する複数の **EventSource** 定義があると、実行時に問題が発生します。 定義済みの各イベントには一意の識別子が必要です。 識別子が一意でない場合、ランタイム エラーが発生します。 個々の開発チーム間での競合を回避するために、識別子の値の範囲を事前に割り当てている組織もあります。 詳細については、[Vance のブログ](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)または [MSDN ドキュメント](/previous-versions/msp-n-p/dn774985(v=pandp.20))をご覧ください。

## <a name="aspnet-core-logging"></a>ASP.NET Core のログ記録

コードをインストルメント化する方法を入念に計画することが重要です。 適切なインストルメンテーション計画により、コード ベースの安定性が失われ、コードの再インストルメント化が必要になる状況を回避できます。 リスクを軽減するために、Microsoft ASP.NET Core の一部である [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) などのインストルメンテーション ライブラリを選択できます。 ASP.NET Core には、既存のコードへの影響を最小限に抑えながら、選択したプロバイダーで使用できる [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) インターフェイスがあります。 Windows および Linux 上の ASP.NET Core 内と完全な .NET Framework 内でコードを使用できるので、インストルメンテーション コードが標準化されます。

## <a name="next-steps"></a>次のステップ

アプリケーションとサービスのインストルメント化にログ プロバイダーを選択したら、任意の分析プラットフォームに送信できるようにログとイベントを集計する必要があります。 Azure Monitor の推奨されるオプションについて理解を深めるには、[Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) および [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) に関する記事をご覧ください。
