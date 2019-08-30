---
title: Azure Time Series Insights プレビューのデータ ストレージおよびイングレス | Microsoft Docs
description: Azure Time Series Insights プレビューのデータ ストレージおよびイングレスについて理解します。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 98baa8d3f951a8922bcd1f40449fa26840f3a3c4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051477"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのデータ ストレージおよびイングレス

この記事では、Azure Time Series Insights プレビューからのデータ ストレージとイングレスの変更について説明します。 基になるストレージ構造、ファイル形式、および Time Series ID プロパティについて取り上げます。 さらに、基になるイングレス プロセス、スループット、および制限事項についても説明します。

## <a name="data-ingress"></a>データのイングレス

Azure Time Series Insights データ イングレス ポリシーは、データのソースとすることができる場所とデータの形式を決定します。

[![タイム シリーズ モデルの概要](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>イングレス ポリシー

Time Series Insights プレビューは、Time Series Insights が現在サポートしているもの同じイベント ソースとファイルの種類をサポートします。

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights では、Azure IoT Hub または Azure Event Hubs を介して送信された JSON がサポートされています。 IoT JSON データを最適化するには、[JSON 構造化方法](./time-series-insights-send-events.md#json)に関するページを参照してください。

### <a name="data-storage"></a>データ ストレージ

Time Series Insights プレビューの従量課金制 SKU 環境を作成する場合、次の 2 つのリソースを作成します。

* Time Series Insights 環境。
* データを格納する Azure Storage 汎用 V1 アカウント。

Time Series Insights プレビューでは、Parquet ファイルの種類で Azure Blob Storage を使用します。 Time Series Insights は、Azure ストレージ アカウント内のデータの BLOB の作成、インデックス作成、パーティション分割などのすべてのデータ操作を管理します。 これらの BLOB を作成するには、Azure Storage アカウントを使用します。

他の Azure Storage BLOB のように、Time Series Insights が作成した BLOB により、それらを読み書きすることで、さまざまな統合シナリオをサポートできます。

### <a name="data-availability"></a>データの可用性

Time Series Insights プレビューは、BLOB サイズの最適化戦略を使用してデータのインデックスを作成します。 データは、インデックスの作成後、クエリに使用できるようになり、それは取り込むデータの量と速度に基づきます。

> [!IMPORTANT]
> * Time Series Insights の一般提供 (GA) リリースでは、イベント ソースに到着後 60 秒以内でデータを利用できるようになります。
> * プレビュー中は、データを利用できるようになるまでにかかる時間が長くなると予想されます。
> * 大幅な待機時間が発生した場合は、お問い合わせください。

### <a name="scale"></a>スケール

Time Series Insights プレビューは、環境あたり最大 1 MB/秒 (Mbps) の初期イングレス スケールをサポートします。 拡張スケーリング サポートが進行中です。 これらの機能強化を反映するように、ドキュメントを更新する予定です。

## <a name="parquet-file-format"></a>Parquet ファイル形式

Parquet は、次の目的で設計された列指向のデータ ファイル形式です。

* 相互運用性
* スペース効率
* クエリの効率

Time Series Insights では、複雑なデータを一括処理できる拡張されたパフォーマンスで効率的なデータ圧縮とエンコード スキームを提供するために、Parquet が選択されています。

Parquet ファイルの種類の詳細については、[Parquet のドキュメント](https://parquet.apache.org/documentation/latest/)を参照してください。

Azure での Parquet ファイル形式の詳細については、[Azure Storage でサポートされているファイルの種類](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format)に関するページを参照してください。

### <a name="event-structure-in-parquet"></a>Parquet のイベント構造

Time Series Insights では、次の 2 つの形式で BLOB のコピーが作成され、格納されます。

1. まず、初期コピーが到着時刻でパーティション分割されます。

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * 到着時刻でパーティション分割された BLOB の BLOB 作成時刻。

1. 2 つ目に、タイム シリーズ ID の動的なグループ化によって、パーティション再分割対象のコピーがパーティション分割されます。

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * タイム シリーズ ID によってパーティション分割された BLOB の BLOB 内で最小のイベント タイムスタンプ。

> [!NOTE]
> * `<YYYY>` は、4 桁の年表記にマップされます。
> * `<MM>` は、2 桁の月表記にマップされます。
> * `<YYYYMMDDHHMMSSfff>` は、4 桁の年 (`YYYY`)、2 桁の月 (`MM`)、2 桁の日 (`DD`)、2 桁の時間 (`HH`)、2 桁の分 (`MM`)、2 桁の秒 (`SS`)、および 3 桁のミリ秒 (`fff`) のタイムスタンプ表記にマップされます。

Time Series Insights イベントは、次のように、Parquet ファイル コンテンツにマップされます。

* 各イベントは、1 行にマップされます。
* イベントのタイムスタンプを含む組み込みの **Timestamp** 列。 Timestamp プロパティは null にできません。 イベント ソースに Timestamp プロパティが指定されていない場合、既定値は**イベント ソース エンキュー時刻**に設定されます。 Timestamp は UTC です。 
* 列にマップされているその他のすべてのプロパティは、プロパティの型に応じて、`_string` (文字列)、`_bool` (ブール値)、`_datetime` (datetime)、および `_double` (double) で終わります。
* それは、最初のバージョンのファイル形式のマッピング スキームであり、それを **V=1** と呼んでいます。 この機能の発展に伴って、名前は **V=2**、**V=3** というように増分されます。

## <a name="azure-storage"></a>Azure Storage

このセクションでは、Azure Time Series Insights に関連する Azure Storage の詳細について説明します。

Azure Blob Storage サービスの詳細については、 [Storage BLOB の概要](../storage/blobs/storage-blobs-introduction.md)に関する記述を参照してください。

### <a name="your-storage-account"></a>ストレージ アカウント

Time Series Insights の従量課金制環境を作成する際に、Time Series Insights 環境と、データが格納される Azure Storage 汎用 V1 アカウントの 2 つのリソースを作成します。 相互運用性、価格、およびパフォーマンスのため、Azure Storage 汎用 V1 を既定のリソースとして選択しました。

Time Series Insights は、Azure Storage アカウントで、各イベントの最大 2 つのコピーを発行します。 初期コピーは常に保持されるため、他のサービスを使用して、すばやく照会できます。 生の Parquet ファイル全体のタイム シリーズ ID 間で、Spark、Hadoop、およびその他の使い慣れたツールを簡単に使用できます。これらのエンジンでは、基本的なファイル名フィルターがサポートされているためです。 年および月で BLOB をグループ化することは、カスタム ジョブの特定の時間範囲内の BLOB を一覧表示する便利な方法です。

さらに、Time Series Insights は、Parquet ファイルのパーティションを再分割し、Time Series Insights API 用に最適化します。 最新のパーティション再分割されたファイルも保存されます。

パブリック プレビュー中、データは、Azure Storage アカウントに無期限に格納されます。

### <a name="writing-and-editing-time-series-insights-blobs"></a>Time Series Insights BLOB の作成と編集

クエリのパフォーマンスとデータの可用性を確保するため、Time Series Insights によって作成されたすべての BLOB を編集または削除しないでください。

> [!TIP]
> BLOB の読み書きが著しく頻繁な場合に、Time Series Insights のパフォーマンスが影響を受けることがあります。

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Time Series Insights プレビューからのデータのアクセスとエクスポート

他のサービスと連携して使用するために、Time Series Insights プレビュー エクスプローラーに格納されているデータにアクセスする場合があります。 たとえば、Power BI でレポートする、Azure Machine Learning Studio を使用して機械学習を実行する、または Jupyter Notebook のノートブック アプリケーションで使用するためにデータを使用することがあります。

次の 3 つの一般的な方法でデータにアクセスできます。

* Time Series Insights プレビュー エクスプローラーから: Time Series Insights プレビュー エクスプローラーから CSV ファイルとして、データをエクスポートできます。 詳細については、[Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)に関するページを参照してください。
* Time Series Insights プレビュー API から: API エンドポイントには、`/getRecorded` で到達できます。 この API の詳細については、[Time Series クエリ](./time-series-insights-update-tsq.md)に関するページを参照してください。
* Azure Storage アカウントから直接 (下記参照)。

#### <a name="from-an-azure-storage-account"></a>Azure Storage アカウントから

* Time Series Insights データへのアクセスに使用するどのアカウントにも読み取りアクセスが必要です。 詳細については、[ストレージ アカウント リソースへのアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。
* Azure Blob Storage からデータを読み取る直接の方法の詳細については、「[データ転送用の Azure ソリューションを選択する](../storage/common/storage-choose-data-transfer-solution.md)」を参照してください。
* Azure ストレージ アカウントからデータをエクスポートするには:
    * まず、アカウントがデータをエクスポートするための要件を満たしていることを確認します。 詳細については、[ストレージのインポートとエクスポートの要件](../storage/common/storage-import-export-requirements.md)に関するページを参照してください。
    * Azure ストレージ アカウントからデータをエクスポートするその他の方法については、[BLOB からのデータのインポートおよびエクスポート](../storage/common/storage-import-export-data-from-blobs.md)に関するページを参照してください。

### <a name="data-deletion"></a>データの削除

BLOB は削除しないでください。 Time Series Insights プレビューは、データの記録の監査と保持に役立つだけでなく、各 BLOB 内には BLOB のメタデータも保持されます。

## <a name="partitions"></a>パーティション

各 Time Series Insights プレビュー環境には、それを一意に識別する **Time Series ID** プロパティと **Timestamp** プロパティが必要です。 タイム シリーズ ID は、データの論理パーティションとして機能し、Time Series Insights プレビュー環境に、物理パーティション間でデータを分散するための自然な境界を提供します。 物理パーティションは、Azure Storage アカウントで Time Series Insights プレビューによって管理されます。

Time Series Insights は、動的なパーティション分割を使用して、パーティションの削除や再作成によって、ストレージとクエリのパフォーマンスを最適化します。 Time Series Insights プレビューの動的パーティション分割アルゴリズムでは、1 つの物理パーティションが、複数の個別の論理パーティションのデータを保持することがないようにします。 つまり、パーティション分割アルゴリズムでは、他のタイム シリーズ ID とインターリーブされることなく、すべてのデータが、Parquet ファイルに排他的に存在する 1 つのタイム シリーズ ID に固有のものとされます。 さらに、動的パーティション分割アルゴリズムでは、1 つのタイム シリーズ ID 内のイベントの元の順序を維持しようとします。

最初の受信時に、特定の時間範囲内の 1 つの論理パーティションを複数の物理パーティションに分散できるように、データがタイムスタンプによってパーティション分割されます。 1 つの物理パーティションに、多くのまたはすべての論理パーティションが含まれることもあります。 BLOB のサイズ制限のため、最適なパーティション分割でも、1 つの論理パーティションで、複数の物理パーティションを占有することがあります。

> [!NOTE]
> 既定で、タイムスタンプ値は、構成されているイベント ソースのメッセージ、*エンキューされた時刻*です。

履歴データまたはバッチ メッセージをアップロードしている場合は、データと共に格納する値を、適切なタイムスタンプにマップされる Timestamp プロパティに割り当てます。 Timestamp プロパティ名は大文字と小文字が区別されます。 詳細については、「[Time Series Model](./time-series-insights-update-tsm.md)」(Time Series モデル) を参照してください。

### <a name="physical-partitions"></a>物理パーティション

物理パーティションは、ストレージ アカウントに格納されているブロック BLOB です。 BLOB の実際のサイズはプッシュ率に依存するため、さまざまに異なることがあります。 ただし、BLOB のサイズは約 20 MB から 50 MB になるものと予想されます。 この予想により、Time Series Insights チームでは、クエリ パフォーマンスを最適化するサイズとして 20 MB を選択しました。 このサイズは、ファイル サイズとデータ イングレスの速度に応じて、時間の経過と共に変わる可能性があります。

> [!NOTE]
> * BLOB のサイズは 20 MB に設定されます。
> * Azure BLOB は時々、パフォーマンス向上のために、削除および再作成によって、パーティションが再分割されます。
> * さらに、同じ Time Series Insights データが複数の BLOB に存在する可能性があります。

### <a name="logical-partitions"></a>論理パーティション

論理パーティションは、1 つのパーティション キー値に関連付けられているすべてのデータを格納する物理パーティション内のパーティションです。 Time Series Insights プレビューは、次の 2 つのプロパティに基づいて各 BLOB を論理的にパーティション分割します。

* **Time Series ID**:イベント ストリームとモデル内のすべての Time Series Insights データのパーティション キー。
* **Timestamp**:初期イングレスに基づいた時間。

Time Series Insights プレビューは、これら 2 つのプロパティに基づいた高いパフォーマンスのクエリを提供します。 これら 2 つのプロパティは、Time Series Insights データを迅速に配信するための最も効果的な方法も提供します。

Time Series ID は不変のプロパティであるため、適切なタイム シリーズ ID を選択することが重要です。 詳細については、[タイム シリーズ ID の選択](./time-series-insights-update-how-to-id.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)について確認する。

- 新しい[データ モデリング](./time-series-insights-update-tsm.md)について確認する。
