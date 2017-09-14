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
ms.date: 06/12/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9fcf23f5ff47bd7457e5afa69eb2b9b33e0bf0fe
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="what-is-log-analytics"></a>Log Analytics とは
Log Analytics は、[Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持します。  Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。  この記事では、Log Analytics によって提供される価値と Log Analytics の動作について概要を説明すると共に、さらにより詳しい内容へのリンクを紹介します。

## <a name="is-log-analytics-for-you"></a>Log Analytics のメリット
現在 Azure 環境の監視を行っていない場合は、Azure リソースの監視データを収集および分析するための [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) から始めてください。  Log Analytics を使用すると、[Azure Monitor からデータを収集](log-analytics-azure-storage.md)して他のデータと関連付け、追加の分析を行うことができます。

オンプレミス環境を監視する場合や、Azure Monitor、System Center Operations Manager などのサービスを使用して監視を既に行っている場合は、Log Analytics を使用すると大きなメリットが得られます。  Log Analytics では、エージェントだけでなく、他のツールのデータも直接収集して単一のリポジトリに格納できます。  Log Analytics のログ検索、ビュー、ソリューションなどの分析ツールは、収集されたすべてのデータに対して動作し、環境全体の一元的な分析を実現します。


## <a name="using-log-analytics"></a>Log Analytics の使用
Log Analytics には、OMS ポータルか、任意のブラウザーで実行できる Azure Portal を介してアクセスできます。Log Analytics では、構成設定や複数のツールを使用して、収集されたデータの分析と操作を行うことができます。  [ログ検索](log-analytics-log-searches.md)は、ポータルから利用できます。ここではクエリを作成して、収集されたデータ、最も重要な検索のグラフィカル表示でカスタマイズできる[ダッシュボード](log-analytics-dashboards.md)、および追加機能と分析ツールを備えた[ソリューション](log-analytics-add-solutions.md)を分析します。

次の図に、OMS ポータルを示します。そのダッシュボードに、ワークスペースにインストールされている[ソリューション](#add-functionality-with-management-solutions)の概要情報が表示されています。  いずれかのタイルをクリックすると、そのソリューションのデータを詳しく確認できます。

![OMS ポータル](media/log-analytics-overview/portal.png)

Log Analytics には、リポジトリ内のデータを迅速に取得して統合するためのクエリ言語が用意されています。  [ログ検索](log-analytics-log-searches.md)を作成して保存し、ポータル内のデータを直接分析するか、ログ検索を自動的に実行し、クエリの結果が重要な条件を示す場合のアラートを作成できます。

![ログ検索](media/log-analytics-overview/log-search.png)

環境全体の正常性の迅速なグラフィカル表示を取得するには、保存されているログ検索の視覚エフェクトを[ダッシュボード](log-analytics-dashboards.md)に追加します。   

![ダッシュボード](media/log-analytics-overview/dashboard.png)

Log Analytics の外部にあるデータを分析するには、OMS リポジトリから [Power BI](log-analytics-powerbi.md) または Excel などのツールにデータをエクスポートできます。  [ログ検索 API](log-analytics-log-search-api.md) を利用して、Log Analytics データを活用するカスタム ソリューションを作成するか、その他のシステムと統合することもできます。

## <a name="add-functionality-with-management-solutions"></a>管理ソリューションによる機能の追加
[管理ソリューション](log-analytics-add-solutions.md)を使用すると、OMS に機能を追加して、Log Analytics に追加のデータおよび分析ツールを提供できます。  また、ダッシュボードのソリューションによって提供されるログ検索または追加のユーザー インターフェイスで分析できる収集対象の新しいレコードの種類を定義することもできます。  次の例の画像は、[変更追跡ソリューション](log-analytics-change-tracking.md)を示しています。

![ソリューションの変更の追跡](media/log-analytics-overview/change-tracking.png)

ソリューションはさまざまな機能に対応し、その数も増え続けています。  ソリューション ギャラリーまたは Azure Marketplace で利用可能なソリューションを簡単に参照し、[OMS ワークスペースに追加](log-analytics-add-solutions.md)できます。  多くは自動的にデプロイされ、すぐに動作を開始しますが、ある程度の構成が必要になる場合もあります。

![ソリューション ギャラリー](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Log Analytics コンポーネント
Log Analytics の核となる機能は、Azure クラウドでホストされている OMS リポジトリです。  データは、データ ソースを構成してソリューションをサブスクリプションに追加することによって接続されているソースからリポジトリに収集されます。  データ ソースとソリューションは、独自のプロパティのセットを持つさまざまなレコードの種類をそれぞれ作成しますが、リポジトリへのクエリでまとめて分析することもできます。  これにより、同じツールと方法を使用して、異なるソースによって収集されたさまざまな種類のデータを操作できます。

![OMS リポジトリ](media/log-analytics-overview/overview.png)

接続先のソースは、Log Analytics によって収集されたデータを生成するコンピューターとその他のリソースです。  これには、直接接続している [Windows](log-analytics-windows-agents.md) および [Linux](log-analytics-linux-agents.md) のコンピューターにインストールされているエージェント、または[接続されている System Center Operations Manager 管理グループ](log-analytics-om-agents.md)のエージェントを含めることができます。  Azure のリソースについては、[Azure Monitor および Azure Diagnostics](log-analytics-azure-storage.md) のデータがLog Analytics によって収集されます。

[データソース](log-analytics-data-sources.md) は接続されている各ソースから収集されたさまざまな種類のデータです。  これには、[IIS ログ](log-analytics-data-sources-iis-logs.md)や[カスタム テキスト ログ](log-analytics-data-sources-custom-logs.md)などのソースに加えて、[Windows](log-analytics-data-sources-windows-events.md) および Linux エージェントからの[イベント](log-analytics-data-sources-windows-events.md)と[パフォーマンス データ](log-analytics-data-sources-performance-counters.md)が含まれます。  収集するデータ ソースをそれぞれ構成すると、構成は接続されているソースごとに自動的に提供されます。

カスタムの要件がある場合は、[HTTP データ コレクター API](log-analytics-data-collector-api.md) を使用してデータをリポジトリから REST API クライアントに書き込むことができます。

## <a name="log-analytics-architecture"></a>Log Analytics アーキテクチャ
Log Analytics のデプロイ要件は、中央のコンポーネントが Azure クラウドでホストされているため最小限で済みます。  これには、収集されたデータを関連付けおよび分析できるサービスに加えて、リポジトリが含まれます。  クライアント ソフトウェアには要件がないため、ポータルは任意のブラウザーからアクセスできます。

エージェントは [Windows](log-analytics-windows-agents.md) および [Linux](log-analytics-linux-agents.md) コンピューター上にインストールする必要がありますが、[接続されている SCOM 管理グループ](log-analytics-om-agents.md)の既存のメンバーであるコンピューターに必要な追加エージェントはありません。  SCOM エージェントは、データを Log Analytics に転送する管理サーバーと継続的に通信します。  ただし、一部のソリューションには、Log Analytics と直接通信するエージェントが必要です。  各ソリューションのドキュメントでは、その通信要件を指定します。

[Log Analytics にサインアップ](log-analytics-get-started.md)する場合は、OMS ワークスペースを作成します。  ワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Log Analytics 環境として考えることができます。 サブスクリプションで複数のワークスペースを作成し、運用およびテストなど複数の環境をサポートできます。

![Log Analytics アーキテクチャ](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>次のステップ
* [無料の Log Analytics アカウントにサインアップ](log-analytics-get-started.md) し、独自の環境でテストします。
* OMS リポジトリにデータを収集できるさまざまな [データソース](log-analytics-data-sources.md) を表示します。
* [ソリューションギャラリーで利用可能なソリューションを参照](log-analytics-add-solutions.md) し、Log Analytics に機能を追加します。


