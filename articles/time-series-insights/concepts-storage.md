---
title: ストレージの概要 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 のデータ ストレージについて説明します。
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: a0f1e7789c0cebdd1cb5b22f21151020a0be09c9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855110"
---
# <a name="data-storage"></a>データ ストレージ

Azure Time Series Insights Gen2 環境を作成するときは、次の 2 つの Azure リソースを作成します。

* ウォーム データ ストレージ用に構成できる Azure Time Series Insights Gen2 環境。
* コールド データ ストレージ用の Azure Storage アカウント。

ウォーム ストア内のデータは、[Time Series Query API](./time-series-insights-update-tsq.md) および [Azure Time Series Insights TSI エクスプローラー](./time-series-insights-update-explorer.md)を介してのみ使用できます。 ウォーム ストアには、Azure Time Series Insights Gen2 環境の作成時に選択された[保持期間](./time-series-insights-update-plan.md#the-preview-environment)内の最新のデータが含まれます。

Azure Time Series Insights Gen2 を使用すると、コールド ストア データが [Parquet ファイル形式](#parquet-file-format-and-folder-structure)で Azure BLOB ストレージに保存されます。 Azure Time Series Insights Gen2 ではこのコールド ストア データが排他的に管理されますが、標準の Parquet ファイルとして直接読み取ることができます。

> [!WARNING]
> コールド ストア データが存在する Azure Blob ストレージ アカウントの所有者は、アカウント内のすべてのデータに対するフルアクセス権を持っています。 このアクセスには、書き込みおよび削除のアクセス許可が含まれます。 Azure Time Series Insights Gen2 によって書き込まれたデータを編集または削除しないでください。データが失われる原因になる可能性があります。

## <a name="data-availability"></a>データの可用性

Azure Time Series Insights Gen2 では、最適なクエリ パフォーマンスのために、データのパーティション分割とインデックス付けが行われます。 データは、インデックスが付けられた後、ウォーム ストア (有効な場合) とコールド ストアの両方からクエリを実行できるようになります。 取り込まれたされるデータの量は、この可用性に影響を与える可能性があります。

> [!IMPORTANT]
> データが利用可能になるまでに最大 60 秒の時間が発生することがあります。 待機時間が 60 秒を大きく上回る場合は、Azure portal を通じてサポート チケットを送信してください。

## <a name="azure-storage"></a>Azure Storage

このセクションでは、Azure Time Series Insights Gen2 に関連する Azure Storage の詳細について説明します。

Azure Blob Storage の詳細については、[Storage Blob の概要](../storage/blobs/storage-blobs-introduction.md)に関する記事をご覧ください。

### <a name="your-storage-account"></a>ストレージ アカウント

Azure Time Series Insights Gen2 の環境を作成すると、Azure Storage アカウントが長期的なコールド ストアとして作成されます。  

Azure Time Series Insights Gen2 では、Azure Storage アカウント内の各イベントのコピーが最大 2 つ保持されます。 1 つのコピーには、イベントがインジェスト時間によって並べ替えられて格納され、常に時間順に並べられた一連のイベントにアクセスできるようになります。 時間の経過と共に、Azure Time Series Insights Gen2 では、高パフォーマンスのクエリ用に最適化するために、データの再パーティション分割コピーも作成されます。

すべてのデータは、Azure Storage アカウントに無期限に格納されます。

#### <a name="writing-and-editing-blobs"></a>BLOB の作成と編集

クエリのパフォーマンスとデータの可用性を確保するため、Azure Time Series Insights Gen2 によって作成された BLOB の編集および削除は行わないでください。

#### <a name="accessing-cold-store-data"></a>コールド ストア データへのアクセス

[Azure Time Series Insights TSI エクスプローラー](./time-series-insights-update-explorer.md)および [Time Series Query API](./time-series-insights-update-tsq.md) からデータにアクセスするだけでなく、コールド ストアに格納されている Parquet ファイルから直接データにアクセスすることもできます。 たとえば、Jupyter Notebook でデータの読み取り、変換、クレンジングを行った後、それを使用して同じ Spark ワークフローで Azure Machine Learning モデルをトレーニングできます。

Azure Storage アカウントから直接データにアクセスするには、Azure Time Series Insights Gen2 データを格納するために使用しているアカウントへの読み取りアクセス権が必要です。 次に、「[Parquet ファイル形式](#parquet-file-format-and-folder-structure)」セクションで説明されている `PT=Time` フォルダーにある Parquet ファイルの作成時刻に基づいて、選択したデータを読み取ることができます。  ストレージ アカウントへの読み取りアクセスを有効にする方法の詳細については、[ストレージ アカウント リソースへのアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。

#### <a name="data-deletion"></a>データの削除

Azure Time Series Insights Gen2 のファイルは削除しないでください。 関連するデータは、Azure Time Series Insights Gen2 内からのみ管理してください。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet ファイル形式とフォルダー構造

Parquet は、効率的なストレージとパフォーマンスのために設計されている、オープンソースの列指向ファイル形式です。 Azure Time Series Insights Gen2 では、Parquet を使用して、大規模な時系列 ID ベースのクエリ パフォーマンスを実現します。  

Parquet ファイルの種類の詳細については、[Parquet のドキュメント](https://parquet.apache.org/documentation/latest/)を参照してください。

Azure Time Series Insights Gen2 では、次のようにデータのコピーが格納されます。

* 1 つ目の初期コピーは、インジェスト時間によって分割され、データは到着順にほぼ格納されます。 データは次のような `PT=Time` フォルダーにあります。

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 2 つ目の再パーティション分割されるコピーは、時系列 ID に応じてグループ化され、`PT=TsId` フォルダーに格納されます。

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

`PT=Time` フォルダー内の BLOB 名のタイムスタンプは、イベントのタイムスタンプではなく、Azure Time Series Insights Gen2 へのデータの到着時間に対応します。

`PT=TsId` フォルダー内のデータは、時間の経過と共にクエリに合わせて最適化され、静的ではありません。 パーティションの再分割中は、いくつかのイベントが複数の BLOB に存在する可能性があります。 このフォルダー内の BLOB の名前付けは、同じままであることは保証されていません。

一般に、Parquet ファイルを介してデータに直接アクセスする必要がある場合は、`PT=Time` フォルダーを使用します。  今後提供される機能により、`PT=TsId` フォルダーに効率的にアクセスできるようになります。

> [!NOTE]
>
> * `<YYYY>` は、4 桁の年表記に対応します。
> * `<MM>` は、2 桁の月表記に対応します。
> * `<YYYYMMDDHHMMSSfff>` は、4 桁の年 (`YYYY`)、2 桁の月 (`MM`)、2 桁の日 (`DD`)、2 桁の時 (`HH`)、2 桁の分 (`MM`)、2 桁の秒 (`SS`)、3 桁のミリ秒 (`fff`) のタイムスタンプ表現に対応します。

Azure Time Series Insights Gen2 のイベントは、次のように、Parquet ファイル コンテンツにマップされます。

* 各イベントは、1 行にマップされます。
* すべての行には、イベントのタイムスタンプの **timestamp** 列が含まれています。 タイムスタンプ プロパティが null になることはありません。 イベント ソースでタイム スタンプ プロパティが指定されていない場合の既定値は、**イベント ソース エンキュー時刻**になります。 格納されているタイムスタンプは、常に UTC 形式です。
* すべての行には、Azure Time Series Insights Gen2 環境の作成時に定義される時系列 ID (TSID) 列が含まれます。 TSID プロパティ名には、サフィックス `_string` が含まれます。
* テレメトリ データとして送信される他のすべてのプロパティは、プロパティの型に応じて、`_bool` (ブール値)、`_datetime` (タイム スタンプ)、`_long` (long)、`_double` (倍精度浮動小数点数型)、`_string` (文字列)、または `dynamic` (動的) で終わる列名にマップされます。  詳細については、「[サポートされるデータ型](./concepts-supported-data-types.md)」を参照してください。
* このマッピング スキーマは、ファイル形式の最初のバージョンに適用され、**V = 1** として参照され、同じ名前のベース フォルダーに格納されます。 この機能が進化するにつれて、このマッピング スキーマが変更され、参照名が増える可能性があります。

## <a name="next-steps"></a>次のステップ

* [データ モデリング](./time-series-insights-update-tsm.md)を確認します。

* [Azure Time Series Insights Gen2 環境](./time-series-insights-update-plan.md)を計画します。
