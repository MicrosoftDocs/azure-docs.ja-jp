---
title: "Azure Data Factory - よく寄せられる質問 | Microsoft Docs"
description: "Azure データ ファクトリについてよく寄せられる質問です。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 95b088d2fdc331dc33e973172d32892693fcb648
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - よく寄せられる質問
この記事は、Azure Data Factory サービスのバージョン 2 に適用されます。 よくあるご質問 (FAQ) とその回答について説明します。  

## <a name="what-is-azure-data-factory"></a>Azure Data Factory とは何ですか。 
Data Factory は、データの移動や変換を自動化する完全管理型クラウドベースのデータ統合サービスです。 原材料を機械で加工して最終製品を作成する工場と同じように、Azure Data Factory は生データを収集してすぐに使用できる情報に変換する既存のサービスを調整します。 Azure Data Factory を使用すると、データ ドリブンのワークフローを作成して、オンプレミスとクラウドのデータ ストアの間でデータを移動したり、Azure HDInsight、Azure Data Lake Analytics、SQL Server Integration Services (SSIS) Integration Runtime などのコンピューティング サービスを使用してデータを処理/変換したりすることができます。 Data Factory を使用すると、Azure ベースのクラウド サービスでデータ処理を実行するか、SSIS、SQL Server、Oracle などのセルフホステッド コンピューティング環境を利用することができるようになります。 必要なアクションを実行するパイプラインを作成した後で、それを定期的に実行するようにスケジュールすることができます (毎時、毎日、毎週など)。また、イベントの発生からパイプラインをトリガーすることもできます。 詳細については、[Azure Data Factory の概要](introduction.md)に関するページをご覧ください。

## <a name="whats-different-in-version-2"></a>バージョン 2 の変更点
Azure Data Factory バージョン 2 は、元の Azure Data Factory のデータ移動と変換サービスを基に構築されていますが、クラウド優先データ統合シナリオがさらに拡充されています。 Azure Data Factory バージョン 2 では、次の機能が提供されます。

- フローとスケールの制御
- Azure での SSIS パッケージのデプロイと実行

バージョン 1 のリリース以降、お客様はクラウド、オンプレミス、クラウド VM 内でデータ移動と処理の両方を実行する、複雑なハイブリッド データ統合シナリオを設計することを必要としていることがわかりました。 これらの要件を満たすには、セキュリティ保護された VNET 環境内でデータ転送と処理を行い、オンデマンドの処理能力を使用してスケールアウトする必要があります。

データ パイプラインがビジネス分析戦略の重要な要素になっているため、これらの重要なデータ活動には、データの増分読み込みやイベント トリガー型の実行をサポートする柔軟なスケジューリング機能が必要となっています。 最後の点として、このようなオーケストレーションが一層複雑になるにつれて、分岐処理、ループ処理、条件処理などの一般的なワークフロー パラダイムをサポートするサービスも一層必要になります。

バージョン 2 を使うと、既存の SQL Server Integration Services (SSIS) パッケージをクラウドに移行し、SSIS をリフトアンドシフトして、新しい “統合ランタイム” (IR) 機能を利用して ADF 内で管理される Azure のサービスとすることもできます。 バージョン 2 で SSIS IR をスピンアップすると、クラウド内で SSIS パッケージを実行、管理、監視、ビルドする機能が得られます。

### <a name="control-flow-and-scale"></a>フローとスケールの制御 
最新のデータ ウェアハウスの多様な統合フローとパターンをサポートするため、Data Factory では、時系列データと関連付けられていない新しい柔軟なデータ パイプライン モデルが利用できます。 このリリースでは、データ パイプラインの制御フロー内の条件や分岐をモデル化したり、これらのフロー内またはフロー間でパラメーターを明示的に渡したりすることができます。

データ統合に必要な任意のフロー スタイルをモデル化でき、それをオンデマンドで、またはスケジュール化してディスパッチできます。 これまで実現できなかったもので、今回実現できるようになった一般的なフローのいくつかを次に紹介します。   

- 制御フロー:
    - パイプライン内のシーケンスでのアクティビティの連鎖
    - パイプライン内のアクティビティの分岐
    - parameters
        - パイプライン レベルでパラメーターを定義でき、オンデマンドで、あるいはトリガーからパイプラインを呼び出す際に引数を渡すことができます
        - アクティビティは、パイプラインに渡される引数を使用できます。
    - カスタム状態の受け渡し
        - 状態などのアクティビティ出力は、パイプライン内の後続のアクティビティで使用できます。
    - ループ コンテナー
        - For-each 
- トリガー ベースのフロー
    - パイプラインは、オンデマンドで、または実時間に合わせてトリガーできます。
- 差分フロー
    - 差分コピー用にパラメーターを使用したり最大値を定義したりして、オンプレミスやクラウドのリレーショナル ストアからディメンションまたは参照テーブルを移動し、データをレイクに読み込みます。 

詳細については、[制御フローのチュートリアル](tutorial-control-flow.md)に関するページを参照してください。

### <a name="deploy-ssis-packages-to-azure"></a>SSIS パッケージを Azure にデプロイする 
SSIS ワークロードを移動する場合は、データ ファクトリ バージョン 2 を作成し、Azure-SSIS 統合ランタイム (IR) をプロビジョニングできます。 Azure-SSIS IR は、クラウドでの SSIS パッケージ実行専用の、Azure VM (ノード) の完全に管理されたクラスターです。 詳しい手順については、「チュートリアル: [SSIS パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md)」を参照してください。 
 

### <a name="sdks"></a>SDK
プログラマティック インターフェイスを必要とする詳しい知識のあるユーザー向けに、バージョン 2 では、使い慣れた IDE を使用してパイプラインを作成、管理、監視するために使用できる豊富な SDK のセットが用意されています。

- .NET SDK - バージョン 2 で .NET SDK は更新されています。 
- PowerShell - バージョン 2 で PowerShell コマンドレットは更新されています。 バージョン 2 のコマンドレットには、名前に **DataFactoryV2** が含まれています。 たとえば、Get AzureRmDataFactoryV2 となります。 
- Python SDK - この SDK は、バージョン 2 で新しく導入されました。
- REST API - REST API はバージョン 2 で更新されています。  

バージョン 2 で更新された SDK は、バージョン 1 クライアントと下位互換性がありません。 

### <a name="monitoring"></a>監視
現在、バージョン 2 は SDK のみを使用したデータ ファクトリの監視をサポートしています。 ポータルは、まだバージョン 2 データ ファクトリの監視をサポートしていません。 

## <a name="what-is-integration-runtime"></a>Integration Runtime の概要
Integration Runtime (IR) は、異なるネットワーク環境間で以下のデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。

- **データ移動**: パブリック ネットワーク内のデータ ストアとプライベート ネットワーク (オンプレミスまたは仮想プライベート ネットワーク) 内のデータ ストアの間でデータを移動します。 組み込みコネクタ、形式の変換、列のマッピング、パフォーマンスとスケーラビリティに優れたデータ転送に関するサポートを提供します。
- **アクティビティのディスパッチ**: Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server などのさまざまなコンピューティング サービスで実行される変換アクティビティをディスパッチして監視します。
- **SSIS パッケージの実行**: 管理されている Azure コンピューティング環境で SQL Server Integration Services (SSIS) パッケージをネイティブに実行します。

データの移動と変換に必要な 1 つ以上の Integration Runtime インスタンスをデプロイできます。  Integration Runtime は、Azure パブリック ネットワークまたはプライベート ネットワーク (オンプレミス、Azure Virtual Network、またはアマゾン ウェブ サービス Virtual Private Cloud (VPC)) で実行できます。 

詳細については、[Azure Data Factory の Integration Runtime](concepts-integration-runtime.md) に関するページを参照してください。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Integration Runtime 数の制限
1 つのデータ ファクトリに含めることができる Integration Runtime インスタンス数に厳密な制限はありません。 ただし、SSIS パッケージの実行について、1 サブスクリプションにつき Integration Runtime が使用できる仮想マシン (VM) コア数には制限があります。 詳細については、「[Data Factory の制限](../azure-subscription-service-limits.md#data-factory-limits)」を参照してください。

## <a name="when-to-use-version-2-rather-than-version-1"></a>バージョン 1 ではなくバージョン 2 を使用する場合 
Azure Data Factory を初めて使用する場合は、直接バージョン 2 から始めてください。 既にバージョン 1 を使用している場合は、バージョン 2 でデータ ファクトリを再構築してください。

> [!WARNING]
> Data Factory のバージョン 2 は、一般公開 (GA) ではなくプレビュー段階です。 そのため、GA である Data Factory のバージョン 1 と同じ Azure サービス レベル アグリーメント (SLA) の義務の対象にはなりません。 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>バージョン 2 の最上位の概念
1 つの Azure サブスクリプションで 1 つ以上の Azure Data Factory インスタンス (データ ファクトリ) を利用できます。 Azure Data Factory は、4 つの主要コンポーネントで構成されたプラットフォームです。このプラットフォームを基盤とし、データ移動とデータ変換のステップを含んだデータ主導型のワークフローを作成することができます。

### <a name="pipeline"></a>パイプライン
データ ファクトリには、1 つまたは複数のパイプラインを設定できます。 パイプラインは、1 つの作業単位を実行するための複数のアクティビティから成る論理的なグループです。 パイプライン内のアクティビティがまとまって 1 つのタスクを実行します。 たとえば、パイプラインに Azure BLOB からデータを取り込むアクティビティのグループを含め、HDInsight クラスターで Hive クエリを実行してデータをパーティション分割することができます。 パイプラインのメリットは、アクティビティを個別にではなく、セットとして管理できることです。 パイプライン内のアクティビティは、連鎖して順次処理することも、独立して並行処理することもできます。

### <a name="activity"></a>アクティビティ
アクティビティは、パイプライン内の処理ステップを表します。 たとえば、コピー アクティビティを使用して、データ ストア間でデータをコピーできます。 同様に、Azure HDInsight クラスターに対して Hive クエリを実行する Hive アクティビティを使用して、データを変換または分析できます。 Data Factory では、データ移動アクティビティ、データ変換アクティビティ、制御アクティビティの 3 種類のアクティビティがサポートされています。

### <a name="datasets"></a>データセット
データセットは、データ ストア内のデータ構造を表しています。アクティビティ内でデータを入力または出力として使用したい場合は、そのデータをポイントまたは参照するだけで済みます。 

### <a name="linked-services"></a>リンクされたサービス
リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 リンクされたサービスはデータ ソースへの接続を定義するもので、データセットはデータの構造を表すもの、と捉えることができます。 たとえば、Azure Storage アカウントに接続するための接続文字列は、Azure Storage のリンクされたサービスで指定します。 また、データを格納する BLOB コンテナーやフォルダーは、Azure BLOB データセットで指定します。

Data Factory ではリンクされたサービスは 2 つの目的に使用されます。

- オンプレミスの SQL Server、Oracle データベース、ファイル共有、Azure Blob Storage アカウント、その他の **データ ストア** を表すため。 サポートされるデータ ストアの一覧については、「[コピー アクティビティ](copy-activity-overview.md)」を参照してください。
- アクティビティの実行をホストできる **コンピューティング リソース** を表すため。 たとえば、HDInsightHive アクティビティは HDInsight Hadoop クラスターで実行されます。 変換アクティビティとサポートされているコンピューティング環境の一覧については、「[データの変換](transform-data.md)」を参照してください。

### <a name="triggers"></a>トリガー
トリガーは、パイプラインの実行をいつ開始する必要があるかを決定する処理単位を表します。 さまざまな種類のイベントに合わせて、さまざまな種類のトリガーがあります。プレビュー用には、ウォール クロック スケジューラ トリガーがサポートされています。 


### <a name="pipeline-runs"></a>パイプライン実行
パイプライン実行は、パイプラインを実行するインスタンスです。 パイプライン実行は、通常、パイプラインで定義されたパラメーターに引数を渡してインスタンス化されます。 引数は、手動で渡すか、トリガー定義内で渡すことができます。

### <a name="parameters"></a>parameters
パラメーターは、読み取り専用の構成のキーと値のペアです。  パラメーターはパイプラインで定義され、定義済みパラメーターの引数は、実行時に、トリガーが作成した実行コンテキストか、手動で実行されるパイプラインから渡されます。 パイプライン内のアクティビティは、パラメーターの値を使用します。
データセットとは、厳密に型指定されたパラメーターと、再利用または参照可能なエンティティのことです。 アクティビティは、データセットを参照でき、データセットの定義で定義されたプロパティを使用できます。

リンクされたサービスも厳密に型指定されたパラメーターであり、これにはデータ ストアかコンピューティング環境のいずれかへの接続情報が入ります。 これは再利用可能または参照可能なエンティティでもあります。

### <a name="control-flow"></a>制御フロー
パイプライン アクティビティのオーケストレーションです。これには、シーケンスに従うアクティビティの連鎖、分岐、パイプライン レベルで定義できるパラメーター、オンデマンドかトリガーからパイプラインが呼び出される際に渡される引数が含まれます。 さらに、カスタム状態の受け渡しや、ループ コンテナー、つまり For-each 反復子も含まれます。


Data Factory の概念について詳しくは、次の記事をご覧ください。

- [データセットとリンクされたサービス](concepts-datasets-linked-services.md)
- [パイプラインとアクティビティ](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory の価格モデル
Azure Data Factory の価格の詳細については、[Data Factory の価格の詳細](https://azure.microsoft.com/pricing/details/data-factory/)に関するページを参照してください。

## <a name="what-regions-support-azure-data-factory-version-2"></a>Azure Data Factory バージョン 2 をサポートするリージョン
現時点では、バージョン 2 でデータ ファクトリを作成できるのは、米国東部と米国西部の 2 つのリージョンです。 ただし、データ ファクトリで別リージョンの Integration Runtime を使用して、データストア間のデータの移動、コンピューティング サービスに対するアクティビティのディスパッチ、または SSIS パッケージのディスパッチを行うことができます。  詳細については、[Data Factory の場所](concepts-integration-runtime.md#integration-runtime-location)に関するセクションを参照してください。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory の最新情報を入手する方法
Azure Data Factory の最新情報を入手するには、次のサイトを参照してください。

- [ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [ドキュメントのホーム ページ](/azure/data-factory)
- [製品のホーム ページ](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>技術的な詳細情報 

### <a name="can-i-have-version-1-and-version-2-pipelines-run-side-by-side"></a>バージョン 1 とバージョン 2 のパイプラインを並行して実行できますか?
いいえ。 バージョン 2 またはバージョン 1 のデータ ファクトリは、他のバージョンのエンティティ (リンクされているサービス、データセット、パイプラインなど) を持つことができません。   

### <a name="do-i-still-need-to-define-datasets-in-version-2"></a>バージョン 2 でもデータセットを定義する必要はありますか?
ほとんどのアクティビティで、データセットは必須のエンティティではなくなりました。 コピー、機械学習、参照、検証、および変換のためにデータセット内のスキーマや他のメタデータ情報を使用するカスタム アクティビティには必要です。 その他のアクティビティでは、データセットは必須ではなくなりました。

### <a name="can-i-chain-two-activities-without-a-dataset-in-version-2"></a>バージョン 2 ではデータセットを使用せずに 2 つのアクティビティを連結できますか?
はい。 バージョン 2 では、データセットを使用せずにアクティビティを連結できます。 アクティビティを連結するには、パイプラインの JSON 定義で **dependsOn** プロパティを使用します。 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>バージョン 1 のすべてのアクティビティはバージョン 2 でサポートされていますか? 
はい。バージョン 1 のすべてのアクティビティはサポートされています

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>バージョン 2 のパイプラインをスケジュールするにはどうすればよいですか? 
バージョン 2 のパイプラインをスケジュールするには、スケジューラのトリガーを使用できます。 ウォールクロック カレンダー スケジュールを使用して、ユーザーが定期的に、またはカレンダーベースの周期パターンを使用してパイプラインをスケジュールできるようにします (たとえば、毎週月曜日午後 6 時と木曜日午後 9 時など)。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>バージョン 2 で実行されるパイプラインにパラメーターを渡すことはできますか?
はい。パラメーターは、バージョン 2 で第 1 級で最上位の概念です。 オンデマンドで、またはトリガーを使用して実行されるパイプラインを実行するときに、パイプライン レベルでパラメーターを定義し、引数を渡すことができます。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>パイプライン パラメーターの既定値は定義できますか? 
はい。 パイプラインではパラメーターの既定値を定義できます。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-passed-to-a-pipeline-run"></a>パイプラインのアクティビティは、実行されているパイプラインに渡された引数を使用できますか? 
はい。 パイプライン内の各アクティビティは、`@parameter` コンストラクトで実行されているパイプラインに渡されたパラメーター値を使用できます。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>アクティビティの出力プロパティは別のアクティビティで使用できますか? 
はい。 アクティビティの出力は、@activity コンストラクトを使用して後続のアクティビティで使用できます。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>アクティビティの出力で null 値を適切に処理するにはどうすればよいですか? 
式で `@coalesce` コンストラクトを使用すると、null 値を適切に処理できます。 

### <a name="can-i-use-retries-timeouts-at-the-activity-level-in-version-2"></a>バージョン 2 では、アクティビティ レベルで再試行とタイムアウトを使用できますか?
はい。 バージョン 1 と同様に、バージョン 2 では、アクティビティ レベルで再試行とタイムアウトを構成して、アクティビティの実行を制御できます。 

## <a name="next-steps"></a>次のステップ
バージョン 2 のデータ ファクトリを作成する手順については、次のチュートリアルを参照してください。

- [クイックスタート: データ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [チュートリアル: クラウドのデータをコピーする](tutorial-copy-data-dot-net.md)

