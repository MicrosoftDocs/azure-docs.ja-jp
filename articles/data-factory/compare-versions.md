---
title: Azure Data Factory と Data Factory バージョン 1 の比較 | Microsoft Docs
description: この記事では、Azure Data Factory と Azure Data Factory バージョン 1 を比較します。
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: 4cdb517e644d55504bfdafbd3bacdfd4bfa0b36c
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479293"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Azure Data Factory と Data Factory バージョン 1 の比較
この記事では、Data Factory と Azure Data Factory バージョン 1 を比較します。 Data Factory の概要については、[Data Factory の概要](introduction.md)に関するページを参照してください。Data Factory バージョン 1 の概要については、「[Azure Data Factory の概要](v1/data-factory-introduction.md)」を参照してください。 

## <a name="feature-comparison"></a>機能の比較
次の表では、Data Factory の機能と Data Factory バージョン 1 の機能を比較します。 

| 機能 | Version 1 | 現在のバージョン | 
| ------- | --------- | --------- | 
| データセット | アクティビティで入力および出力として使用するデータを参照するデータの名前付きビューです。 データセットは、テーブル、ファイル、フォルダー、ドキュメントなど、さまざまなデータ ストア内のデータを示します。 たとえば、Azure Blob データセットは、アクティビティによってデータが読み取られる、Azure Blob Storage 内の BLOB コンテナーと BLOB フォルダーを示しています。<br/><br/>**可用性**で、データセットの処理時間枠スライス モデル (時間単位、日単位など) を定義します。 | データセットは、現在のバージョンと同じです。 ただし、データセットの**可用性**スケジュールを定義する必要はありません。 クロック スケジューラ パラダイムからパイプラインをスケジュールできるトリガー リソースを定義できます。 詳細については、「[トリガー](concepts-pipeline-execution-triggers.md#triggers)」と[データセット](concepts-datasets-linked-services.md)に関するページを参照してください。 | 
| リンクされたサービス | リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 | リンクされたサービスは Data Factory V1 と同じですが、現在のバージョン の Data Factory の Integration Runtime コンピューティング環境を利用するための新しい **connectVia** プロパティがあります。 詳細については、[Azure Data Factory の統合ランタイム](concepts-integration-runtime.md)に関するページと、[Azure Blob Storage のリンクされたサービスのプロパティ](connector-azure-blob-storage.md#linked-service-properties)に関するページを参照してください。 |
| パイプライン | データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインは、1 つのタスクを連携して実行するアクティビティの論理的なグループです。 パイプラインをスケジュールして実行するには、startTime、endTime、isPaused を使用します。 | パイプラインは、データに対して実行されるアクティビティのグループです。 ただし、パイプライン内のアクティビティのスケジュールは、新しいトリガー リソースに分割されています。 現在のバージョンの Data Factory のパイプラインは、むしろ、トリガーを介して個別にスケジュールされる "ワークフロー単位" として考えることができます。 <br/><br/>現在のバージョンの Data Factory では、パイプラインに実行時間の "枠" がありません。 Data Factory V1 の概念である startTime、endTime、isPaused は、現在のバージョンの Data Factory にはなくなりました。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページと、[パイプラインとアクティビティ](concepts-pipelines-activities.md)に関するページを参照してください。 |
| Activities | アクティビティは、パイプライン内のデータに対して実行するアクションを定義します。 データ移動 (コピー アクティビティ) およびデータ変換アクティビティ (Hive、Pig、MapReduce など) がサポートされています。 | Data Factory の現在のバージョンでも、アクティビティはパイプライン内の定義されたアクションです。 Data Factory の現在のバージョンには、新しい[制御フロー アクティビティ](concepts-pipelines-activities.md#control-activities)が導入されています。 これらのアクティビティは、制御フロー (ループおよび分岐) で使用します。 V1 でサポートされていたデータ移動およびデータ変換アクティビティは、現在のバージョンでもサポートされています。 現在のバージョンでは、データセットを使用せずに変換アクティビティを定義できます。 |
| ハイブリッド データ移動とアクティビティのディスパッチ | [Data Management Gateway](v1/data-factory-data-management-gateway.md) (現在は Integration Runtime と呼ばれます) は、オンプレミスとクラウドの間のデータ移動をサポートしていました。| Data Management Gateway は、セルフホステッド Integration Runtime と呼ばれるようになりました。 機能は V1 と同じです。 <br/><br/> 現在のバージョンの Data Factory の Azure-SSIS Integration Runtime では、クラウドでの SQL Server Integration Services (SSIS) パッケージのデプロイと実行もサポートされています。 詳細については、「[Azure Data Factory の統合ランタイム](concepts-integration-runtime.md)」を参照してください。|
| parameters | NA | パラメーターは、パイプラインで定義されている、読み取り専用構成設定のキーと値のペアです。 パイプラインを手動で実行するときは、パラメーターの引数を渡すことができます。 スケジューラ トリガーを使用している場合は、トリガーでパラメーターの値を渡すこともできます。 パイプライン内のアクティビティは、パラメーターの値を使用します。  |
| 式 | Data Factory V1 では、データ選択クエリやアクティビティ/データセットのプロパティに関数やシステム変数を使用できます。 | 現在のバージョンの Data Factory では、JSON 文字列値の任意の場所で式を使用できます。 詳細については、[現在のバージョンの Data Factory の式と関数](control-flow-expression-language-functions.md)に関するページを参照してください。|
| パイプライン実行 | NA | パイプライン実行の単一のインスタンスです。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 ここでは、パイプラインの 3 つの独立した実行 (パイプライン実行) があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。 それぞれが特定のパイプライン実行を一意に定義する GUID です。 パイプライン実行は、通常、パイプラインで定義されたパラメーターに引数を渡してインスタンス化されます。 |
| アクティビティの実行 | NA | パイプライン内のアクティビティ実行のインスタンス。 | 
| トリガーの実行 | NA | トリガー実行のインスタンス。 詳細については、[トリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。 |
| スケジュール設定 | スケジュール設定は、パイプラインの開始/終了時間、およびデータセットの可用性に基づきます。 | 外部スケジューラを介したスケジューラ トリガーまたは実行。 詳細については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。 |

以降のセクションでは、現在のバージョンの機能の詳細について説明します。 

## <a name="control-flow"></a>制御フロー  
最新のデータ ウェアハウスの多様な統合フローとパターンをサポートするため、現在のバージョンの Data Factory では、時系列データと関連付けられていない新しい柔軟なデータ パイプライン モデルが利用できます。 これまで実現できなかったもので、今回実現できるようになった一般的なフローのいくつかを次に紹介します。 以降のセクションでは、ここに挙げた手順について説明します。   

### <a name="chaining-activities"></a>連鎖するアクティビティ
V1 では、アクティビティの出力を別のアクティビティの入力として構成し、それらを連鎖させる必要がありました。 現在のバージョンでは、パイプライン内のシーケンスでアクティビティを連鎖させることができます。 アクティビティ定義の **dependsOn** プロパティを使用して、アップストリーム アクティビティに連鎖させることができます。 詳細と例については、[パイプラインとアクティビティ](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline)に関する記事と、[アクティビティの分岐と連鎖](tutorial-control-flow.md)に関するページを参照してください。 

### <a name="branching-activities"></a>アクティビティの分岐
現在のバージョンでは、パイプライン内でアクティビティを分岐できるようになりました。 [If-condition アクティビティ](control-flow-if-condition-activity.md)は、プログラミング言語における `if` ステートメントと同じ働きを持ちます。 条件が `true` に評価されたときの一連のアクティビティと `false` に評価されたときの一連のアクティビティが評価されます。 アクティビティの分岐の例については、[アクティビティの分岐と連鎖](tutorial-control-flow.md)に関するチュートリアルを参照してください。

### <a name="parameters"></a>parameters 
パイプライン レベルでパラメーターを定義し、パイプラインをオンデマンドで起動するかトリガーから起動するときに引数を渡すことができます。 アクティビティは、パイプラインに渡される引数を使用できます。 詳細については、[パイプラインとトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。 

### <a name="custom-state-passing"></a>カスタム状態の受け渡し
状態などのアクティビティ出力は、パイプライン内の後続のアクティビティで使用できます。 たとえば、アクティビティの JSON 定義で `@activity('NameofPreviousActivity').output.value` という構文を使用して、前のアクティビティの出力にアクセスできます。 この機能を使用すると、アクティビティ間で値を受け渡すワークフローを構築できます。

### <a name="looping-containers"></a>ループ コンテナー
[ForEach アクティビティ](control-flow-for-each-activity.md)は、パイプライン内の繰り返し制御フローを定義します。 このアクティビティは、コレクションを反復処理するために使用され、指定されたアクティビティをループで実行します。 このアクティビティのループの実装は、プログラミング言語の Foreach ループ構造に似ています。 

[Until](control-flow-until-activity.md) アクティビティは、プログラミング言語における do-until ループ構文と同じ働きを持ちます。 Until アクティビティでは、そこに関連付けられている条件が `true` に評価されるまで、一連のアクティビティがループ実行されます。 Data Factory では、until アクティビティのタイムアウト値を指定することができます。  

### <a name="trigger-based-flows"></a>トリガー ベースのフロー
パイプラインは、オンデマンド (イベント ベースである BLOB ポスト) で、または実時間に合わせてトリガーできます。 トリガーの詳細については、[パイプラインとトリガー](concepts-pipeline-execution-triggers.md)に関する記事を参照してください。 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>別のパイプラインからのパイプラインの呼び出し
[パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)を使用すると、Data Factory のパイプラインが別のパイプラインを呼び出すことができます。

### <a name="delta-flows"></a>差分フロー
ETL パターンの主要な使用例は、"差分読み込み" です。つまり、パイプラインの最後の反復以降に変更されたデータのみを読み込みます。 現在のバージョンの新機能 ([ルックアップ アクティビティ](control-flow-lookup-activity.md)、柔軟なスケジュール設定、制御フローなど) により、この使用例を自然な方法で実現できます。 詳細な手順については、[増分コピーのチュートリアル](tutorial-incremental-copy-powershell.md)を参照してください。

### <a name="other-control-flow-activities"></a>その他の制御フロー アクティビティ
現在のバージョンの Data Factory でサポートされているその他の制御フロー アクティビティをいくつか次に示します。 

制御アクティビティ | 説明
---------------- | -----------
[ForEach アクティビティ](control-flow-for-each-activity.md) | パイプライン内の繰り返し制御フローを定義します。 このアクティビティは、コレクションを反復処理するために使用され、指定されたアクティビティをループで実行します。 このアクティビティのループの実装は、プログラミング言語の Foreach ループ構造に似ています。
[Web アクティビティ](control-flow-web-activity.md) | Data Factory パイプラインからカスタム REST エンドポイントを呼び出します。 このアクティビティで使用したり、アクセスしたりするデータセットやリンクされたサービスを渡すことができます。 
[Lookup アクティビティ](control-flow-lookup-activity.md) | 外部ソースからレコードまたはテーブル名を読み取るか検索します。 この出力は、後続のアクティビティによってさらに参照できます。 
[GetMetadata アクティビティ](control-flow-get-metadata-activity.md) | Azure Data Factory の任意のデータのメタデータを取得します。 
[Wait アクティビティ](control-flow-wait-activity.md) | 指定した期間、パイプラインを一時停止します。

## <a name="deploy-ssis-packages-to-azure"></a>SSIS パッケージを Azure にデプロイする 
SSIS ワークロードをクラウドに移動し、現在のバージョンを使用してデータ ファクトリを作成し、Azure-SSIS Integration Runtime をプロビジョニングする場合は Azure-SSIS を使用します。

Azure-SSIS Integration Runtime は、クラウドでの SSIS パッケージの実行専用の、Azure VM (ノード) のフル マネージド クラスターです。 Azure-SSIS Integration Runtime のプロビジョニング後は、SSIS パッケージをオンプレミスの SSIS 環境にデプロイするために使用していたのと同じツールを使用できます。 

たとえば、SQL Server Data Tools または SQL Server Management Studio を使用して、Azure 上のこのランタイムに SSIS パッケージをデプロイできます。 手順については、チュートリアル「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md)」を参照してください。 

## <a name="flexible-scheduling"></a>柔軟なスケジュール設定
現在のバージョンの Data Factory では、データセットの可用性スケジュールを定義する必要はありません。 クロック スケジューラ パラダイムからパイプラインをスケジュールできるトリガー リソースを定義できます。 柔軟なスケジュール設定および実行モデルのために、トリガーからパイプラインにパラメーターを渡すこともできます。 

現在のバージョンの Data Factory では、パイプラインに実行時間の "枠" がありません。 Data Factory V1 の概念である startTime、endTime、isPaused は、現在のバージョンの Data Factory には存在しません。 現在のバージョンの Data Factory でパイプラインを構築してスケジュールする方法については、[パイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)に関するページを参照してください。

## <a name="support-for-more-data-stores"></a>より多くのデータ ストアのサポート
現在のバージョンでは、V1 よりも多くのデータ ストアとの間でのデータ コピーがサポートされています。 サポートされているデータ ストアの一覧については、以下の記事を参照してください。

- [バージョン 1 - サポートされているデータ ストア](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [現在のバージョン -サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>オンデマンドの Spark クラスターのサポート
現在のバージョンでは、オンデマンドの Azure HDInsight Spark クラスターの作成がサポートされています。 オンデマンドの Spark クラスターを作成するには、オンデマンドの HDInsight のリンクされたサービスに対する定義で、クラスターの種類を Spark として指定します。 その後、このリンクされたサービスを使用するように、パイプラインの Spark アクティビティを構成できます。 

実行時、アクティビティが実行されるときに、Data Factory サービスによって自動的に Spark クラスターが作成されます。 詳細については、次の記事を参照してください。

- [現在のバージョンの Data Factory の Spark アクティビティ](transform-data-using-spark.md)
- [Azure HDInsight のオンデマンドのリンクされたサービス](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>カスタム アクティビティ
V1 では、IDotNetActivity インターフェイスの Execute メソッドを実装するクラスが含まれた .NET クラス ライブラリ プロジェクトを作成して (カスタム) DotNet アクティビティ コードを実装します。 そのため、カスタム コードは、.NET Framework 4.5.2 で記述し、Windows ベースの Azure Batch プール ノードで実行する必要があります。 

現在のバージョンのカスタム アクティビティでは、.NET インターフェイスを実装する必要はありません。 コマンドとスクリプトを直接実行できるようになり、実行可能ファイルとしてコンパイルされた独自のカスタム コードを実行できるようになりました。 

詳細については、[Data Factory とバージョン 1 でのカスタム アクティビティの違い](transform-data-using-dotnet-custom-activity.md#compare-v2-v1)に関するページを参照してください。

## <a name="sdks"></a>SDK
 現在のバージョンの Data Factory では、パイプラインの作成、管理、および監視に使用できる、より豊富な SDK セットが用意されています。

- **.NET SDK**:.NET SDK は現在のバージョンで更新されています。

- **PowerShell**:PowerShell コマンドレットは現在のバージョンで更新されています。 現在のバージョンのコマンドレットには、名前に **DataFactoryV2** が付いています (たとえば、Get-AzDataFactoryV2)。 

- **Python SDK**:この SDK は、現在のバージョンで新しく導入されました。

- **REST API**:REST API は現在のバージョンで更新されています。 

現在のバージョンで更新された SDK は、V1 クライアントと下位互換性がありません。 

## <a name="authoring-experience"></a>作成のエクスペリエンス

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| Azure ポータル | [はい](quickstart-create-data-factory-portal.md) | いいえ |
| Azure PowerShell | [はい](quickstart-create-data-factory-powershell.md) | [はい](data-factory-build-your-first-pipeline-using-powershell.md) |
| .NET SDK | [はい](quickstart-create-data-factory-dot-net.md) | [はい](data-factory-build-your-first-pipeline-using-vs.md) |
| REST API | [はい](quickstart-create-data-factory-rest-api.md) | [はい](data-factory-build-your-first-pipeline-using-rest-api.md) |
| Python SDK | [はい](quickstart-create-data-factory-python.md) | いいえ |
| Resource Manager テンプレート | [はい](quickstart-create-data-factory-resource-manager-template.md) | [はい](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>ロールとアクセス許可

Data Factory バージョン 1 の共同作成者ロールを使って、現在のバージョンの Data Factory リソースの作成と管理を行うことができます。 詳しくは、「[Data Factory Contributor](../role-based-access-control/built-in-roles.md#data-factory-contributor)」をご覧ください。

## <a name="monitoring-experience"></a>監視のエクスペリエンス
現在のバージョンでは、[Azure Monitor](monitor-using-azure-monitor.md) を使用してデータ ファクトリを監視することもできます。 新しい PowerShell コマンドレットでは、[統合ランタイム](monitor-integration-runtime.md)の監視がサポートされています。 V1 と V2 のどちらでも、Azure Portal から起動できる監視アプリケーションによる視覚的な監視がサポートされています。


## <a name="next-steps"></a>次の手順
データ ファクトリの作成手順について、[PowerShell](quickstart-create-data-factory-powershell.md)、[.NET](quickstart-create-data-factory-dot-net.md)、[Python](quickstart-create-data-factory-python.md)、[REST API](quickstart-create-data-factory-rest-api.md) の各クイック スタートを参照してください。 
