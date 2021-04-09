---
title: Azure Stream Analytics on IoT Edge
description: Azure Stream Analytics でエッジ ジョブを作成し、Azure IoT Edge で実行中のデバイスに展開します。
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012616"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics on IoT Edge
 
Azure Stream Analytics on IoT Edge を使用することで、開発者は、ほぼリアルタイムの分析インテリジェンスをより IoT デバイスに近い場所に展開し、デバイス生成データの価値を最大限に活用できるようになります。 Azure Stream Analytics は、低遅延、回復性、帯域幅の効率的な使用、およびコンプライアンスを考慮して設計されています。 企業は制御ロジックを生産工程に近い場所に展開し、クラウドで実行されるビッグ データ分析を補完できます。

Azure Stream Analytics on IoT Edge は [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) フレームワークの内部で実行されます。 Stream Analytics でジョブが作成されたら、IoT Hub を使用して展開し、管理できます。

## <a name="common-scenarios"></a>一般的なシナリオ

このセクションでは、Stream Analytics on IoT Edge の一般的なシナリオについて説明します。 次の図は、IoT デバイスと Azure クラウド間でのデータ フローを示したものです。

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge の概要図":::

### <a name="low-latency-command-and-control"></a>コマンドと制御の待機時間が短い

製造安全システムは、非常に短い待機時間で運用データに応答する必要があります。 Stream Analytics on IoT Edge を使用することで、センサー データをほぼリアルタイムで分析し、異常を検出した際はコマンドを発行してマシンを停止したり、アラートをトリガーしたりできます。

### <a name="limited-connectivity-to-the-cloud"></a>クラウドへの接続が制限されている

リモート採掘装置、接続された船舶、海洋掘削など、ミッション クリティカルなシステムでは、クラウド接続が断続的なときでも、データを分析し、そのデータに対応する必要があります。 Stream Analytics では、ネットワーク接続とは関係なくストリーミング ロジックが実行され、さらなる処理や保存のためにクラウドに送信するデータを選択できます。

### <a name="limited-bandwidth"></a>帯域幅が制限されている

ジェット エンジンやコネクテッド カーによって生成されるデータ量は膨大になる可能性があるため、クラウドに送信する前に、データをフィルター処理または前処理する必要があります。 Stream Analytics を使用すると、クラウドに送信する必要があるデータをフィルター処理したり集計したりできます。

### <a name="compliance"></a>コンプライアンス

法令順守では、一部のデータについてはクラウドに送信する前に、ローカルでの匿名化または集計が必要になる可能性があります。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics のエッジ ジョブ

Stream Analytics エッジ ジョブは、[Azure IoT Edge デバイス](../iot-edge/about-iot-edge.md)に展開されたコンテナー内で実行されます。 Edge ジョブは 2 つの部分で構成されます。

* ジョブ定義の役割を担うクラウド部分: ユーザーが、クラウド内の入力、出力、クエリ、およびその他の設定 (順不同のイベントなど) を定義します。

* IoT デバイス上で実行されているモジュール。 モジュールが Stream Analytics エンジンを含んでおり、クラウドからジョブ定義を受け取ります。 

Stream Analytics では、IoT Hub を使用してエッジ ジョブをデバイスに展開します。 詳細については、[IoT Edge のデプロイ](../iot-edge/module-deployment-monitoring.md)に関するページを参照してください。

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure Stream Analytics エッジ ジョブ":::

## <a name="edge-job-limitations"></a>Edge ジョブの制限事項

目的は、IoT Edge ジョブとクラウド ジョブの間の類似性を確認することです。 ほとんどの SQL クエリ言語機能は、エッジとクラウドの両方でサポートされています。 ただし、次の機能はエッジ ジョブではサポートされていません。
* JavaScript でのユーザー定義関数 (UDF)。 UDF は、[IoT Edge ジョブ向けの C#](./stream-analytics-edge-csharp-udf.md) (プレビュー) で使用できます。
* ユーザー定義集計 (UDA)。
* Azure ML 関数。
* 入力/出力の AVRO 形式。 現時点では、CSV と JSON のみがサポートされています。
* 次の SQL 演算子:
    * PARTITION BY
    * GetMetadataPropertyValue
* 遅延到着ポリシー

### <a name="runtime-and-hardware-requirements"></a>ランタイムとハードウェア要件
Stream Analytics on IoT Edge を実行するには、[Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) を実行できるデバイスが必要です。 

Stream Analytics と Azure IoT Edge では、**Docker** コンテナーを使用して、複数のホスト オペレーティング システム (Windows、Linux) で実行されるポータブル ソリューションを提供します。

Stream Analytics on IoT Edge は、x86-64 または ARM (Advanced RISC Machines) アーキテクチャの両方で実行される Windows および Linux イメージとして使用可能になります。 


## <a name="input-and-output"></a>入力と出力

Stream Analytics Edge ジョブは、IoT Edge デバイスで実行されている他のモジュールから入力と出力を取得できます。 特定のモジュールとの接続を確立するには、展開時にルーティング構成を設定します。 詳細については、[IoT Edge モジュールの構成に関するドキュメント](../iot-edge/module-composition.md)をご覧ください。

入力と出力の両方で、CSV 形式および JSON 形式がサポートされます。

Stream Analytics ジョブで作成した入力ストリームおよび出力ストリームごとに、対応するエンドポイントが、展開済みのモジュールに作成されます。 こうしたエンドポイントは、展開のルートで使用できます。

サポートされているストリーム入力の種類は次のとおりです。
* Edge ハブ
* イベント ハブ
* IoT Hub

サポートされているストリーム出力の種類は次のとおりです。
* Edge ハブ
* SQL Database
* イベント ハブ
* Blob Storage/ADLS Gen2

参照入力では参照ファイルがサポートされます。 他の出力には、ダウンストリームのクラウド ジョブを使用して到達できます。 たとえば、Edge 内でホストされている Stream Analytics ジョブで出力が Edge Hub に送信された後、Edge Hub から IoT Hub に出力を送信できます。 クラウドでホストされている 2 つ目の Azure Stream Analytics ジョブを、IoT Hub からの入力と Power BI または別の出力の種類への出力で使用できます。

## <a name="license-and-third-party-notices"></a>ライセンスとサード パーティに関する通知
* [Azure Stream Analytics on IoT Edge ライセンス](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [Azure Stream Analytics on IoT Edge に関するサード パーティへの通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics モジュールのイメージ情報 

このバージョン情報は、2020 年 9 月 21 日に最終更新が行われました。

- イメージ: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - 基本イメージ: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - プラットフォーム:
      - アーキテクチャ: amd64
      - OS: linux
 
- イメージ: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - 基本イメージ: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - プラットフォーム:
      - アーキテクチャ: arm
      - OS: linux
 
- イメージ: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - 基本イメージ: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - プラットフォーム:
      - アーキテクチャ: arm64
      - OS: linux
      
      
## <a name="get-help"></a>ヘルプの参照
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure IoT Edge の詳細](../iot-edge/about-iot-edge.md)
* [Stream Analytics on IoT Edge のチュートリアル](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visual Studio Tools を使用して Stream Analytics Edge ジョブを作成する](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [API を使用して Stream Analytics 向けの CI/CD を実装する](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
