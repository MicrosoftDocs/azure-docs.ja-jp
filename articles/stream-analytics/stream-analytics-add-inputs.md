---
title: Azure Stream Analytics の入力について
description: この記事では、Azure Stream Analytics ジョブでの入力の概念について説明し、参照データ入力へのストリーミングの入力と比較します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 87e260c97a748807929a0e7021e3efb2ae8f8e7b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329282"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Azure Stream Analytics の入力について

Azure Stream Analytics ジョブは、1 つまたは複数のデータ入力に接続します。 各入力は、既存のデータ ソースへの接続を定義しています。 Stream Analytics が受け取るデータは、Event Hubs、IoT Hub、Blob ストレージなど、各種のイベント ソースから着信します。 各ジョブのために記述するストリーミング SQL クエリ内では、入力は名前によって参照されます。 クエリでは、複数の入力を結合してデータをブレンドするか、参照データへの参照を使用してストリーミング データを比較し、結果を出力に渡すことができます。 

Stream Analytics には、入力として次の 3 種類のリソースを使用する優れた統合機能が組み込まれています。
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure BLOB Storage](https://azure.microsoft.com/services/storage/blobs/) 

これらの入力ソースは、Stream Analytics ジョブと同じ Azure サブスクリプションに存在していても、異なるサブスクリプションに存在していてもかまいません。

Stream Analytics ジョブへの入力の作成、編集、テストは、[Azure portal](stream-analytics-quick-create-portal.md#configure-job-input)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput)、[.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)、[REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)、[Visual Studio](stream-analytics-tools-for-visual-studio-install.md) を使用して行えます。

## <a name="stream-and-reference-inputs"></a>ストリームと参照入力
データがそのデータ ソースにプッシュされると、Stream Analytics ジョブによって使用され、リアルタイムで処理されます。 入力は 2 つのタイプであるデータ ストリーム入力と参照データ入力に分けられます。

### <a name="data-stream-input"></a>データ ストリーム入力
データ ストリームは、時間をかけて受け取る、制約のない一連のイベントです。 Stream Analytics のジョブには、少なくとも 1 つのデータ ストリーム入力を含める必要があります。 Event Hubs、IoT Hub、および Blob Storage は、データ ストリーム入力ソースとしてサポートされます。 Event Hubs は、複数のデバイスとサービスからイベント ストリームを収集するために使用されます。 これらのストリームには、ソーシャル メディア アクティビティ フィード、株式取引情報、またはセンサーからのデータが含まれている場合があります。 IoT Hub はモノのインターネット化 (IoT) シナリオ内で、接続されているデバイスからデータを収集するように最適化されています。  ストリームとしてバルク データ (ログ ファイルなど) を取り込むための入力ソースとして、Blob Storage を使用できます。  

ストリーミングによるデータ入力の詳細については、「[Stream Analytics に入力としてデータをストリーム配信する](stream-analytics-define-inputs.md)」を参照してください

### <a name="reference-data-input"></a>参照データ入力
Stream Analytics は、"*参照データ*" と呼ばれる入力もサポートします。 参照データは、完全に静的であるか、ゆっくりと変化しているかのいずれかです。 これは通常、相関関係の関連付けと参照を実行するために使用されます。 たとえば、SQL の結合を実行して静的な値を参照する場合と同様に、データ ストリーム入力のデータを参照データのデータに結合できます。 参照データ入力ソースとして、Azure Blob Storage と Azure SQL Database が現在サポートされています。 クエリの複雑さと割り当てられているストリーミング ユニットに応じて、参照データのソース BLOB には最大 300 MB というサイズ制限があります (詳細については、参照データのドキュメントの「[サイズ制限](stream-analytics-use-reference-data.md#size-limitation)」セクションをご覧ください)。

参照データ入力に関する詳細については、「[Stream Analytics での参照に参照データを使用する](stream-analytics-use-reference-data.md)」を参照してください

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
