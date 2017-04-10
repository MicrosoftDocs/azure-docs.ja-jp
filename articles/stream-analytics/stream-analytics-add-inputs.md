---
title: "Stream Analytics ジョブへのデータ入力の追加 | Microsoft Docs"
description: "Event Hubs からのストリーミング データ入力または BLOG ストレージの参照データとしてデータ ソースを Stream Analytics ジョブに接続する方法について説明します。"
keywords: "データ入力、ストリーミング データ"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f95fe2afab1205998f4c8401e162748b50ae9850
ms.lasthandoff: 11/17/2016


---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Stream Analytics ジョブへのストリーミング データ入力または参照データの追加
Event Hubs からのストリーミング データ入力または BLOB ストレージの参照データとしてデータ ソースを Stream Analytics ジョブに接続する方法について説明します。

Azure Stream Analytics ジョブを 1 つ以上のデータ入力に接続できます。各データ入力では、既存のデータ ソースへの接続が定義されています。 データがそのデータ ソースに送信されると、Stream Analytics ジョブによって使用され、リアルタイムでストリーミング データとして処理されます。 Stream Analytics は、ジョブ サブスクリプションの内外の [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) および [Azure BLOB Storage](../storage/storage-dotnet-how-to-use-blobs.md) と高度に統合されています。

この記事は、 [Stream Analytics のラーニング パス](/documentation/learning-paths/stream-analytics/)の 1 ステップです。

## <a name="data-input-streaming-data-and-reference-data"></a>データ入力: ストリーミング データと参照データ
Stream Analytics には、データ ストリームと参照データという 2 種類の入力があります。

* **データ ストリーム**: Stream Analytics のジョブには、ジョブで使用および変換される少なくとも 1 つのデータ ストリームの入力を含める必要があります。 Azure BLOB ストレージと Azure Event Hubs は、データ ストリーム入力ソースとしてサポートされます。 Azure Event Hubs は、接続されているデバイス、サービス、アプリケーションからイベント ストリームを収集するために使用されます。 ストリームとしてバルク データを取り込むための入力ソースとして、Azure BLOB ストレージを使用できます。  
* **参照データ**: Stream Analytics は、参照データと呼ばれる第 2 の種類の補助入力をサポートしています。  このデータは、動的なものではなく、静的またはあまり変更されないものです。  通常は、高度なデータ セットを作成するための検索およびデータ ストリームとの関連付けを実行するために使用されます。  現在、Azure BLOB ストレージは参照データをサポートする唯一の入力ソースです。  

Stream Analytics ジョブに入力を追加するには:

1. Azure Portal の Stream Analytics ジョブで、**[入力]** をクリックし、**[入力の追加]** をクリックします。
   
    ![Azure クラシック ポータル - 入力の追加](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    Azure ポータルで、Stream Analytics ジョブの **[入力]** タイルをクリックします。  
   
    ![Azure ポータル - データ入力の追加](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. 入力の種類として、**[データ ストリーム]** または **[参照データ]** を指定します。
   
    ![適切なデータ入力の追加、ストリーミングまたは参照](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![適切なデータ入力の追加、ストリーミングまたは参照](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. データ ストリーム入力を作成する場合は、入力のソースの種類を指定します。  参照データを作成する場合は、現時点でサポートされるのは BLOB ストレージだけなので、この手順はスキップできます。
   
    ![データ ストリームのデータ入力の追加](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![データ ストリームのデータ入力の追加](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. [入力のエイリアス] ボックスに、この入力のわかりやすい名前を入力します。  この名前は、後で入力を参照するためにジョブのクエリで使用されます。
   
    データ ソースに接続するために必要な他の接続プロパティを入力します。 これらのフィールドは入力の種類とソースの種類によって異なります。詳細は[ここ](stream-analytics-create-a-job.md)に定義されています。  
   
    ![イベント ハブのデータ入力の追加](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. 入力データのシリアル化の設定を指定します。
   
   * クエリを正しく動作させるには、受信データの **[イベントのシリアル化の形式]** を指定します。  サポートされているシリアル化形式は、JSON、CSV、Avro です。
   * データの **[エンコード]** を確認します。  現時点でサポートされているエンコード形式は UTF-8 だけです。
     
     ![データ入力のデータ シリアル化の設定](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![データ入力のデータ シリアル化の設定](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. 入力の作成が完了すると、Stream Analytics は入力ソースに接続できることを確認します。  Notification Hub でテスト接続操作の状態を表示できます。
   
    ![ストリーミング データ入力のテスト接続](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![ストリーミング データ入力のテスト接続](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>ストリーミング データ入力に関するヘルプ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


