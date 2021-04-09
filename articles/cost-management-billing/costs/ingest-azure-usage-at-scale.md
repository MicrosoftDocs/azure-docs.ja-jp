---
title: エクスポートを使用してサイズの大きなコスト データセットを繰り返し取得する
description: この記事は、エクスポートを使用して Azure Cost Management から大量のデータを定期的にエクスポートする場合に役立ちます。
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509632"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>エクスポートを使用してサイズの大きなコスト データセットを繰り返し取得する

この記事は、エクスポートを使用して Azure Cost Management から大量のデータを定期的にエクスポートする場合に役立ちます。 エクスポートは、未集計のコスト データを取得する際に推奨される方法です。 特に、使用状況ファイルが大きすぎるために、Usage Details API を使用して確実に呼び出したりダウンロードしたりできない場合に推奨されます。 エクスポートされたデータは、自分が選択した Azure Storage アカウントに配置されます。 そこから、お使いのシステムにそれを読み込み、必要に応じて分析できます。 Azure portal でエクスポートを構成するには、[データのエクスポート](tutorial-export-acm-data.md)に関するページを参照してください。

さまざまなスコープでエクスポートを自動化する場合は、次のセクションのサンプル API 要求が出発点として適しています。 Exports API を使用して、一般的な環境構成の一部として自動エクスポートを作成できます。 自動エクスポートは、必要なデータを確実に入手するのに役立ちます。 Azure の使用の拡大に伴って、ご自分の組織のシステムで使用できます。

## <a name="common-export-configurations"></a>一般的なエクスポート構成

最初のエクスポートを作成する前に、実際のシナリオと、それを実現するために必要な構成オプションを検討します。 次のエクスポート オプションを検討してください。

- **繰り返し** - エクスポート ジョブを実行する頻度と、自分の Azure Storage アカウントにファイルを配置するタイミングを決定します。 Daily、Weekly、Monthly から選択します。 自分の組織の内部システムで使用されているデータ インポート ジョブに合わせて繰り返しを構成してみてください。
- **繰り返し期間** - エクスポートの有効期間を決定します。 ファイルは、繰り返し期間中にのみエクスポートされます。
- **期間** - 特定の実行時にエクスポートによって生成されるデータの量を決定します。 一般的なオプションは、MonthToDate と WeekToDate です。
- **開始日** - エクスポート スケジュールをいつ開始するかを構成します。 エクスポートは開始日に作成され、その後は繰り返しに基づいて作成されます。
- **種類** - エクスポートには 3 つの種類があります。
  - ActualCost - 指定された期間の合計使用量とコストを示します。これらが計上され、お客様の請求書に表示されます。
  - AmortizedCost - 該当する予約購入コストに償却を適用して、指定された期間の合計使用量とコストを示します。
  - Usage - 2020 年 7 月 20 日より前に作成されたエクスポートは、いずれも種類が Usage になります。 スケジュールされたすべてのエクスポートを、ActualCost または AmortizedCost として更新します。
- **列** - エクスポート ファイルに含めるデータ フィールドを定義します。 これらは、Usage Details API で使用できるフィールドに対応しています。 詳細については、[Usage Details API](/rest/api/consumption/usagedetails/list) に関する記事をご覧ください。

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>サブスクリプションに対して月度累計の日単位のエクスポートを作成する

要求 URL: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>サイズの大きな Azure ストレージ BLOB をコピーする

Cost Management を使用すると、スケジュールを設定して Azure の使用状況の詳細を BLOB として Azure のストレージ アカウントにエクスポートすることができます。 その結果できる BLOB のサイズが、数ギガバイトを超えることがあります。 Azure Cost Management チームは、Azure Storage チームと共同で、サイズの大きな Azure ストレージ BLOB をコピーするテストを実施しました。 その結果については、後のセクションで説明します。 ある Azure リージョンから別のリージョンにストレージ BLOB をコピーした場合には、これとよく似た結果になるものと考えて問題ありません。

チームは、米国西部リージョンのストレージ アカウントから得られた BLOB を同一リージョンおよび他のリージョンに転送し、そのパフォーマンスをテストしました。 速度は、同一リージョンに転送した場合の 1 秒あたり 2 GB から、東南アジア リージョンのストレージ アカウントに転送した場合の 1 秒あたり 150 MB まで、幅広く観測されました。

### <a name="test-configuration"></a>テスト構成

BLOB の転送速度を測定するにあたっては、NuGet 経由で入手した Azure Data Movement Library (DLM) の最新版 (v2.0.1) を参照するシンプルな .NET コンソール アプリケーションを作成しました。 DLM は、Azure Storage チームの転送サービスにプログラムを使って簡単にアクセスできるよう、同チームが提供している SDK の一種です。 さらに、さまざまなリージョンに Standard V2 ストレージ アカウントを作成しました。ソース リージョンは米国西部としました。 ストレージ アカウントにはコンテナーを置き、それぞれに 2 GB のブロック BLOB を 10 件格納しました。 コンテナーを他のストレージ アカウントにコピーするにあたっては、DLM の _TransferManager.CopyDirectoryAsync()_ メソッドを、_CopyMethod.ServiceSideSyncCopy_ オプションを設定して使用しました。 テストは、Windows 10 が稼働している 12 コアのコンピューターで、1-GbE ネットワークを使用して実施しました。

アプリケーションで使用した設定:

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_。 チームは、この設定が全体のスループットに最も大きな影響を及ぼすことを突き止めました。 コア数の 32 倍という値を使用したところ、テスト クライアントに最高のスループットが実現しました。
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_。 最大値に設定することにより、転送の並列処理の制御を全面的に上の _ParallelOperations_ の設定に渡す効果が生じます。
- _TransferManager.Configurations.BlockSize = 4,194,304_。 4 MB にすると転送速度にある程度の効果が見られ、テストに最適であることがわかりました。

詳細とサンプル コードについては、「[次のステップ](#next-steps)」セクションのリンクを参照してください。

### <a name="test-results"></a>テスト結果

| **テスト番号** | **コピー先リージョン** | **BLOB** | **時間 (秒)** | **MB/秒** | **コメント** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2,000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 8 つのストレージ アカウントを使用した 4 並列転送: 西部の 4 アカウントから東部の 4 アカウントへの転送 1 件あたりの平均 |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 1 つのストレージ アカウントからもう 1 つのストレージ アカウントへの 4 並列転送 |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | 8 つのストレージ アカウントを使用した 8 並列転送: 西部の 4 アカウントから東部の 4 x 2 アカウントへの転送 1 件あたりの平均 |
| 7 | 東南アジア | 2 GB x 10 | 133 | 150 |   |
| 8 | 東南アジア | 2 GB x 10 x 4 | 444 | 180 | 1 つのストレージ アカウントからもう 1 つのストレージ アカウントへの 4 並列転送 |

### <a name="sync-transfer-characteristics"></a>同期転送の特徴

ここでは、DML を使用した場合のサービス側の同期転送の特徴のうち、その使用に関するものをいくつかご紹介します。

- DML では、1 つの BLOB またはディレクトリを転送できます。 ディレクトリを転送する場合には、BLOB のプレフィックスにマッチさせる検索パターンを使用できます。
- ブロック BLOB の転送は並列的に発生します。 いずれも、転送プロセスの終わりに向けて処理が進められます。 個々の BLOB ブロックは並列的に転送されます。
- クライアントでは、転送が非同期的に実行されます。 転送の状態は、_TransferContext_ オブジェクトで定義できるメソッドを対象としたコールバックにより、定期的に利用できます。
- 転送の進行中はチェックポイントが作成され、_TransferCheckpoint_ オブジェクトとして公開されます。 このオブジェクトは、_TransferContext_ オブジェクトで最新のチェックポイントとして表示されます。 転送がキャンセルまたは中止される前に _TransferCheckpoint_ が保存されていた場合には、最大 7 日間にわたり、チェックポイントから転送を再開できます。 転送の再開は、最新のチェックポイントだけでなく、どのチェックポイントからでも可能です。
- チェックポイント機能を実装しない状態で転送クライアントのプロセスが中止され、再開された場合。
  - BLOB の転送が 1 件も完了していない場合には、転送が再開されます。
  - BLOB の転送が 1 件でも完了していた場合には、転送が完了していない BLOB のみ転送が再開されます。
- クライアントの実行を一時停止すると、転送が一時停止します。
- BLOB の転送機能では、一時的な障害の影響がクライアントにそのまま及ばないようになってています。 たとえば、ストレージ アカウントに対する調整が発生しても、転送が失敗することは通常ありません。ただし、転送は遅くなります。
- サービス側での転送では、CPU とメモリ、一部のネットワーク帯域幅、および接続について、クライアント リソースの使用量が少なくなります。

### <a name="async-transfer-characteristics"></a>非同期転送の特徴

_TransferManager.CopyDirectoryAsync()_ メソッドは、_CopyMethod.ServiceSideAsyncCopy_ オプションを設定して呼び出すことができます。 その動作は、クライアントから見ると同期転送のメカニズムによく似ていますが、次の点が異なります。

- 転送速度は、同条件の同期転送よりも大幅に遅くなります (通常は 1 秒あたり 10 MB 以下です)。
- クライアント プロセスが終了しても、転送が継続します。
- チェックポイントはサポートしていますが、_TransferCheckpoint_ を使って転送を再開した場合、チェックポイントの時点からではなく、転送の現在の状態から再開されます。

### <a name="test-summary"></a>テストのまとめ

サービス側の同期転送機能を使うと、Azure BLOB ストレージでグローバルに高い転送速度を実現できます。 .NET アプリケーションでこの機能を使用するときは、Data Movement Library を使うのが簡単です。 Cost Management からエクスポートした数百ギガバイトものサイズのデータを、1 時間もかけることなく、どこにあるストレージ アカウントにも、高い確度でコピーできます。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Storage Data Movement Library](https://github.com/Azure/azure-storage-net-data-movement) のソースを確認する。
- [Data Movement Library を使用してデータを転送する](../../storage/common/storage-use-data-movement-library.md)。
- [AzureDmlBackup サンプル アプリケーション](https://github.com/markjbrown/AzureDmlBackup)のソース サンプルを確認する。
- 「[Azure Blob Storage を使った高スループット](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage)」を読む。