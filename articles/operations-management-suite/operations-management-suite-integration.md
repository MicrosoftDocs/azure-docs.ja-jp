---
title: "Operations Management Suite (OMS) との統合 | Microsoft Docs"
description: "OMS の標準機能を使用するだけでなく、他の管理アプリケーションおよびサービスと統合して、ハイブリッドの管理環境を提供したり、環境に固有のカスタム管理シナリオを提供したり、お客様用のカスタム管理エクスペリエンスを提供したりすることができます。  この記事では、OMS と統合するさまざまなオプションおよび詳細な技術情報を提供する記事へのリンクを示します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 31291a9b0f452adeae42f744a976c14e61a4b303


---
# <a name="integrating-with-operations-management-suite-oms"></a>Operations Management Suite (OMS) との統合
Operations Management Suite は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。  OMS の標準機能を使用するだけでなく、他の管理アプリケーションおよびサービスと統合して、ハイブリッドの管理環境を提供したり、環境に固有のカスタム管理シナリオを提供したり、お客様用のカスタム管理エクスペリエンスを提供したりすることができます。  この記事では、OMS サービスと統合するさまざまなオプションおよび詳細な技術情報を提供する記事へのリンクを示します。 

## <a name="log-analytics"></a>Log Analytics
Log Analytics によって収集される管理データは、Azure にホストされているリポジトリに保存されます。  リポジトリに保存されているデータはすべて、極めて大量のデータを迅速に分析するログ検索に使用できます。  統合の要件は、分析を利用できるように新しいデータでリポジトリを作成したり、新しい視覚化を提供できるようにリポジトリのデータを抽出したり、他の管理ツールと統合したりすることである場合があります。

リポジトリ内のデータの各項目は、レコードとして保存されます。  リポジトリを作成すると、ソリューションで使用するレコードとそのプロパティの説明を用いて、ユーザーを指定する必要があります。  データを取得する場合、作業しているデータについてのこの情報が必要になります。

![OMS リポジトリを作成する](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Log Analytics リポジトリの作成
OMS リポジトリを作成するには、複数の方法があります。  使用するメソッドは、ソース データが配置される場所、データの形式、およびサポートする必要があるクライアントなどの要素によって異なります。  一度データがリポジトリに保存されると、収集された方法による違いはありません。

次のセクションでは、OMS リポジトリを作成するためのさまざまなオプションについて説明します。

#### <a name="connected-sources-and-data-sources"></a>接続されたソースとデータ ソース
接続されたソースは、OMS リポジトリに対してデータを取得できる場所です。  データ ソースとソリューションは、接続されたソース上で実行され、収集される特定のデータを定義します。  アプリケーションがこれらのデータ ソースのいずれかにデータを書き込む場合、データ ソースを構成してそのデータを収集することができます。  たとえば、アプリケーションで Syslog イベントが作成される場合、そのイベントは Linux エージェントの Syslog データ ソースから収集することができます。

* [Log Analytics のデータ ソース](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>ソリューション
ソリューションでは、OMS の機能を拡張します。  ソリューションは、接続されたソースからデータを収集したり、既にリポジトリで収集されているレコード上で分析を実行したりすることができます。  Microsoft によって提供される各ソリューションには、収集するデータに関する詳細を提供する個別の記事があります。

* [Log Analytics のソリューション](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>HTTP データ コレクター API
Log Analytics HTTP データ コレクター API は、JSON データを Log Analytics リポジトリに追加できるようにする REST API です。  その他のデータ ソースまたはソリューションの 1 つによってデータを提供しないアプリケーションがある場合は、この API を活用できます。  API を呼び出すことができる任意のクライアントからのリポジトリの作成に、この API を使用できます。これは任意のデータ ソースまたはソリューションの収集スケジュールに依存しません。

* [Log Analytics HTTP データ コレクター API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Log Analytics リポジトリからのデータの取得
OMS リポジトリからデータを取得するには、複数の方法があります。  ユーザーが OMS コンソールを使用してデータを取得するようにし、さまざまな種類の視覚化と分析を提供する必要がある場合があります。  また、別の管理ソリューションなど、外部プロセスからデータを取得することもできます。

#### <a name="log-searches"></a>ログ検索
OMS リポジトリに保存されているデータはすべて、ログ検索で使用できます。  ユーザーは、OMS コンソールで独自のアド ホック分析を実行したり、特定のログ検索を視覚化したダッシュボードを作成したりすることができます。  ソリューションには、事前定義済みの検索に基づいて、視覚化されたカスタム ビューを含めることができます。  外部アプリケーションや管理ツールから OMS リポジトリのデータにアクセスするには、ログ検索 API を使用できます。  

* [Log Analytics におけるログ検索](../log-analytics/log-analytics-log-searches.md)
* [Log Analytics のログ検索 REST API](../log-analytics/log-analytics-log-search-api.md)
* [Log Analytics コマンドレット](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>カスタム ビュー
ビュー デザイナーでは、ソリューション内のデータの視覚化および分析をユーザーに提供する、OMS コンソールでカスタム ビューを作成することができます。  各ビューには、コンソールのメイン ページに表示されるタイル、定義するログ検索に基づいて任意の数の視覚化パーツが含まれます。

* [Log Analytics ビュー デザイナー](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Log Analytics は、その視覚エフェクトと分析ツールを活用できるように、OMS リポジトリから Power BI へのデータを自動的にエクスポートできます。  データを最新の状態に保つように、スケジュールでこのエクスポートを実行します。 

* [Log Analytics データを Power BI にエクスポートする](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automation
OMS は、収集したデータに対応するか、その他の管理機能を実行するために、プロセスを自動化することができます。  これで、アプリケーションからデータを収集し、OMS リポジトリに挿入することができます。また、リポジトリで検出されたデータに応えて、既知の問題の修正を自動化することもできます。 

![Automation](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbook
Azure Automation の Runbook は、Azure クラウドで PowerShell スクリプトとワークフローを実行します。  Azure のリソースやクラウドからアクセスできるその他のリソースを管理するために、それらを使用できます。  Runbook は、Hybrid Runbook Worker を使用して、ローカル データセンターで実行することもできます。  Azure Portal または PowerShell や Automation API などの多くのメソッドを使用して外部プロセスから Runbook を開始することができます。

* [Azure Automation での Runbook の開始](../automation/automation-starting-a-runbook.md)
* [Azure Automation コマンドレット](https://msdn.microsoft.com/library/dn690262.aspx)
* [Automation REST API](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automation .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>アラート
アラート ルールでは、スケジュールに従ってログ検索を自動的に実行します。  結果が特定の条件に一致する場合、結果として得られるアラートは、Azure Automation で Runbook を開始するか、外部プロセスを開始できる webhook を呼び出すことができます。  どちらの応答にも、ログ検索で返されるデータを含むアラートの詳細を含めることができます。

* [Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)
* [Log Analytics のアラート API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>バックアップとサイトの回復
Azure Backup と Site Recovery は、エンタープライズ データを保護して、サーバーおよびアプリケーションの可用性を確保するためのサービスを提供します。  アプリケーションのバックアップ サービスを提供したり、仮想マシンのフェールオーバーを開始したりするようなシナリオを実行するために、これらのサービスを活用することができます。

* [Azure Backup コマンドレット](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Azure Site Recovery コマンドレット](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>カスタム ソリューション
カスタム ソリューションに統合ロジックをカプセル化して、自分のワークスペースまたは顧客のワークスペースで実行できます。  ソリューションには、完全な管理シナリオを提供するために、その他のリソースだけでなく、この記事の統合メソッドのいずれかを含めることができます。  ソリューションが削除される場合や、作成されたすべてのソリューションが、OMS ワークスペースと Azure サブスクリプションから削除される場合などは、ソリューションのリソースはパッケージ化されます。

たとえば、ソリューションには、データを収集して処理するために Automation Runbook を含めて、HTTP データ コレクター API を使用して Log Analytics リポジトリを作成することができます。  収集したデータを示して分析するカスタム ビューを含めることもできます。  

* カスタム ソリューションを作成する (近日対応予定)    

## <a name="next-steps"></a>次のステップ
* OMS サービスの技術情報については、「[OMS SDK](operations-management-suite-sdk.md)」をご覧ください。  




<!--HONumber=Nov16_HO3-->


