---
title: Azure Data Factory でのパイプライン オーケストレーションおよびトリガーのトラブルシューティング
description: さまざまな方法を使用して、Azure Data Factory でのパイプライン トリガーの問題のトラブルシューティングを行います
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 2950c175acfdda33394c93649a3e2c41d1264dd2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705995"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory でのパイプライン オーケストレーションおよびトリガーのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory の "パイプライン実行" により、パイプラインの実行のインスタンスが定義されます。 たとえば、午前 8 時、午前 9 時、午前 10 時に実行するパイプラインがあるとします。 この場合、3 つの独立したパイプライン実行があることになります。 各パイプライン実行には、一意のパイプライン実行 ID があります。 実行 ID は、特定のパイプライン実行を定義するグローバル一意識別子 (GUID) です。

パイプライン実行は、通常、パイプラインで定義したパラメーターに引数を渡してインスタンス化されます。 パイプラインを実行するには、手動で行う方法とトリガーを使用する方法があります。 詳細については、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md)」を参照してください。

## <a name="common-issues-causes-and-solutions"></a>一般的な問題、原因、解決策

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions アプリ パイプラインからプライベート エンドポイント接続のエラーがスローされる
 
Data Factory と Azure 関数アプリがプライベート エンドポイントで実行されているとします。 関数アプリと連携するパイプラインを実行しようとしています。 3 つの異なるメソッドを試行しましたが、1 つのメソッドでエラー "Bad Request" が返され、他の 2 つのメソッドで "103 Error Forbidden" が返されました。

**原因**:現在、Data Factory では、関数アプリ用のプライベート エンドポイント コネクタはサポートされていません。 Azure Functions は、プライベート リンクからの接続のみを許可するように構成されているため、呼び出しは拒否されます。

**解決方法**:**PrivateLinkService** エンドポイントを作成し、関数アプリの DNS を指定します。

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>パイプライン実行をキャンセルしたにもかかわらず、モニターに進行状況が表示される

パイプラインの実行をキャンセルしても、多くの場合、パイプラインの監視で進行状況が表示されます。 これは、ブラウザーのキャッシュの問題が原因で発生します。 また、正しい監視フィルターがない可能性もあります。

**解決方法**:ブラウザーを最新の状態に更新し、正しい監視フィルターを適用します。
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>パイプラインをコピーするときに "DelimitedTextMoreColumnsThanDefined" エラーが表示される
 
コピーしているフォルダーに、不定の列数、異なる区切り文字、引用符文字の設定、何らかのデータ問題などスキーマが異なるファイルが含まれている場合、Data Factory パイプラインによって次のエラーがスローされる場合があります。

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**解決方法**:Copy アクティビティの作成時に **[バイナリ コピー]** オプションを選択します。 これにより、データの一括コピーやデータ レイク間でのデータの移行の際に、Data Factory によってファイルが開かれてスキーマが読み取られることがなくなります。 代わりに、Data Factory によって各ファイルがバイナリとして扱われて、他の場所にコピーされます。

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>統合ランタイムの容量の上限に達したときに、パイプライン実行が失敗する

エラー メッセージ

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**原因**:統合ランタイムの容量の上限に達しました。 同じ統合ランタイムを同時に使用して、大量のデータ フローを実行している可能性があります。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2)」を参照してください。

**解決方法**:
 
- 異なるトリガーのタイミングでパイプラインを実行します。
- 新しい統合ランタイムを作成し、パイプラインを複数の統合ランタイムで分割します。

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>パイプラインでアクティビティレベルのエラーと障害が発生する

Azure Data Factory オーケストレーションでは、条件付きロジックを使用することができ、ユーザーは前のアクティビティの結果に基づいて異なるパスを選択できます。 次の 4 つの条件付きパスを使用できます。**成功時** (既定のパス)、**失敗時**、**完了時**、**スキップ時**。 

Azure Data Factory では、すべてのリーフレベルのアクティビティの結果が評価されます。 すべてのリーフが成功した場合にのみ、パイプラインの結果は成功します。 リーフ アクティビティがスキップされた場合は、代わりにその親アクティビティを評価します。 

**解像度**

1. [パイプラインの失敗とエラーの処理方法](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)に関するページに従って、アクティビティレベルのチェックを実装します。
1. [Factory によるクエリ](/rest/api/datafactory/pipelineruns/querybyfactory)に関するページに従って、Azure Logic Apps を使用して定期的な間隔でパイプラインを監視します。

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>定期的な間隔でパイプライン エラーを監視する方法

障害が発生した Data Factory パイプラインを、特定の間隔 (5 分など) で監視する必要がある場合があります。 エンドポイントを使用して、データ ファクトリからパイプライン実行にクエリを実行し、フィルター処理することができます。 

**解決方法** [Factory によるクエリ](/rest/api/datafactory/pipelineruns/querybyfactory)に関するページで説明されているように、失敗したすべてのパイプラインのクエリを 5 分ごとに実行するように Azure ロジック アプリを設定できます。 そうすると、インシデントをチケット システムに報告できるようになります。

詳細については、[Data Factory から通知を送信する、パート 2](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/) に関するページを参照してください。

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>並列処理の次数を増やしてもスループットが向上しない

**原因** 

*ForEach* の並列処理の次数は、実際には並列処理の最大限度です。 このパラメーターは、特定の同時実行数を保証するものではなく、設定された値を超えないことを保証するものです。 これは、ソースとシンクへの同時アクセスを制御するときに利用できる制限値として考えてください。

*ForEach* に関する既知の事実
 * Foreach には、batch count(n) というプロパティがあります。既定値は 20 で、最大値は 50 です。
 * バッチ カウント (n) は、作成するキューの個数を指定するために使用されます。 これらのキューの作成方法の詳細については、後で説明します。
 * 各キューは順番に実行されますが、複数のキューを並列に実行することもできます。
 * キューは事前に作成されます。 つまり、実行時にはキューの再調整は行われません。
 * 処理されるアイテムは、キューごとに常に 1 つだけです。 つまり、一度に処理される最大項目数は n 個となります。
 * foreach の合計処理時間は、最も長いキューの処理時間と同じです。 つまり、foreach アクティビティのあり方は、キューの作成方法によって決まります。
 
**解像度**

 * *SetVariable* アクティビティは、並列で実行される *For Each* 内では使用しないでください。
 * お客様は、キューの作成のしくみを考慮しながら、複数の *foreach* を設定し、各 foreach の項目の処理時間が同程度になるようにすることで、foreach のパフォーマンスを向上させることができます。 これにより、長時間の実行が順次ではなく、並列で処理されるようになります。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
