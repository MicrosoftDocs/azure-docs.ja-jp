---
title: Azure Data Factory でのデータ フロー アクティビティ | Microsoft Docs
description: データ ファクトリ パイプラインの内側からデータ フローを実行する方法。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: cbfa1acac34187263f8c4203e41bbe61d7e4c745
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030515"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure Data Factory でのデータ フロー アクティビティ

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
integrationRuntime | データ フローが実行されているコンピューティング環境 | IntegrationRuntimeReference | はい
staging.linkedService | SQL DW ソースまたはシンクを使用している場合は、PolyBase ステージングに使用するストレージ アカウント | LinkedServiceReference | データ フローが SQL DW に対して読み取りまたは書き込みを行う場合のみ
staging.folderPath | SQL DW ソースまたはシンクを使用している場合は、PolyBase ステージングに使用する BLOB ストレージ アカウント内のフォルダー パス | string | データ フローが SQL DW に対して読み取りまたは書き込みを行う場合のみ

![データ フローの実行](media/data-flow/activity-data-flow.png "データ フローの実行")

### <a name="data-flow-integration-runtime"></a>データ フロー統合ランタイム

データ フロー アクティビティの実行に使用する統合ランタイムを選択します。 既定では、Data Factory は 4 つのワーカー コアを持ち有効期限 (TTL) のない自動解決 Azure 統合ランタイムを使用します。 この IR は汎用目的のコンピューティングの種類で、ご使用のファクトリと同じリージョンで実行します。 データ フロー アクティビティを実行するための特定のリージョン、コンピューティングの種類、コア数、および TTL を定義する、独自の Azure 統合ランタイムを作成できます。

パイプライン実行の場合、クラスターはジョブ クラスターであり、実行が開始されるまでに数分かかります。 TTL が指定されていない場合は、すべてのパイプライン実行でこのスタートアップ時間が必要になります。 TTL を指定した場合、前回の実行後に指定された時間だけウォーム クラスター プールがアクティブのままになるため、スタートアップ時間が短縮されます。 たとえば、TTL が 60 分の場合、そのデータ フローを 1 時間に 1 回実行すると、クラスター プールはアクティブのままになります。 詳細については、[Azure 統合ランタイム](concepts-integration-runtime.md)に関するページを参照してください。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> データ フロー アクティビティでの Integration Runtime の選択は、お使いのパイプラインの*トリガー済みの実行*のみに適用されます。 データ フローを使用したパイプラインのデバッグは、デバッグ セッションで指定されたクラスターで実行されます。

### <a name="polybase"></a>PolyBase

Azure SQL Data Warehouse をシンクまたはソースとして使用する場合は、PolyBase バッチ読み込み用のステージングの場所を選択する必要があります。 PolyBase を使用すると、データを行ごとに読み込む代わりに一括してバッチ読み込みを行うことができます。 PolyBase を実行すると、SQL DW への読み込み時間が大幅に短縮されます。

## <a name="parameterizing-data-flows"></a>データ フローをパラメーター化する

### <a name="parameterized-datasets"></a>パラメーター化されたデータセット

データ フローでパラメーター化されたデータセットを使用する場合は、 **[設定]** タブでパラメーター値を設定します。

![データ フローの実行のパラメーター](media/data-flow/params.png "パラメーター")

### <a name="parameterized-data-flows"></a>パラメーター化されたデータ フロー

データ フローがパラメーター化されている場合は、 **[パラメーター]** タブでデータ フロー パラメーターの動的な値を設定します。ADF パイプラインの式言語 (文字列型についてのみ) またはデータ フローの式言語のいずれかを使用して、動的またはリテラルのパラメーター値を割り当てることができます。 詳しくは、[データ フロー パラメーター](parameters-data-flow.md)に関するページを参照してください。

![データ フローの実行パラメーターの例](media/data-flow/parameter-example.png "パラメーターの例")

## <a name="pipeline-debug-of-data-flow-activity"></a>データ フロー アクティビティのパイプライン デバッグ

データ フロー アクティビティを使用してデバッグ パイプラインを実行するには、上部バーにある **[Data Flow Debug]\(データ フロー デバッグ\)** スライダーを使用して、データ フロー デバッグ モードをオンに切り替える必要があります。 デバッグ モードでは、アクティブな Spark クラスターに対してデータ フローを実行できます。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

![デバッグ ボタン](media/data-flow/debugbutton.png "デバッグ ボタン")

デバッグ パイプラインは、データ フロー アクティビティ設定で指定された統合ランタイム環境ではなく、アクティブなデバッグ クラスターに対して実行されます。 デバッグ モードを開始するときに、デバッグ コンピューティング環境を選択できます。

## <a name="monitoring-the-data-flow-activity"></a>データ フロー アクティビティを監視する

データ フロー アクティビティには、パーティション分割、ステージ時間、およびデータ系列の情報を表示できる特別な監視エクスペリエンスがあります。 **[アクション]** の下にある眼鏡アイコンを使用して、[監視] ウィンドウを開きます。 詳しくは、[データ フローの監視](concepts-data-flow-monitoring.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Data Factory でサポートされている制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
