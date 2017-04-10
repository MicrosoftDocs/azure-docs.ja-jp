---
title: "Operations Management Suite (OMS) の Log Analytics とは | Microsoft Docs"
description: "Log Analytics は、Operations Management Suite (OMS) のサービスであり、 クラウドおよびオンプレミスの環境内にあるリソースで生成された運用データを収集して分析するのに役立ちます。  この記事では、Log Analytics のさまざまなコンポーネントと詳細なコンテンツへのリンクの概要について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 4d6ea516717077ffb4b832caa9e8a064905fce09
ms.openlocfilehash: dcf61991fcbf989c905aa7c18e73df19afa89c36


---
# <a name="what-is-log-analytics"></a>Log Analytics とは
Log Analytics は、[Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) のサービスであり、クラウドおよびオンプレミスの環境内にあるリソースで生成されたデータを収集して分析するのに役立ちます。 統合検索とカスタム ダッシュボードによるリアルタイムの洞察が得られるため、物理的な場所に関係なくワークロードおよびサーバー全体の無数のレコードをただちに分析できます。

## <a name="log-analytics-components"></a>Log Analytics コンポーネント
Log Analytics の核となる機能は、Azure クラウドでホストされている OMS リポジトリです。  データは、データ ソースを構成してソリューションをサブスクリプションに追加することによって接続されているソースからリポジトリに収集されます。  データ ソースとソリューションは、独自のプロパティのセットを持つさまざまなレコードの種類をそれぞれ作成しますが、リポジトリへのクエリでまとめて分析することもできます。  これにより、同じツールと方法を使用して、異なるソースによって収集されたさまざまな種類のデータを操作できます。

![OMS リポジトリ](media/log-analytics-overview/overview.png)

接続先のソースは、Log Analytics によって収集されたデータを生成するコンピューターとその他のリソースです。  これには、直接接続している [Windows](log-analytics-windows-agents.md) および [Linux](log-analytics-linux-agents.md) のコンピューターにインストールされているエージェント、または[接続されている System Center Operations Manager 管理グループ](log-analytics-om-agents.md)のエージェントを含めることができます。  Log Analytics は [Azure ストレージ](log-analytics-azure-storage.md)からのデータも収集できます。

[データソース](log-analytics-data-sources.md) は接続されている各ソースから収集されたさまざまな種類のデータです。  これには、[IIS ログ](log-analytics-data-sources-iis-logs.md)や[カスタム テキスト ログ](log-analytics-data-sources-custom-logs.md)などのソースに加えて、[Windows](log-analytics-data-sources-windows-events.md) および Linux エージェントからのイベントと[パフォーマンス データ](log-analytics-data-sources-performance-counters.md)が含まれます。  収集するデータ ソースをそれぞれ構成すると、構成は接続されているソースごとに自動的に提供されます。

## <a name="analyzing-log-analytics-data"></a>Log Analytics データの分析
Log Analytics の操作のほとんどは OMS ポータルを通じて行います。これは任意のブラウザーで実行し、構成設定と複数のツールにアクセスすることによって、収集されたデータの分析と操作を行うことができます。  [ログ検索](log-analytics-log-searches.md)は、ポータルから利用できます。ここではクエリを作成して、収集されたデータ、最も重要な検索のグラフィカル表示でカスタマイズできる[ダッシュボード](log-analytics-dashboards.md)、および追加機能と分析ツールを備えた[ソリューション](log-analytics-add-solutions.md)を分析します。

![OMS ポータル](media/log-analytics-overview/portal.png)

Log Analytics は、リポジトリ内のデータを迅速に取得して統合するクエリ構文を提供します。  [ログ検索](log-analytics-log-searches.md) を作成して保存し、OMS ポータル内のデータを直接分析するか、またはログ検索を自動的に実行し、クエリの結果が重要な条件を示す場合のアラートを作成できます。

![ログ検索](media/log-analytics-overview/log-search.png)

環境全体の正常性の迅速なグラフィカル表示を提供するには、保存されているログ検索の視覚エフェクトを [ダッシュボード](log-analytics-dashboards.md)に追加します。   

![ダッシュボード](media/log-analytics-overview/dashboard.png)

Log Analytics の外部にあるデータを分析するには、OMS リポジトリから [Power BI](log-analytics-powerbi.md) または Excel などのツールにデータをエクスポートできます。  [ログ検索 API](log-analytics-log-search-api.md) を利用して、Log Analytics データを活用するカスタム ソリューションを作成するか、その他のシステムと統合することもできます。

## <a name="solutions"></a>解決方法
ソリューションにより、Log Analytics に機能が追加されます。  これらは主にクラウドで実行し、OMS リポジトリで収集されたデータの分析を提供します。 また、OMS ダッシュボードのソリューションによって提供されるログ検索または追加のユーザー インターフェイスで分析できる収集対象の新しいレコードの種類を定義することもできます。  

![ソリューションの変更の追跡](media/log-analytics-overview/change-tracking.png)

ソリューションはさまざまな機能を利用できるため、利用可能なソリューションを簡単に参照し、ソリューション ギャラリーから [OMS ワークスペースに追加](log-analytics-add-solutions.md) できます。  多くは自動的にデプロイされ、すぐに操作を開始しますが、いくつかの構成を必要とする場合もあります。

![ソリューション ギャラリー](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Log Analytics アーキテクチャ
Log Analytics のデプロイ要件は、中央のコンポーネントが Azure クラウドでホストされているため最小限で済みます。  これには、収集されたデータを関連付けおよび分析できるサービスに加えて、リポジトリが含まれます。  クライアント ソフトウェアには要件がないため、ポータルは任意のブラウザーからアクセスできます。

エージェントは [Windows](log-analytics-windows-agents.md) および [Linux](log-analytics-linux-agents.md) コンピューター上にインストールする必要がありますが、[接続されている SCOM 管理グループ](log-analytics-om-agents.md)の既存のメンバーであるコンピューターに必要な追加エージェントはありません。  SCOM エージェントは、データを Log Analytics に転送する管理サーバーと継続的に通信します。  ただし、一部のソリューションには、Log Analytics と直接通信するエージェントが必要です。  各ソリューションのドキュメントでは、その通信要件を指定します。

[Log Analytics にサインアップ](log-analytics-get-started.md)する場合は、OMS ワークスペースを作成します。  ワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションに固有の OMS 環境として考えることができます。 サブスクリプションで複数のワークスペースを作成し、運用およびテストなど複数の環境をサポートできます。

![Log Analytics アーキテクチャ](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>次のステップ
* [無料の Log Analytics アカウントにサインアップ](log-analytics-get-started.md) し、独自の環境でテストします。
* OMS リポジトリにデータを収集できるさまざまな [データソース](log-analytics-data-sources.md) を表示します。
* [ソリューションギャラリーで利用可能なソリューションを参照](log-analytics-add-solutions.md) し、Log Analytics に機能を追加します。




<!--HONumber=Jan17_HO4-->


