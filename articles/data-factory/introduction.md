---
title: Azure Data Factory の概要 | Microsoft Docs
description: データの移動と変換を調整、自動化するクラウド データ統合サービスである Azure Data Factory について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 66ea269e2f29bfd39cdb81086391e0277474219d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961357"
---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory の概要 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-introduction.md)
> * [現在のバージョン](introduction.md)

ビッグ データの世界では、未整理の生データは、多くの場合、リレーショナル、非リレーショナル、その他のストレージ システム内に格納されます。 ただし、生データ自体には、アナリストや、データ サイエンティスト、管理職意思決定者に有用な分析を提供するのに必要となる正しいコンテキストや意味はありません。 

ビッグ データに必要なのは、データ制御と運用化の処理によって、膨大な量の生データを実用的なビジネス分析へと転換できるサービスです。 Azure Data Factory は、ETL (抽出 - 変換 - 読み込み)、ELT (抽出 - 読み込み - 変換)、データ統合という複雑なハイブリッド プロジェクト用に構築された、管理クラウド サービスです。

たとえば、クラウド内のゲームで生成される、数ペタバイト規模のゲーム ログを収集するゲーム会社があるとします。 その会社がこれらのログを分析して顧客の好みや内訳、利用行動などについての洞察を得たいと考えているとしましょう。 また同社は、アップセルやクロス セルの商機の見極め、魅力的な新機能の開発、ビジネスの成長の後押し、カスタマー エクスペリエンスの向上にも興味があります。

これらのログを分析するには、顧客情報やゲーム情報、マーケティング キャンペーン情報など、オンプレミスのデータ ストアにある参照データを使用する必要があります。 この会社は、オンプレミスのデータ ストアにあるこれらのデータを、クラウドのデータ ストアにある追加のログ データと統合して活用しようと考えています。 

状況を正確に把握するために、クラウドの Spark クラスターを使用して統合したデータを処理し (Azure HDInsight)、変換したデータを Azure SQL Data Warehouse などのクラウドのデータ ウェアハウスに公開して、これらのデータに基づくレポートを簡単に作成できるようにすることを希望しています。 このワークフローを自動化し、日単位のスケジュールに従って監視、管理することが目標となります。 それを実行するタイミングは、BLOB ストア コンテナーにファイルが到着したときとします。

Azure Data Factory は、このようなデータ シナリオを解決するプラットフォームです。 *そのクラウドベースのデータ統合サービスを通じて、データの移動と変換を制御して自動化するデータ主導型のワークフローをクラウドに作成*することができます。 Azure Data Factory を使えば、各種のデータ ストアからデータを取り込むことができるデータ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。 そのデータは、Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning などのコンピューティング サービスを使って処理し、変換することができます。 

さらに、ビジネス インテリジェンス (BI) アプリケーションから利用できるよう、Azure SQL Data Warehouse などのデータ ストアに出力データを公開することもできます。 Azure Data Factory を使うと、最終的に、生データを意味のあるデータ ストアとデータ レイクに整理し、より的確な意思決定に活用できます。

![Data Factory の最上位ビュー](media/introduction/big-picture.png)

## <a name="how-does-it-work"></a>それはどのように機能しますか?
通常、Azure Data Factory のパイプライン (データドリブン ワークフロー) では次の 4 つのステップが実行されます。

![データドリブン ワークフローの 4 つのステップ](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>接続と収集

企業は、さまざまな種類のデータをさまざまなソースで管理しています。ソースには、オンプレミスのソースやクラウド内のソース、構造化されたもの、構造化されていないもの、半構造化されたものなどがあり、そのデータの受信間隔も速度もまちまちです。 

情報生成システム構築の最初のステップは、サービスとしてのソフトウェア (SaaS) サービス、データベース、ファイル共有、FTP Web サービスなど、必要なすべてのデータ ソースと処理の機能に接続することです。 その後、必要に応じて後続の処理機能にデータを移動するステップへと続きます。

Data Factory を使用していない企業では、カスタムのデータ移動コンポーネントを構築するか、これらのデータ ソースと処理を統合するカスタム サービスを作成しなければなりません。 このようなシステムの統合と保守は高コストで、容易ではありません。 そのうえ、エンタープライズ クラスの監視やアラートが欠け、フル マネージドのサービスが提供できるような制御機能を用意できないことも少なくありません。

Data Factory を使用すれば、データ パイプラインの[コピー アクティビティ](copy-activity-overview.md)で、オンプレミスとクラウドの両方のソース データ ストアからクラウド内の一元化されたデータ ストアにデータを移動し、詳しく分析することができます。 たとえば、Azure Data Lake Store でデータを収集し、後で Azure Data Lake Analytics コンピューティング サービスを使用してデータを変換することができます。 または、Azure Blob Storage でデータを収集し、後で Azure HDInsight Hadoop クラスターを使用してデータを変換することもできます。

### <a name="transform-and-enrich"></a>変換と強化
クラウド上の一元的なデータ ストアにデータが集まったら、HDInsight Hadoop、Spark、Data Lake Analytics、Machine Learning などのコンピューティング サービスを使って、その収集されたデータを処理または変換します。 保守しやすい管理されたスケジュールで確実に変換データを生成し、信頼性の高いデータを運用環境に提供する必要があります。

### <a name="publish"></a>発行
生データが変換されてビジネスに即応して利用できる形態になったら、このデータを、ビジネス ユーザーがビジネス インテリジェンス ツールから参照できる Azure Data Warehouse、Azure SQL Database、Azure CosmosDB、またはその他の分析エンジンに読み込みます。

### <a name="monitor"></a>監視
データ統合パイプラインを正常に構築してデプロイし、変換したデータからビジネス価値を生み出せるようになったなら、スケジュール化したアクティビティとパイプラインを監視して、成功率と失敗率を確認することができます。 Azure Data Factory には、Azure Monitor、API、PowerShell、Azure Monitor ログ、Azure portal の正常性パネルを利用してパイプラインを監視する、ビルトイン サポートが用意されています。

## <a name="top-level-concepts"></a>トップレベルの概念
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory は、4 つの主要コンポーネントで構成されています。 これらのコンポーネントの連携によって実現するプラットフォームを基盤として、データ移動とデータ変換のステップを含んだデータ主導型のワークフローを作成することができます。

### <a name="pipeline"></a>パイプライン
データ ファクトリには、1 つまたは複数のパイプラインを設定できます。 パイプラインは、1 つの作業単位を実行するための複数のアクティビティから成る論理的なグループです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 たとえば、パイプラインに Azure BLOB からデータを取り込むアクティビティのグループを含め、HDInsight クラスターで Hive クエリを実行してデータをパーティション分割することができます。 

パイプラインのメリットは、アクティビティを個別にではなく、セットとして管理できることです。 パイプライン内のアクティビティは、連鎖して順次処理することも、独立して並行処理することもできます。

### <a name="activity"></a>アクティビティ
アクティビティは、パイプライン内の処理ステップを表します。 たとえば、コピー アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティ、データ変換アクティビティ、制御アクティビティの 3 種類のアクティビティがサポートされています。

### <a name="datasets"></a>データセット
データセットは、データ ストア内のデータ構造を表しています。アクティビティ内でデータを入力または出力として使用したい場合は、そのデータをポイントまたは参照するだけで済みます。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 リンクされたサービスはデータ ソースへの接続を定義するもので、データセットはデータの構造を表すもの、と捉えることができます。 たとえば、Azure Storage アカウントに接続するための接続文字列は、Azure Storage のリンクされたサービスで指定します。 また、データを格納する BLOB コンテナーやフォルダーは、Azure BLOB データセットで指定します。

Data Factory ではリンクされたサービスは 2 つの目的に使用されます。

- オンプレミスの SQL Server データベース、Oracle データベース、ファイル共有、Azure Blob Storage アカウント、その他の "**データ ストア**" を表すため。 サポートされるデータ ストアの一覧については、「[コピー アクティビティ](copy-activity-overview.md)」を参照してください。

- アクティビティの実行をホストできる **コンピューティング リソース** を表すため。 たとえば、HDInsightHive アクティビティは HDInsight Hadoop クラスターで実行されます。 変換アクティビティとサポートされているコンピューティング環境の一覧については、「[データの変換](transform-data.md)」を参照してください。

### <a name="triggers"></a>トリガー
トリガーは、パイプラインの実行をいつ開始する必要があるかを決定する処理単位を表します。 さまざまな種類のイベントに合わせて、さまざまな種類のトリガーがあります。

### <a name="pipeline-runs"></a>パイプライン実行
パイプライン実行は、パイプラインを実行するインスタンスです。 パイプライン実行は、通常、パイプラインで定義されたパラメーターに引数を渡してインスタンス化されます。 引数は、手動で渡すか、トリガー定義内で渡すことができます。

### <a name="parameters"></a>parameters
パラメーターは、読み取り専用の構成のキーと値のペアです。  パラメーターはパイプラインで定義されます。 定義済みパラメーターの引数は、実行時に、トリガーが作成した実行コンテキストか、手動で実行されるパイプラインから渡されます。 パイプライン内のアクティビティは、パラメーターの値を使用します。

データセットとは、厳密に型指定されたパラメーターと、再利用または参照可能なエンティティのことです。 アクティビティは、データセットを参照でき、データセットの定義で定義されたプロパティを使用できます。

リンクされたサービスも厳密に型指定されたパラメーターであり、これにはデータ ストアかコンピューティング環境への接続情報が入ります。 これは再利用可能または参照可能なエンティティでもあります。

### <a name="control-flow"></a>制御フロー
制御フローは、パイプライン アクティビティのオーケストレーションです。これには、シーケンスに従うアクティビティの連鎖、分岐、パイプライン レベルでのパラメーターの定義、オンデマンドかトリガーからパイプラインが呼び出される際の引数の受け渡しが含まれます。 さらに、カスタム状態の受け渡しや、ループ コンテナー、つまり For-each 反復子も含まれます。


Data Factory の概念について詳しくは、次の記事をご覧ください。

- [データセットとリンクされたサービス](concepts-datasets-linked-services.md)
- [パイプラインとアクティビティ](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)

## <a name="supported-regions"></a>サポートされているリージョン

現在 Data Factory が利用できる Azure リージョンの一覧については、次のページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。

Azure Data Factory 自体は、データを保存しません。 Azure Data Factory を使用すると、データ主導型のワークフローを作成し、サポートされているデータ ストア間でのデータ移動と、他のリージョンまたはオンプレミスの環境にあるコンピューティング サービスを使用したデータ処理を調整できます。 また、プログラムと UI の両方のメカニズムを使用して、ワークフローを監視および管理することもできます。

Data Factory を利用できるリージョンが特定のリージョンのみであっても、Data Factory 内でデータ移動を実行するサービスは、いくつかのリージョンでグローバルに利用できます。 データ ストアがファイアウォールの内側にある場合は、オンプレミスの環境にインストールされているセルフホステッド統合ランタイムがデータを移動します。

たとえば、Azure HDInsight クラスターや Azure Machine Learning などのコンピューティング環境が西ヨーロッパ リージョン以外で稼働しているものと想定します。 米国東部または米国東部 2 に Azure Data Factory インスタンスを作成して利用すると、西ヨーロッパのコンピューティング環境でジョブのスケジュール設定にそのインスタンスを使用することができます。 Data Factory がコンピューティング環境でジョブをトリガーするまでに数ミリ秒かかりますが、コンピューティング環境でのジョブの実行時間は変わりません。

## <a name="accessibility"></a>アクセシビリティ

Azure portal の Data Factory ユーザー エクスペリエンスはアクセシビリティ対応です。

## <a name="compare-with-version-1"></a>バージョン 1 との比較
Data Factory サービスのバージョン 1 と現在のバージョンの相違点の一覧については、「[バージョン 1 との比較](compare-versions.md)」を参照してください。 

## <a name="next-steps"></a>次の手順
次のツールおよび SDK のいずれかを使用して、Data Factory パイプラインの作成を開始します。 

- [Azure Portal の Data Factory UI](quickstart-create-data-factory-portal.md)
- [Azure Portal の [データのコピー] ツール](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager テンプレート](quickstart-create-data-factory-resource-manager-template.md)
 
