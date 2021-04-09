---
title: Azure Media Services テレメトリ | Microsoft Docs
description: この記事では、Microsoft Azure Media Services テレメトリの概要を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: b17b5901248056f6000710fa25d2ea1e9df2e2a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009093"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services テレメトリ  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Azure Media Services (AMS) を使用して、サービスのテレメトリ/メトリック データにアクセスできます。 現在のバージョンの AMS では、ライブ **チャネル** エンティティ、**ストリーミング ポイント** エンティ、およびライブ **アーカイブ** エンティティのテレメトリ データを取得できます。 

テレメトリは、指定した Azure Storage アカウントのストレージ テーブルに書き込まれます (通常は、AMS アカウントに関連付けられているストレージ アカウントを使用します)。 

テレメトリ システムはデータの保持期間を管理しません。 古いテレメトリ データを削除するには、ストレージ テーブルを削除します。

このトピックでは、AMS テレメトリを構成して使用する方法について説明します。

## <a name="configuring-telemetry"></a>テレメトリの構成

テレメトリはコンポーネント レベルの粒度で構成でき、 "Normal" と "Verbose" の 2 つの詳細レベルがあります。 現時点では、どちらのレベルでも同じ情報が返ります。 "Normal" を使用することをお勧めします。 

次のトピックで、テレメトリを有効にする方法を示します。

[.NET を使用したテレメトリの有効化](media-services-dotnet-telemetry.md)に関するトピック 

[REST を使用したテレメトリの有効化](media-services-rest-telemetry.md)に関するトピック

## <a name="consuming-telemetry-information"></a>テレメトリ情報の使用

テレメトリは、Media Services アカウントのテレメトリを構成するときに指定したストレージ アカウントの Azure Storage テーブルに書き込まれます。 このセクションでは、メトリック用のストレージ テーブルについて説明します。

テレメトリ データは、次のいずれかの方法で使用できます。

- Azure Table Storage から直接データを読み取ります (Storage SDK の使用など)。 テレメトリのストレージ テーブルの説明については、 **こちら** のトピックの「 [Consuming telemetry information (テレメトリ情報の使用)](/previous-versions/azure/mt742089(v=azure.100)) 」を参照してください。

または

- Media Services .NET SDK のサポートを利用してストレージ データを読み取ります ([こちら](media-services-dotnet-telemetry.md)のトピックを参照してください)。 


次に示すテレメトリ スキーマは、Azure Table Storage の制限内で高いパフォーマンスが得られるように設計されています。

- データは、各サービスのテレメトリを個別に照会できるように、アカウント ID とサービス ID 別にパーティション分割されています。
- 各パーティションには、パーティション サイズに適切な上限を設定するために日付が含まれています。
- 行キーは、特定のサービスの最新のテレメトリ項目をクエリできるように、時刻の降順で並べられます。

これにより、次のような一般的なクエリを効率的に実行できます。

- 並列で別々に実行される、複数のサービスのデータのダウンロード。
- 特定のサービスの特定の日付範囲にあるすべてのデータの取得。
- サービスの最新データの取得。

### <a name="telemetry-table-storage-output-schema"></a>テレメトリ テーブル ストレージの出力スキーマ

テレメトリ データは、1 つのテーブルにまとめて格納されます (例: "TelemetryMetrics20160321"。"20160321" はテーブルの作成日です)。 テレメトリ システムでは、00:00 UTC に基づいて日が変わるごとに個別のテーブルが作成されます。 テーブルは、繰り返し発生する値 (特定の時間帯の取り込みビットレートや送信されたバイト数など) を格納するために使用されます。 

プロパティ|値|例/メモ
---|---|---
パーティション キー|{アカウント ID}_{エンティティ ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>アカウント ID は、ワークフローを簡単にするためにパーティション キーに含まれ、複数の Media Services アカウントが同じストレージ アカウントに書き込まれます。
RowKey|{午前 0 時までの秒数}_{ランダム値}|01688_00199<br/><br/>行キーは、パーティション内の上位 n 件を取得するスタイルのクエリを可能にするために、午前 0 時までの秒数から始まります。 詳細については、[こちらの記事](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern)を参照してください。 
Timestamp|日付/時刻|Azure Table の自動タイムスタンプ 2016-09-09T22:43:42.241Z
種類|テレメトリ データを提供するエンティティの種類|Channel/StreamingEndpoint/Archive<br/><br/>イベントの種類は単なる文字列値です。
名前|テレメトリ イベントの名前|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|テレメトリ イベントが発生した時刻 (UTC)|2016-09-09T22:42:36.924Z<br/><br/>監視時刻は、テレメトリを送信するエンティティ (たとえばチャネル) によって提供されます。 コンポーネント間で時間同期問題が存在する可能性があるため、この値は概算値です。
ServiceID|{サービス ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
エンティティ固有のプロパティ|イベントによって定義されたとおり|StreamName: stream1, Bitrate 10123, …<br/><br/>残りのプロパティは、指定されたイベントの種類に対して定義されます。 Azure Table の内容は、キーと値のペアです   (つまり、テーブル内の異なる行には、異なるプロパティのセットが格納されます)。

### <a name="entity-specific-schema"></a>エンティティ固有のスキーマ

エンティティ固有の 3 種類のテレメトリ データ エントリがあり、それぞれ次の頻度でプッシュされます。

- ストリーミング エンドポイント: 30 秒ごと
- ライブ チャネル: 1 分ごと
- ライブ アーカイブ: 1 分ごと

**ストリーミング エンドポイント**

プロパティ|値|例
---|---|---
パーティション キー|パーティション キー|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Azure Table の自動タイムスタンプ 2016-09-09T22:43:42.241Z
種類|種類|StreamingEndpoint
名前|名前|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|サービス ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|エンドポイントのホスト名|builddemoserver.origin.mediaservices.windows.net
StatusCode|HTTP 状態の記録|200
ResultCode|結果コードの詳細|S_OK
RequestCount|集計内の要求数の合計|3
BytesSent|集計された送信バイト数|2987358
ServerLatency|サーバーの平均待機時間 (ストレージを含みます)|129
E2ELatency|エンド ツー エンドの平均待機時間|250

**ライブ チャネル**

プロパティ|値|例/メモ
---|---|---
パーティション キー|パーティション キー|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Azure Table の自動タイムスタンプ 2016-09-09T22:43:42.241Z
種類|種類|チャネル
名前|名前|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|サービス ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|トラックの種類 (video/audio/text)|video/audio
TrackName|トラックの名前|video/audio_1
Bitrate|トラックのビットレート|785000
CustomAttributes||   
IncomingBitrate|実際の受信ビットレート|784548
OverlapCount|取り込み内の重複|0
DiscontinuityCount|トラックの不連続性|0
LastTimestamp|最後に取り込んだデータのタイムスタンプ|1800488800
NonincreasingCount|タイムスタンプに変化がないために破棄されたフラグメントの数|2
UnalignedKeyFrames|キー フレームがアラインされていないフラグメントを受信したかどうか (品質レベル) |True
UnalignedPresentationTime|プレゼンテーション時間がアラインされていないフラグメントを受信したかどうか (品質レベル/トラック)|True
UnexpectedBitrate|オーディオ/ビデオ トラックの計算ビットレートまたは実ビットレートが > 40,000 bps であり、IncomingBitrate == 0 であるか IncomingBitrate と actualBitrate の差が 50% の場合は True |True
Healthy|次の場合は True: <br/>overlapCount、 <br/>DiscontinuityCount、 <br/>NonIncreasingCount、 <br/>UnalignedKeyFrames、 <br/>UnalignedPresentationTime、 <br/>UnexpectedBitrate<br/> がいずれも 0|True<br/><br/>Healthy は、次の条件のいずれかに該当する場合は false を返す複合関数です。<br/><br/>- OverlapCount > 0<br/>- DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**ライブ アーカイブ**

プロパティ|値|例/メモ
---|---|---
パーティション キー|パーティション キー|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Azure Table の自動タイムスタンプ 2016-09-09T22:43:42.241Z
種類|種類|アーカイブ
名前|名前|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|サービス ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|プログラムの URL|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|トラックの名前|audio_1
TrackType|トラックの種類|Audio/video
カスタム属性|名前とビットレートが同じ複数のトラックを区別する 16 進数の文字列 (マルチ カメラ アングル)|
Bitrate|トラックのビットレート|785000
Healthy|FragmentDiscardedCount == 0 && ArchiveAcquisitionError == False の場合は True|True (これら 2 つの値はメトリック内には存在しないがソース イベントには存在する)<br/><br/>Healthy は、次の条件のいずれかに該当する場合は false を返す複合関数です。<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>全般的な Q&A

### <a name="how-to-consume-metrics-data"></a>メトリック データの使用方法は?

メトリック データは、一連の Azure Table として顧客のストレージ アカウントに格納されます。 このデータは、次のツールで使用できます。

- AMS SDK
- Microsoft Azure Storage Explorer (Excel で処理できるコンマ区切り値形式でのエクスポートをサポートします)
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>帯域幅の平均使用量を調べる方法は?

帯域幅の平均使用量は、一定期間の BytesSent の平均です。

### <a name="how-to-define-streaming-unit-count"></a>ストリーミング ユニット数を定義する方法は?

ストリーミング ユニット数は、サービスのストリーミング エンドポイントからのピーク スループットを 1 つのストリーミング エンドポイントのピーク スループットで除算したピーク スループットと定義できます。 1 つのストリーミング エンドポイントの使用可能なピーク スループットは、160 Mbps です。
たとえば、顧客のサービスからのピーク スループットが 40 MBps であるとします (一定時間の BytesSent の最大値)。 この場合、ストリーミング ユニット数は、(40 MBps)*(8 ビット/バイト)/(160 Mbps) = 2 になります。

### <a name="how-to-find-average-requestssecond"></a>平均要求数/秒を調べる方法は?

平均要求数/秒を調べるには、一定時間の要求 (RequestCount) の平均数を計算します。

### <a name="how-to-define-channel-health"></a>チャネルの状態を定義する方法は?

チャネルの状態は、次の条件のいずれかに該当する場合は false になる複合ブール関数として定義できます。

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>不連続性を検出する方法は?

不連続性を検出するには、DiscontinuityCount > 0 であるすべての Channel データ エントリを探します。 対応する ObservedTime タイムスタンプが、不連続性の発生時刻を示します。

### <a name="how-to-detect-timestamp-overlaps"></a>タイムスタンプの重複を検出する方法は?

タイムスタンプの重複を検出するには、OverlapCount > 0 であるすべての Channel データ エントリを探します。 対応する ObservedTime タイムスタンプが、タイムスタンプの重複が発生した時刻を示します。

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>ストリーミング要求エラーとその理由を調べる方法は?

ストリーミング要求エラーとその理由を調べるには、ResultCode が S_OK ではないすべてのストリーミング エンドポイントのデータ エントリを探します。 対応する StatusCode フィールドで、要求エラーの理由が示されます。

### <a name="how-to-consume-data-with-external-tools"></a>外部ツールを使用してデータを使用する方法は?

テレメトリ データは、次のツールで処理して視覚化できます。

- PowerBI
- Application Insights
- Azure Monitor (旧称 Shoebox)
- AMS ライブ ダッシュボード
- Azure Portal (保留中のリリース)

### <a name="how-to-manage-data-retention"></a>データ保有期間を管理する方法は?

テレメトリ システムは、データ保有期間の管理も古いレコードの自動削除も実行しません。 このため、ストレージ テーブルの管理と古いレコードの削除は、手動で行う必要があります。 方法については、Storage SDK を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
