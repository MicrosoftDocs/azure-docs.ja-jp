---
title: ストレージの概要 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 のデータ ストレージについて説明します。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676853"
---
# <a name="data-storage"></a>データ ストレージ

この記事では、Azure Time Series Insights Gen2 でのデータ ストレージについて説明します。 ウォームおよびコールド、データの可用性、およびベスト プラクティスについて説明します。

## <a name="provisioning"></a>プロビジョニング

Azure Time Series Insights Gen2 環境を作成するときには、次のオプションがあります。

* コールド データ ストレージ:
  * 環境に合わせて選択したサブスクリプションとリージョンに新しい Azure Storage リソースを作成します。
  * 既存の Azure Storage アカウントをアタッチします。 このオプションは、Azure Resource Manager [テンプレート](/azure/templates/microsoft.timeseriesinsights/allversions)から展開する場合にのみ使用でき、Azure portal には表示されません。
* ウォーム データ ストレージ:
  * ウォーム ストアはオプションであり、プロビジョニング時またはプロビジョニング後に有効または無効にすることができます。 ウォーム ストアを後で有効にする場合、コールド ストアに既にデータがあるときは、後述の[この](concepts-storage.md#warm-store-behavior)セクションを参照して、想定されている動作を理解してください。 ウォーム ストアのデータ保持期間は 7 ～ 31 日に構成でき、必要に応じて調整することもできます。

イベントが取り込まれると、ウォーム ストア (有効な場合) とコールド ストアの両方でインデックスが付けられます。

[![ストレージの概要](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> コールド ストア データが存在する Azure Blob ストレージ アカウントの所有者は、アカウント内のすべてのデータに対するフルアクセス権を持っています。 このアクセスには、書き込みおよび削除のアクセス許可が含まれます。 Azure Time Series Insights Gen2 によって書き込まれたデータを編集または削除しないでください。データが失われる原因になる可能性があります。

## <a name="data-availability"></a>データの可用性

Azure Time Series Insights Gen2 では、最適なクエリ パフォーマンスのために、データのパーティション分割とインデックス付けが行われます。 データは、インデックスが付けられた後、ウォーム ストア (有効な場合) とコールド ストアの両方からクエリを実行できるようになります。 取り込まれるデータの量とパーティションごとのスループット レートは、可用性に影響を与える可能性があります。 最適なパフォーマンスを得るには、イベント ソースの[スループットの制限](./concepts-streaming-ingress-throughput-limits.md)と[ベスト プラクティス](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)を確認してください。 環境でデータ処理の問題が発生した場合に通知されるように、遅延の[警告](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)を構成することもできます。

> [!IMPORTANT]
> データが利用可能になるまでに最大 60 秒の時間が発生することがあります。 待機時間が 60 秒を大きく上回る場合は、Azure portal を通じてサポート チケットを送信してください。

## <a name="warm-store"></a>ウォーム ストア

ウォーム ストア内のデータは、[Time Series Query API](./concepts-query-overview.md)、[Azure Time Series Insights TSI エクスプローラー](./concepts-ux-panels.md)、または [Power BI コネクタ](./how-to-connect-power-bi.md)を介してのみ使用できます。 ウォーム ストア クエリは無料であり、クォータはありませんが、同時要求数 [30 の制限](/rest/api/time-series-insights/reference-api-limits#query-apis---limits)があります。

### <a name="warm-store-behavior"></a>ウォーム ストアの動作

* 有効にすると、イベントのタイムスタンプに関係なく、環境にストリーミングされるすべてのデータがウォーム ストアにルーティングされます。 ストリーミング インジェスト パイプラインは、ほぼリアルタイムのストリーミング用に構築されており、履歴イベントの取り込みは[サポートされていません](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)。
* 保持期間は、イベントのタイムスタンプではなく、ウォーム ストアでイベントにインデックスが付けられた時点に基づいて計算されます。 これは、イベントのタイムスタンプが将来のものであっても、保持期間が経過した後はデータがウォーム ストアで使用できなくなることを意味します。
  * 例: 10 日間の天気予報を含むイベントが、7 日間の保持期間で構成されたウォーム ストレージ コンテナーに取り込まれ、インデックスが付けられます。 7 日が経過すると、ウォーム ストアでは予報にアクセスできなくなりますが、コールド ストアからは照会できます。
* コールド ストレージで最近のデータに既にインデックスが付けられている既存の環境でウォーム ストアを有効にすると、このデータはウォーム ストアにバックフィルされないことに注意してください。
* ウォーム ストアを有効にしたばかりで、最近のデータをエクスプローラーで表示するときに問題が発生している場合は、次のようにウォーム ストア クエリを一時的にオフにすることができます。

   [![ウォーム クエリを無効にする](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>コールド ストア

このセクションでは、Azure Time Series Insights Gen2 に関連する Azure Storage の詳細について説明します。

Azure Blob Storage の詳細については、[Storage Blob の概要](../storage/blobs/storage-blobs-introduction.md)に関する記事をご覧ください。

### <a name="your-cold-storage-account"></a>コールド ストレージ アカウント

Azure Time Series Insights Gen2 では、Azure Storage アカウント内の各イベントのコピーが最大 2 つ保持されます。 1 つのコピーには、イベントがインジェスト時間によって並べ替えられて格納され、常に時間順に並べられた一連のイベントにアクセスできるようになります。 時間の経過と共に、Azure Time Series Insights Gen2 では、高パフォーマンスのクエリ用に最適化するために、データの再パーティション分割コピーも作成されます。

すべてのデータは、Azure Storage アカウントに無期限に格納されます。

#### <a name="writing-and-editing-blobs"></a>BLOB の作成と編集

クエリのパフォーマンスとデータの可用性を確保するため、Azure Time Series Insights Gen2 によって作成された BLOB の編集および削除は行わないでください。

#### <a name="accessing-cold-store-data"></a>コールド ストア データへのアクセス

[Azure Time Series Insights Explorer](./concepts-ux-panels.md) および [Time Series Query API](./concepts-query-overview.md) からデータにアクセスするだけでなく、コールド ストアに格納されている Parquet ファイルから直接データにアクセスすることもできます。 たとえば、Jupyter Notebook でデータの読み取り、変換、クレンジングを行った後、それを使用して同じ Spark ワークフローで Azure Machine Learning モデルをトレーニングできます。

Azure Storage アカウントから直接データにアクセスするには、Azure Time Series Insights Gen2 データを格納するために使用しているアカウントへの読み取りアクセス権が必要です。 次に、「[Parquet ファイル形式](#parquet-file-format-and-folder-structure)」セクションで説明されている `PT=Time` フォルダーにある Parquet ファイルの作成時刻に基づいて、選択したデータを読み取ることができます。  ストレージ アカウントへの読み取りアクセスを有効にする方法の詳細については、[ストレージ アカウント リソースへのアクセスの管理](../storage/blobs/anonymous-read-access-configure.md)に関するページを参照してください。

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
* すべての行には、イベントのタイムスタンプの **timestamp** 列が含まれています。 タイムスタンプ プロパティが null になることはありません。 イベント ソースでタイム スタンプ プロパティが指定されていない場合の既定値は、**イベント ソース エンキュー時刻** になります。 格納されているタイムスタンプは、常に UTC 形式です。
* すべての行には、Azure Time Series Insights Gen2 環境の作成時に定義される時系列 ID (TSID) 列が含まれます。 TSID プロパティ名には、サフィックス `_string` が含まれます。
* テレメトリ データとして送信される他のすべてのプロパティは、プロパティの型に応じて、`_bool` (ブール値)、`_datetime` (タイム スタンプ)、`_long` (long)、`_double` (倍精度浮動小数点数型)、`_string` (文字列)、または `dynamic` (動的) で終わる列名にマップされます。  詳細については、「[サポートされるデータ型](./concepts-supported-data-types.md)」を参照してください。
* このマッピング スキーマは、ファイル形式の最初のバージョンに適用され、**V = 1** として参照され、同じ名前のベース フォルダーに格納されます。 この機能が進化するにつれて、このマッピング スキーマが変更され、参照名が増える可能性があります。

## <a name="next-steps"></a>次のステップ

* [データ モデリング](./concepts-model-overview.md)を確認します。

* [Azure Time Series Insights Gen2 環境](./how-to-plan-your-environment.md)を計画します。
