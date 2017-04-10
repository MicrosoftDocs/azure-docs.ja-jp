---
title: "Microsoft 監視製品の比較 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) は、Microsoft のクラウド ベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。  この記事では、OMS に含まれるさまざまなサービスについて説明し、詳細なコンテンツへのリンクを提供します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d7325b0bb900dcaf789c01e1a9b534d7f72c8c43


---
# <a name="microsoft-monitoring-product-comparison"></a>Microsoft 監視製品の比較
この記事では、アーキテクチャ、リソースの監視方法のロジック、収集したデータの分析方法の観点から、System Center Operations Manager (SCOM) と Operations Management Suite (OMS) の Log Analytics の比較について説明します。  両者の相違点と相対的なメリットについて基本的な理解を得ることを目的としています。  

## <a name="basic-architecture"></a>基本のアーキテクチャ
### <a name="system-center-operations-manager"></a>System Center Operations Manager
SCOM のコンポーネントはすべてデータセンターにインストールされます。  [エージェントがインストールされます](http://technet.microsoft.com/library/hh551142.aspx) 。  エージェントは、SCOM データベースおよびデータ ウェアハウスと通信する [管理サーバー](https://technet.microsoft.com/library/hh301922.aspx) に接続します。  エージェントと管理サーバーの接続には、ドメイン認証が使用されます。  管理サーバーが信頼されたドメインの外部にある場合は、証明書認証を実行するか、 [ゲートウェイ サーバー](https://technet.microsoft.com/library/hh212823.aspx)に接続できます。

SCOM には、2 つの SQL データベースが必要です。1 つは運用データ用、もう 1 つはレポート作成とデータ分析をサポートするデータ ウェアハウスです。  [レポート サーバー](https://technet.microsoft.com/library/hh298611.aspx)は、SQL Reporting Services を実行して、データ ウェアハウスのデータに関するレポートを作成します。 

SCOM は、[Azure](https://www.microsoft.com/download/details.aspx?id=38414)、[Office 365](https://www.microsoft.com/download/details.aspx?id=43708)、[AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html) などの製品用の管理パックを使用して、クラウド リソースを監視できます。  これらの管理パックでは、1 つ以上のローカル エージェントをプロキシとして使用して、クラウド リソースの探索やワークフローを実行し、パフォーマンスと可用性を測定します。  プロキシ エージェントは、 [ネットワーク デバイスの監視](https://technet.microsoft.com/library/hh212935.aspx) やその他の外部リソースの監視にも使用されます。

オペレーション コンソールは、いずれかの管理サーバーに接続する Windows アプリケーションです。管理者はこのコンソールを使用して、収集されたデータの表示と分析、SCOM 環境の構成を実行できます。  Web ベースのコンソールを任意の IIS サーバーでホストし、ブラウザーからデータ分析を実行できます。

![SCOM Architecture](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
ほとんどの OMS コンポーネントは、Azure クラウド上にあるため、最小限のコストと管理作業でデプロイして管理できます。  Log Analytics によって収集されたデータはすべて、OMS リポジトリに格納されます。

Log Analytics は、次の 3 つのソースのいずれかからデータを収集できます。

* Windows と [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) または Linux と [Operations Management Suite Agent for Linux](https://technet.microsoft.com/library/mt622052.aspx) を実行する物理マシンおよび仮想マシン。  オンプレミスのマシンや、Azure などのクラウド上の仮想マシンを使用できます。
* Azure の worker ロール、Web ロール、または仮想マシンで収集された [Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics.md) データを格納する Azure ストレージ アカウント。
* [SCOM 管理グループへの接続](https://technet.microsoft.com/library/mt484104.aspx)。  この構成では、エージェントが SCOM 管理サーバーと通信します。データは管理サーバーから SCOM データベースに配信され、そこから OMS データ ストアに配信されます。
  管理者は OMS ポータルで、収集したデータの分析と Log Analytics の構成を行います。OMS ポータルは Azure でホストされ、任意のブラウザーからアクセスできます。  このデータにアクセスするためのモバイル アプリが、標準的なプラットフォーム向けに提供されています。

![Log Analytics Architecture](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>SCOM と Log Analytics の統合
SCOM を Log Analytics のデータ ソースとして使用すると、ハイブリッド監視環境で両方の製品の機能を利用できます。  既存の SCOM エージェントを OMS の管理対象とするようにオペレーション コンソールから構成でき、一方で引き続き SCOM から管理パックを使用できます。  
接続された SCOM 管理グループのデータは、次の 4 つの方法のいずれかを使用して Log Analytics に配信されます。

* イベントとパフォーマンス データは、エージェントによって収集されて、SCOM に配信されます。  さらに、SCOM の管理サーバーから Log Analytics にデータが配信されます。
* IIS ログやセキュリティ イベントなどの一部のイベントは、引き続き、エージェントから Log Analytics に直接配信されます。
* ソリューションには、エージェントに追加のソフトウェアを提供するものや、追加のデータを収集するためにソフトウェアのインストールが必要になるものもあります。  これらのデータは、通常、Log Analytics に直接送信されます。
* 一部のソリューションでは、エージェントからではなく、SCOM 管理サーバーから直接データを収集します。  たとえば、 [アラート管理ソリューション](https://technet.microsoft.com/library/mt484092.aspx) では、アラートが生成されると SCOM からアラートが収集されます。

## <a name="monitoring-logic"></a>監視ロジック
SCOM と Log Analytics は、エージェントから収集された同様のデータを扱いますが、データ収集のロジックの定義と実装の方法および収集したデータの分析方法が根本的に異なります。

### <a name="operations-manager"></a>Operations Manager
SCOM の監視ロジックは、[管理パック](https://technet.microsoft.com/library/hh457558.aspx)に実装されています。管理パックには、監視対象コンポーネントの検出、コンポーネントの正常性の測定、および分析するデータの収集に関するロジックが含まれています。  データの監視は、イベントやパフォーマンス カウンターの収集のようなシンプルな場合もあれば、スクリプトで実装される複雑なロジックを使用する場合もあります。  包括的な監視機能が含まれた管理パックは、さまざまな [Microsoft アプリケーションやサード パーティ アプリケーション](http://go.microsoft.com/fwlink/?LinkId=82105)向け、およびハードウェアやネットワーク デバイス向けに提供されています。  カスタム アプリケーション用に[独自の管理パックを作成する](http://aka.ms/mpauthor)こともできます。

管理パックには複数のワークフローが含まれており、パフォーマンス カウンターのサンプリング、サービスの状態の確認、スクリプトの実行など、それぞれが異なる監視機能を実行します。  各ワークフローは個別に実行され、書き込み先のデータベースや、アラートを生成するかどうかなど、結果についても個別に定義されています。 

実行する頻度、エラーと判断するしきい値、生成するアラートの重要度などのワークフローの詳細は管理者が上書きできます。  また、独自のワークフローを追加することで機能を追加できます。

![Overrides](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

管理パックは Operations Manager データベースにインストールされ、管理サーバーによってエージェントに自動的に配布されます。  各エージェントでは自動的に管理パックがダウンロードされ、インストールされているアプリケーションに関連するワークフローが読み込まれます。  エージェントによって収集されたデータは、管理サーバーに配信され、SCOM データベースとデータ ウェアハウスに挿入されます。  オペレーション コンソールを使用すると、管理パックに含まれるカスタム ビュー、ダッシュボード、およびレポートを使用して、これらのデータの表示と分析を実行できます。

管理パックの配布プロセスを、次の図に示します。

![Management pack flow](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>イベントとパフォーマンスの収集
Log Analytics は、Windows イベント ログ、IIS ログ、Syslog などのソースを使用して、エージェント システムからイベントとパフォーマンス カウンターを収集します。  収集するデータを Log Analytics ポータルで定義し、収集されたデータを分析するためのログ クエリを作成することができます。  OMS ワークスペースを作成すると、標準的な条件のセットが定義されます。特定のアプリケーションに対して追加のデータを定義することもできます。 

![Defining event logs in Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

SCOM には多数の詳細なワークフローがあり、通常はデータに対する特定の条件と、それに呼応して実行されるアクションが定義されているのに対して、Log Analytics ではデータ収集に対する全般的な条件が用意されています。  ログ クエリとソリューションでは、データの収集後にクラウド上の特定のデータに対して分析や操作を行うための的を絞った条件を使用できます。

#### <a name="solutions"></a>解決方法
ソリューションを使用すると、データの収集と分析のロジックを追加できます。  ソリューションは、ソリューション ギャラリーから選択して、OMS サービスに追加できます。

![Solutions Gallery](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

大部分のソリューションはクラウドで実行され、OMS リポジトリに収集されたイベントとパフォーマンス カウンターを分析します。  また、収集対象データを追加で定義して、OMS ダッシュボードのソリューションによって提供されるログ クエリや追加のユーザー インターフェイスで分析することもできます。 

たとえば、[変更の追跡ソリューション](https://technet.microsoft.com/library/mt484099.aspx)では、エージェント システムの構成の変更を検出し、OMS リポジトリにイベントが書き込まれます。これらを、検出された変更の概要を示す複数のグラフィカル ビューで分析できます。  概要ビューからログ クエリにドリルダウンすると、ソリューションによって収集された詳細なデータが表示されます。

サブスクリプションに追加するソリューションは選択できますが、現時点では、独自にソリューションを作成することはできません。  収集するイベントとパフォーマンス カウンターを選択し、独自のログ クエリに基づいてカスタム ビューを作成することは可能です。

Log Analytics の監視ロジックの概要を次の図に示します。

![Log Analytics Solution flow](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>正常性の監視
### <a name="operations-manager"></a>Operations Manager
SCOM では、アプリケーションのさまざまなコンポーネントをモデル化し、それぞれの正常性をリアルタイムで提供できます。  これにより、検出されたエラーとパフォーマンスを時系列で表示できるだけでなく、特定の時点におけるアプリケーションやシステムおよびそのコンポーネントの実際の状態も検証できます。  SCOM のヘルス エンジンは、アプリケーションが使用可能である期間を認識するため、アプリケーションの可用性を分析して時系列でレポートするサービス レベル契約 (SLA) もサポートしています。

たとえば、次のビューは、SCOM によって監視されている SQL データベース エンジンの正常性がリアルタイムで表示されます。  ビューの下半分には、1 つのデータベース エンジンが対応している各データベースの正常性が表示されます。

![State view](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

下の図は、1 つのデータベース エンジンに関するヘルス エクスプローラーと、全体的な正常性を判断する際に使用されるモニターを示しています。  これらのモニターは、SQL 管理パックで定義され、SCOM によって検出されたすべての SQL データベース エンジンに対して実行できます。

![Health explorer](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

複数のシステム上のコンポーネントを組み合わせて、分散アプリケーションの正常性を測定できます。  この方法は、複数の分散コンポーネントで構成された基幹業務アプリケーションを利用している環境で特に有用です。  各コンポーネントの正常性を測定するモデルを作成し、アプリケーションで利用できるようにロールアップすることができます。

たとえば、Active Directory は、分散コンポーネントを分析するモデルを提供する管理パックの 1 つです。  次のサンプル図は、環境全体と、フォレスト間、ドメイン間、およびドメイン コントローラー間の信頼関係の正常性を示しています。  これらの各コンポーネントには、上記の SQL の例のように、サブコンポーネントと複数のモニターが含まれています。

![SCOM diagram view](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS には、アプリケーションのモデル化やリアルタイムの正常性測定を実行するための共通エンジンが含まれていません。  個々のソリューションが、収集したデータに基づいて特定のサービスの全体的な正常性を評価することは可能です。また、ソリューションがカスタム ロジックをエージェントにインストールして、リアルタイム分析を実行する場合もあります。  ソリューションはクラウドで実行され、OMS リポジトリにアクセスできるため、多くの場合、管理パックで通常実行されるよりも詳細な分析を提供できます。 

たとえば、 [AD 評価ソリューションや SQL 評価ソリューション](https://technet.microsoft.com/library/mt484102.aspx) を使用して、収集したデータを分析し、環境のさまざまな側面を評価できます。  これらのソリューションでは、環境の可用性とパフォーマンスを向上させるために実施できる改善の推奨事項も提供されます。

![AD Assessment solution](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>データ分析
SCOM と Log Analytics は、それぞれ異なる収集データ分析機能を備えています。  SCOM のオペレーション コンソールに備えられたビューとダッシュボードではさまざまな形式で最新データを分析でき、レポートにはデータ ウェアハウスのデータを表形式で表示できます。  Log Analytics には、OMS リポジトリ内のデータを分析するための充実したログ クエリ言語とインターフェイスが用意されています。  SCOM が Log Analytics のデータ ソースとして使用されている場合、リポジトリには SCOM によって収集されたデータが含まれるため、Log Analytics ツールを使用して両方のシステムのデータを分析できます。

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>ビュー
オペレーション コンソールのビューには、SCOM によって収集されたさまざまなタイプのデータを表示できます。通常は、イベント、アラート、状態データを表形式で表示し、パフォーマンス データを折れ線グラフで表示します。  ビューでは最小限のデータ分析やデータ統合が実行されますが、特定の条件に従ってフィルター処理することもできます。 

![ビュー](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

管理パックには、通常、監視対象のアプリケーションやシステムに対応した複数のビューが含まれています。  これには、管理パックによって検出されたさまざまなオブジェクトの状態ビュー、検出された問題のアラート ビュー、カウンターのパフォーマンス ビューなどがあります。

ビューは、監視対象のシステムやオブジェクトのオープンなアラートや正常性状態など、環境の現状分析に特に適しています。  特定のアラートに対応する詳細なイベントやパフォーマンス データにドリルダウンすれば、根本原因を診断できます。 同様に、アプリケーションのさまざまなコンポーネントのパフォーマンスと正常性を表示して、現在の正常性を評価できます。

#### <a name="dashboards"></a>ダッシュボード
オペレーション コンソールのダッシュボードは、主にビューと同じデータを扱いますが、カスタマイズの自由度が高く、使用できる視覚エフェクトが多くなっています。  標準ダッシュボードが複数用意されており、目的に合わせて簡単にカスタマイズできます。  PowerShell ウィジェットを使用して、PowerShell クエリから返されるデータを表示することもできます。

![ダッシュボード](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

開発者は、管理パックに含まれているダッシュボードにカスタム コンポーネントを追加できます。  管理パック内のダッシュボードには、次に示す SQL 管理パックのダッシュボードのように、特定のアプリケーションに特化されているものがあります。  このダッシュボードは、カスタム バージョンのテンプレートとしても使用できます。

![SQL Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>レポート
SCOM のレポートには、データ ウェアハウスのデータに関する分析が表形式で表示されます。  レポートは、PDF、CSV、Word など、各種ファイル形式で印刷したり、自動配信するようにスケジュール設定したりできます。  レポートはデータ ウェアハウスのデータを扱うため、特に長期的な傾向の分析に適しています。

管理パックには、通常、特定のアプリケーション向けのカスタム レポートが含まれています。  汎用のレポートをライブラリから選択して、自社アプリケーションやアド ホック分析向けにカスタマイズすることもできます。

Active Directory 管理パックによって収集されたデータを表示するパフォーマンス レポートの例を次に示します。

![レポート](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics には、複数のアプリケーションから収集したデータの分析を実行するために使用できる [クエリ言語](https://technet.microsoft.com/library/mt484120.aspx) が用意されているため、カスタム ビューやカスタム レポートを作成する必要がありません。  OMS はクラウドに実装されているため、クエリやデータの分析のパフォーマンスはハードウェアの制限を受けず、何百万ものレコードが含まれたクエリも迅速に分析することができます。 

Log Analytics のクエリは、他の機能の基盤でもあります。  クエリの保存、クエリ結果の Excel へのエクスポート、定期的な自動実行が可能で、クエリの結果が特定の条件に一致する場合にアラートを生成することもできます。  

![Log query flow](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Log Analytics クエリの例を以下に示します。  この例では、名前に "started" が含まれているすべてのイベントが返され、イベント ID でグループ化されています。  ユーザーはクエリを指定するだけで、分析を実行するためのユーザー インターフェイスが動的に生成されます。  一覧から項目を選択すると、詳細なイベント データが返されます。

![Log query](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Log Analytics のクエリは、アド ホック分析を実行できるだけでなく、将来使用できるように保存したり、次に示す例のように [OMS のダッシュボード](http://technet.microsoft.com/library/mt484090.aspx) に追加したりできます。

![OMS のダッシュボード](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>次のステップ
* [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx)をデプロイする。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics)にサインアップする。  




<!--HONumber=Nov16_HO3-->


