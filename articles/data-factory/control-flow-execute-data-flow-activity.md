---
title: Data Flow アクティビティ
description: データ ファクトリ パイプラインの内側からデータ フローを実行する方法。
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 01/03/2021
ms.openlocfilehash: 0663690318773ccad3bddfaaa03e456c2f58895e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383383"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure Data Factory でのデータ フロー アクティビティ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ フロー アクティビティを使用して、Mapping Data Flow を介してデータを変換および移動します。 データ フローを初めて扱う場合は、[Mapping Data Flow の概要](concepts-data-flow-overview.md)に関するページを参照してください。

## <a name="syntax"></a>構文

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
dataflow | 実行されているデータ フローへの参照 | DataFlowReference | はい
integrationRuntime | データ フローが実行されているコンピューティング環境です。 指定されていない場合は、自動解決 Azure 統合ランタイムが使用されます。 | IntegrationRuntimeReference | いいえ
compute.coreCount | Spark クラスター内で使用されるコアの数です。 自動解決 Azure 統合ランタイムが使用されている場合にのみ指定できます。 | 8、16、32、48、80、144、272 | いいえ
compute.computeType | Spark クラスター内で使用されるコンピューティングの種類です。 自動解決 Azure 統合ランタイムが使用されている場合にのみ指定できます。 | "General"、"ComputeOptimized"、"MemoryOptimized" | いいえ
staging.linkedService | Azure Synapse Analytics ソースまたはシンクを使用している場合は、PolyBase ステージングに使用するストレージ アカウントを指定します。<br/><br/>Azure Storage が VNet サービス エンドポイントを使用して構成されている場合は、ストレージ アカウントで [信頼された Microsoft サービスを許可する] を有効にしたマネージド ID 認証を使用する必要があります。「[Azure Storage で VNet サービス エンドポイントを使用した場合の影響](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)」を参照してください。 また、[Azure Blob](connector-azure-blob-storage.md#managed-identity) と [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) に必要な構成についても説明します。<br/> | LinkedServiceReference | データ フローが Azure Synapse Analytics に対して読み取りまたは書き込みを行う場合のみ
staging.folderPath | Azure Synapse Analytics ソースまたはシンクを使用している場合は、PolyBase ステージングに使用する BLOB ストレージ アカウント内のフォルダー パス | String | データ フローが Azure Synapse Analytics に対して読み取りまたは書き込みを行う場合のみ
traceLevel | データ フロー アクティビティの実行のログ レベルを設定します | Fine、Coarse、None | No

![データ フローの実行](media/data-flow/activity-data-flow.png "データ フローの実行")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>実行時、データ フロー コンピューティングのサイズを動的に設定する

Core Count プロパティと Compute Type プロパティは、実行時に入ってくるソース データのサイズに合わせて調整されるよう、動的に設定できます。 ソース データセット データのサイズを見つける目的で、Lookup や Get Metadata など、パイプライン アクティビティを使用します。 次に、Data Flow アクティビティ プロパティで Add Dynamic Content を使用します。

![動的データ フロー](media/data-flow/dyna1.png "動的データ フロー")

[この短い動画チュートリアルでこの手法について説明しています](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>データ フロー統合ランタイム

データ フロー アクティビティの実行に使用する統合ランタイムを選択します。 Data Factory では、4 つのワーカー コアを持ち、有効期限 (TTL) のない自動解決 Azure 統合ランタイムが既定で使用されます。 この IR は汎用目的のコンピューティングの種類で、ご使用のファクトリと同じリージョンで実行します。 データ フロー アクティビティを実行するための特定のリージョン、コンピューティングの種類、コア数、および TTL を定義する、独自の Azure 統合ランタイムを作成できます。

パイプライン実行の場合、クラスターはジョブ クラスターであり、実行が開始されるまでに数分かかります。 TTL が指定されていない場合は、すべてのパイプライン実行でこのスタートアップ時間が必要になります。 TTL を指定した場合、前回の実行後に指定された時間だけウォーム クラスター プールがアクティブのままになるため、スタートアップ時間が短縮されます。 たとえば、TTL が 60 分の場合、そのデータ フローを 1 時間に 1 回実行すると、クラスター プールはアクティブのままになります。 詳細については、[Azure 統合ランタイム](concepts-integration-runtime.md)に関するページを参照してください。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> データ フロー アクティビティでの Integration Runtime の選択は、お使いのパイプラインの *トリガー済みの実行* のみに適用されます。 データ フローを使用したパイプラインのデバッグは、デバッグ セッションで指定されたクラスターで実行されます。

### <a name="polybase"></a>PolyBase

Azure Synapse Analytics をシンクまたはソースとして使用する場合は、PolyBase バッチ読み込み用のステージングの場所を選択する必要があります。 PolyBase を使用すると、データを行ごとに読み込む代わりに一括してバッチ読み込みを行うことができます。 PolyBase を実行すると、Azure Synapse Analytics への読み込み時間が大幅に短縮されます。

## <a name="logging-level"></a>ログ記録レベル

データ フロー アクティビティのすべてのパイプラインを実行してすべての詳細なテレメトリ ログを完全にログする必要がない場合は、必要に応じてログ レベルを "基本" または "なし" に設定できます。 データ フローを "詳細" モード (既定値) で実行する場合、データ変換中に各パーティション レベルでアクティビティを完全にログするように ADF に要求します。 これは負荷の高い操作であるため、トラブルシューティングを行うときにのみ詳細を有効にすることで、データ フローとパイプラインのパフォーマンス全体を向上させることができます。 "基本" モードでは、その変換の期間だけがログされるのに対し、"なし" を指定した場合は、期間の概要のみが提供されます。

![ログ記録レベル](media/data-flow/logging.png "ログ レベルの設定")

## <a name="sink-properties"></a>シンクのプロパティ

データ フローのグループ化機能を使用すると、シンクの実行順序を設定できるほか、同じグループ番号を使用してシンクをグループ化できます。 グループを管理しやすくするため、シンクを同じグループ内で並列で実行するように ADF に要求できます。 また、いずれかのシンクでエラーが発生しても続行するようにシンク グループを設定することもできます。

データ フロー シンクの既定の動作では、各シンクが逐次実行され、シンクでエラーが発生した場合はデータ フローが失敗します。 さらに、データ フロー プロパティでシンクに異なる優先順位を設定しない限り、すべてのシンクは既定で同じグループに設定されます。

![シンクのプロパティ](media/data-flow/sink-properties.png "シンク プロパティの設定")

## <a name="parameterizing-data-flows"></a>データ フローをパラメーター化する

### <a name="parameterized-datasets"></a>パラメーター化されたデータセット

データ フローでパラメーター化されたデータセットを使用する場合は、 **[設定]** タブでパラメーター値を設定します。

![データ フローの実行パラメーター](media/data-flow/params.png "パラメーター")

### <a name="parameterized-data-flows"></a>パラメーター化されたデータ フロー

データ フローがパラメーター化されている場合は、 **[パラメーター]** タブでデータ フロー パラメーターの動的な値を設定します。ADF パイプラインの式言語またはデータ フローの式言語のいずれかを使用して、動的またはリテラルのパラメーター値を割り当てることができます。 詳しくは、[データ フロー パラメーター](parameters-data-flow.md)に関するページを参照してください。

### <a name="parameterized-compute-properties"></a>パラメーター化されたコンピューティングのプロパティ

コア カウントまたはコンピューティングの種類は、自動解決 Azure 統合ランタイムを使用し、かつ compute.coreCount と compute.computeType の値を指定する場合にパラメーター化が可能です。

![データ フローの実行パラメーターの例](media/data-flow/parameterize-compute.png "パラメーターの例")

## <a name="pipeline-debug-of-data-flow-activity"></a>データ フロー アクティビティのパイプライン デバッグ

データ フロー アクティビティを使用してデバッグ パイプラインを実行するには、上部バーにある **[Data Flow Debug]\(データ フロー デバッグ\)** スライダーを使用して、データ フロー デバッグ モードをオンに切り替える必要があります。 デバッグ モードでは、アクティブな Spark クラスターに対してデータ フローを実行できます。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

![デバッグ ボタン](media/data-flow/debugbutton.png "デバッグ ボタン")

デバッグ パイプラインは、データ フロー アクティビティ設定で指定された統合ランタイム環境ではなく、アクティブなデバッグ クラスターに対して実行されます。 デバッグ モードを開始するときに、デバッグ コンピューティング環境を選択できます。

## <a name="monitoring-the-data-flow-activity"></a>データ フロー アクティビティを監視する

データ フロー アクティビティには、パーティション分割、ステージ時間、およびデータ系列の情報を表示できる特別な監視エクスペリエンスがあります。 **[アクション]** の下にある眼鏡アイコンを使用して、[監視] ウィンドウを開きます。 詳しくは、[データ フローの監視](concepts-data-flow-monitoring.md)に関するページを参照してください。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>後続のアクティビティでデータ フロー アクティビティの結果を使用する

データ フロー アクティビティは、各シンクに書き込まれた行の数と各ソースから読み取られた行に関するメトリックを出力します。 これらの結果は、アクティビティの実行結果の `output` セクションに返されます。 返されるメトリックは、以下の JSON の形式です。

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

たとえば、'dataflowActivity' という名前のアクティビティで、'sink1' という名前のシンクに書き込まれた行の数を取得するには、`@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` を使用します。

このシンクで使用されていた、'source1' という名前のソースから読み取られた行の数を取得するには、`@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` を使用します。

> [!NOTE]
> シンクに書き込まれた行が 0 の場合は、メトリックに表示されません。 存在を確認するには、`contains` 関数を使用します。 たとえば、`contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` は、sink1 に行が書き込まれたかどうかを確認します。

## <a name="next-steps"></a>次のステップ

Data Factory でサポートされている制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
