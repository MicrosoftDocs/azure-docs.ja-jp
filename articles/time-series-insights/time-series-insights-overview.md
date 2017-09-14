---
title: "Azure Time Series Insights の概要 | Microsoft Docs"
description: "時系列のデータ分析と IoT ソリューション向けの新しいサービスである Azure Time Series Insight の概要"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: abd66208ab7ac30831f3f1eddb2891ed7bcd3995
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="what-is-azure-time-series-insights"></a>Azure Time Series Insights とは

Azure Time Series Insights は、ストレージ、分析、および視覚化コンポーネントを備えた管理型のクラウド サービスです。これらのコンポーネントにより、簡単に、数十億ものイベントを並行して取り込み、保存、調査、分析することができます。 Time Series Insights では、データの全体像を確認でき、隠れた傾向や異常の検出、ほぼリアルタイムでの根本原因分析の実施が可能になるため、IoT ソリューションの検証をすばやく行い、デバイスの大きなダウンタイムを防ぐことができます。 Time Series Insights は、イベント ブローカー (例: IoT Hub や Event Hubs) から時系列のデータを取り込み、そのデータにインデックスを作成して、構成可能な保持ポリシーに基づいてデータを削除します。 ユーザーは、直観的な UX または REST クエリ API を介してそのデータを使用します。

![Time Series Insights の概要](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>主なシナリオ

* 短時間での IoT ソリューションの監視と検証。
* 大規模な IoT データの視覚化と分析。
* 根本原因分析と異常検出の迅速化。
* 複数のデバイス、プラント、データの全体像の作成。

## <a name="capabilities-and-benefits"></a>機能と利点

* **簡単に開始できる**: Azure Time Series Insights は、事前のデータ準備が必要なく、非常に高速です。 Azure IoT Hub またはイベント ハブに含まれる数十億ものイベントに数分で接続することができます。 関連付けた後、数秒でセンサー データを視覚化および処理して、IoT ソリューションの検証を迅速に行うことができます。 Time Series Insights は手軽に使用でき、コードを 1 行も記述することなく、データを操作できます。  新しい言語を習得する必要もありません。Time Series Insights には、詳しい知識のあるユーザー向けの自由書式の詳細なクエリ画面のほか、だれでもポイントしてクリックするだけで使用できる調査機能が備わっています。

* **ほぼリアルタイムでの迅速な洞察**: Time Series Insights では、1 日あたり数億件のセンサー イベントを取り込むことができます (待ち時間は 1 分)。そのため、変化に迅速に対応できます。 Time Series Insights により、傾向や異常にすばやく焦点を当てることでセンサー データに対する理解が深まるため、複雑な根本原因分析を実施し、コストのかかるダウンタイムを回避することができます。 リアルタイム データと履歴データ間の相互相関が実現するため、Time Series Insights を使用すると、ユーザーはデータに隠れていた傾向を発見することができます。

* **カスタム ソリューションの構築**: Azure Time Series Insights のデータを既存のアプリケーションに埋め込んだり、Time Series Insights REST API を使用して新しいカスタム ソリューションを作成したりすることができます。 カスタマイズされたビューの作成と共有によって、検出したデータを他のユーザーが簡単に探索できるようになります。

* **スケーラビリティ**: Time Series Insights は、大規模に IoT をサポートするように設計されています。 プレビューでは、1 日あたり 100 万～ 1 億件のイベントを受信できます (既定の保有期間は 31 日間)。 ユーザーは大量の履歴データと共に、ほぼリアルタイムでライブ データ ストリームを視覚化して分析できます。 今後は、絶え間なく進化するエンタープライズ規模に合わせて受信レートと保有率が大きくなります。

## <a name="time-series-insights-glossary"></a>Time Series Insights 用語集

* **環境**: 環境は、受信とストレージの容量を備えた Azure リソースです。  導入ユーザーは、Azure Portal を使用して、必要な容量を備えた環境をプロビジョニングします。
* **イベント ソース**: イベント ソースは、Azure Event Hubs のようなイベント ブローカーから派生しています。  Time Series Insights は直接イベント ソースに接続してデータ ストリームを取り込みます。コードを記述する必要は一切ありません。 現在 Time Series Insights では、Azure Event Hubs と Azure IoT Hub がサポートされています。
* **参照データ**: Time Series Insights により、ユーザーは、時系列のデータを参照データと結合することができます。  参照データには、デバイスに関するメタデータや、比較的変更頻度の低いその他の静的データを含めることができます。 Time Series Insights では参照データがデータ ストリームと結合されるため、ユーザーはこのデータをほぼリアルタイムで視覚化して分析できます。

